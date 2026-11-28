---
title : "Tổng quan"
date : 2026-11-27
weight : 1
chapter : false
pre : " <b> 5.1. </b> "
---

#### Trust Check là gì

Trust Check kiểm tra một link, một email hoặc một file xem có dấu hiệu lừa đảo hay mã độc không. Thay vì chỉ trả lời "an toàn" hoặc "không an toàn", hệ thống cho bạn một điểm tin cậy từ 0 đến 100 và nói rõ vì sao điểm bị trừ.

| Điểm | Mức |
|---|---|
| 80 – 100 | SAFE |
| 50 – 79 | SUSPICIOUS |
| dưới 50 | DANGEROUS |

Mỗi kết quả còn có trường `confidence` (HIGH, MEDIUM, LOW) cho biết có bao nhiêu nguồn uy tín bên ngoài đã trả lời. Điểm SAFE mà `confidence` là LOW chỉ có nghĩa là chưa thấy dấu hiệu xấu, chưa phải đã được xác nhận an toàn.

#### Bạn sẽ dựng những gì

+ **API Gateway (HTTP API):** nhận `POST /scan` và `GET /scan/{job_id}`.
+ **Lambda (Python 3.12):** bốn hàm `IngestLambda`, `StatusLambda`, `FastDetectWorker`, `FileDetectWorker`.
+ **SQS:** hàng đợi `phishing-fast-queue` cho link và email, `phishing-file-queue` cho file, và `phishing-dlq` cho các message lỗi.
+ **DynamoDB:** bảng `PhishingScanJobs` lưu trạng thái và kết quả, bảng `PhishingScanCache` lưu bộ đệm có TTL.
+ **S3:** bucket nhận file người dùng tải lên bằng presigned URL.
+ **Systems Manager Parameter Store (tùy chọn):** cất API key dạng `SecureString`.
+ **CloudWatch:** xem log và metric.
+ **Giao diện web** viết bằng HTML, CSS, JavaScript, chạy trên máy bạn.

Ngoài AWS, hệ thống còn gọi VirusTotal, Google Safe Browsing, URLhaus, MalwareBazaar và RDAP, đều bằng gói miễn phí.

#### Làm xong bạn sẽ

+ Hiểu vai trò của từng dịch vụ và vì sao link, email được xử lý tách khỏi file.
+ Tự triển khai được một hệ thống serverless hoàn chỉnh trên AWS Console.
+ Thử với link, email và file mẫu, đọc được điểm tin cậy và lý do.
+ Xem được log, metric và hàng đợi lỗi trên CloudWatch và SQS.
+ Xóa sạch tài nguyên sau khi làm xong.

#### Thông tin chung

+ **Region:** Singapore (`ap-southeast-1`).
+ **Thời gian:** khoảng 2 đến 3 giờ (ước tính).
+ **Chi phí:** các dịch vụ tính theo mức dùng, ở quy mô workshop nhiều khả năng nằm trong AWS Free Tier. Bạn vẫn nên xem trang Billing và làm mục 5.6 sau khi xong.

#### Những gì chưa có

Workshop bám đúng mã nguồn hiện tại, nên có vài giới hạn bạn nên biết trước:

+ ClamAV chỉ chạy khi môi trường có `clamscan`. Trên Lambda thường thì nó bị bỏ qua.
+ YARA là bộ luật chuỗi byte tự viết, chưa phải thư viện YARA thật.
+ Phân tích email dùng bộ luật Anh – Việt, không dùng mô hình học máy.
+ SPF, DKIM, DMARC được đọc từ header email bạn dán vào, hệ thống không tự tra DNS.
+ Chưa có xuất báo cáo PDF và chưa có đăng nhập người dùng.

![Kiến trúc Trust Check](/aws-workshop/images/5-Workshop/5.1-Workshop-overview/trust_check_architecture.png)