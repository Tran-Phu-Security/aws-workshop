---
title : "Điều kiện tiên quyết"
date : 2026-11-27
weight : 2
chapter : false
pre : " <b> 5.2. </b> "
---

Trước khi triển khai hệ thống **Trust Check** ở mục 5.4, bạn cần chuẩn bị đầy đủ tài khoản AWS, quyền hạn, công cụ trên máy cá nhân và mã nguồn như bên dưới.

#### 1. Tài khoản AWS & Quyền hạn
- Một **tài khoản AWS** đang hoạt động, triển khai thống nhất tại Region **Asia Pacific (Singapore) `ap-southeast-1`**.
- IAM User hoặc Role có quyền tạo và cấu hình các dịch vụ: **DynamoDB, Amazon S3, Amazon SQS, IAM, AWS Lambda, API Gateway (HTTP API), Systems Manager Parameter Store (tùy chọn) và CloudWatch**.
- Khuyến nghị bật cảnh báo chi phí (Billing Alert) để theo dõi tài nguyên trong quá trình thực hành.

#### 2. Công cụ trên máy cá nhân
Mở PowerShell (hoặc Terminal) và kiểm tra đã cài đặt:
- **Python 3.12** (khớp với runtime `Python 3.12` trên Lambda) — kiểm tra bằng lệnh `python --version`. Không cần cài thêm thư viện `pip` ngoài vì backend chỉ dùng thư viện chuẩn của Python và `boto3` (có sẵn trên Lambda).
- **Git** — kiểm tra bằng lệnh `git --version`.
- **Trình duyệt web** (Chrome hoặc Edge) để thao tác AWS Management Console và kiểm thử giao diện frontend.

#### 3. API Key từ các nguồn Threat Intelligence (Tùy chọn)
Hệ thống vẫn chạy bình thường khi không có API Key (độ tin cậy `confidence` sẽ thấp hơn). Để kiểm tra đầy đủ ở mục 5.4.6 / 5.4.8, chuẩn bị trước các key sau:
- **VirusTotal** (`VT_API_KEY`, tùy chọn key dự phòng `VT_BACKUP_API_KEYS`)
- **Google Safe Browsing** (`GSB_API_KEY`)
- **abuse.ch** (`ABUSECH_AUTH_KEY`)

#### 4. Tải mã nguồn Workshop
Mở PowerShell và clone kho mã nguồn `AWS-Phishing-Detection` phục vụ cho mục 5.4.1:

    git clone <link-repo-AWS-Phishing-Detection>
    cd AWS-Phishing-Detection

Kiểm tra thư mục `backend` có đầy đủ các tệp/thư mục: `ingest_lambda.py`, `status_lambda.py`, `build_zip.py`, `common/`, `detectors/` và `tests/`.