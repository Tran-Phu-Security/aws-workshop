---
title : "Tạo role IAM"
date : 2026-11-27
weight : 5
chapter : false
pre : " <b> 5.4.5. </b> "
---

Mỗi hàm Lambda dùng một role IAM riêng, chỉ được cấp đúng những quyền hàm đó cần. Nhờ vậy một hàm bị lỗi hoặc bị lợi dụng sẽ không truy cập được các tài nguyên khác.

| Role | Dùng cho hàm | Quyền cần |
|---|---|---|
| `PhishingIngestLambdaRole` | `IngestLambda` | Đọc/ghi hai bảng DynamoDB, gửi message vào hai hàng đợi, tạo presigned URL ghi vào bucket tải lên |
| `PhishingStatusLambdaRole` | `StatusLambda` | Đọc bảng `PhishingScanJobs` |
| `PhishingFastWorkerRole` | `FastDetectWorker` | Nhận message từ `phishing-fast-queue`, cập nhật job, ghi bộ đệm |
| `PhishingFileWorkerRole` | `FileDetectWorker` | Như trên, cộng đọc file từ bucket tải lên |

Cả bốn role đều gắn thêm chính sách có sẵn `AWSLambdaBasicExecutionRole` để hàm ghi log lên CloudWatch.

Trong các đoạn JSON dưới đây, thay `<ACCOUNT_ID>` bằng số tài khoản AWS gồm 12 chữ số. Tên bảng, hàng đợi và bucket lấy từ các mục 5.4.2 đến 5.4.4.

#### Cách tạo một role

Thực hiện lần lượt cho cả bốn role:

1. Mở dịch vụ **IAM**, chọn **Roles** ở menu bên trái, rồi chọn **Create role**.
2. Ở **Trusted entity type** chọn **AWS service**, ở **Use case** chọn **Lambda**, chọn **Next**.
3. Tìm và chọn chính sách **AWSLambdaBasicExecutionRole**, chọn **Next**.
4. Nhập **Role name** theo bảng trên, chọn **Create role**.
5. Mở role vừa tạo, chọn **Add permissions** rồi **Create inline policy**.
6. Chuyển sang tab **JSON**, dán đoạn JSON tương ứng bên dưới.
7. Chọn **Next**, đặt **Policy name** là `PhishingAccess`, chọn **Create policy**.

#### PhishingIngestLambdaRole

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ScanTables",
      "Effect": "Allow",
      "Action": ["dynamodb:GetItem", "dynamodb:PutItem"],
      "Resource": [
        "arn:aws:dynamodb:ap-southeast-1:<ACCOUNT_ID>:table/PhishingScanJobs",
        "arn:aws:dynamodb:ap-southeast-1:<ACCOUNT_ID>:table/PhishingScanCache"
      ]
    },
    {
      "Sid": "SendToQueues",
      "Effect": "Allow",
      "Action": ["sqs:GetQueueUrl", "sqs:SendMessage"],
      "Resource": [
        "arn:aws:sqs:ap-southeast-1:<ACCOUNT_ID>:phishing-fast-queue",
        "arn:aws:sqs:ap-southeast-1:<ACCOUNT_ID>:phishing-file-queue"
      ]
    },
    {
      "Sid": "PresignedUpload",
      "Effect": "Allow",
      "Action": "s3:PutObject",
      "Resource": "arn:aws:s3:::phishing-raw-uploads-<ACCOUNT_ID>/uploads/*"
    }
  ]
}
```

Presigned URL mang quyền của role đã tạo ra nó. Vì vậy role này cần `s3:PutObject` trên tiền tố `uploads/`, nếu không việc tải file lên từ trình duyệt sẽ bị S3 từ chối dù URL hợp lệ.

#### PhishingStatusLambdaRole

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ReadJobs",
      "Effect": "Allow",
      "Action": "dynamodb:GetItem",
      "Resource": "arn:aws:dynamodb:ap-southeast-1:<ACCOUNT_ID>:table/PhishingScanJobs"
    }
  ]
}
```

#### PhishingFastWorkerRole

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ConsumeFastQueue",
      "Effect": "Allow",
      "Action": ["sqs:ReceiveMessage", "sqs:DeleteMessage", "sqs:GetQueueAttributes"],
      "Resource": "arn:aws:sqs:ap-southeast-1:<ACCOUNT_ID>:phishing-fast-queue"
    },
    {
      "Sid": "UpdateJobs",
      "Effect": "Allow",
      "Action": ["dynamodb:GetItem", "dynamodb:UpdateItem"],
      "Resource": "arn:aws:dynamodb:ap-southeast-1:<ACCOUNT_ID>:table/PhishingScanJobs"
    },
    {
      "Sid": "WriteCache",
      "Effect": "Allow",
      "Action": "dynamodb:PutItem",
      "Resource": "arn:aws:dynamodb:ap-southeast-1:<ACCOUNT_ID>:table/PhishingScanCache"
    }
  ]
}
```

#### PhishingFileWorkerRole

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ConsumeFileQueue",
      "Effect": "Allow",
      "Action": ["sqs:ReceiveMessage", "sqs:DeleteMessage", "sqs:GetQueueAttributes"],
      "Resource": "arn:aws:sqs:ap-southeast-1:<ACCOUNT_ID>:phishing-file-queue"
    },
    {
      "Sid": "UpdateJobs",
      "Effect": "Allow",
      "Action": ["dynamodb:GetItem", "dynamodb:UpdateItem"],
      "Resource": "arn:aws:dynamodb:ap-southeast-1:<ACCOUNT_ID>:table/PhishingScanJobs"
    },
    {
      "Sid": "WriteCache",
      "Effect": "Allow",
      "Action": "dynamodb:PutItem",
      "Resource": "arn:aws:dynamodb:ap-southeast-1:<ACCOUNT_ID>:table/PhishingScanCache"
    },
    {
      "Sid": "ReadUploadedFile",
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::phishing-raw-uploads-<ACCOUNT_ID>/uploads/*"
    },
    {
      "Sid": "CheckUploadExists",
      "Effect": "Allow",
      "Action": "s3:ListBucket",
      "Resource": "arn:aws:s3:::phishing-raw-uploads-<ACCOUNT_ID>"
    }
  ]
}
```

Quyền `s3:ListBucket` áp dụng cho chính bucket (không có `/*`). Worker kiểm tra file đã tới S3 chưa bằng cách hỏi thông tin đối tượng. Khi có quyền ListBucket và file chưa có, S3 trả lỗi 404 và worker chờ rồi thử lại. Khi thiếu quyền này, S3 trả lỗi 403 và worker dừng ngay với lỗi, dù file đã tải lên đúng.

#### Quyền tùy chọn cho hai worker

Hai quyền sau chỉ cần khi bật các tính năng tùy chọn. Thêm vào mảng `Statement` của **cả hai** role worker.

Ghi log JSON lên bucket báo cáo (khi đặt biến `REPORTS_BUCKET`):

```json
{
  "Sid": "WriteAuditLog",
  "Effect": "Allow",
  "Action": "s3:PutObject",
  "Resource": "arn:aws:s3:::phishing-audit-logs-<ACCOUNT_ID>/logs/*"
}
```

Đọc API key từ Parameter Store (mục 5.4.8):

```json
{
  "Sid": "ReadApiKeys",
  "Effect": "Allow",
  "Action": "ssm:GetParameter",
  "Resource": "arn:aws:ssm:ap-southeast-1:<ACCOUNT_ID>:parameter/phishing/*"
}
```

#### Kết quả

+ Có bốn role: `PhishingIngestLambdaRole`, `PhishingStatusLambdaRole`, `PhishingFastWorkerRole`, `PhishingFileWorkerRole`.
+ Mỗi role gắn `AWSLambdaBasicExecutionRole` và một inline policy tên `PhishingAccess`.
+ Mục 5.4.6 sẽ gán từng role vào hàm Lambda tương ứng.