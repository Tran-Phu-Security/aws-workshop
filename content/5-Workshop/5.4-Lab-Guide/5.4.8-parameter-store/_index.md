---
title : "Store API keys in Parameter Store (optional)"
date : 2026-11-27
weight : 8
chapter : false
pre : " <b> 5.4.8. </b> "
---

In section 5.4.6, API keys can be set directly in the Lambda environment variables. A safer alternative is to keep the keys encrypted in AWS Systems Manager Parameter Store. The keys are stored in one place and both worker functions read them at run time. This section is optional. Skip it if the keys are already set as environment variables or no keys are used.

The code looks up a key in this order: environment variable, then Parameter Store, then it skips that source.

#### Create the parameters

1. Open **Systems Manager** in the AWS Console and select the **Asia Pacific (Singapore) ap-southeast-1** Region.
2. Choose **Parameter Store** in the left menu, then choose **Create parameter**.
3. Enter the details of each parameter from this table:

| Name | Value |
|---|---|
| `/phishing/VT_API_KEY` | VirusTotal key |
| `/phishing/GSB_API_KEY` | Google Safe Browsing key |
| `/phishing/ABUSECH_AUTH_KEY` | abuse.ch key |
| `/phishing/VT_BACKUP_API_KEYS` | Backup VirusTotal keys separated by commas (optional) |

   For each parameter, set **Tier** to **Standard**, **Type** to **SecureString**, and paste the key into the **Value** field. Keep **KMS key source** as My current account with the default key `alias/aws/ssm`.
4. Choose **Create parameter**.

Parameter names must match exactly, including upper and lower case. The Standard tier has no storage charge.

#### Grant read permission

The `ssm:GetParameter` permission on `/phishing/*` was described in section 5.4.5 under the optional permissions. Add the `ReadApiKeys` statement to the `PhishingAccess` inline policy of both `PhishingFastWorkerRole` and `PhishingFileWorkerRole` if it is not there yet. The default encryption key `alias/aws/ssm` needs no extra `kms:Decrypt` permission. If a customer-managed KMS key is used, that permission must be granted as well.

#### Configure the two worker functions

Do the following for both `FastDetectWorker` and `FileDetectWorker`:

1. Open the function, choose **Configuration**, then **Environment variables** and **Edit**.
2. Add the variable `SSM_PREFIX` with the value `/phishing`.
3. **Delete** the variables that hold keys: `VT_API_KEY`, `GSB_API_KEY`, `ABUSECH_AUTH_KEY` and `VT_BACKUP_API_KEYS`.
4. Choose **Save**.

Deleting them is required, because the code always prefers environment variables. If an old variable remains, the function will not read the key from Parameter Store.

#### Operational notes

+ A key read from Parameter Store is remembered for the lifetime of a Lambda execution environment. When a key is changed in Parameter Store, the new value takes effect only when the function starts a new environment. To apply it right away, save the function configuration to force a restart.
+ If a key is missing, the matching source is skipped and the result has lower `confidence`. The system keeps working.
+ The worker log may show `ParameterNotFound` for `/phishing/VT_BACKUP_API_KEYS` when that parameter was not created. This is normal.

#### Result

+ The `/phishing/...` parameters of type SecureString exist in the Singapore Region.
+ Both workers have `SSM_PREFIX` set to `/phishing` and no environment variable that holds a key.