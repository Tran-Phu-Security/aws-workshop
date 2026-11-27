---
title : "Create the Lambda functions"
date : 2026-11-27
weight : 6
chapter : false
pre : " <b> 5.4.6. </b> "
---

The system has four Lambda functions. All use the Python 3.12 runtime and the arm64 architecture. The source code uses only the Python standard library and `boto3` (already present on Lambda), so no extra packages are needed.

| Function | Role (section 5.4.5) | Source | Memory | Timeout |
|---|---|---|---|---|
| `IngestLambda` | `PhishingIngestLambdaRole` | `backend\ingest_lambda.py` | 128 MB | 10 seconds |
| `StatusLambda` | `PhishingStatusLambdaRole` | `backend\status_lambda.py` | 128 MB | 10 seconds |
| `FastDetectWorker` | `PhishingFastWorkerRole` | `detect-workers.zip` | 512 MB | 45 seconds |
| `FileDetectWorker` | `PhishingFileWorkerRole` | `detect-workers.zip` | 1024 MB | 90 seconds |

#### Create IngestLambda

1. Open **Lambda** in the AWS Console, select the **Asia Pacific (Singapore) ap-southeast-1** Region, then choose **Create function**.
2. Choose **Author from scratch** and enter:
   + **Function name:** `IngestLambda`
   + **Runtime:** Python 3.12
   + **Architecture:** arm64
3. Expand **Change default execution role**, choose **Use an existing role** and select `PhishingIngestLambdaRole`.
4. Choose **Create function**.
5. On the **Code** tab, open `lambda_function.py`, delete the sample code and paste the full content of `backend\ingest_lambda.py`. Choose **Deploy**.
6. Open the **Configuration** tab, choose **General configuration**, choose **Edit** and set **Timeout** to `10` seconds. Choose **Save**.
7. Choose **Environment variables**, choose **Edit** and add:

| Key | Value |
|---|---|
| `JOBS_TABLE` | `PhishingScanJobs` |
| `CACHE_TABLE` | `PhishingScanCache` |
| `FAST_QUEUE_NAME` | `phishing-fast-queue` |
| `FILE_QUEUE_NAME` | `phishing-file-queue` |
| `RAW_BUCKET` | `phishing-raw-uploads-<ACCOUNT_ID>` |

`RAW_BUCKET` is required. Without it the system cannot create the presigned URL for files. The other four variables have defaults equal to the values above, but it is better to set them explicitly.

Because the code is pasted into `lambda_function.py`, keep the default **Handler** `lambda_function.lambda_handler`.

#### Create StatusLambda

Follow the same steps as `IngestLambda` with:

+ **Function name:** `StatusLambda`
+ **Execution role:** `PhishingStatusLambdaRole`
+ **Code:** the content of `backend\status_lambda.py`, pasted into `lambda_function.py`, then **Deploy**
+ **Timeout:** `10` seconds
+ **Environment variables:** `JOBS_TABLE` = `PhishingScanJobs`

#### Create FastDetectWorker

1. Choose **Create function**, choose **Author from scratch** and enter:
   + **Function name:** `FastDetectWorker`
   + **Runtime:** Python 3.12
   + **Architecture:** arm64
   + **Existing role:** `PhishingFastWorkerRole`
2. On the **Code** tab choose **Upload from**, choose **.zip file**, select `backend\dist\detect-workers.zip` built in section 5.4.1, then choose **Save**.
3. Under **Runtime settings** choose **Edit** and set **Handler** to `fast_detect_worker.lambda_handler`. Choose **Save**.
4. Under **Configuration → General configuration → Edit**, set **Memory** to `512` MB and **Timeout** to `45` seconds.
5. Under **Configuration → Environment variables**, add:

| Key | Value | Note |
|---|---|---|
| `JOBS_TABLE` | `PhishingScanJobs` | Required |
| `CACHE_TABLE` | `PhishingScanCache` | Required |
| `REPORTS_BUCKET` | `phishing-audit-logs-<ACCOUNT_ID>` | Optional, writes JSON logs to S3 |
| `VT_API_KEY` | VirusTotal key | Optional |
| `GSB_API_KEY` | Google Safe Browsing key | Optional |
| `ABUSECH_AUTH_KEY` | abuse.ch key | Optional |

The API key variables are optional. A source without a key is skipped and the result has lower `confidence`. The keys can be stored in Parameter Store instead of environment variables (section 5.4.8).

6. Under **Configuration → Triggers**, choose **Add trigger** and enter:
   + **Source:** SQS
   + **SQS queue:** `phishing-fast-queue`
   + **Batch size:** `1`
7. Choose **Add**.

#### Create FileDetectWorker

Follow the same steps as `FastDetectWorker` with:

+ **Function name:** `FileDetectWorker`
+ **Existing role:** `PhishingFileWorkerRole`
+ **Code:** the same `detect-workers.zip`
+ **Handler:** `file_detect_worker.lambda_handler`
+ **Memory:** `1024` MB, **Timeout:** `90` seconds
+ **Ephemeral storage** (in General configuration): `1024` MB, because the downloaded file is stored temporarily in `/tmp`
+ **Trigger:** SQS `phishing-file-queue`, **Batch size** `1`

Environment variables for this function:

| Key | Value | Note |
|---|---|---|
| `JOBS_TABLE` | `PhishingScanJobs` | Required |
| `CACHE_TABLE` | `PhishingScanCache` | Required |
| `REPORTS_BUCKET` | `phishing-audit-logs-<ACCOUNT_ID>` | Optional |
| `VT_API_KEY` | VirusTotal key | Optional |
| `ABUSECH_AUTH_KEY` | abuse.ch key | Optional |
| `VT_UPLOAD_UNKNOWN_FILES` | `true` or `false` | Optional, default `true` |

When `VT_UPLOAD_UNKNOWN_FILES` is `true`, a file that VirusTotal does not know yet is uploaded to VirusTotal for analysis. Set it to `false` to look up by hash only and never send file content outside.

#### Batch size and the VirusTotal limit

A batch size of 1 means each Lambda invocation processes a single message, which makes debugging easier. The free VirusTotal plan allows 4 requests per minute, so scanning many files in a row can produce `HTTP 429` errors. In that case the result has lower `confidence` instead of failing.

#### Result

+ Four Lambda functions exist: `IngestLambda`, `StatusLambda`, `FastDetectWorker`, `FileDetectWorker`.
+ `FastDetectWorker` is triggered by `phishing-fast-queue` and `FileDetectWorker` by `phishing-file-queue`, both with state **Enabled**.
+ `IngestLambda` and `StatusLambda` are connected to API Gateway in section 5.4.7.