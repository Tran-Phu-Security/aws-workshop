
+++
title = "Worklog Tuần 10"
date = 2026-11-16
weight = 10
chapter = false
pre = "<b> 1.10. </b>"
+++

# Tuần 10: Tìm hiểu kiến trúc Serverless trên AWS

## 1. Mục tiêu công việc

- Tìm hiểu khái niệm Serverless Computing và những đặc điểm cơ bản của mô hình này.
- Nắm được vai trò của AWS Lambda trong việc thực thi mã nguồn mà không cần tự quản lý máy chủ.
- Tìm hiểu cách tạo, cấu hình và quản lý Lambda Functions.
- Tìm hiểu Amazon API Gateway và vai trò của dịch vụ trong việc xây dựng, quản lý API.
- Tìm hiểu cách kết nối API Gateway với Lambda để xử lý các yêu cầu từ phía người dùng.
- Tìm hiểu các nguồn kích hoạt (Triggers), quyền thực thi và cơ chế giám sát hoạt động của Lambda.
- Khảo sát mô hình kết hợp Lambda, S3 và DynamoDB trong ứng dụng Serverless.

## 2. Công việc thực hiện

| STT | Nội dung công việc | Ngày bắt đầu | Ngày hoàn thành |
|---|---|---|---|
| 1 | Tìm hiểu tổng quan về Serverless Computing, nguyên lý hoạt động và lợi ích của mô hình này trong phát triển ứng dụng. | 16/11/2026 | 16/11/2026 |
| 2 | Tìm hiểu AWS Lambda, cách tạo Function, lựa chọn Runtime, cấu hình Handler và các thiết lập cơ bản. | 16/11/2026 | 16/11/2026 |
| 3 | Tìm hiểu cơ chế thực thi Lambda, Execution Role, thời gian Timeout, bộ nhớ và các giới hạn tài nguyên. | 16/11/2026 | 16/11/2026 |
| 4 | Tìm hiểu Amazon API Gateway, các thành phần cơ bản của API và cách cấu hình Endpoint để tiếp nhận yêu cầu. | 16/11/2026 | 16/11/2026 |
| 5 | Tìm hiểu cách tích hợp API Gateway với AWS Lambda để xử lý yêu cầu HTTP và trả về phản hồi cho người dùng. | 16/11/2026 | 16/11/2026 |
| 6 | Tìm hiểu các nguồn kích hoạt Lambda (Triggers), bao gồm API Gateway và sự kiện phát sinh từ Amazon S3. | 16/11/2026 | 16/11/2026 |
| 7 | Khảo sát mô hình ứng dụng Serverless sử dụng Lambda, S3 và DynamoDB; tìm hiểu cách giám sát hoạt động thông qua CloudWatch. | 16/11/2026 | 16/11/2026 |

## 3. Kết quả đạt được

- Hiểu được mô hình Serverless Computing, trong đó nhà cung cấp dịch vụ chịu trách nhiệm quản lý hạ tầng máy chủ bên dưới.
- Nắm được vai trò của AWS Lambda trong việc thực thi mã nguồn dựa trên sự kiện mà không cần tự quản lý máy chủ.
- Hiểu được các thành phần cơ bản của Lambda Function như Runtime, Handler, Execution Role, Timeout và Memory.
- Nắm được chức năng của Amazon API Gateway trong việc tạo và quản lý API để ứng dụng hoặc người dùng có thể gửi yêu cầu đến hệ thống.
- Hiểu được cơ chế tích hợp API Gateway với Lambda để xử lý yêu cầu HTTP và trả về kết quả.
- Biết được Lambda có thể được kích hoạt bởi nhiều nguồn sự kiện, chẳng hạn như API Gateway và Amazon S3.
- Có cái nhìn tổng quan về kiến trúc Serverless kết hợp Lambda, S3 và DynamoDB.
- Hiểu được vai trò của CloudWatch trong việc theo dõi Logs, Metrics và hỗ trợ kiểm tra lỗi khi Lambda thực thi.

## 4. Tài liệu tham khảo

- [AWS Cloud Journey – Serverless với Lambda, S3 và DynamoDB](https://000078.awsfcaj.com/)
- [AWS Cloud Journey – Frontend và API Gateway](https://000079.awsfcaj.com/)
- [AWS Lambda Documentation](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html)
- [Amazon API Gateway Documentation](https://docs.aws.amazon.com/apigateway/latest/developerguide/welcome.html)
