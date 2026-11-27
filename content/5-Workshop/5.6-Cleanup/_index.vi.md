---
title : "Dọn dẹp tài nguyên"
date : 2026-11-27
weight : 6
chapter : false
pre : " <b> 5.6. </b> "
---

Các tài nguyên dùng trong workshop có thể phát sinh chi phí nếu để lại, nhất là dung lượng DynamoDB đã cấp phát, dữ liệu S3 và log CloudWatch. Sau khi hoàn tất kiểm thử, xóa theo đúng thứ tự dưới đây. Thứ tự đi từ lớp ngoài vào lớp trong để tránh lỗi do tài nguyên còn được tham chiếu.

#### Bước 1: Xóa API Gateway

1. Mở dịch vụ **API Gateway** và chọn HTTP API của hệ thống.
2. Chọn **Actions**, chọn **Delete**, rồi xác nhận.

#### Bước 2: Xóa các hàm Lambda

1. Mở dịch vụ **Lambda**.
2. Chọn bốn hàm `IngestLambda`, `StatusLambda`, `FastDetectWorker` và `FileDetectWorker`.
3. Chọn **Actions**, chọn **Delete**, nhập `confirm` rồi xác nhận.

Việc xóa hàm cũng xóa luôn các event source mapping nối hàm với SQS.

#### Bước 3: Xóa các hàng đợi SQS

1. Mở dịch vụ **SQS**.
2. Chọn lần lượt ba hàng đợi `phishing-fast-queue`, `phishing-file-queue` và `phishing-dlq`.
3. Chọn **Delete**, nhập `delete` rồi xác nhận.

#### Bước 4: Xóa các bảng DynamoDB

1. Mở dịch vụ **DynamoDB** và chọn **Tables**.
2. Chọn bảng `PhishingScanJobs`, chọn **Delete**, rồi xác nhận.
3. Lặp lại với bảng `PhishingScanCache`.

#### Bước 5: Xóa các bucket S3

Bucket phải trống mới xóa được.

1. Mở dịch vụ **S3** và chọn bucket upload (giá trị `RAW_BUCKET`).
2. Chọn **Empty**, nhập `permanently delete` rồi xác nhận.
3. Chọn **Delete**, nhập tên bucket rồi xác nhận.
4. Lặp lại với bucket báo cáo (giá trị `REPORTS_BUCKET`).

#### Bước 6: Xóa vai trò và chính sách IAM

1. Mở dịch vụ **IAM** và chọn **Roles**.
2. Chọn các role thực thi đã tạo ở mục 5.4.5, rồi chọn **Delete**.
3. Chọn **Policies**, lọc theo **Customer managed**, rồi xóa các chính sách tự tạo cho hệ thống.

#### Bước 7: Xóa tham số trong Parameter Store

1. Mở **AWS Systems Manager** và chọn **Parameter Store**.
2. Chọn các tham số có tiền tố `/phishing/`, chọn **Delete**, rồi xác nhận.

Bước này xóa các khóa API đã lưu ở mục 5.4.8.

#### Bước 8: Xóa log group của CloudWatch

Log group không bị xóa cùng hàm Lambda và vẫn tính phí lưu trữ.

1. Mở dịch vụ **CloudWatch**, chọn **Logs**, rồi chọn **Log groups**.
2. Gõ `/aws/lambda/` vào ô tìm kiếm.
3. Chọn các log group của bốn hàm trên, chọn **Actions**, chọn **Delete log group(s)**, rồi xác nhận.

#### Kiểm tra lại

Mở lại từng dịch vụ ở trên và xác nhận danh sách tài nguyên của hệ thống đã trống. Có thể kiểm tra thêm ở trang **Billing and Cost Management** vào ngày hôm sau để chắc chắn không còn khoản phí phát sinh.