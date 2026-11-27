---
title : "Error and cache testing"
date : 2026-11-27
weight : 2
chapter : false
pre : " <b> 5.5.2. </b> "
---

This section checks how the system reacts to invalid input, a job that does not exist, a file that is never uploaded, and how the cache behaves. The commands use PowerShell. Replace `<INVOKE_URL>` with the address recorded in section 5.4.7.

Declare the address and a helper function that waits for a result, used in the parts below:

```powershell
$base = "<INVOKE_URL>"
function Wait-Scan($id) {
  do {
    Start-Sleep 5
    $r = Invoke-RestMethod -Uri "$base/scan/$id"
  } while ($r.status -ne "COMPLETED")
  $r
}
```

#### Test 1: invalid input

Send a request with an unsupported `type`:

```powershell
try {
  Invoke-RestMethod -Uri "$base/scan" -Method Post -ContentType "application/json" `
    -Body '{"type":"sms","payload":"abc"}'
} catch {
  $_.Exception.Response.StatusCode.value__
  $_.ErrorDetails.Message
}
```

Expected result: status code `400` and the body `{"error":"invalid input"}`. `IngestLambda` accepts only `link`, `email` or `file` as `type`.

#### Test 2: a job that does not exist

```powershell
try {
  Invoke-RestMethod -Uri "$base/scan/khong-ton-tai"
} catch {
  $_.Exception.Response.StatusCode.value__
  $_.ErrorDetails.Message
}
```

Expected result: status code `404` and the body `{"error":"Job ID not found!"}`.

#### Test 3: an unsupported link

The system analyses only `http` and `https` links. Submit a link that uses another protocol:

```powershell
$job = Invoke-RestMethod -Uri "$base/scan" -Method Post -ContentType "application/json" `
  -Body '{"type":"link","payload":"ftp://example.com/file"}'
$r = Wait-Scan $job.job_id
$r.result.risk_level
$r.result.details.error
```

Expected result: `risk_level` is `SCAN ERROR` and `details.error` is `ValueError: Chỉ hỗ trợ link http/https`. An error during analysis is recorded in the result instead of breaking the system. In the web frontend this result is displayed as "Chưa kiểm tra được" (could not be checked).

#### Test 4: a file that is not uploaded

Create a file job but do not upload anything to the `upload_url`:

```powershell
$job = Invoke-RestMethod -Uri "$base/scan" -Method Post -ContentType "application/json" `
  -Body '{"type":"file","payload":"chua_tai_len.pdf"}'
$r = Wait-Scan $job.job_id
$r.result.risk_level
$r.result.details.error
```

`FileDetectWorker` waits up to 30 seconds for the file to appear in S3, so the command takes about 30 to 40 seconds to return a result.

Expected result: `risk_level` is `SCAN ERROR` and `details.error` is `FileNotFoundError: Không nhận được tệp tải lên (hết thời gian chờ)`.

#### Test 5: the cache

`IngestLambda` hashes the content of a link or email with SHA-256 and looks it up in the `PhishingScanCache` table before creating a new job. A result is written to the cache only when `confidence` is not `LOW` and `risk_level` is not `SCAN ERROR`. Results are kept for 24 hours.

The behaviour therefore depends on whether the API keys (section 5.4.6 or 5.4.8) are configured:

| Situation | What happens when the same link is scanned twice |
|---|---|
| No API keys | The result has `LOW` confidence and is not cached. The second scan creates a new job with a different `job_id`. |
| API keys configured and the result is reliable enough | The first scan stores the result in the cache. The second scan hits the cache. |

To check that the cache was written:

1. Scan a link (for example `https://example.com`) and wait for it to complete.
2. Open **DynamoDB**, choose **Tables**, choose the `PhishingScanCache` table, then choose **Explore table items**.
3. With API keys configured, the table has an item with `target_hash`, `job_id`, `result` and an `expires_at` equal to the scan time plus 24 hours. Without API keys, the table has no items.

**Known limitation in the cache branch.** In the current version of the code, when the second scan hits the cache, `IngestLambda` can return an `HTTP 500` error with the body `Object of type Decimal is not JSON serializable`. The cause is that DynamoDB returns numbers as `Decimal`, and the cache branch of `ingest_lambda.py` converts the result to JSON without handling this type. The branch that creates a new job is not affected.

To fix it, in the `response` function of `ingest_lambda.py` change the line `'body': json.dumps(body)` to `'body': json.dumps(body, default=str)`, then **Deploy** the function again. The second scan then returns `cached` set to `true`, together with the `job_id` and `result` of the first scan.

#### Result

| Test | Expected result |
|---|---|
| Invalid input | `400`, `invalid input` |
| Job does not exist | `404`, `Job ID not found!` |
| Link that is not http/https | `SCAN ERROR` with `ValueError: Chỉ hỗ trợ link http/https` |
| File not uploaded | `SCAN ERROR` with `FileNotFoundError: ...` after about 30 seconds |
| Cache | An item in `PhishingScanCache` when the result is reliable enough |