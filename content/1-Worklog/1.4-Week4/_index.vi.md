
---
title: "Worklog Tuần 4"
date: 2026-10-05
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Mục tiêu tuần 4:

- Hiểu được chức năng và kiến trúc cơ bản của Amazon Simple Storage Service (S3).
- Tìm hiểu các khái niệm Bucket, Object, Key và Region.
- Nắm được cách tạo, quản lý và kiểm soát quyền truy cập vào S3 Bucket.
- Tìm hiểu các tính năng bảo mật và quản lý dữ liệu như Bucket Policy và Versioning.
- Nghiên cứu Lifecycle Management nhằm tối ưu việc lưu trữ và quản lý dữ liệu.
- Thực hành tạo và cấu hình S3 Bucket trên AWS.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Tìm hiểu tổng quan về Amazon S3.<br>- Nghiên cứu vai trò của S3 trong việc lưu trữ dữ liệu trên nền tảng AWS.<br>- Tìm hiểu các khái niệm Bucket, Object, Key và Region.<br>- Phân biệt S3 với các dịch vụ lưu trữ khác trên AWS. | 05/10/2026 | 05/10/2026 | [Amazon S3 Workshop](https://000057.awsfcaj.com/) |
| 2 | - Tìm hiểu cách tạo và quản lý S3 Bucket.<br>- Nghiên cứu các thiết lập cơ bản khi tạo Bucket.<br>- Thực hành tải lên, tải xuống và xóa Object.<br>- Tìm hiểu cách tổ chức dữ liệu bằng Object Key và Prefix. | 05/10/2026 | 05/10/2026 | [Amazon S3 Workshop](https://000057.awsfcaj.com/) |
| 2 | - Tìm hiểu cơ chế kiểm soát quyền truy cập trên Amazon S3.<br>- Nghiên cứu Bucket Policy và cấu trúc policy dạng JSON.<br>- Tìm hiểu Block Public Access và các thiết lập bảo mật liên quan.<br>- Phân tích những rủi ro khi cấu hình quyền truy cập công khai không phù hợp. | 05/10/2026 | 05/10/2026 | [Amazon S3 Workshop](https://000057.awsfcaj.com/) |
| 2 | - Tìm hiểu chức năng S3 Versioning.<br>- Nghiên cứu cách Versioning hỗ trợ lưu giữ và khôi phục các phiên bản Object.<br>- Tìm hiểu Delete Markers và cách quản lý các phiên bản dữ liệu.<br>- Thực hành bật Versioning cho S3 Bucket. | 05/10/2026 | 05/10/2026 | [Amazon S3 Workshop](https://000057.awsfcaj.com/) |
| 2 | - Tìm hiểu S3 Lifecycle Management và các quy tắc quản lý vòng đời dữ liệu.<br>- Nghiên cứu cách chuyển đổi Object giữa các lớp lưu trữ.<br>- Tìm hiểu các chính sách tự động xóa hoặc chuyển đổi dữ liệu theo thời gian.<br>- Thực hành cấu hình Lifecycle Rules và kiểm tra lại các thiết lập của Bucket. | 05/10/2026 | 05/10/2026 | [Amazon S3 Workshop](https://000057.awsfcaj.com/) |

### Kết quả đạt được tuần 4:

Trong tuần thứ tư, mình tập trung tìm hiểu Amazon Simple Storage Service (S3), một dịch vụ lưu trữ đối tượng (Object Storage) của AWS. Qua quá trình học tập, mình hiểu được vai trò của S3 trong việc lưu trữ, quản lý và truy xuất dữ liệu trên nền tảng đám mây.

Trước tiên, mình tìm hiểu các khái niệm cơ bản như Bucket, Object, Key và Region. Mình hiểu rằng Bucket là nơi chứa các Object, trong khi Object là đơn vị dữ liệu được lưu trữ trên S3 và được định danh thông qua Object Key. Bên cạnh đó, mình tìm hiểu cách lựa chọn Region phù hợp để tổ chức và quản lý dữ liệu.

Tiếp theo, mình nghiên cứu cách tạo và quản lý S3 Bucket. Mình tìm hiểu các thiết lập cơ bản khi khởi tạo Bucket, đồng thời thực hành tải lên, tải xuống và xóa Object. Ngoài ra, mình cũng tìm hiểu cách sử dụng Prefix để tổ chức dữ liệu theo cấu trúc logic, giúp việc quản lý và truy xuất dữ liệu thuận tiện hơn.

Một nội dung quan trọng khác là cơ chế kiểm soát quyền truy cập trên Amazon S3. Mình đã tìm hiểu Bucket Policy, cấu trúc policy dạng JSON và chức năng Block Public Access. Qua đó, mình nhận thức được tầm quan trọng của việc cấu hình quyền truy cập phù hợp nhằm hạn chế nguy cơ dữ liệu bị truy cập hoặc công khai ngoài ý muốn.

Bên cạnh vấn đề phân quyền, mình tìm hiểu tính năng S3 Versioning. Tính năng này cho phép lưu giữ nhiều phiên bản của một Object, hỗ trợ khôi phục dữ liệu khi có thay đổi hoặc xóa nhầm. Mình cũng nghiên cứu khái niệm Delete Markers và cách quản lý các phiên bản dữ liệu trong Bucket.

Cuối cùng, mình tìm hiểu S3 Lifecycle Management, một cơ chế hỗ trợ tự động quản lý vòng đời dữ liệu. Mình hiểu được cách thiết lập Lifecycle Rules để chuyển Object sang các lớp lưu trữ khác nhau hoặc tự động xóa dữ liệu theo những điều kiện được xác định trước. Qua đó, mình nhận thức rõ hơn vai trò của Lifecycle Management trong việc tối ưu chi phí và quản lý dữ liệu lâu dài.

Sau tuần thứ tư, mình đã nắm được những kiến thức cơ bản về Amazon S3, từ cách tổ chức dữ liệu, quản lý Bucket và Object đến kiểm soát quyền truy cập, lưu giữ phiên bản và quản lý vòng đời dữ liệu. Đây là nền tảng cần thiết để tiếp tục tìm hiểu các dịch vụ lưu trữ và quản lý dữ liệu khác trên AWS.
