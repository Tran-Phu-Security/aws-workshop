---
title : "Tạo bucket S3"
date : 2026-11-27
weight : 3
chapter : false
pre : " <b> 5.4.3. </b> "
---

Hệ thống dùng hai bucket S3:

| Bucket | Bắt buộc | Mục đích |
|---|---|---|
| `phishing-raw-uploads-<ACCOUNT_ID>` | Có | Nhận file người dùng tải lên trực tiếp từ trình duyệt qua presigned URL |
| `phishing-audit-logs-<ACCOUNT_ID>` | Không | Lưu log JSON của mỗi lần quét tại `logs/<job_id>.json` |

Tên bucket S3 phải là duy nhất trên toàn cầu. Thay `<ACCOUNT_ID>` bằng số tài khoản AWS gồm 12 chữ số. Chỉ dùng chữ thường, số và dấu gạch ngang.

#### Tạo bucket nhận file tải lên

1. Mở dịch vụ **S3** trên AWS Console, chọn **Create bucket**.
2. Nhập thông tin:
   + **Bucket type:** General purpose
   + **Bucket name:** `phishing-raw-uploads-<ACCOUNT_ID>`
   + **AWS Region:** Asia Pacific (Singapore) ap-southeast-1
   + **Object Ownership:** ACLs disabled
   + **Block Public Access settings for this bucket:** giữ chọn **Block all public access**
   + **Bucket Versioning:** Disable
   + **Default encryption:** Server-side encryption with Amazon S3 managed keys (SSE-S3)
3. Chọn **Create bucket**.

#### Cấu hình CORS

Trình duyệt tải file lên S3 bằng yêu cầu `PUT` từ một địa chỉ khác với S3, nên bucket phải cho phép nguồn gốc đó.

1. Mở bucket vừa tạo, chọn tab **Permissions**.
2. Kéo xuống **Cross-origin resource sharing (CORS)**, chọn **Edit**.
3. Dán cấu hình sau rồi chọn **Save changes**:

```json
[
  {
    "AllowedHeaders": ["*"],
    "AllowedMethods": ["PUT"],
    "AllowedOrigins": ["http://localhost:8000"],
    "ExposeHeaders": []
  }
]
```

`http://localhost:8000` là địa chỉ giao diện web khi chạy trên máy (mục 5.4.9). Nếu giao diện được đặt ở một địa chỉ khác, thêm địa chỉ đó vào `AllowedOrigins`.

#### Tự động xóa file tải lên

File người dùng tải lên chỉ cần tồn tại trong lúc phân tích. Quy tắc vòng đời giúp xóa chúng tự động.

1. Trong bucket, mở tab **Management**, chọn **Create lifecycle rule**.
2. Nhập thông tin:
   + **Lifecycle rule name:** `expire-uploads`
   + **Choose a rule scope:** Limit the scope of this rule using one or more filters
   + **Prefix:** `uploads/`
   + **Lifecycle rule actions:** chọn **Expire current versions of objects**
   + **Days after object creation:** `1`
3. Xác nhận cảnh báo của S3 rồi chọn **Create rule**.

Mã nguồn lưu mỗi file tại khóa `uploads/<job_id>/<tên file>`, vì vậy tiền tố `uploads/` bao phủ toàn bộ file tải lên.

#### Tạo bucket log (tùy chọn)

Bucket này chỉ được dùng khi biến môi trường `REPORTS_BUCKET` của worker được đặt. Nếu không đặt, hệ thống bỏ qua việc ghi log lên S3.

Tạo bucket `phishing-audit-logs-<ACCOUNT_ID>` với các thiết lập giống bucket trên (Region Singapore, Block all public access, SSE-S3). Bucket này không cần CORS.

#### Kết quả

+ Bucket `phishing-raw-uploads-<ACCOUNT_ID>` có Block all public access, CORS cho `PUT` và quy tắc xóa sau 1 ngày với tiền tố `uploads/`.
+ Nếu cần ghi log: bucket `phishing-audit-logs-<ACCOUNT_ID>`.
+ Ghi lại tên bucket, vì mục 5.4.5 và 5.4.6 sẽ dùng.