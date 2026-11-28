
---
title: "Worklog Tuần 3"
date: 2026-09-28
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Mục tiêu tuần 3:

- Hiểu kiến trúc và chức năng cơ bản của Amazon Virtual Private Cloud (VPC).
- Tìm hiểu các thành phần mạng quan trọng như CIDR, Subnet, Route Table và Internet Gateway.
- Phân biệt Public Subnet và Private Subnet.
- Tìm hiểu cơ chế kiểm soát lưu lượng mạng thông qua Security Group và Network ACL (NACL).
- Thực hành xây dựng và cấu hình một mạng VPC cơ bản trên AWS.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| Thứ Hai | - Tìm hiểu tổng quan về Amazon Virtual Private Cloud (VPC).<br>- Nghiên cứu vai trò của VPC trong việc xây dựng môi trường mạng riêng trên AWS.<br>- Tìm hiểu khái niệm CIDR và cách xác định dải địa chỉ IP cho VPC. | 28/09/2026 | 28/09/2026 | [Amazon VPC Workshop](https://000003.awsfcaj.com/) |
| Thứ Ba | - Tìm hiểu khái niệm Subnet và cách phân chia mạng VPC thành các mạng con.<br>- Phân biệt Public Subnet và Private Subnet.<br>- Tìm hiểu cách lựa chọn Availability Zone khi tạo Subnet. | 29/09/2026 | 29/09/2026 | [Amazon VPC Workshop](https://000003.awsfcaj.com/) |
| Thứ Tư | - Tìm hiểu chức năng của Route Table trong việc định tuyến lưu lượng mạng.<br>- Nghiên cứu Internet Gateway (IGW) và cách kết nối VPC với Internet.<br>- Tìm hiểu mối quan hệ giữa Subnet, Route Table và Internet Gateway. | 30/09/2026 | 30/09/2026 | [Amazon VPC Workshop](https://000003.awsfcaj.com/) |
| Thứ Năm | - Tìm hiểu cơ chế kiểm soát truy cập mạng của Security Group và Network ACL (NACL).<br>- Phân biệt Security Group và NACL về phạm vi áp dụng, trạng thái kết nối và quy tắc xử lý lưu lượng.<br>- Tìm hiểu cách cấu hình Inbound Rules và Outbound Rules. | 01/10/2026 | 01/10/2026 | [Amazon VPC Workshop](https://000003.awsfcaj.com/) |
| Thứ Sáu | - Thực hành tạo một VPC trên AWS.<br>- Tạo Public Subnet và Private Subnet trong các Availability Zone phù hợp.<br>- Cấu hình Route Table và Internet Gateway.<br>- Thiết lập Security Group và Network ACL.<br>- Kiểm tra cấu hình mạng và khả năng kết nối giữa các thành phần trong VPC. | 02/10/2026 | 02/10/2026 | [Amazon VPC Workshop](https://000003.awsfcaj.com/) |

### Kết quả đạt được tuần 3:

Trong tuần thứ ba, mình tập trung tìm hiểu Amazon Virtual Private Cloud (VPC), dịch vụ cho phép xây dựng môi trường mạng ảo riêng biệt trên AWS. Qua quá trình học tập, mình hiểu được vai trò của VPC trong việc tổ chức tài nguyên, phân chia mạng và kiểm soát khả năng kết nối giữa các thành phần trong hệ thống đám mây.

Trước tiên, mình tìm hiểu cách xác định dải địa chỉ IP cho VPC thông qua ký hiệu CIDR (Classless Inter-Domain Routing). Mình hiểu được ý nghĩa của địa chỉ mạng và subnet mask, cũng như cách lựa chọn dải địa chỉ phù hợp để tránh xung đột IP khi thiết kế hệ thống mạng.

Tiếp theo, mình nghiên cứu khái niệm Subnet và cách chia một VPC thành nhiều mạng con. Đặc biệt, mình phân biệt được Public Subnet và Private Subnet dựa trên cấu hình định tuyến và khả năng truy cập Internet. Mình cũng tìm hiểu vai trò của Availability Zone trong việc phân bố tài nguyên và hỗ trợ tăng tính sẵn sàng cho hệ thống.

Một nội dung quan trọng khác là cơ chế định tuyến lưu lượng mạng thông qua Route Table và Internet Gateway (IGW). Mình hiểu được Route Table có nhiệm vụ xác định đường đi của lưu lượng mạng, trong khi Internet Gateway cho phép các tài nguyên trong VPC giao tiếp với Internet khi được cấu hình phù hợp. Qua đó, mình nhận thức rõ hơn mối liên hệ giữa VPC, Subnet, Route Table và Internet Gateway trong quá trình xây dựng kiến trúc mạng.

Bên cạnh đó, mình tìm hiểu hai cơ chế kiểm soát lưu lượng mạng quan trọng là Security Group và Network ACL (NACL). Mình phân biệt được Security Group hoạt động ở cấp độ tài nguyên và có trạng thái kết nối (stateful), trong khi Network ACL hoạt động ở cấp độ Subnet và không lưu trạng thái kết nối (stateless). Những kiến thức này giúp mình hiểu rõ hơn cách thiết lập các quy tắc Inbound và Outbound để kiểm soát lưu lượng truy cập.

Cuối cùng, mình thực hành xây dựng một môi trường VPC cơ bản trên AWS, bao gồm tạo VPC, phân chia Subnet, cấu hình Route Table, Internet Gateway, Security Group và Network ACL. Thông qua quá trình thực hành, mình có cơ hội liên kết các kiến thức lý thuyết với cách triển khai thực tế và hiểu rõ hơn cách các thành phần mạng phối hợp với nhau.

Sau tuần thứ ba, mình đã nắm được những kiến thức nền tảng về thiết kế và quản lý mạng trên AWS. Đây là cơ sở quan trọng để tiếp tục triển khai các hệ thống có cấu trúc mạng rõ ràng, an toàn và dễ mở rộng trong những tuần tiếp theo.
