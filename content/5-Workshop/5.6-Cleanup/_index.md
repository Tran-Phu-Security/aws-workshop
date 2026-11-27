---
title : "Clean-up"
date : 2026-11-27
weight : 6
chapter : false
pre : " <b> 5.6. </b> "
---

The resources used in this workshop can keep generating charges if left in place, especially provisioned DynamoDB capacity, S3 data and CloudWatch logs. Once testing is complete, delete them in the order below. The order works from the outer layers inward, which avoids errors caused by resources that are still referenced.

#### Step 1: Delete API Gateway

1. Open **API Gateway** and choose the HTTP API of the system.
2. Choose **Actions**, choose **Delete**, then confirm.

#### Step 2: Delete the Lambda functions

1. Open **Lambda**.
2. Select the four functions `IngestLambda`, `StatusLambda`, `FastDetectWorker` and `FileDetectWorker`.
3. Choose **Actions**, choose **Delete**, enter `confirm`, then confirm.

Deleting a function also deletes the event source mappings that connect it to SQS.

#### Step 3: Delete the SQS queues

1. Open **SQS**.
2. Select the three queues `phishing-fast-queue`, `phishing-file-queue` and `phishing-dlq` one at a time.
3. Choose **Delete**, enter `delete`, then confirm.

#### Step 4: Delete the DynamoDB tables

1. Open **DynamoDB** and choose **Tables**.
2. Select the `PhishingScanJobs` table, choose **Delete**, then confirm.
3. Repeat for the `PhishingScanCache` table.

#### Step 5: Delete the S3 buckets

A bucket must be empty before it can be deleted.

1. Open **S3** and choose the upload bucket (the `RAW_BUCKET` value).
2. Choose **Empty**, enter `permanently delete`, then confirm.
3. Choose **Delete**, enter the bucket name, then confirm.
4. Repeat for the reports bucket (the `REPORTS_BUCKET` value).

#### Step 6: Delete the IAM roles and policies

1. Open **IAM** and choose **Roles**.
2. Select the execution roles created in section 5.4.5, then choose **Delete**.
3. Choose **Policies**, filter by **Customer managed**, then delete the policies that were created for the system.

#### Step 7: Delete the Parameter Store parameters

1. Open **AWS Systems Manager** and choose **Parameter Store**.
2. Select the parameters with the `/phishing/` prefix, choose **Delete**, then confirm.

This step removes the API keys stored in section 5.4.8.

#### Step 8: Delete the CloudWatch log groups

Log groups are not deleted together with the Lambda functions and continue to incur storage charges.

1. Open **CloudWatch**, choose **Logs**, then choose **Log groups**.
2. Type `/aws/lambda/` in the search box.
3. Select the log groups of the four functions above, choose **Actions**, choose **Delete log group(s)**, then confirm.

#### Final check

Open each service above again and confirm that the resource list of the system is empty. The **Billing and Cost Management** page can be checked the following day to make sure no further charges appear.