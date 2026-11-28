
---
title: "Worklog Tuần 2"
date: 2026-09-21
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Mục tiêu tuần 2:

- Hiểu được vai trò và chức năng của AWS Identity and Access Management (IAM).
- Tìm hiểu cách quản lý IAM Users, Groups, Roles và Policies.
- Nắm được nguyên tắc cấp quyền tối thiểu (Principle of Least Privilege).
- Tìm hiểu các phương thức bảo mật tài khoản AWS, bao gồm Multi-Factor Authentication (MFA) và Access Keys.
- Thực hành tạo người dùng, nhóm và phân quyền truy cập trên AWS IAM.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| Thứ Hai | - Tìm hiểu tổng quan về AWS Identity and Access Management (IAM).<br>- Nghiên cứu vai trò của IAM trong việc quản lý danh tính và kiểm soát quyền truy cập vào các tài nguyên AWS.<br>- Phân biệt các khái niệm IAM User, Group, Role và Policy. | 21/09/2026 | 21/09/2026 | [AWS IAM Workshop](https://000002.awsfcaj.com/) |
| Thứ Ba | - Tìm hiểu cách tạo và quản lý IAM Users.<br>- Thực hành tạo IAM User và thiết lập thông tin đăng nhập.<br>- Tìm hiểu cách tổ chức người dùng thành các IAM Groups.<br>- Thực hành thêm người dùng vào nhóm và kiểm tra quyền được kế thừa từ nhóm. | 22/09/2026 | 22/09/2026 | [AWS IAM Workshop](https://000002.awsfcaj.com/) |
| Thứ Tư | - Tìm hiểu IAM Policies và cấu trúc của một policy dạng JSON.<br>- Phân tích các thành phần Effect, Action, Resource và Condition.<br>- Thực hành gán policy cho IAM User và IAM Group.<br>- Tìm hiểu cách sử dụng AWS managed policies và customer managed policies. | 23/09/2026 | 23/09/2026 | [AWS IAM Workshop](https://000002.awsfcaj.com/) |
| Thứ Năm | - Tìm hiểu IAM Roles và sự khác biệt giữa IAM Roles với IAM Users.<br>- Nghiên cứu cách sử dụng IAM Role để cấp quyền truy cập giữa các dịch vụ AWS.<br>- Tìm hiểu nguyên tắc cấp quyền tối thiểu (Principle of Least Privilege). | 24/09/2026 | 24/09/2026 | [AWS IAM Workshop](https://000002.awsfcaj.com/) |
| Thứ Sáu | - Tìm hiểu Multi-Factor Authentication (MFA) và vai trò của MFA trong bảo vệ tài khoản AWS.<br>- Nghiên cứu cách quản lý Access Keys và các rủi ro khi sử dụng thông tin xác thực lâu dài.<br>- Tìm hiểu các khuyến nghị bảo mật IAM và thực hành kiểm tra lại quyền truy cập đã cấu hình. | 25/09/2026 | 25/09/2026 | [AWS IAM Workshop](https://000002.awsfcaj.com/)<br>[AWS MFA Documentation](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_mfa.html) |

### Kết quả đạt được tuần 2:

Trong tuần thứ hai, mình tập trung tìm hiểu dịch vụ AWS Identity and Access Management (IAM), một thành phần quan trọng trong việc quản lý danh tính và kiểm soát quyền truy cập vào các tài nguyên trên AWS.

Trước tiên, mình đã tìm hiểu chức năng của IAM và cách dịch vụ này hỗ trợ quản lý người dùng, nhóm, vai trò cũng như quyền truy cập. Qua đó, mình hiểu rõ hơn sự khác biệt giữa IAM User, IAM Group, IAM Role và IAM Policy. Mỗi thành phần có chức năng riêng nhưng đều phối hợp với nhau để xây dựng cơ chế quản lý truy cập cho hệ thống AWS.

Tiếp theo, mình tìm hiểu cách tạo và quản lý IAM Users, đồng thời thực hành tổ chức người dùng thành các IAM Groups. Việc quản lý quyền thông qua nhóm giúp đơn giản hóa quá trình phân quyền và hạn chế việc phải thiết lập chính sách riêng lẻ cho từng người dùng. Mình cũng tìm hiểu cách sử dụng IAM Policies để xác định những hành động mà một danh tính có thể thực hiện trên tài nguyên AWS.

Đối với IAM Policies, mình đã nghiên cứu cấu trúc JSON và các thành phần quan trọng như Effect, Action, Resource và Condition. Qua đó, mình hiểu được cách một policy cho phép hoặc từ chối những hành động cụ thể trên các tài nguyên nhất định. Ngoài ra, mình cũng tìm hiểu sự khác biệt giữa AWS managed policies và customer managed policies.

Một nội dung quan trọng khác trong tuần là IAM Roles và nguyên tắc Principle of Least Privilege. Mình hiểu rằng việc cấp quyền cần được giới hạn trong phạm vi thực sự cần thiết, tránh cấp quyền quá rộng gây ra những rủi ro về bảo mật. Bên cạnh đó, mình tìm hiểu cách IAM Role hỗ trợ các dịch vụ AWS truy cập tài nguyên mà không cần sử dụng thông tin xác thực cố định của người dùng.

Cuối cùng, mình tìm hiểu các biện pháp bảo vệ tài khoản AWS thông qua Multi-Factor Authentication (MFA) và quản lý Access Keys. Mình nhận thức được tầm quan trọng của việc bảo vệ thông tin xác thực, hạn chế sử dụng Access Keys không cần thiết và thường xuyên kiểm tra quyền truy cập đã cấp.

Sau tuần thứ hai, mình đã có cái nhìn rõ ràng hơn về cơ chế quản lý danh tính và phân quyền trên AWS. Những kiến thức về IAM, Policies, Roles, MFA và Principle of Least Privilege là nền tảng quan trọng để triển khai và bảo vệ các tài nguyên đám mây trong những nội dung tiếp theo.
