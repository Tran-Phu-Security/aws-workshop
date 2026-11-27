---
title : "Create the SQS queues"
date : 2026-11-27
weight : 4
chapter : false
pre : " <b> 5.4.4. </b> "
---

The system uses three Standard SQS queues:

| Queue | Role | Visibility timeout |
|---|---|---|
| `phishing-dlq` | Dead-letter queue, holds messages that failed processing repeatedly | Default |
| `phishing-fast-queue` | Link and email scanning jobs | 60 seconds |
| `phishing-file-queue` | File scanning jobs | 300 seconds |

The dead-letter queue must be created first, because the other two queues refer to it.

#### Visibility timeout

When a worker receives a message, SQS hides it from other workers for the visibility timeout. If the worker does not delete the message in that period (because of an error or a timeout), the message becomes visible again and is processed once more. The value must be greater than or equal to the timeout of the Lambda function that consumes the queue. `FastDetectWorker` has a 45-second timeout and `FileDetectWorker` has a 90-second timeout (section 5.4.6), so 60 and 300 seconds satisfy this condition.

#### Create the dead-letter queue phishing-dlq

1. Open **Amazon SQS** in the AWS Console and select the **Asia Pacific (Singapore) ap-southeast-1** Region.
2. Choose **Create queue**.
3. Enter:
   + **Type:** Standard
   + **Name:** `phishing-dlq`
4. Keep the remaining settings and choose **Create queue**.

#### Create the queue phishing-fast-queue

1. Choose **Create queue**.
2. Enter:
   + **Type:** Standard
   + **Name:** `phishing-fast-queue`
3. Under **Configuration**, set **Visibility timeout** to `1` **Minutes** (60 seconds).
4. Scroll to **Dead-letter queue** and choose **Enabled**:
   + **Choose queue:** select `phishing-dlq`
   + **Maximum receives:** `3`
5. Choose **Create queue**.

#### Create the queue phishing-file-queue

Follow the same steps as above with these differences:

+ **Name:** `phishing-file-queue`
+ **Visibility timeout:** `5` **Minutes** (300 seconds)
+ **Dead-letter queue:** Enabled, select `phishing-dlq`, **Maximum receives** `3`

#### How the dead-letter queue works

When a message has been received more than **Maximum receives** times without being deleted, SQS moves it to `phishing-dlq`. This stops a faulty message from being processed forever.

Only infrastructure-level failures, where the Lambda function ends abnormally, send a message back to the queue. Errors during analysis are caught by the code and stored as a `SCAN ERROR` result, so they do not put the message in the dead-letter queue.

#### Result

+ The three queues `phishing-dlq`, `phishing-fast-queue` and `phishing-file-queue` are available in the Singapore Region.
+ Both processing queues point to `phishing-dlq` with Maximum receives set to 3.
+ Connecting the queues to the Lambda functions is done in section 5.4.6.