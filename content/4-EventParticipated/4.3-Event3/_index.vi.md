---
title : "Event 3"
date : 2026-11-28
weight : 3
chapter : false
pre : " <b> 4.3. </b> "
---

# Báo cáo tóm tắt: “Cloud Mastery”

#### Thông tin sự kiện

| Mục | Nội dung |
|---|---|
| Tên sự kiện | Cloud Mastery |
| Đơn vị tổ chức | Cộng đồng AWS Study Group Việt Nam (AWS First Cloud Journey AI) |
| Diễn giả | Khuong Huynh, Bao Nguyen, Phuong Huyen, Khang Duong |
| Thời gian | Sáng thứ Bảy, ngày 14/11/2026 |
| Địa điểm | Văn phòng AWS Việt Nam, Bitexco Financial Tower, TP. Hồ Chí Minh |
| Vai trò | Người tham gia |

Cloud Mastery là buổi chia sẻ của cộng đồng AWS Study Group Việt Nam về chủ đề đưa hệ thống Agentic AI từ bản thử nghiệm lên môi trường production. Bốn diễn giả lần lượt trình bày các tiêu chí kỹ thuật cốt lõi, những case study thực tế và kiến trúc triển khai trên AWS.

#### Nội dung chính tiếp thu

- **Từ prototype đến production.** Khi đưa hệ thống Agentic AI lên AWS, ba tiêu chí cần đáp ứng là bảo mật (Secure), độ tin cậy (Reliable) và khả năng mở rộng (Scalable).
- **Case study Cox Automotive.** Đây là bài toán ứng dụng Agentic AI ở quy mô doanh nghiệp lớn, với hơn 29.000 nhân sự, 2,3 tỷ lượt tương tác trực tuyến mỗi năm và 57 cơ hội ứng dụng AI. Trọng tâm là xây dựng các giải pháp tự động hóa mà người dùng có thể tin tưởng.
- **Case study Duolingo.** Vấn đề của họ là các đội nhóm phải dựng lại cùng một hạ tầng nhiều lần ("Every team rebuilds the same setup"). Cách giải quyết là chuẩn hóa thành một nền tảng dùng chung, gọi là Shared Agent Platform.
- **Mở rộng quy mô agent trong doanh nghiệp.** Khi vận hành hàng trăm agent, hàng nghìn tool, hàng trăm MCP server và hơn 100.000 tool policy, hạ tầng Agentic AI cần được quản trị một cách có hệ thống.
- **Bảo mật và định danh với Amazon Bedrock AgentCore Identity.** Khung kiến trúc gồm 5 bước chuẩn và hai lớp xác thực. Inbound Auth dành cho người dùng truy cập agent qua ứng dụng, dùng JWT hoặc IAM thông qua Authorizer. Outbound Auth dành cho agent truy cập tài nguyên bên ngoài. Hệ thống ghi nhận danh tính người gọi và cấp phát Workload Access Token (WAT).
- **Kiến trúc triển khai trên AWS.** Mô hình mẫu chạy Strands Agent trên Amazon ECS, với container image lấy từ Amazon ECR, đặt trong các Private Subnet của VPC, kết hợp AWS Secrets Manager và các dịch vụ phụ trợ khác.
- **Hỏi đáp và Quiz.** Phần hỏi đáp cho em thêm nhiều chia sẻ thực tế từ các diễn giả về định hướng học tập và nghề nghiệp Cloud, AI. Phần Quiz cuối buổi giúp củng cố lại kiến thức đã nghe.

#### Hình ảnh tại sự kiện

![Hình ảnh tham dự sự kiện 1](/aws-workshop/images/4-EventParticipated/4.3-Event3/photo-1.jpg)

![Hình ảnh tham dự sự kiện 2](/aws-workshop/images/4-EventParticipated/4.3-Event3/photo-2.jpg)

#### Bài học rút ra và đóng góp cá nhân

1. **Triển khai AI trong doanh nghiệp không dừng ở mô hình thử nghiệm.** Em hiểu rõ hơn rằng đưa AI vào thực tế cần tư duy kiến trúc toàn diện, bao gồm hạ tầng dùng chung, bảo mật định danh, quản trị tài nguyên và chính sách ở quy mô lớn, cùng thiết kế mạng an toàn trên AWS.
2. **Dùng chung hạ tầng thay vì mỗi nhóm tự dựng lại.** Câu chuyện của Duolingo cho em thấy việc chuẩn hóa thành một nền tảng chung giúp tiết kiệm công sức và giữ cho các nhóm làm việc nhất quán. Em sẽ cân nhắc cách này khi tổ chức các dự án có nhiều thành phần.
3. **Phân biệt rõ ai gọi agent và agent gọi vào đâu.** Mô hình xác thực hai chiều Inbound và Outbound giúp em nhìn lại cách phân quyền trong Trust Check, nơi khóa API được lưu trong Parameter Store và mỗi Lambda có role riêng với quyền tối thiểu. Em sẽ tiếp tục áp dụng nguyên tắc này khi làm việc với các hệ thống lớn hơn.
4. **Đặt thiết kế mạng và quản lý bí mật vào kiến trúc ngay từ đầu.** Mô hình chạy agent trong Private Subnet cùng Secrets Manager cho thấy bảo mật là một phần của kiến trúc, không phải bước thêm vào sau cùng.
5. **Định hình lộ trình kỹ năng cho bản thân.** Phần hỏi đáp trực tiếp giúp em rõ hơn về hướng phát triển kỹ năng Cloud và AI trong thời gian tới.

Về đóng góp cá nhân, em tham dự đầy đủ, ghi chép nghiêm túc và hệ thống lại kiến thức xuyên suốt chương trình. Em cũng theo dõi và tương tác trong phần hỏi đáp, đồng thời hưởng ứng sôi nổi phần Quiz cuối buổi, góp phần vào không khí học hỏi và kết nối chung của cộng đồng sinh viên AWS tại TP. Hồ Chí Minh.