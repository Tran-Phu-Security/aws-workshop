
---
title: "Worklog Tuần 7"
date: 2026-10-26
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Mục tiêu tuần 7:

- Hiểu khái niệm và vai trò của Infrastructure as Code (IaC).
- Tìm hiểu chức năng của AWS CloudFormation trong việc tự động hóa triển khai hạ tầng.
- Nắm được cấu trúc và các thành phần chính của CloudFormation Template.
- Làm quen với cú pháp YAML và JSON trong việc mô tả tài nguyên AWS.
- Tìm hiểu quy trình tạo, cập nhật và quản lý CloudFormation Stacks.
- Thực hành triển khai một số tài nguyên AWS bằng CloudFormation.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| Thứ Hai | - Tìm hiểu tổng quan về Infrastructure as Code (IaC).<br>- Nghiên cứu lợi ích của việc quản lý hạ tầng bằng mã nguồn thay vì cấu hình thủ công.<br>- Tìm hiểu vai trò của AWS CloudFormation trong việc tự động hóa triển khai tài nguyên AWS.<br>- So sánh cách triển khai tài nguyên thủ công với phương pháp sử dụng IaC. | 26/10/2026 | 26/10/2026 | [AWS CloudFormation Workshop](https://000037.awsfcaj.com/) |
| Thứ Ba | - Tìm hiểu cấu trúc cơ bản của CloudFormation Template.<br>- Nghiên cứu các thành phần Parameters, Resources, Outputs và Mappings.<br>- Tìm hiểu cú pháp YAML và JSON được sử dụng trong Template.<br>- Phân tích cách khai báo tài nguyên AWS trong phần Resources. | 27/10/2026 | 27/10/2026 | [AWS CloudFormation Workshop](https://000037.awsfcaj.com/) |
| Thứ Tư | - Tìm hiểu cách khai báo các tài nguyên AWS trong CloudFormation Template.<br>- Nghiên cứu cách tham chiếu giữa các tài nguyên thông qua Ref và Fn::GetAtt.<br>- Tìm hiểu cách sử dụng Parameters để truyền giá trị đầu vào.<br>- Nghiên cứu Outputs để hiển thị thông tin sau khi triển khai Stack. | 28/10/2026 | 28/10/2026 | [AWS CloudFormation Workshop](https://000037.awsfcaj.com/) |
| Thứ Năm | - Tìm hiểu quy trình tạo và quản lý CloudFormation Stacks.<br>- Nghiên cứu các trạng thái hoạt động của Stack.<br>- Tìm hiểu quy trình cập nhật hạ tầng thông qua Template mới.<br>- Nghiên cứu Change Sets để xem trước những thay đổi trước khi áp dụng. | 29/10/2026 | 29/10/2026 | [AWS CloudFormation Workshop](https://000037.awsfcaj.com/) |
| Thứ Sáu | - Thực hành xây dựng CloudFormation Template bằng YAML.<br>- Triển khai một số tài nguyên AWS thông qua CloudFormation.<br>- Thực hành tạo Stack, kiểm tra trạng thái và cập nhật cấu hình.<br>- Kiểm tra các tài nguyên được tạo và tìm hiểu cách xử lý lỗi triển khai.<br>- Tìm hiểu cách xóa Stack và quản lý tài nguyên sau khi hoàn thành thực hành. | 30/10/2026 | 30/10/2026 | [AWS CloudFormation Workshop](https://000037.awsfcaj.com/) |

### Kết quả đạt được tuần 7:

Trong tuần thứ bảy, mình tập trung tìm hiểu Infrastructure as Code (IaC) và AWS CloudFormation, một dịch vụ hỗ trợ tự động hóa quá trình khởi tạo và quản lý tài nguyên trên AWS thông qua các Template.

Trước tiên, mình nghiên cứu khái niệm Infrastructure as Code và những lợi ích của phương pháp này trong quản lý hạ tầng. Mình hiểu rằng thay vì phải cấu hình từng tài nguyên một cách thủ công trên giao diện quản lý, IaC cho phép mô tả hạ tầng bằng mã nguồn, giúp quá trình triển khai có tính nhất quán, dễ kiểm soát và thuận tiện hơn khi cần tái sử dụng cấu hình.

Tiếp theo, mình tìm hiểu cấu trúc của AWS CloudFormation Template và các thành phần quan trọng như Parameters, Resources, Outputs và Mappings. Mình hiểu được Resources là nơi khai báo các tài nguyên AWS cần triển khai, Parameters cho phép truyền các giá trị đầu vào, trong khi Outputs được sử dụng để hiển thị những thông tin cần thiết sau khi Stack được tạo thành công. Ngoài ra, mình cũng làm quen với cú pháp YAML và JSON trong việc xây dựng Template.

Một nội dung quan trọng khác là cách các tài nguyên trong Template tham chiếu lẫn nhau. Mình tìm hiểu các hàm nội tại như Ref và Fn::GetAtt để lấy giá trị hoặc thuộc tính của những tài nguyên đã được khai báo. Qua đó, mình hiểu rõ hơn cách CloudFormation xác định mối quan hệ và phối hợp triển khai các thành phần trong cùng một hệ thống.

Bên cạnh việc xây dựng Template, mình nghiên cứu quy trình tạo và quản lý CloudFormation Stacks. Mình tìm hiểu các trạng thái của Stack, cách cập nhật cấu hình thông qua Template mới và vai trò của Change Sets trong việc xem trước những thay đổi trước khi thực hiện cập nhật. Điều này giúp mình nhận thức được tầm quan trọng của việc kiểm tra tác động của thay đổi đối với hạ tầng hiện có.

Cuối cùng, mình thực hành xây dựng CloudFormation Template bằng YAML và tìm hiểu quy trình triển khai các tài nguyên AWS thông qua CloudFormation. Mình thực hiện việc tạo Stack, kiểm tra trạng thái triển khai, theo dõi các tài nguyên được khởi tạo và tìm hiểu cách xử lý những lỗi có thể phát sinh. Ngoài ra, mình cũng nghiên cứu cách cập nhật và xóa Stack sau khi hoàn thành quá trình thực hành.

Sau tuần thứ bảy, mình đã hiểu rõ hơn về phương pháp quản lý hạ tầng bằng mã nguồn và cách sử dụng AWS CloudFormation để tự động hóa quá trình triển khai tài nguyên. Đây là kiến thức quan trọng giúp cải thiện tính nhất quán, khả năng tái sử dụng và hiệu quả quản lý hạ tầng trong các hệ thống AWS.
