---
title : "Kiểm thử lỗi và bộ đệm"
date : 2026-11-27
weight : 2
chapter : false
pre : " <b> 5.5.2. </b> "
---

Mục này kiểm tra hệ thống phản ứng thế nào với đầu vào sai, công việc không tồn tại, file không được tải lên và cách bộ đệm hoạt động. Các lệnh dùng PowerShell. Thay `<INVOKE_URL>` bằng địa chỉ ghi ở mục 5.4.7.

Khai báo địa chỉ và một hàm hỗ trợ chờ kết quả, dùng cho các phần sau:

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

#### Kiểm thử 1: đầu vào không hợp lệ

Gửi yêu cầu có `type` không được hỗ trợ:

```powershell
try {
  Invoke-RestMethod -Uri "$base/scan" -Method Post -ContentType "application/json" `
    -Body '{"type":"sms","payload":"abc"}'
} catch {
  $_.Exception.Response.StatusCode.value__
  $_.ErrorDetails.Message
}
```

Kết quả mong đợi: mã trạng thái `400` và nội dung `{"error":"invalid input"}`. `IngestLambda` chỉ chấp nhận `type` là `link`, `email` hoặc `file`.

#### Kiểm thử 2: job không tồn tại

```powershell
try {
  Invoke-RestMethod -Uri "$base/scan/khong-ton-tai"
} catch {
  $_.Exception.Response.StatusCode.value__
  $_.ErrorDetails.Message
}
```

Kết quả mong đợi: mã trạng thái `404` và nội dung `{"error":"Job ID not found!"}`.

#### Kiểm thử 3: link không được hỗ trợ

Hệ thống chỉ phân tích link `http` và `https`. Gửi một link dùng giao thức khác:

```powershell
$job = Invoke-RestMethod -Uri "$base/scan" -Method Post -ContentType "application/json" `
  -Body '{"type":"link","payload":"ftp://example.com/file"}'
$r = Wait-Scan $job.job_id
$r.result.risk_level
$r.result.details.error
```

Kết quả mong đợi: `risk_level` là `SCAN ERROR` và `details.error` là `ValueError: Chỉ hỗ trợ link http/https`. Lỗi trong quá trình phân tích được ghi vào kết quả thay vì làm hỏng hệ thống. Trên giao diện web, kết quả này hiển thị là "Chưa kiểm tra được".

#### Kiểm thử 4: file không được tải lên

Tạo job file nhưng không tải file lên địa chỉ `upload_url`:

```powershell
$job = Invoke-RestMethod -Uri "$base/scan" -Method Post -ContentType "application/json" `
  -Body '{"type":"file","payload":"chua_tai_len.pdf"}'
$r = Wait-Scan $job.job_id
$r.result.risk_level
$r.result.details.error
```

`FileDetectWorker` chờ tối đa 30 giây để file xuất hiện trên S3, nên lệnh cần khoảng 30 đến 40 giây để có kết quả.

Kết quả mong đợi: `risk_level` là `SCAN ERROR` và `details.error` là `FileNotFoundError: Không nhận được tệp tải lên (hết thời gian chờ)`.

#### Kiểm thử 5: bộ đệm

`IngestLambda` băm nội dung link hoặc email bằng SHA-256 rồi tra bảng `PhishingScanCache` trước khi tạo job mới. Một kết quả chỉ được ghi vào bộ đệm khi `confidence` không phải `LOW` và `risk_level` không phải `SCAN ERROR`. Kết quả được giữ 24 giờ.

Vì vậy hành vi phụ thuộc vào việc các khóa API (mục 5.4.6 hoặc 5.4.8) đã được cấu hình hay chưa:

| Tình huống | Điều xảy ra khi quét hai lần cùng một link |
|---|---|
| Chưa có khóa API | Kết quả có `confidence` `LOW`, không được lưu đệm. Lần quét thứ hai tạo một job mới với `job_id` khác. |
| Đã có khóa API và kết quả đủ tin cậy | Lần quét thứ nhất lưu kết quả vào bộ đệm. Lần quét thứ hai gặp bộ đệm. |

Để kiểm tra bộ đệm đã được ghi:

1. Quét một link (ví dụ `https://example.com`) và đợi hoàn tất.
2. Mở dịch vụ **DynamoDB**, chọn **Tables**, chọn bảng `PhishingScanCache`, rồi chọn **Explore table items**.
3. Với khóa API đã cấu hình, bảng có một mục với `target_hash`, `job_id`, `result` và `expires_at` bằng thời điểm quét cộng 24 giờ. Chưa có khóa API thì bảng không có mục nào.

**Giới hạn đã biết ở nhánh bộ đệm.** Với phiên bản mã hiện tại, khi lần quét thứ hai gặp bộ đệm, `IngestLambda` có thể trả lỗi `HTTP 500` với nội dung `Object of type Decimal is not JSON serializable`. Nguyên nhân là DynamoDB trả số dưới dạng `Decimal`, còn nhánh bộ đệm của `ingest_lambda.py` chuyển kết quả sang JSON mà chưa xử lý kiểu này. Nhánh tạo job mới không bị ảnh hưởng.

Cách khắc phục là trong hàm `response` của `ingest_lambda.py`, đổi dòng `'body': json.dumps(body)` thành `'body': json.dumps(body, default=str)`, rồi **Deploy** lại hàm. Khi đó lần quét thứ hai trả về `cached` là `true`, cùng `job_id` và `result` của lần quét trước.

#### Kết quả

| Kiểm thử | Kết quả mong đợi |
|---|---|
| Đầu vào không hợp lệ | `400`, `invalid input` |
| Job không tồn tại | `404`, `Job ID not found!` |
| Link không phải http/https | `SCAN ERROR` kèm `ValueError: Chỉ hỗ trợ link http/https` |
| File không được tải lên | `SCAN ERROR` kèm `FileNotFoundError: ...` sau khoảng 30 giây |
| Bộ đệm | Có mục trong `PhishingScanCache` khi kết quả đủ tin cậy |