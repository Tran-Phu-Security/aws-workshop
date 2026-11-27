---
title : "Tạo hàng đợi SQS"
date : 2026-11-27
weight : 4
chapter : false
pre : " <b> 5.4.4. </b> "
---

Hệ thống dùng ba hàng đợi SQS loại Standard:

| Hàng đợi | Vai trò | Visibility timeout |
|---|---|---|
| `phishing-dlq` | Hàng đợi lỗi, giữ các message xử lý thất bại nhiều lần | Mặc định |
| `phishing-fast-queue` | Công việc quét link và email | 60 giây |
| `phishing-file-queue` | Công việc quét file | 300 giây |

Hàng đợi lỗi cần được tạo trước, vì hai hàng đợi còn lại tham chiếu đến nó.

#### Visibility timeout

Khi một worker nhận message, SQS ẩn message đó với các worker khác trong khoảng visibility timeout. Nếu worker không xóa message trong khoảng này (do lỗi hoặc quá thời gian), message hiện lại và được xử lý lần nữa. Giá trị này phải lớn hơn hoặc bằng timeout của hàm Lambda tiêu thụ hàng đợi. Hàm `FastDetectWorker` có timeout 45 giây và `FileDetectWorker` có timeout 90 giây (mục 5.4.6), nên 60 giây và 300 giây đáp ứng điều kiện này.

#### Tạo hàng đợi lỗi phishing-dlq

1. Mở dịch vụ **Amazon SQS** trên AWS Console, chọn Region **Asia Pacific (Singapore) ap-southeast-1**.
2. Chọn **Create queue**.
3. Nhập thông tin:
   + **Type:** Standard
   + **Name:** `phishing-dlq`
4. Giữ nguyên các thiết lập còn lại, chọn **Create queue**.

#### Tạo hàng đợi phishing-fast-queue

1. Chọn **Create queue**.
2. Nhập thông tin:
   + **Type:** Standard
   + **Name:** `phishing-fast-queue`
3. Ở mục **Configuration**, đặt **Visibility timeout** là `1` **Minutes** (tương đương 60 giây).
4. Cuộn xuống **Dead-letter queue**, chọn **Enabled**:
   + **Choose queue:** chọn `phishing-dlq`
   + **Maximum receives:** `3`
5. Chọn **Create queue**.

#### Tạo hàng đợi phishing-file-queue

Thực hiện như hàng đợi trên với các khác biệt sau:

+ **Name:** `phishing-file-queue`
+ **Visibility timeout:** `5` **Minutes** (tương đương 300 giây)
+ **Dead-letter queue:** Enabled, chọn `phishing-dlq`, **Maximum receives** là `3`

#### Cách hoạt động của hàng đợi lỗi

Khi một message được nhận quá số lần **Maximum receives** mà vẫn chưa được xóa, SQS chuyển message sang `phishing-dlq`. Cơ chế này ngăn một message lỗi bị xử lý lặp vô hạn.

Chỉ lỗi ở tầng hạ tầng, khi hàm Lambda kết thúc bất thường, mới khiến message quay lại hàng đợi. Lỗi trong quá trình phân tích được mã nguồn bắt lại và ghi thành kết quả `SCAN ERROR`, nên không đưa message vào hàng đợi lỗi.

#### Kết quả

+ Ba hàng đợi `phishing-dlq`, `phishing-fast-queue` và `phishing-file-queue` ở trạng thái hoạt động trong Region Singapore.
+ Hai hàng đợi xử lý đều trỏ đến `phishing-dlq` với Maximum receives là 3.
+ Việc nối hàng đợi với hàm Lambda được thực hiện ở mục 5.4.6.