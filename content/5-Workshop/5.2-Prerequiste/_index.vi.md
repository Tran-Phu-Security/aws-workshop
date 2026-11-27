---
title : "Kiến trúc và luồng dữ liệu"
date : 2026-11-27
weight : 3
chapter : false
pre : " <b> 5.3. </b> "
---

![Kiến trúc Trust Check](/images/5-Workshop/5.3-Architecture/trust_check_architecture.png)

#### Ý tưởng chính

Việc quét có thể mất vài giây đến cả phút vì phải gọi nhiều nguồn bên ngoài. Vì vậy hệ thống không bắt người dùng chờ trong một request. Thay vào đó:

1. API nhận yêu cầu, tạo một **job** và trả về `job_id` ngay.
2. Một worker xử lý job ở phía sau.
3. Giao diện hỏi kết quả theo `job_id` (mỗi 2 giây) cho đến khi job hoàn tất.

Link và email nhẹ nên đi qua hàng đợi nhanh. File nặng hơn và phải chờ upload nên đi qua hàng đợi riêng, để một file lớn không làm chậm các yêu cầu link.

#### Vai trò từng dịch vụ

| Dịch vụ | Thành phần | Việc làm |
|---|---|---|
| API Gateway (HTTP API) | `POST /scan`, `GET /scan/{job_id}` | Cửa vào của hệ thống |
| Lambda | `IngestLambda` | Kiểm tra đầu vào, tra bộ đệm, tạo job, đẩy message vào SQS, tạo presigned URL cho file |
| Lambda | `StatusLambda` | Đọc trạng thái và kết quả của job từ DynamoDB |
| Lambda | `FastDetectWorker` | Phân tích link và email |
| Lambda | `FileDetectWorker` | Phân tích file |
| SQS | `phishing-fast-queue` | Hàng đợi link và email (visibility timeout 60 giây) |
| SQS | `phishing-file-queue` | Hàng đợi file (visibility timeout 300 giây) |
| SQS | `phishing-dlq` | Hàng đợi lỗi, giữ message thất bại nhiều lần |
| DynamoDB | `PhishingScanJobs` | Lưu job: trạng thái, điểm, kết quả. Khóa chính `job_id` |
| DynamoDB | `PhishingScanCache` | Lưu kết quả để dùng lại. Khóa chính `target_hash`, có TTL |
| S3 | Bucket upload | Nhận file người dùng tải lên trực tiếp |
| S3 | Bucket báo cáo (tùy chọn) | Lưu log JSON của mỗi lần quét, chỉ ghi khi đặt biến `REPORTS_BUCKET` |
| Systems Manager | Parameter Store (tùy chọn) | Cất API key dạng `SecureString` |
| CloudWatch | Logs, Metrics | Theo dõi hoạt động của các Lambda |

#### Luồng dữ liệu

**Với link và email**

1. Giao diện gửi `POST /scan` với `type` là `link` hoặc `email`, cùng `payload`.
2. `IngestLambda` băm `payload` bằng SHA-256 rồi tra `PhishingScanCache`. Nếu có kết quả còn hạn thì trả luôn.
3. Nếu chưa có, hàm tạo job trạng thái `PENDING` trong `PhishingScanJobs`, gửi message vào `phishing-fast-queue` và trả `job_id`.
4. SQS kích hoạt `FastDetectWorker`. Worker đổi trạng thái sang `PROCESSING`, phân tích, rồi gọi các nguồn bên ngoài.
5. **Scoring Engine** gom các dấu hiệu thành điểm tin cậy, mức rủi ro, độ tin cậy và danh sách lý do.
6. Worker ghi kết quả vào job (`COMPLETED`), ghi vào bộ đệm (TTL 24 giờ), và ghi log JSON lên S3 nếu có bucket báo cáo.
7. Giao diện gọi `GET /scan/{job_id}`. `StatusLambda` đọc DynamoDB và trả kết quả.

**Với file** có thêm hai điểm khác:

+ Ở bước 3, `IngestLambda` tạo thêm **presigned URL** (hiệu lực 5 phút). Giao diện dùng nó để `PUT` file thẳng lên S3, không đi qua Lambda hay API Gateway.
+ `FileDetectWorker` chờ tối đa 30 giây cho file xuất hiện trên S3, tải về `/tmp`, tính SHA-256 và MD5, kiểm tra magic bytes, luật kiểu YARA, macro, mã chủ động trong PDF, tệp thực thi trong file nén, entropy, và tra VirusTotal, MalwareBazaar bằng mã băm. Hệ thống không upload file của bạn lên các nguồn đó, trừ VirusTotal khi biến `VT_UPLOAD_UNKNOWN_FILES` bật (mặc định là bật).

#### Trạng thái job

`PENDING` (đã nhận) → `PROCESSING` (đang quét) → `COMPLETED` (xong, có `result`).

Nếu phân tích lỗi, job vẫn kết thúc ở `COMPLETED` với `risk_level` là `SCAN ERROR`. Kết quả lỗi không được ghi vào bộ đệm.

#### Cách tính điểm

Điểm bắt đầu từ 100 và bị trừ theo từng dấu hiệu. Các detector chỉ phát hiện, còn bảng luật trong `common/scoring.py` quyết định điểm trừ và lý do hiển thị. Một vài nguyên tắc đáng biết:

+ Mỗi mã dấu hiệu chỉ trừ một lần, mỗi nhóm có mức trừ tối đa, nên điểm không bị trừ quá tay.
+ Tín hiệu nặng đặt trần điểm. Ví dụ Google Safe Browsing hoặc URLhaus báo thì điểm tối đa là 20.
+ Nguồn nào thiếu key, hết hạn mức hoặc lỗi mạng thì bị bỏ qua và làm `confidence` giảm. Hệ thống không coi "không hỏi được" là "sạch".
+ Kết quả `confidence` là LOW hoặc `SCAN ERROR` không được lưu vào bộ đệm.

#### Những điểm kỹ thuật đáng chú ý

+ **Gửi trùng message.** SQS Standard có thể giao một message nhiều lần. Worker kiểm tra nếu job đã `COMPLETED` thì bỏ qua.
+ **Giới hạn thời gian.** Mỗi worker tự tính thời gian còn lại của Lambda và dừng gọi nguồn ngoài sớm để kịp ghi kết quả.
+ **Dọn dữ liệu tự động.** Job có `expires_at` sau 7 ngày, bộ đệm sau 24 giờ. TTL của DynamoDB xóa không tức thời nên code luôn tự kiểm tra hạn.
+ **Mạng ra ngoài.** Khi quét link, worker truy cập thật vào link để theo dõi chuyển hướng và đọc trang đích. Code có lớp chặn địa chỉ nội bộ để tránh SSRF.
+ **Quyền hạn.** Mỗi hàm dùng role riêng, chỉ cấp quyền cho đúng bảng, hàng đợi và bucket nó cần (chi tiết ở mục 5.4).

#### Giới hạn hiện tại

+ Quét lại cùng một link hoặc email trong 24 giờ có thể trả lỗi HTTP 500. Nguyên nhân là nhánh bộ đệm của `IngestLambda` chưa chuyển kiểu số của DynamoDB khi tạo JSON. Mục 5.5 sẽ nhắc lại khi kiểm thử.
+ Bộ đệm hiện chủ yếu có tác dụng với link và email. Với file, khóa đệm là SHA-256 của nội dung, còn lúc nhận yêu cầu hệ thống chỉ có tên file nên chưa tra được.
+ ClamAV, YARA thật và xuất PDF chưa có, như đã nêu ở mục 5.1.