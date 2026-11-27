---
title : "Tạo các hàm Lambda"
date : 2026-11-27
weight : 6
chapter : false
pre : " <b> 5.4.6. </b> "
---

Hệ thống gồm bốn hàm Lambda. Tất cả dùng runtime Python 3.12 và kiến trúc arm64. Mã nguồn chỉ dùng thư viện chuẩn của Python và `boto3` (có sẵn trên Lambda), nên không cần cài thêm gói.

| Hàm | Role (mục 5.4.5) | Mã nguồn | Memory | Timeout |
|---|---|---|---|---|
| `IngestLambda` | `PhishingIngestLambdaRole` | `backend\ingest_lambda.py` | 128 MB | 10 giây |
| `StatusLambda` | `PhishingStatusLambdaRole` | `backend\status_lambda.py` | 128 MB | 10 giây |
| `FastDetectWorker` | `PhishingFastWorkerRole` | `detect-workers.zip` | 512 MB | 45 giây |
| `FileDetectWorker` | `PhishingFileWorkerRole` | `detect-workers.zip` | 1024 MB | 90 giây |

#### Tạo hàm IngestLambda

1. Mở dịch vụ **Lambda** trên AWS Console, chọn Region **Asia Pacific (Singapore) ap-southeast-1**, rồi chọn **Create function**.
2. Chọn **Author from scratch** và nhập:
   + **Function name:** `IngestLambda`
   + **Runtime:** Python 3.12
   + **Architecture:** arm64
3. Mở **Change default execution role**, chọn **Use an existing role** và chọn `PhishingIngestLambdaRole`.
4. Chọn **Create function**.
5. Ở tab **Code**, mở file `lambda_function.py`, xóa nội dung mẫu và dán toàn bộ nội dung file `backend\ingest_lambda.py`. Chọn **Deploy**.
6. Mở tab **Configuration**, chọn **General configuration**, chọn **Edit** và đặt **Timeout** là `10` giây. Chọn **Save**.
7. Chọn **Environment variables**, chọn **Edit** rồi thêm:

| Key | Value |
|---|---|
| `JOBS_TABLE` | `PhishingScanJobs` |
| `CACHE_TABLE` | `PhishingScanCache` |
| `FAST_QUEUE_NAME` | `phishing-fast-queue` |
| `FILE_QUEUE_NAME` | `phishing-file-queue` |
| `RAW_BUCKET` | `phishing-raw-uploads-<ACCOUNT_ID>` |

Biến `RAW_BUCKET` là bắt buộc. Nếu thiếu, hệ thống không tạo được presigned URL cho file. Bốn biến còn lại có giá trị mặc định trùng với tên ở trên, nhưng nên đặt rõ ràng.

Do code được dán vào `lambda_function.py`, giữ nguyên **Handler** mặc định `lambda_function.lambda_handler`.

#### Tạo hàm StatusLambda

Thực hiện như `IngestLambda` với các thông tin sau:

+ **Function name:** `StatusLambda`
+ **Execution role:** `PhishingStatusLambdaRole`
+ **Code:** nội dung file `backend\status_lambda.py`, dán vào `lambda_function.py`, rồi **Deploy**
+ **Timeout:** `10` giây
+ **Environment variables:** `JOBS_TABLE` = `PhishingScanJobs`

#### Tạo hàm FastDetectWorker

1. Chọn **Create function**, chọn **Author from scratch** và nhập:
   + **Function name:** `FastDetectWorker`
   + **Runtime:** Python 3.12
   + **Architecture:** arm64
   + **Existing role:** `PhishingFastWorkerRole`
2. Ở tab **Code**, chọn **Upload from**, chọn **.zip file**, chọn file `backend\dist\detect-workers.zip` đã tạo ở mục 5.4.1, rồi chọn **Save**.
3. Trong **Runtime settings**, chọn **Edit** và đặt **Handler** là `fast_detect_worker.lambda_handler`. Chọn **Save**.
4. Ở **Configuration → General configuration → Edit**, đặt **Memory** là `512` MB và **Timeout** là `45` giây.
5. Ở **Configuration → Environment variables**, thêm:

| Key | Value | Ghi chú |
|---|---|---|
| `JOBS_TABLE` | `PhishingScanJobs` | Bắt buộc |
| `CACHE_TABLE` | `PhishingScanCache` | Bắt buộc |
| `REPORTS_BUCKET` | `phishing-audit-logs-<ACCOUNT_ID>` | Tùy chọn, ghi log JSON lên S3 |
| `VT_API_KEY` | khóa VirusTotal | Tùy chọn |
| `GSB_API_KEY` | khóa Google Safe Browsing | Tùy chọn |
| `ABUSECH_AUTH_KEY` | khóa abuse.ch | Tùy chọn |

Các biến khóa API là tùy chọn. Nguồn nào thiếu khóa sẽ bị bỏ qua và độ tin cậy (`confidence`) của kết quả giảm đi. Có thể lưu khóa trong Parameter Store thay cho biến môi trường (mục 5.4.8).

6. Ở **Configuration → Triggers**, chọn **Add trigger** và nhập:
   + **Source:** SQS
   + **SQS queue:** `phishing-fast-queue`
   + **Batch size:** `1`
7. Chọn **Add**.

#### Tạo hàm FileDetectWorker

Thực hiện như `FastDetectWorker` với các thông tin sau:

+ **Function name:** `FileDetectWorker`
+ **Existing role:** `PhishingFileWorkerRole`
+ **Code:** cùng file `detect-workers.zip`
+ **Handler:** `file_detect_worker.lambda_handler`
+ **Memory:** `1024` MB, **Timeout:** `90` giây
+ **Ephemeral storage** (trong General configuration): `1024` MB, vì file tải về được lưu tạm trong `/tmp`
+ **Trigger:** SQS `phishing-file-queue`, **Batch size** `1`

Biến môi trường của hàm này:

| Key | Value | Ghi chú |
|---|---|---|
| `JOBS_TABLE` | `PhishingScanJobs` | Bắt buộc |
| `CACHE_TABLE` | `PhishingScanCache` | Bắt buộc |
| `REPORTS_BUCKET` | `phishing-audit-logs-<ACCOUNT_ID>` | Tùy chọn |
| `VT_API_KEY` | khóa VirusTotal | Tùy chọn |
| `ABUSECH_AUTH_KEY` | khóa abuse.ch | Tùy chọn |
| `VT_UPLOAD_UNKNOWN_FILES` | `true` hoặc `false` | Tùy chọn, mặc định `true` |

Khi `VT_UPLOAD_UNKNOWN_FILES` là `true`, file chưa có trên VirusTotal sẽ được tải lên VirusTotal để phân tích. Đặt `false` để chỉ tra theo mã băm và không gửi nội dung file ra ngoài.

#### Batch size và giới hạn của VirusTotal

Batch size bằng 1 nghĩa là mỗi lần gọi Lambda xử lý một message, giúp việc gỡ lỗi dễ hơn. Gói miễn phí của VirusTotal giới hạn 4 lượt mỗi phút, vì vậy nếu quét nhiều file liên tiếp có thể gặp lỗi `HTTP 429`. Khi đó kết quả có `confidence` thấp hơn thay vì báo lỗi.

#### Kết quả

+ Có bốn hàm Lambda: `IngestLambda`, `StatusLambda`, `FastDetectWorker`, `FileDetectWorker`.
+ `FastDetectWorker` được kích hoạt bởi `phishing-fast-queue` và `FileDetectWorker` bởi `phishing-file-queue`, cả hai trạng thái **Enabled**.
+ Hai hàm `IngestLambda` và `StatusLambda` sẽ được nối với API Gateway ở mục 5.4.7.