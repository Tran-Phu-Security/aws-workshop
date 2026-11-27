---
title : "Create the IAM roles"
date : 2026-11-27
weight : 5
chapter : false
pre : " <b> 5.4.5. </b> "
---

Each Lambda function uses its own IAM role, granted only the permissions that function needs. As a result, a function that fails or is abused cannot reach the other resources.

| Role | Used by | Permissions needed |
|---|---|---|
| `PhishingIngestLambdaRole` | `IngestLambda` | Read and write the two DynamoDB tables, send messages to the two queues, create presigned URLs that write to the upload bucket |
| `PhishingStatusLambdaRole` | `StatusLambda` | Read the `PhishingScanJobs` table |
| `PhishingFastWorkerRole` | `FastDetectWorker` | Receive messages from `phishing-fast-queue`, update jobs, write the cache |
| `PhishingFileWorkerRole` | `FileDetectWorker` | The same, plus read files from the upload bucket |

All four roles also get the managed policy `AWSLambdaBasicExecutionRole` so that the function can write logs to CloudWatch.

In the JSON snippets below, replace `<ACCOUNT_ID>` with your 12-digit AWS account number. The table, queue and bucket names come from sections 5.4.2 to 5.4.4.

#### How to create a role

Do the following for each of the four roles:

1. Open **IAM**, choose **Roles** in the left menu, then choose **Create role**.
2. Under **Trusted entity type** choose **AWS service**, under **Use case** choose **Lambda**, then choose **Next**.
3. Search for and select the policy **AWSLambdaBasicExecutionRole**, then choose **Next**.
4. Enter the **Role name** from the table above and choose **Create role**.
5. Open the new role, choose **Add permissions**, then **Create inline policy**.
6. Switch to the **JSON** tab and paste the matching JSON below.
7. Choose **Next**, set **Policy name** to `PhishingAccess`, and choose **Create policy**.

#### PhishingIngestLambdaRole

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ScanTables",
      "Effect": "Allow",
      "Action": ["dynamodb:GetItem", "dynamodb:PutItem"],
      "Resource": [
        "arn:aws:dynamodb:ap-southeast-1:<ACCOUNT_ID>:table/PhishingScanJobs",
        "arn:aws:dynamodb:ap-southeast-1:<ACCOUNT_ID>:table/PhishingScanCache"
      ]
    },
    {
      "Sid": "SendToQueues",
      "Effect": "Allow",
      "Action": ["sqs:GetQueueUrl", "sqs:SendMessage"],
      "Resource": [
        "arn:aws:sqs:ap-southeast-1:<ACCOUNT_ID>:phishing-fast-queue",
        "arn:aws:sqs:ap-southeast-1:<ACCOUNT_ID>:phishing-file-queue"
      ]
    },
    {
      "Sid": "PresignedUpload",
      "Effect": "Allow",
      "Action": "s3:PutObject",
      "Resource": "arn:aws:s3:::phishing-raw-uploads-<ACCOUNT_ID>/uploads/*"
    }
  ]
}
```

A presigned URL carries the permissions of the role that created it. This role therefore needs `s3:PutObject` on the `uploads/` prefix. Without it, S3 rejects the browser upload even though the URL is valid.

#### PhishingStatusLambdaRole

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ReadJobs",
      "Effect": "Allow",
      "Action": "dynamodb:GetItem",
      "Resource": "arn:aws:dynamodb:ap-southeast-1:<ACCOUNT_ID>:table/PhishingScanJobs"
    }
  ]
}
```

#### PhishingFastWorkerRole

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ConsumeFastQueue",
      "Effect": "Allow",
      "Action": ["sqs:ReceiveMessage", "sqs:DeleteMessage", "sqs:GetQueueAttributes"],
      "Resource": "arn:aws:sqs:ap-southeast-1:<ACCOUNT_ID>:phishing-fast-queue"
    },
    {
      "Sid": "UpdateJobs",
      "Effect": "Allow",
      "Action": ["dynamodb:GetItem", "dynamodb:UpdateItem"],
      "Resource": "arn:aws:dynamodb:ap-southeast-1:<ACCOUNT_ID>:table/PhishingScanJobs"
    },
    {
      "Sid": "WriteCache",
      "Effect": "Allow",
      "Action": "dynamodb:PutItem",
      "Resource": "arn:aws:dynamodb:ap-southeast-1:<ACCOUNT_ID>:table/PhishingScanCache"
    }
  ]
}
```

#### PhishingFileWorkerRole

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ConsumeFileQueue",
      "Effect": "Allow",
      "Action": ["sqs:ReceiveMessage", "sqs:DeleteMessage", "sqs:GetQueueAttributes"],
      "Resource": "arn:aws:sqs:ap-southeast-1:<ACCOUNT_ID>:phishing-file-queue"
    },
    {
      "Sid": "UpdateJobs",
      "Effect": "Allow",
      "Action": ["dynamodb:GetItem", "dynamodb:UpdateItem"],
      "Resource": "arn:aws:dynamodb:ap-southeast-1:<ACCOUNT_ID>:table/PhishingScanJobs"
    },
    {
      "Sid": "WriteCache",
      "Effect": "Allow",
      "Action": "dynamodb:PutItem",
      "Resource": "arn:aws:dynamodb:ap-southeast-1:<ACCOUNT_ID>:table/PhishingScanCache"
    },
    {
      "Sid": "ReadUploadedFile",
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::phishing-raw-uploads-<ACCOUNT_ID>/uploads/*"
    },
    {
      "Sid": "CheckUploadExists",
      "Effect": "Allow",
      "Action": "s3:ListBucket",
      "Resource": "arn:aws:s3:::phishing-raw-uploads-<ACCOUNT_ID>"
    }
  ]
}
```

The `s3:ListBucket` permission applies to the bucket itself (no `/*`). The worker checks whether the file has reached S3 by requesting the object's metadata. With ListBucket permission and a missing file, S3 returns a 404 error and the worker waits and retries. Without this permission, S3 returns a 403 error and the worker stops immediately with an error, even though the file was uploaded correctly.

#### Optional permissions for the two workers

The two statements below are needed only when the optional features are turned on. Add them to the `Statement` array of **both** worker roles.

Writing JSON logs to the reports bucket (when the `REPORTS_BUCKET` variable is set):

```json
{
  "Sid": "WriteAuditLog",
  "Effect": "Allow",
  "Action": "s3:PutObject",
  "Resource": "arn:aws:s3:::phishing-audit-logs-<ACCOUNT_ID>/logs/*"
}
```

Reading API keys from Parameter Store (section 5.4.8):

```json
{
  "Sid": "ReadApiKeys",
  "Effect": "Allow",
  "Action": "ssm:GetParameter",
  "Resource": "arn:aws:ssm:ap-southeast-1:<ACCOUNT_ID>:parameter/phishing/*"
}
```

#### Result

+ Four roles exist: `PhishingIngestLambdaRole`, `PhishingStatusLambdaRole`, `PhishingFastWorkerRole`, `PhishingFileWorkerRole`.
+ Each role has `AWSLambdaBasicExecutionRole` and an inline policy named `PhishingAccess`.
+ Section 5.4.6 assigns each role to its Lambda function.