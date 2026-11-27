---
title: "Blog 3"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.3. </b> "
---


# KHI AI TRỞ THÀNH “ĐIỀU TRA VIÊN” AN NINH MẠNG: TÌM HIỂU AMAZON GUARDDUTY INVESTIGATION AGENT
Trước đây mình nghĩ rằng khi một hệ thống bảo mật phát hiện hoạt động đáng ngờ và gửi cảnh báo, công việc tiếp theo chỉ đơn giản là kiểm tra xem cảnh báo đó có nguy hiểm hay không.

Nhưng thực tế, một cảnh báo chỉ là điểm khởi đầu. Security Analyst có thể phải dành hàng giờ để kiểm tra log, đối chiếu các sự kiện, xác định tài nguyên bị ảnh hưởng và tìm hiểu liệu có một cuộc tấn công thực sự đang diễn ra hay không.

Khi đọc bài viết trên AWS Security Blog về Amazon GuardDuty Investigation Agent, mình thấy một cách tiếp cận khá thú vị: thay vì để con người tự phân tích từng cảnh báo, AWS sử dụng AI Agent để hỗ trợ quá trình điều tra, tổng hợp bằng chứng và đưa ra đánh giá rủi ro.

Mình tóm tắt lại cơ chế hoạt động của tính năng này, hy vọng giúp ích cho các bạn đang tìm hiểu về Cloud Security và ứng dụng AI trong an ninh mạng.
<figure style="text-align: center;">
  <img src="/aws-workshop/images/3-BlogsPosted/3.3-Blog3/guardduty-agent-HD.png" alt="Amazon GuardDuty Investigation Agent" style="width: 90%; max-width: 1100px; height: auto;">
  <figcaption>Amazon GuardDuty Investigation Agent</figcaption>
</figure>1. TRƯỚC HẾT, VÀI KHÁI NIỆM
- Amazon GuardDuty: Dịch vụ phát hiện mối đe dọa của AWS, liên tục giám sát các tài khoản và workload để phát hiện hoạt động đáng ngờ hoặc có khả năng độc hại.
- Security Finding: Cảnh báo bảo mật được tạo ra khi hệ thống phát hiện dấu hiệu bất thường. Một finding không đồng nghĩa với việc chắc chắn đã xảy ra tấn công.
- AI Agent: Hệ thống AI có khả năng thực hiện một chuỗi tác vụ để đạt được mục tiêu. Trong trường hợp này, Agent được sử dụng để thu thập, liên kết và phân tích thông tin phục vụ điều tra.
- Threat Investigation: Quá trình điều tra mối đe dọa, nhằm xác định chuyện gì đã xảy ra, mức độ nghiêm trọng và những tài nguyên có thể bị ảnh hưởng.
- MITRE ATT&CK: Framework mô tả các chiến thuật và kỹ thuật mà kẻ tấn công thường sử dụng. Giúp Security Analyst hiểu hành vi đáng ngờ có thể thuộc giai đoạn nào của một cuộc tấn công.
2. VẤN ĐỀ: PHÁT HIỆN ĐƯỢC CẢNH BÁO CHƯA CÓ NGHĨA LÀ ĐÃ HIỂU ĐƯỢC CUỘC TẤN CÔNG
Giả sử GuardDuty phát hiện một IAM credential đang được sử dụng từ một vị trí bất thường.

Điều này có thể cho thấy thông tin xác thực đã bị đánh cắp. Nhưng cũng có thể đơn giản là nhân viên đang sử dụng VPN hoặc truy cập từ một địa điểm mới.

Để xác định nguyên nhân, Security Analyst cần trả lời hàng loạt câu hỏi:
- Credential này trước đó thường được sử dụng ở đâu?
- Có API call bất thường nào xuất hiện không?
- Tài khoản có thực hiện những hành động nhạy cảm như thay đổi quyền IAM hay truy cập dữ liệu quan trọng không?
- Có các cảnh báo khác liên quan đến cùng tài khoản hoặc tài nguyên hay không?
Vấn đề nằm ở chỗ: GuardDuty có thể phát hiện những dấu hiệu đáng ngờ, nhưng việc xác định bối cảnh và đánh giá mức độ rủi ro vẫn cần một quá trình điều tra.

Khi số lượng cảnh báo tăng lên, việc phân tích thủ công trở nên tốn thời gian, đặc biệt với những tổ chức quản lý nhiều AWS account.
3. GIẢI PHÁP: AMAZON GUARDDUTY INVESTIGATION AGENT
Ngày 20/07/2026, AWS giới thiệu Amazon GuardDuty Investigation Agent, một tính năng sử dụng AI để hỗ trợ điều tra các phát hiện bảo mật trong môi trường AWS.

Điểm khác biệt nằm ở việc nó không chỉ hiển thị cảnh báo, mà còn hỗ trợ phân tích và liên kết những bằng chứng liên quan để tạo ra báo cáo đánh giá có cấu trúc.

Nói đơn giản:
- GuardDuty: Phát hiện điều gì đó đáng ngờ đang xảy ra.
- Investigation Agent: Hỗ trợ tìm hiểu những dấu hiệu đó có liên quan với nhau không, nghiêm trọng đến mức nào và nên xử lý ra sao.
Theo AWS, tính năng này có thể giúp rút ngắn thời gian điều tra từ hàng giờ xuống còn vài phút.

Đặc biệt, Investigation Agent được tích hợp trực tiếp trong GuardDuty. Người dùng có thể khởi tạo điều tra thông qua AWS Management Console, AWS CLI, API hoặc SDK mà không cần tự xây dựng và vận hành một AI Agent riêng.
4. AI AGENT ĐIỀU TRA CẢNH BÁO NHƯ THẾ NÀO?
Theo tài liệu AWS, quy trình có thể hiểu qua 4 bước chính.
4.1. Xác định phạm vi điều tra
Người dùng có thể yêu cầu AI Agent điều tra theo:
- Một GuardDuty Finding ID cụ thể.
- Một AWS account.
- Toàn bộ các tài khoản trong AWS Organization mà họ được phép truy cập.
Khi sử dụng AWS CLI hoặc API, người dùng còn có thể cung cấp yêu cầu bằng ngôn ngữ tự nhiên thông qua trigger-prompt, tối đa 2.048 ký tự.

Ví dụ:

Investigate the recent unauthorized access finding.
4.2. Thu thập và liên kết bằng chứng
Sau khi nhận yêu cầu, Agent phân tích các GuardDuty findings, đối chiếu hoạt động có liên quan và xem xét bối cảnh bảo mật của những tài nguyên trong phạm vi điều tra.

Thay vì xem từng cảnh báo độc lập, nó tìm kiếm mối liên hệ giữa các dấu hiệu để xây dựng nhận định tổng thể.
4.3. Đánh giá mối đe dọa
Sau khi phân tích, Agent trả về các thông tin quan trọng:
- Risk Level: Mức độ rủi ro, gồm Info, Low, Medium, High và Critical.
- Confidence: Mức độ tin cậy của đánh giá, gồm Unknown, Low, Medium và High.
- Summary: Tóm tắt những phát hiện và quan sát quan trọng.
- MITRE ATT&CK Mapping: Liên kết hành vi đáng ngờ với các kỹ thuật tấn công đã được biết đến.
- Resource Mapping: Xác định những tài nguyên AWS có liên quan.
4.4. Đề xuất hướng xử lý
Không dừng lại ở việc đánh giá, Agent còn đưa ra các hành động khắc phục được sắp xếp theo mức độ ưu tiên, có thể bao gồm những câu lệnh AWS CLI cụ thể.

Điều này giúp Security Analyst biết nên kiểm tra hoặc xử lý vấn đề nào trước.
5. ĐIỂM ĐÁNG CHÚ Ý: RISK LEVEL KHÔNG PHẢI CONFIDENCE
Đây là phần mình thấy khá quan trọng khi đọc tài liệu.

Ban đầu, mình nghĩ nếu AI đánh giá một cảnh báo có mức độ nguy hiểm HIGH thì có nghĩa là nó đã chắc chắn xác nhận được cuộc tấn công.

Nhưng thực ra Risk Level và Confidence là hai khái niệm khác nhau:
- Risk Level: Đánh giá mức độ nghiêm trọng của mối đe dọa.
- Confidence: Thể hiện mức độ tin cậy của kết luận dựa trên những bằng chứng được phân tích.
Ví dụ minh họa:
- Risk: Critical | Confidence: High
  → Dấu hiệu cho thấy một mối đe dọa rất nghiêm trọng và AI có mức độ tin cậy cao vào đánh giá này.
- Risk: High | Confidence: Low
  → Mối đe dọa có thể gây tác động lớn, nhưng bằng chứng hiện tại chưa đủ mạnh để đưa ra kết luận với độ tin cậy cao.
Điều mình rút ra là: một cảnh báo có mức độ rủi ro cao vẫn cần được xem xét cùng chất lượng bằng chứng, thay vì chỉ nhìn vào nhãn High hay Critical.
6. VÍ DỤ THỰC TẾ: ĐIỀU GÌ XẢY RA KHI MỘT MÁY CHỦ BỊ XÂM NHẬP?
Trong bài viết, AWS đưa ra một ví dụ kết quả điều tra liên quan đến môi trường Amazon EKS (Elastic Kubernetes Service).

Báo cáo mẫu thể hiện:
- Một worker node có dấu hiệu bị xâm nhập trong lúc hoạt động.
- Xuất hiện reverse shell với đặc quyền root.
- Có hành vi truy cập Docker socket và thực thi file độc hại.
- Ghi nhận khoảng 500 tín hiệu runtime liên quan đến nhiều chiến thuật tấn công.
Kết quả Agent đánh giá:
- Risk Level: Critical
- Confidence: High
Đây là một ví dụ cho thấy giá trị của việc phân tích nhiều dấu hiệu cùng lúc.

Nếu chỉ nhìn vào một cảnh báo, người quản trị có thể chưa hình dung đầy đủ mức độ nghiêm trọng. Nhưng khi kết hợp các dấu hiệu liên quan, Agent có thể cung cấp một bức tranh tổng thể hơn về cuộc xâm nhập.

Lưu ý: Đây là kết quả mẫu được AWS trình bày trong tài liệu, không phải một cuộc tấn công mình trực tiếp thực hiện hoặc xác minh.
7. 3 BƯỚC ĐỂ TRẢI NGHIỆM INVESTIGATION AGENT
7.1. Bật Amazon GuardDuty
Truy cập AWS Management Console và đảm bảo GuardDuty đã được kích hoạt tại Region được hỗ trợ.

Sau đó vào mục Investigations → Settings → Enable để bật tính năng điều tra.
7.2. Khởi tạo cuộc điều tra
Tại mục Investigations, chọn Initiate Investigation.

Người dùng có thể nhập Finding ID, chọn AWS account hoặc phạm vi toàn tổ chức tùy theo quyền được cấp.
7.3. Xem kết quả đánh giá
Sau khi quá trình điều tra hoàn tất, mở báo cáo để xem:
- Tổng quan các phát hiện.
- Các kỹ thuật tấn công được ánh xạ theo MITRE ATT&CK.
- Mức độ rủi ro và độ tin cậy.
- Những tài nguyên bị ảnh hưởng.
- Các hành động khắc phục được đề xuất.
Theo bài AWS công bố trong giai đoạn Public Preview, điều tra ở phạm vi tài khoản thường mất khoảng 2–5 phút, còn điều tra một finding cụ thể khoảng 10–12 phút.

Đây là thời gian tham khảo trong giai đoạn preview, không phải cam kết cố định cho mọi cuộc điều tra.
8. ĐIỂM HAY: CÓ THỂ TÍCH HỢP VÀO SECURITY WORKFLOW
Investigation Agent không nhất thiết phải được sử dụng thủ công trên AWS Console.

Nhờ các API, doanh nghiệp có thể tích hợp khả năng điều tra vào quy trình giám sát bảo mật sẵn có.

Một kiến trúc minh họa AWS đề cập là:

GuardDuty → EventBridge → AWS Lambda → Investigation Agent → SIEM

Trong đó:
1. GuardDuty tạo security finding.
2. EventBridge chuyển sự kiện vào workflow.
3. Lambda gọi API để khởi tạo cuộc điều tra.
4. Investigation Agent phân tích và trả về kết quả.
5. Kết quả được chuyển đến SIEM (hệ thống quản lý thông tin và sự kiện bảo mật) để Security Analyst theo dõi và xử lý.
Ví dụ, những cảnh báo được đánh giá Critical có thể được chuyển đến hàng đợi xử lý sự cố ưu tiên cao.

Điểm đáng chú ý là AI Agent không thay thế toàn bộ hệ thống giám sát bảo mật, mà đóng vai trò bổ sung khả năng phân tích và hỗ trợ ưu tiên xử lý cảnh báo.
9. NHỮNG LƯU Ý KHI ÁP DỤNG
9.1. AI không đồng nghĩa với kết luận chính xác tuyệt đối
Dù Agent có thể đưa ra đánh giá với Confidence cao, kết quả vẫn nên được Security Analyst kiểm tra, đặc biệt trước các quyết định nhạy cảm như khóa tài khoản hoặc cô lập tài nguyên.
9.2. Phải phân quyền IAM phù hợp
Để sử dụng tính năng, AWS yêu cầu các quyền như:
- guardduty:CreateInvestigation
- guardduty:GetInvestigation
- guardduty:ListInvestigations
Không phải tài khoản nào cũng được phép khởi tạo điều tra cho toàn bộ tổ chức. AWS có mô hình phân quyền riêng giữa administrator account và member account.
9.3. Chú ý đến phạm vi Region và xử lý dữ liệu
Investigation Agent sử dụng cơ chế Cross-Region Inference để xử lý đánh giá trong phạm vi địa lý AWS quy định.

Dữ liệu được lưu tại Region khởi tạo yêu cầu, nhưng dữ liệu điều tra và kết quả tóm tắt có thể được xử lý tại Region khác trong cùng geography.

Vì vậy, với những tổ chức có yêu cầu nghiêm ngặt về vị trí xử lý dữ liệu, đây là yếu tố cần kiểm tra trước khi triển khai.
9.4. Kiểm tra tình trạng hỗ trợ và chi phí
Tại thời điểm bài blog được công bố (20/07/2026), tính năng đang ở giai đoạn Public Preview tại 10 AWS Regions, được sử dụng miễn phí với giới hạn 10 cuộc điều tra mỗi tài khoản mỗi ngày và tối đa 100 cuộc điều tra mỗi tài khoản trong suốt giai đoạn preview.

Những giới hạn này có thể thay đổi, nên cần kiểm tra tài liệu AWS mới nhất trước khi thực hành.
10. ĐIỀU MÌNH HỌC ĐƯỢC
- Phát hiện mối đe dọa chỉ là bước đầu tiên. Một cảnh báo có giá trị hơn khi chúng ta hiểu được nguyên nhân, những sự kiện liên quan và mức độ ảnh hưởng của nó.
- AI có thể hỗ trợ giảm tải công việc điều tra lặp lại. Thay vì dành phần lớn thời gian đối chiếu cảnh báo và tổng hợp thông tin, Security Analyst có thể tập trung xác minh kết quả và quyết định phương án xử lý.
- Đừng nhầm lẫn giữa mức độ nghiêm trọng và độ tin cậy. Risk cao không có nghĩa là kết luận chắc chắn đúng. Cả hai yếu tố đều quan trọng khi đánh giá sự cố.
- Tự động hóa không đồng nghĩa với loại bỏ con người. AI Agent là một công cụ hỗ trợ quá trình điều tra, nhưng những quyết định bảo mật quan trọng vẫn cần sự giám sát và đánh giá phù hợp.
Là một người đang học về Cloud Security, mình thấy Amazon GuardDuty Investigation Agent cho thấy một hướng ứng dụng AI rất thực tế trong an ninh mạng: không chỉ phát hiện điều bất thường, mà còn hỗ trợ con người hiểu điều gì đang thực sự xảy ra và nên ưu tiên xử lý như thế nào.
11. NGUỒN THAM KHẢO
- AWS Security Blog – Introducing the Amazon GuardDuty investigation agent: on-demand AI-powered threat assessment
- Amazon GuardDuty User Guide