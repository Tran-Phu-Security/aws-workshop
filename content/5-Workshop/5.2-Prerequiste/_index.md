---
title : "Architecture and data flow"
date : 2026-11-27
weight : 3
chapter : false
pre : " <b> 5.3. </b> "
---

![Trust Check architecture](/aws-workshop/images/5-Workshop/5.3-Architecture/trust_check_architecture.png)

#### The main idea

A scan can take a few seconds up to a minute because it calls several external sources. So the system does not make the user wait inside one request. Instead:

1. The API accepts the request, creates a **job** and returns a `job_id` right away.
2. A worker processes the job in the background.
3. The frontend asks for the result by `job_id` (every 2 seconds) until the job is done.

Links and emails are light, so they use a fast queue. Files are heavier and need an upload first, so they use a separate queue. That way one large file does not slow down link requests.

#### What each service does

| Service | Component | Job |
|---|---|---|
| API Gateway (HTTP API) | `POST /scan`, `GET /scan/{job_id}` | Entry point of the system |
| Lambda | `IngestLambda` | Validates input, checks the cache, creates the job, sends the SQS message, creates the presigned URL for files |
| Lambda | `StatusLambda` | Reads job status and result from DynamoDB |
| Lambda | `FastDetectWorker` | Analyses links and emails |
| Lambda | `FileDetectWorker` | Analyses files |
| SQS | `phishing-fast-queue` | Queue for links and emails (visibility timeout 60 s) |
| SQS | `phishing-file-queue` | Queue for files (visibility timeout 300 s) |
| SQS | `phishing-dlq` | Dead-letter queue, holds messages that failed repeatedly |
| DynamoDB | `PhishingScanJobs` | Stores jobs: status, score, result. Partition key `job_id` |
| DynamoDB | `PhishingScanCache` | Stores results for reuse. Partition key `target_hash`, with a TTL |
| S3 | Upload bucket | Receives files uploaded directly by users |
| S3 | Reports bucket (optional) | Stores a JSON log per scan, written only when `REPORTS_BUCKET` is set |
| Systems Manager | Parameter Store (optional) | Stores API keys as `SecureString` |
| CloudWatch | Logs, Metrics | Monitors the Lambda functions |

#### Data flow

**For links and emails**

1. The frontend sends `POST /scan` with `type` set to `link` or `email`, plus the `payload`.
2. `IngestLambda` hashes the `payload` with SHA-256 and looks it up in `PhishingScanCache`. If a valid result exists, it returns it immediately.
3. Otherwise it creates a `PENDING` job in `PhishingScanJobs`, sends a message to `phishing-fast-queue` and returns the `job_id`.
4. SQS triggers `FastDetectWorker`. The worker sets the status to `PROCESSING`, analyses the input and calls external sources.
5. The **Scoring Engine** turns the findings into a trust score, risk level, confidence and a list of reasons.
6. The worker writes the result to the job (`COMPLETED`), writes it to the cache (24-hour TTL), and writes a JSON log to S3 if a reports bucket is set.
7. The frontend calls `GET /scan/{job_id}`. `StatusLambda` reads DynamoDB and returns the result.

**For files** two things differ:

+ In step 3, `IngestLambda` also creates a **presigned URL** (valid for 5 minutes). The frontend uses it to `PUT` the file straight to S3, without going through Lambda or API Gateway.
+ `FileDetectWorker` waits up to 30 seconds for the file to appear in S3, downloads it to `/tmp`, computes SHA-256 and MD5, checks magic bytes, YARA-style rules, macros, active content in PDFs, executables inside archives and entropy, then looks up VirusTotal and MalwareBazaar by hash. Your file is not uploaded to those sources, except to VirusTotal when `VT_UPLOAD_UNKNOWN_FILES` is on (it is on by default).

#### Job states

`PENDING` (received) → `PROCESSING` (scanning) → `COMPLETED` (done, has a `result`).

If the analysis fails, the job still ends as `COMPLETED` with `risk_level` set to `SCAN ERROR`. Error results are not written to the cache.

#### How the score works

The score starts at 100 and loses points for each finding. Detectors only report findings. The rule table in `common/scoring.py` decides the penalty and the reason shown. A few principles worth knowing:

+ Each finding code counts once and each group has a maximum penalty, so the score is not over-penalised.
+ Serious findings set a ceiling. For example, if Google Safe Browsing or URLhaus flags a link, the score is at most 20.
+ A source with a missing key, exhausted quota or network error is skipped and lowers `confidence`. The system never treats "could not ask" as "clean".
+ Results with LOW `confidence` or `SCAN ERROR` are not cached.

#### Technical points worth noting

+ **Duplicate messages.** SQS Standard can deliver a message more than once. The worker skips a job that is already `COMPLETED`.
+ **Time budget.** Each worker tracks the remaining Lambda time and stops calling external sources early enough to save the result.
+ **Automatic cleanup.** Jobs get an `expires_at` of 7 days and cache entries 24 hours. DynamoDB TTL deletes are not instant, so the code always checks expiry itself.
+ **Outbound network.** When scanning a link, the worker really visits it to follow redirects and read the landing page. The code blocks internal addresses to prevent SSRF.
+ **Permissions.** Each function uses its own role, limited to the tables, queues and buckets it needs (details in section 5.4).

#### Current limitations

+ Scanning the same link or email again within 24 hours can return HTTP 500. The cache branch of `IngestLambda` does not convert DynamoDB numeric types when it builds the JSON. Section 5.5 mentions this again during testing.
+ The cache currently helps mainly with links and emails. For files the cache key is the SHA-256 of the content, but at request time the system only knows the file name, so it cannot look it up.
+ ClamAV, the real YARA and PDF export are not available, as noted in section 5.1.