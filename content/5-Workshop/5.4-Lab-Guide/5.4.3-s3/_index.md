---
title : "Create the S3 buckets"
date : 2026-11-27
weight : 3
chapter : false
pre : " <b> 5.4.3. </b> "
---

The system uses two S3 buckets:

| Bucket | Required | Purpose |
|---|---|---|
| `phishing-raw-uploads-<ACCOUNT_ID>` | Yes | Receives files that users upload directly from the browser through a presigned URL |
| `phishing-audit-logs-<ACCOUNT_ID>` | No | Stores a JSON log of every scan at `logs/<job_id>.json` |

S3 bucket names must be globally unique. Replace `<ACCOUNT_ID>` with your 12-digit AWS account number. Use only lowercase letters, digits and hyphens.

#### Create the upload bucket

1. Open **S3** in the AWS Console and choose **Create bucket**.
2. Enter:
   + **Bucket type:** General purpose
   + **Bucket name:** `phishing-raw-uploads-<ACCOUNT_ID>`
   + **AWS Region:** Asia Pacific (Singapore) ap-southeast-1
   + **Object Ownership:** ACLs disabled
   + **Block Public Access settings for this bucket:** keep **Block all public access** selected
   + **Bucket Versioning:** Disable
   + **Default encryption:** Server-side encryption with Amazon S3 managed keys (SSE-S3)
3. Choose **Create bucket**.

#### Configure CORS

The browser uploads a file to S3 with a `PUT` request from an origin different from S3, so the bucket must allow that origin.

1. Open the new bucket and choose the **Permissions** tab.
2. Scroll to **Cross-origin resource sharing (CORS)** and choose **Edit**.
3. Paste the following configuration and choose **Save changes**:

```json
[
  {
    "AllowedHeaders": ["*"],
    "AllowedMethods": ["PUT"],
    "AllowedOrigins": ["http://localhost:8000"],
    "ExposeHeaders": []
  }
]
```

`http://localhost:8000` is the address of the web frontend when it runs on a local machine (section 5.4.9). If the frontend is served from another address, add that address to `AllowedOrigins`.

#### Delete uploaded files automatically

Uploaded files only need to exist while they are being analysed. A lifecycle rule removes them automatically.

1. In the bucket, open the **Management** tab and choose **Create lifecycle rule**.
2. Enter:
   + **Lifecycle rule name:** `expire-uploads`
   + **Choose a rule scope:** Limit the scope of this rule using one or more filters
   + **Prefix:** `uploads/`
   + **Lifecycle rule actions:** select **Expire current versions of objects**
   + **Days after object creation:** `1`
3. Acknowledge the S3 warning and choose **Create rule**.

The code stores each file at the key `uploads/<job_id>/<file name>`, so the `uploads/` prefix covers every uploaded file.

#### Create the log bucket (optional)

This bucket is used only when the worker environment variable `REPORTS_BUCKET` is set. When it is not set, the system skips writing logs to S3.

Create the bucket `phishing-audit-logs-<ACCOUNT_ID>` with the same settings as above (Singapore Region, Block all public access, SSE-S3). This bucket needs no CORS.

#### Result

+ The bucket `phishing-raw-uploads-<ACCOUNT_ID>` has Block all public access, CORS for `PUT`, and a rule that deletes objects with the `uploads/` prefix after 1 day.
+ If logging is wanted: the bucket `phishing-audit-logs-<ACCOUNT_ID>`.
+ Note the bucket names, which sections 5.4.5 and 5.4.6 use.