---
title : "Event 1"
date : 2026-11-28
weight : 1
chapter : false
pre : " <b> 4.1. </b> "
---

# Báo cáo tóm tắt: “Navigating the Future of Cloud & AI in Vietnam”

#### Thông tin sự kiện

| Mục | Nội dung |
|---|---|
| Tên sự kiện | Navigating the Future of Cloud & AI in Vietnam |
| Diễn giả | Dr. Werner Vogels, Chief Technology Officer của Amazon |
| Thời gian | Ngày 02 tháng 10 năm 2026 |
| Địa điểm | [Bitexco Tower - TP. Hồ Chí Minh.] |
| Vai trò | Người tham dự |

Em tham dự buổi chia sẻ này để nghe Dr. Werner Vogels nói về tương lai của nghề kỹ sư phần mềm. Là sinh viên vừa học trên lớp vừa làm dự án riêng, em vẫn thường tự hỏi liệu AI có thay thế lập trình viên hay không, nên chủ đề này rất gần với em. Cả hội trường ghi chép liên tục, và mỗi lần diễn giả nhắc đến AI, không gian lại yên hơn hẳn.

#### Nội dung chính tiếp thu

- **Sự cố trong ngày mua sắm cao điểm.** Ông Vogels kể về thời kỳ đầu của Amazon: vào mùa mua sắm cuối năm, cơ sở dữ liệu quan hệ bị quá tải, hệ thống ngừng hoạt động suốt một ngày và công ty thiệt hại rất lớn. Điều em ấn tượng nhất là cách đội ngũ phản ứng. Nhà cung cấp phần mềm cho rằng Amazon đã dùng sản phẩm vượt xa giới hạn thiết kế, và ông thẳng thắn công nhận họ đúng. Các kỹ sư không vá tạm mà lùi lại để đo cách hệ thống thực sự được sử dụng. Kết quả cho thấy phần lớn truy vấn chỉ là tra cứu key-value, không cần đến một cơ sở dữ liệu quan hệ nặng nề. Từ phát hiện đó ra đời Dynamo, và về sau là DynamoDB. Em có dùng DynamoDB cho các bảng lưu job và bộ đệm kết quả trong dự án Trust Check, nên câu chuyện này giúp em hiểu rõ hơn vì sao dịch vụ ấy được tạo ra.
- **Kỷ luật vận hành.** Theo ông Vogels, nhìn vào số trung bình là cách dễ tự đánh lừa mình, vì trung bình che đi trải nghiệm tệ của những người dùng ở hai đầu cực. Ông nhấn mạnh tư duy "mọi thứ đều có lúc hỏng". Amazon chủ động diễn tập bằng cách cố ý tắt một phần hạ tầng đang chạy thật để kiểm tra hệ thống chịu lỗi tốt đến đâu.
- **“Renaissance Developer”.** Đây là phần em thích nhất. Lập trình viên sẽ không bị đào thải nếu phát triển quanh năm phẩm chất: sự tò mò, tư duy hệ thống, tinh thần làm chủ, kiến thức rộng đi cùng chuyên môn sâu, và kỹ năng giao tiếp. Ông ví AI như một trình biên dịch ngôn ngữ tự nhiên: rất nhanh nhưng có thể sai, vì vậy kết quả của nó luôn cần con người kiểm tra lại.

#### Hình ảnh tại sự kiện

![Hình ảnh tham dự sự kiện 1](/aws-workshop/images/4-EventParticipated/4.1-Event1/photo-1.jpg)

![Hình ảnh tham dự sự kiện 2](/aws-workshop/images/4-EventParticipated/4.1-Event1/photo-2.jpg)

#### Bài học rút ra và đóng góp cá nhân

1. **AI viết mã, nhưng trách nhiệm thuộc về em.** Trước đây em hay lấy mã do AI sinh ra, chạy thử nhanh, thấy chạy được là đưa thẳng vào dự án. Sau buổi này, em tự đặt quy tắc: không commit dòng mã nào mà em không giải thích được. Trước khi nhận mã từ AI, em sẽ xem xét các trường hợp biên, lỗ hổng bảo mật và điểm nghẽn hiệu năng. Phần nào chưa hiểu thì em không thể nhận là sản phẩm của mình.
2. **Nhìn toàn bộ hệ thống thay vì từng chức năng riêng lẻ.** Bài tập ở trường thường chỉ xoay quanh từng module. Em sẽ vẽ lại luồng dữ liệu của Trust Check từ giao diện, qua API Gateway, SQS và Lambda, đến DynamoDB, rồi tự hỏi: nếu một thành phần ngừng hoạt động thì điều gì xảy ra. Các sự cố lớn hầu như đều bắt nguồn từ những giả định chưa được kiểm chứng.
3. **Hiểu khối lượng công việc thực tế trước khi chọn công nghệ.** Em hay chọn công cụ chỉ vì nó phổ biến hoặc đang được nhắc nhiều. Câu chuyện của Amazon nhắc em phải bắt đầu từ cách hệ thống thực sự được dùng. Từ nay, trước khi chọn cơ sở dữ liệu hay framework, em sẽ liệt kê các kiểu truy vấn và xử lý dữ liệu mà ứng dụng thực sự cần. Bảng bộ đệm của Trust Check tra theo mã băm của nội dung, đúng kiểu truy vấn key-value mà ông Vogels nhắc tới.
4. **Coi trọng kỹ năng giao tiếp.** Trước đây em xem giao tiếp là việc phụ. Giờ em thấy nó giúp kỹ sư tìm ra vấn đề cốt lõi đằng sau những yêu cầu mơ hồ và giải thích các đánh đổi kỹ thuật cho người không chuyên. Em sẽ tập viết tài liệu ngắn gọn cho phần việc của mình và thuyết trình nhiều hơn trong các dự án nhóm.
5. **Cảnh giác với câu “xưa nay vẫn làm như vậy”.** Ông Vogels kết thúc bằng lời cảnh báo nổi tiếng của Grace Hopper về câu nói nguy hiểm nhất trong ngôn ngữ. Với em, đó là lời nhắc định kỳ xem lại và làm mới cách mình học và viết mã.

Rời hội trường, em thấy nhẹ nhõm nhiều hơn là lo lắng. Điều em mang về không phải một lời trấn an suông rằng "đừng lo về AI", mà là cái nhìn rõ hơn về những gì AI chưa thay thế được: khả năng phán đoán, tư duy hệ thống, trách nhiệm và lòng tự trọng nghề nghiệp. Đó cũng là những kỹ năng em cần rèn ngay từ bây giờ. Việc đầu tiên trong tuần này của em là vẽ lại sơ đồ kiến trúc của dự án hiện tại và rà soát toàn bộ phần mã trước đây em từng để AI viết hộ.