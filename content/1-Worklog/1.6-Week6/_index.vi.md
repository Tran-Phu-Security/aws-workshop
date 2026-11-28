
---
title: "Worklog Tuần 6"
date: 2026-10-19
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Mục tiêu tuần 6:

- Hiểu được vai trò và chức năng của Elastic Load Balancing (ELB).
- Tìm hiểu các loại Load Balancer, đặc biệt là Application Load Balancer (ALB).
- Nắm được cách hoạt động của Target Groups và Health Checks.
- Hiểu cơ chế tự động mở rộng và thu hẹp tài nguyên thông qua EC2 Auto Scaling.
- Tìm hiểu Launch Templates và Scaling Policies.
- Thực hành triển khai hệ thống EC2 kết hợp Load Balancer và Auto Scaling.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| Thứ Hai | - Tìm hiểu tổng quan về Elastic Load Balancing (ELB).<br>- Nghiên cứu vai trò của Load Balancer trong việc phân phối lưu lượng truy cập đến nhiều máy chủ.<br>- Tìm hiểu các loại Load Balancer trên AWS và đặc điểm của từng loại.<br>- Phân biệt Application Load Balancer (ALB), Network Load Balancer (NLB) và Gateway Load Balancer (GWLB). | 19/10/2026 | 19/10/2026 | [AWS Auto Scaling & Load Balancing Workshop](https://000006.awsfcaj.com/) |
| Thứ Ba | - Tìm hiểu kiến trúc và cách hoạt động của Application Load Balancer (ALB).<br>- Nghiên cứu Listener, Listener Rules và Target Groups.<br>- Tìm hiểu cách ALB phân phối HTTP/HTTPS traffic đến các EC2 Instances.<br>- Thực hành cấu hình Target Group và đăng ký các EC2 Instances làm Targets. | 20/10/2026 | 20/10/2026 | [AWS Auto Scaling & Load Balancing Workshop](https://000006.awsfcaj.com/) |
| Thứ Tư | - Tìm hiểu cơ chế Health Checks của Target Groups.<br>- Nghiên cứu các trạng thái hoạt động của Targets.<br>- Tìm hiểu cách Load Balancer phát hiện và xử lý các máy chủ không hoạt động bình thường.<br>- Thực hành kiểm tra trạng thái Health Check của các EC2 Instances. | 21/10/2026 | 21/10/2026 | [AWS Auto Scaling & Load Balancing Workshop](https://000006.awsfcaj.com/) |
| Thứ Năm | - Tìm hiểu tổng quan về EC2 Auto Scaling.<br>- Nghiên cứu các thành phần như Auto Scaling Group, Minimum Capacity, Desired Capacity và Maximum Capacity.<br>- Tìm hiểu Launch Templates và vai trò của chúng trong việc khởi tạo EC2 Instances.<br>- Phân tích mối quan hệ giữa Auto Scaling Group và Load Balancer. | 22/10/2026 | 22/10/2026 | [AWS Auto Scaling & Load Balancing Workshop](https://000006.awsfcaj.com/) |
| Thứ Sáu | - Tìm hiểu các loại Scaling Policies và điều kiện kích hoạt mở rộng hoặc thu hẹp tài nguyên.<br>- Nghiên cứu Target Tracking Scaling và các chỉ số giám sát liên quan.<br>- Thực hành cấu hình Auto Scaling Group kết hợp với Application Load Balancer.<br>- Kiểm tra khả năng tự động mở rộng, thu hẹp và phân phối lưu lượng truy cập của hệ thống. | 23/10/2026 | 23/10/2026 | [AWS Auto Scaling & Load Balancing Workshop](https://000006.awsfcaj.com/) |

### Kết quả đạt được tuần 6:

Trong tuần thứ sáu, mình tập trung tìm hiểu Elastic Load Balancing (ELB) và EC2 Auto Scaling, hai thành phần quan trọng trong việc xây dựng hệ thống có khả năng phân phối lưu lượng truy cập và tự động điều chỉnh tài nguyên trên AWS.

Trước tiên, mình tìm hiểu vai trò của Elastic Load Balancing trong việc phân phối lưu lượng truy cập đến nhiều máy chủ hoặc EC2 Instances. Mình nghiên cứu các loại Load Balancer được AWS cung cấp, bao gồm Application Load Balancer (ALB), Network Load Balancer (NLB) và Gateway Load Balancer (GWLB). Qua đó, mình hiểu được sự khác biệt về mục đích sử dụng và phạm vi hoạt động của từng loại.

Tiếp theo, mình tập trung tìm hiểu Application Load Balancer (ALB), đặc biệt là cách ALB tiếp nhận và phân phối lưu lượng HTTP/HTTPS đến các máy chủ đích. Mình nghiên cứu các thành phần Listener, Listener Rules và Target Groups, đồng thời hiểu được cách đăng ký EC2 Instances vào Target Group để Load Balancer có thể phân phối yêu cầu đến các máy chủ phù hợp.

Một nội dung quan trọng khác là Health Checks. Mình tìm hiểu cách Target Group kiểm tra tình trạng hoạt động của các Targets thông qua những yêu cầu kiểm tra được cấu hình trước. Qua đó, mình hiểu được cách Load Balancer xác định máy chủ đang hoạt động bình thường hoặc gặp sự cố, từ đó hạn chế việc chuyển tiếp lưu lượng đến những máy chủ không đáp ứng yêu cầu.

Bên cạnh Load Balancing, mình nghiên cứu EC2 Auto Scaling và các thành phần như Auto Scaling Group, Minimum Capacity, Desired Capacity và Maximum Capacity. Mình hiểu được cách Auto Scaling Group quản lý số lượng EC2 Instances dựa trên cấu hình và nhu cầu sử dụng tài nguyên. Ngoài ra, mình tìm hiểu Launch Templates, thành phần lưu trữ các thông tin cấu hình cần thiết để khởi tạo những EC2 Instances mới.

Cuối cùng, mình tìm hiểu Scaling Policies, đặc biệt là Target Tracking Scaling, để hiểu cách hệ thống tự động điều chỉnh số lượng máy chủ dựa trên các chỉ số giám sát. Thông qua nội dung thực hành, mình tìm hiểu cách kết hợp Auto Scaling Group với Application Load Balancer nhằm phân phối lưu lượng truy cập và duy trì khả năng phục vụ của hệ thống khi nhu cầu thay đổi.

Sau tuần thứ sáu, mình đã hiểu rõ hơn về cách xây dựng hệ thống có khả năng cân bằng tải và tự động mở rộng trên AWS. Những kiến thức về Load Balancer, Target Groups, Health Checks và Auto Scaling là nền tảng quan trọng để triển khai các ứng dụng có khả năng mở rộng và duy trì tính sẵn sàng cao.
