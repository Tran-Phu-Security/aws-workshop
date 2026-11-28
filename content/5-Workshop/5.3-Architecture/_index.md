---
title : "Architecture and data flow"
date : 2026-11-27
weight : 3
chapter : false
pre : " <b> 5.3. </b> "
---

#### Architecture overview

Trust Check is an event-driven serverless system. A scan request passes through API Gateway to an ingest Lambda, is placed on an SQS queue for workers to process asynchronously, and the result is stored in DynamoDB for the web frontend to query.

![Trust Check architecture overview](/aws-workshop/images/5-Workshop/5.3-Architecture/architecture-overview.png)

#### Components

| Component | Role |
|---|---|
| Amazon API Gateway (HTTP API) | Exposes two routes: `POST /scan` to submit a request and `GET /scan/{job_id}` to fetch a result |
| `IngestLambda` | Validates input, checks the cache, creates a job and sends it to the matching queue |
| `StatusLambda` | Reads the status and result of a job from DynamoDB |
| `FastDetectWorker` | Analyses links and emails, taking work from the fast queue |
| `FileDetectWorker` | Analyses files uploaded to S3, taking work from the file queue |
| Amazon SQS | Three queues: fast, file and a dead-letter queue (DLQ) |
| Amazon DynamoDB | The `PhishingScanJobs` table stores jobs and results, the `PhishingScanCache` table stores reusable results |
| Amazon S3 | Receives user files uploaded through a presigned URL |
| AWS Systems Manager Parameter Store | Stores API keys as `SecureString` parameters under the `/phishing/` prefix |
| AWS IAM | Gives each Lambda the minimum permissions on exactly the resources it needs |
| Amazon CloudWatch | Stores the logs of the Lambda functions |

The Lambda functions use Python 3.12 on the arm64 architecture. The two workers can add results from VirusTotal, Google Safe Browsing and URLhaus to the internal analysis.

#### Data flow

![Data flow of a scan](/aws-workshop/images/5-Workshop/5.3-Architecture/data-flow.png)

1. The web frontend sends `POST /scan` with a `type` of `link`, `email` or `file`, together with a `payload`.
2. API Gateway forwards the request to `IngestLambda`. The function validates the input and returns a `400` error if it is invalid.
3. For links and emails, the function hashes the content with SHA-256 and looks it up in the `PhishingScanCache` table. If a result that has not expired exists, the function returns it immediately.
4. Otherwise the function creates a job in the `PhishingScanJobs` table with a pending status, then sends a message to a queue. Links and emails go to the fast queue. Files go to the file queue, and the response includes an `upload_url` so that the frontend can upload the file to S3.
5. A worker receives the message, analyses the content, computes the trust score and writes the result into the job. For files, `FileDetectWorker` waits up to 30 seconds for the file to appear in S3.
6. The frontend calls `GET /scan/{job_id}` at intervals. `StatusLambda` returns the status and, once the job is complete, the result.

A message that fails processing several times is moved to the dead-letter queue so that it does not block the main queue.

#### Analysis result

Each result has a trust score from 0 to 100 and a matching risk level:

| Score | Risk level |
|---|---|
| 80 and above | `SAFE` |
| 50 to 79 | `SUSPICIOUS` |
| Below 50 | `DANGEROUS` |

A result also has a confidence of `HIGH`, `MEDIUM` or `LOW`. When analysis fails, the risk level is `SCAN ERROR`. Results with `LOW` confidence and `SCAN ERROR` are not cached. The others are kept for 24 hours through the `expires_at` attribute used by DynamoDB TTL.

#### Why this architecture

- **Asynchronous:** the API answers immediately with a `job_id`, while the slow analysis runs in the workers, so one slow request does not block the others.
- **Separated load:** links, emails and files each have their own queue and worker, and each scales independently.
- **Pay per use:** every service is billed by usage, and no server runs continuously.
- **Security:** files go straight from the browser to S3 through a presigned URL, API keys live in Parameter Store instead of the source code, and each Lambda has only the permissions it needs.