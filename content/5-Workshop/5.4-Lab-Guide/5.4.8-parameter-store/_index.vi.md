---
title : "Lưu API key vào Parameter Store (tùy chọn)"
date : 2026-11-27
weight : 8
chapter : false
pre : " <b> 5.4.8. </b> "
---

Ở mục 5.4.6, API key có thể được đặt trực tiếp trong biến môi trường của Lambda. Cách khác an toàn hơn là cất khóa mã hóa trong AWS Systems Manager Parameter Store. Khóa được lưu ở một nơi, và hai hàm worker tự đọc khi chạy. Mục này là tùy chọn. Bỏ qua mục này nếu đã đặt khóa bằng biến môi trường hoặc không dùng khóa.

Mã nguồn lấy khóa theo thứ tự: biến môi trường, sau đó Parameter Store, sau đó bỏ qua nguồn đó.

#### Tạo các parameter

1. Mở dịch vụ **Systems Manager** trên AWS Console, chọn Region **Asia Pacific (Singapore) ap-southeast-1**.
2. Chọn **Parameter Store** ở menu bên trái, rồi chọn **Create parameter**.
3. Nhập thông tin cho từng parameter trong bảng sau:

| Name | Value |
|---|---|
| `/phishing/VT_API_KEY` | Khóa VirusTotal |
| `/phishing/GSB_API_KEY` | Khóa Google Safe Browsing |
| `/phishing/ABUSECH_AUTH_KEY` | Khóa abuse.ch |
| `/phishing/VT_BACKUP_API_KEYS` | Các khóa VirusTotal dự phòng, ngăn cách bằng dấu phẩy (tùy chọn) |

   Với mỗi parameter, đặt **Tier** là **Standard**, **Type** là **SecureString**, và dán khóa vào ô **Value**. Giữ **KMS key source** là My current account với khóa mặc định `alias/aws/ssm`.
4. Chọn **Create parameter**.

Tên parameter phải đúng từng chữ, phân biệt chữ hoa và chữ thường. Loại Standard không tính phí lưu trữ.

#### Cấp quyền đọc

Quyền `ssm:GetParameter` trên `/phishing/*` đã được mô tả ở mục 5.4.5, phần quyền tùy chọn. Thêm statement `ReadApiKeys` vào inline policy `PhishingAccess` của cả hai role `PhishingFastWorkerRole` và `PhishingFileWorkerRole` nếu chưa làm. Khóa mã hóa mặc định `alias/aws/ssm` không cần thêm quyền `kms:Decrypt`. Nếu dùng khóa KMS tự tạo thì phải cấp thêm quyền này.

#### Cấu hình hai hàm worker

Thực hiện cho cả `FastDetectWorker` và `FileDetectWorker`:

1. Mở hàm, chọn **Configuration**, rồi chọn **Environment variables** và **Edit**.
2. Thêm biến `SSM_PREFIX` với giá trị `/phishing`.
3. **Xóa** các biến chứa khóa: `VT_API_KEY`, `GSB_API_KEY`, `ABUSECH_AUTH_KEY` và `VT_BACKUP_API_KEYS`.
4. Chọn **Save**.

Việc xóa là bắt buộc, vì mã nguồn luôn ưu tiên biến môi trường. Nếu biến cũ còn tồn tại, hàm sẽ không đọc khóa từ Parameter Store.

#### Lưu ý khi vận hành

+ Khóa đọc từ Parameter Store được nhớ trong suốt vòng đời của một môi trường thực thi Lambda. Khi đổi khóa trên Parameter Store, giá trị mới chỉ có hiệu lực khi hàm khởi động môi trường mới. Để áp dụng ngay, có thể lưu lại cấu hình hàm để buộc khởi động lại.
+ Nếu thiếu một khóa, nguồn tương ứng bị bỏ qua và `confidence` của kết quả giảm đi. Hệ thống vẫn hoạt động.
+ Log của worker có thể ghi `ParameterNotFound` cho `/phishing/VT_BACKUP_API_KEYS` khi không tạo parameter này. Đây là trạng thái bình thường.

#### Kết quả

+ Các parameter `/phishing/...` kiểu SecureString đã được tạo trong Region Singapore.
+ Cả hai worker có biến `SSM_PREFIX` là `/phishing` và không còn biến môi trường chứa khóa.