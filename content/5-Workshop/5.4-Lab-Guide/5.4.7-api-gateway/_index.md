---
title : "Create the API Gateway"
date : 2026-11-27
weight : 7
chapter : false
pre : " <b> 5.4.7. </b> "
---

The web frontend calls the system through an Amazon API Gateway HTTP API with two routes:

| Method | Route | Lambda function |
|---|---|---|
| `POST` | `/scan` | `IngestLambda` |
| `GET` | `/scan/{job_id}` | `StatusLambda` |

An HTTP API costs less than a REST API, and the two Lambda functions from section 5.4.6 are written for the HTTP API event format (payload version 2.0).

#### Create the API and the two routes

1. Open **API Gateway** in the AWS Console and select the **Asia Pacific (Singapore) ap-southeast-1** Region.
2. Under **HTTP API**, choose **Build**.
3. Under **Integrations**, choose **Add integration** and enter:
   + **Integration type:** Lambda
   + **Lambda function:** `IngestLambda`
4. Choose **Add integration** again and select the function `StatusLambda`.
5. Enter **API name:** `PhishingDetectionAPI` and choose **Next**.
6. Under **Configure routes**, create two routes:
   + **Method** `POST`, **Resource path** `/scan`, **Integration target** `IngestLambda`
   + **Method** `GET`, **Resource path** `/scan/{job_id}`, **Integration target** `StatusLambda`

   Delete the default route `ANY /{proxy+}` if it is present.
7. Choose **Next**. Under **Configure stages**, keep the `$default` stage with **Auto-deploy** turned on, then choose **Next**.
8. Choose **Create**.

When an integration is created in the Console, API Gateway automatically grants itself permission to invoke each Lambda function. No manual permission is needed.

#### Configure CORS

The browser sends a preflight request (`OPTIONS`) before calling `POST /scan` with a JSON body. The API must answer this request correctly, otherwise the web frontend reports a CORS error.

1. Open the new API, choose **CORS** in the left menu (Develop section), then choose **Configure**.
2. Enter:
   + **Access-Control-Allow-Origin:** `*`
   + **Access-Control-Allow-Methods:** `GET`, `POST`, `OPTIONS`
   + **Access-Control-Allow-Headers:** `content-type`
3. Choose **Save**.

The value `*` allows any origin to call the API, which is suitable for a workshop. In production, replace it with the specific address of the web frontend.

#### Rate limiting (optional)

Limiting the request rate protects the system and the quotas of the external sources.

1. Choose **Stages** in the left menu and select the `$default` stage.
2. Under **Default route throttling**, choose **Edit**.
3. Set **Rate** to `10` requests per second and **Burst** to `20`, then choose **Save**.

#### Record the API address

Choose **API details** (or the API overview page) and copy the **Invoke URL**, which looks like:

    https://<api-id>.execute-api.ap-southeast-1.amazonaws.com

This address is used in section 5.4.9 to configure the frontend and in section 5.5 for testing.

#### Quick check

Open PowerShell and replace `<INVOKE_URL>` with the address you copied:

```powershell
$base = "<INVOKE_URL>"
Invoke-RestMethod -Uri "$base/scan" -Method Post -ContentType "application/json" `
  -Body '{"type":"link","payload":"http://paypal-security-update-fake.com/login"}'
```

A correct result is an object with a `job_id` and a `status` of `PENDING`.

#### Result

+ The API `PhishingDetectionAPI` has two routes: `POST /scan` and `GET /scan/{job_id}`.
+ CORS is configured.
+ The **Invoke URL** has been recorded.
+ The quick check returns a `job_id`.