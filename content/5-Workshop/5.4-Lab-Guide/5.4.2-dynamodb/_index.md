---
title : "Create the DynamoDB tables"
date : 2026-11-27
weight : 2
chapter : false
pre : " <b> 5.4.2. </b> "
---

The system uses two DynamoDB tables: `PhishingScanJobs` stores each scan job and its result, and `PhishingScanCache` stores results for reuse during 24 hours.

![Structure of the two DynamoDB tables](/aws-workshop/images/5-Workshop/5.4-Lab-Guide/5.4.2-dynamodb/dynamodb-schema.png)

#### Create the PhishingScanJobs table

1. Open the AWS Console and select the **Asia Pacific (Singapore) ap-southeast-1** Region.
2. Open **DynamoDB**, choose **Tables** in the left menu, then choose **Create table**.
3. Enter:
   + **Table name:** `PhishingScanJobs`
   + **Partition key:** `job_id`, type **String**
   + Do not define a sort key.
4. Under **Table settings**, choose **Customize settings**.
5. Under **Read/write capacity settings**, choose **Provisioned**. For both **Read capacity** and **Write capacity**, set **Auto scaling** to **Off** and **Provisioned capacity units** to `5`.
6. Choose **Create table**. Wait until the table status is **Active**.

#### Create the PhishingScanCache table

Repeat the steps above with:

+ **Table name:** `PhishingScanCache`
+ **Partition key:** `target_hash`, type **String**
+ **Capacity mode:** Provisioned, Read `5`, Write `5`, Auto scaling **Off**

5 RCU and 5 WCU per table fit within the permanent DynamoDB free tier (25 RCU and 25 WCU in total), which suits the scale of this workshop.

#### Turn on TTL for both tables

TTL lets DynamoDB delete expired items automatically. Both tables use the same attribute name, `expires_at`, which stores the expiry time as Unix time (seconds).

For each table in turn:

1. Choose the table name to open its detail page.
2. Open the **Additional settings** tab.
3. In the **Time to Live (TTL)** section, choose **Turn on**.
4. Enter **TTL attribute name:** `expires_at`, then choose **Turn on TTL**.

| Table | Value of `expires_at` |
|---|---|
| `PhishingScanJobs` | Job creation time plus 7 days |
| `PhishingScanCache` | Scan time plus 24 hours |

DynamoDB removes expired items in the background, which can happen later than the expiry time. For that reason the code always checks `expires_at` itself before using a cached result, and does not rely on the item having been deleted.

#### Result

+ Both `PhishingScanJobs` and `PhishingScanCache` are **Active** in the Singapore Region.
+ Both have TTL turned on for the `expires_at` attribute.