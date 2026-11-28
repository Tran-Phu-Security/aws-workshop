
---
title: "Worklog Tuần 5"
date: 2026-10-12
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Mục tiêu tuần 5:

- Hiểu được chức năng và vai trò của Amazon Relational Database Service (RDS).
- Tìm hiểu các hệ quản trị cơ sở dữ liệu (Database Engines) được Amazon RDS hỗ trợ.
- Nắm được cách khởi tạo và cấu hình một RDS DB Instance.
- Tìm hiểu cơ chế kết nối, kiểm soát truy cập và bảo mật cơ sở dữ liệu.
- Nghiên cứu các tính năng sao lưu, phục hồi và quản lý cơ sở dữ liệu.
- Thực hành triển khai và quản lý một cơ sở dữ liệu quan hệ trên AWS.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| Thứ Hai | - Tìm hiểu tổng quan về Amazon Relational Database Service (RDS).<br>- Nghiên cứu vai trò của RDS trong việc triển khai và quản lý cơ sở dữ liệu quan hệ trên AWS.<br>- Tìm hiểu các Database Engines được hỗ trợ như MySQL, PostgreSQL, MariaDB và Microsoft SQL Server.<br>- Phân biệt mô hình quản lý cơ sở dữ liệu trên Amazon RDS với việc tự triển khai database trên EC2. | 12/10/2026 | 12/10/2026 | [Amazon RDS Workshop](https://000005.awsfcaj.com/) |
| Thứ Ba | - Tìm hiểu các thành phần cấu hình của RDS DB Instance.<br>- Nghiên cứu DB Instance Class, Storage Type, Allocated Storage và Database Port.<br>- Tìm hiểu cách lựa chọn Database Engine và phiên bản phù hợp.<br>- Nghiên cứu các tùy chọn triển khai Single-AZ và Multi-AZ. | 13/10/2026 | 13/10/2026 | [Amazon RDS Workshop](https://000005.awsfcaj.com/) |
| Thứ Tư | - Tìm hiểu cách kết nối đến RDS DB Instance.<br>- Nghiên cứu vai trò của VPC, Subnet Group và Security Group trong việc kiểm soát kết nối cơ sở dữ liệu.<br>- Tìm hiểu cách cấu hình Inbound Rules cho Database Port.<br>- Thực hành kiểm tra kết nối từ máy khách hoặc ứng dụng đến cơ sở dữ liệu. | 14/10/2026 | 14/10/2026 | [Amazon RDS Workshop](https://000005.awsfcaj.com/) |
| Thứ Năm | - Tìm hiểu các tính năng sao lưu và phục hồi dữ liệu của Amazon RDS.<br>- Nghiên cứu Automated Backups, Manual Snapshots và Point-in-Time Recovery.<br>- Tìm hiểu các thiết lập Backup Retention Period.<br>- Phân tích vai trò của sao lưu trong việc bảo vệ dữ liệu và duy trì tính liên tục của hệ thống. | 15/10/2026 | 15/10/2026 | [Amazon RDS Workshop](https://000005.awsfcaj.com/) |
| Thứ Sáu | - Thực hành khởi tạo một RDS DB Instance trên AWS.<br>- Cấu hình Database Engine, Instance Class, Storage và Connectivity.<br>- Thực hành kết nối đến cơ sở dữ liệu và kiểm tra hoạt động.<br>- Tìm hiểu cách chỉnh sửa cấu hình, tạo Snapshot và quản lý trạng thái DB Instance.<br>- Nghiên cứu cách dừng hoặc xóa tài nguyên sau khi hoàn thành thực hành nhằm tránh phát sinh chi phí không cần thiết. | 16/10/2026 | 16/10/2026 | [Amazon RDS Workshop](https://000005.awsfcaj.com/) |

### Kết quả đạt được tuần 5:

Trong tuần thứ năm, mình tập trung tìm hiểu Amazon Relational Database Service (RDS), dịch vụ cơ sở dữ liệu quan hệ được quản lý bởi AWS. Qua quá trình học tập, mình hiểu được vai trò của RDS trong việc đơn giản hóa quá trình triển khai, vận hành và quản lý cơ sở dữ liệu trên nền tảng đám mây.

Trước tiên, mình tìm hiểu tổng quan về Amazon RDS và các hệ quản trị cơ sở dữ liệu được dịch vụ này hỗ trợ, bao gồm MySQL, PostgreSQL, MariaDB và Microsoft SQL Server. Mình hiểu được rằng RDS giúp giảm bớt khối lượng công việc quản trị cơ sở dữ liệu như thiết lập hạ tầng, sao lưu và bảo trì. Đồng thời, mình cũng phân biệt được mô hình sử dụng RDS với việc tự cài đặt và quản lý hệ quản trị cơ sở dữ liệu trên một EC2 Instance.

Tiếp theo, mình nghiên cứu các thành phần cấu hình của RDS DB Instance như Database Engine, Instance Class, Storage Type và Allocated Storage. Mình hiểu được cách lựa chọn cấu hình phù hợp với nhu cầu sử dụng và tìm hiểu sự khác biệt giữa hai mô hình triển khai Single-AZ và Multi-AZ. Qua đó, mình nhận thức được vai trò của Multi-AZ trong việc tăng tính sẵn sàng và hỗ trợ khả năng phục hồi của hệ thống cơ sở dữ liệu.

Một nội dung quan trọng khác là cơ chế kết nối và bảo mật RDS. Mình tìm hiểu mối quan hệ giữa RDS, VPC, Subnet Group và Security Group trong việc kiểm soát kết nối đến cơ sở dữ liệu. Đặc biệt, mình hiểu được tầm quan trọng của việc giới hạn nguồn truy cập thông qua Inbound Rules và chỉ cho phép những hệ thống hoặc máy khách cần thiết kết nối đến Database Port.

Bên cạnh đó, mình nghiên cứu các tính năng sao lưu và phục hồi dữ liệu của Amazon RDS, bao gồm Automated Backups, Manual Snapshots và Point-in-Time Recovery. Mình hiểu được sự khác biệt giữa các phương thức này cũng như vai trò của Backup Retention Period trong việc duy trì khả năng khôi phục dữ liệu khi xảy ra sự cố.

Cuối cùng, mình thực hành tìm hiểu quy trình khởi tạo và cấu hình một RDS DB Instance trên AWS. Các nội dung thực hành tập trung vào lựa chọn Database Engine, cấu hình tài nguyên, thiết lập kết nối và kiểm tra hoạt động của cơ sở dữ liệu. Ngoài ra, mình cũng tìm hiểu cách tạo Snapshot, chỉnh sửa cấu hình và quản lý vòng đời DB Instance để hạn chế việc sử dụng tài nguyên không cần thiết.

Sau tuần thứ năm, mình đã hiểu rõ hơn về cách triển khai và quản lý cơ sở dữ liệu quan hệ trên AWS. Những kiến thức về RDS, kết nối mạng, bảo mật và sao lưu dữ liệu là nền tảng quan trọng để xây dựng các hệ thống ứng dụng có khả năng lưu trữ và quản lý dữ liệu ổn định.
