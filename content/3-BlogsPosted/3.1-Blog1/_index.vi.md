---
title: "Blog 1"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# AWS CONTINUUM: ĐƯA PENTEST VÀO TỪNG LẦN DEPLOY (CI/CD)

## 1. Giới thiệu

Trong quy trình phát triển phần mềm hiện đại, ứng dụng thường xuyên được cập nhật. Những tính năng mới, thay đổi cấu hình hoặc chỉnh sửa mã nguồn đều có thể tạo ra điểm yếu bảo mật ở bất kỳ giai đoạn nào.

Theo phương pháp truyền thống, Penetration Testing (Pentest) thường được thực hiện định kỳ, chẳng hạn trước một đợt phát hành lớn hoặc trong các đợt đánh giá bảo mật theo lịch. Mặc dù có giá trị, cách tiếp cận này có thể khiến những thay đổi phát sinh giữa hai chu kỳ kiểm thử chưa được đánh giá bảo mật kịp thời.

Trong khi đó, các công cụ tự động như SAST, DAST và SCA giúp phát hiện những dấu hiệu có khả năng liên quan đến lỗ hổng. Tuy nhiên, kết quả từ các công cụ này vẫn có thể cần được kiểm tra thêm để xác định vấn đề có thực sự khai thác được trong môi trường ứng dụng hay không.

**AWS Continuum for Penetration Testing** cung cấp một cách tiếp cận nhằm tích hợp Pentest vào CI/CD pipeline. Hệ thống có thể tự động kiểm thử ứng dụng sau khi triển khai lên môi trường tiền production và sử dụng kết quả làm cổng kiểm soát bảo mật trước khi đưa phiên bản mới lên production.

<figure>
  <img src="/aws-workshop/images/3-BlogsPosted/3.1-Blog1/aws-continuum.jpg" alt="AWS Continuum for Penetration Testing" style="display:block;width:90%;max-width:1100px;height:auto;margin:0 auto;" />
  <figcaption style="text-align:center;">AWS Continuum for Penetration Testing được tích hợp vào quy trình CI/CD.</figcaption>
</figure>

## 2. Các khái niệm cần hiểu

Trước khi tìm hiểu cách tích hợp, cần nắm một số khái niệm liên quan đến bảo mật và DevOps.

### 2.1. SAST, DAST và SCA

- **SAST (Static Application Security Testing):** Phân tích mã nguồn hoặc mã đã biên dịch mà không cần chạy ứng dụng nhằm tìm kiếm các điểm yếu bảo mật tiềm ẩn.
- **DAST (Dynamic Application Security Testing):** Kiểm thử ứng dụng đang hoạt động bằng cách gửi các request và quan sát phản hồi.
- **SCA (Software Composition Analysis):** Xác định các thư viện và dependency bên thứ ba, sau đó kiểm tra lỗ hổng đã được công bố hoặc các vấn đề liên quan đến giấy phép sử dụng.

Những công cụ này cung cấp tín hiệu hữu ích cho quá trình đánh giá bảo mật. Tuy nhiên, kết quả phát hiện không phải lúc nào cũng chứng minh được rằng điểm yếu có thể bị khai thác trong môi trường ứng dụng thực tế.

### 2.2. Penetration Testing

Penetration Testing là hoạt động đánh giá bảo mật ứng dụng thông qua việc mô phỏng các hành vi tấn công trong phạm vi đã được cho phép.

Mục tiêu là xác định liệu những điểm yếu có thể bị khai thác hay không và đánh giá tác động tiềm tàng của chúng.

Khác với các công cụ chủ yếu phát hiện dấu hiệu bất thường, Pentest có thể kiểm chứng khả năng khai thác thực tế của vấn đề bảo mật.

### 2.3. CI/CD Pipeline

CI/CD là viết tắt của Continuous Integration và Continuous Delivery hoặc Deployment. Đây là quy trình tự động hóa các công đoạn như build, test và deploy những thay đổi của ứng dụng.

Một security gate có thể được tích hợp vào pipeline để yêu cầu bản triển khai đáp ứng các điều kiện bảo mật đã xác định trước khi chuyển sang giai đoạn tiếp theo.

### 2.4. OpenID Connect (OIDC)

OIDC cho phép CI/CD provider nhận temporary credentials của AWS thông qua cơ chế liên kết danh tính (identity federation).

Thay vì lưu trữ AWS Access Keys dài hạn trong các biến của pipeline, workflow sử dụng OIDC token và trao đổi token đó thông qua AWS Security Token Service (AWS STS) để đảm nhận một IAM role.

## 3. Hạn chế của phương pháp kiểm thử bảo mật truyền thống

Các công cụ quét tự động và hoạt động Pentest có vai trò khác nhau nhưng bổ trợ cho nhau.

Công cụ tự động giúp xác định những khu vực cần chú ý, trong khi Pentest giúp kiểm chứng liệu các điểm yếu liên quan có thể khai thác được hay không.

Khi Pentest chỉ được thực hiện theo lịch định kỳ, những thay đổi được đưa vào sau đợt đánh giá có thể phải chờ đến chu kỳ tiếp theo mới được kiểm thử. Ngược lại, nếu thực hiện một bài Pentest toàn diện sau mỗi lần deploy, thời gian và chi phí vận hành có thể tăng lên đáng kể.

Vì vậy, thách thức đặt ra là làm thế nào để kiểm thử bảo mật liên tục mà không phải kiểm tra toàn bộ ứng dụng sau mỗi thay đổi nhỏ.

## 4. AWS Continuum for Penetration Testing hoạt động như thế nào?

AWS Continuum for Penetration Testing giải quyết vấn đề trên bằng cách tích hợp hoạt động Pentest có phạm vi xác định vào deployment pipeline.

Quy trình tổng quát gồm các bước:

1. Developer commit những thay đổi của ứng dụng.
2. CI/CD pipeline thực hiện build và test.
3. Ứng dụng được deploy lên môi trường staging hoặc pre-production.
4. Pipeline xác thực với AWS thông qua OIDC.
5. Integration khởi chạy bài Pentest đối với ứng dụng và phạm vi đã cấu hình.
6. AWS Continuum đánh giá kết quả kiểm thử và trả trạng thái về pipeline.
7. Pipeline quyết định cho phép hoặc chặn việc promote lên production theo các quy tắc đã thiết lập.

Job Pentest được thực thi phía AWS. CI/CD runner đóng vai trò điều phối quá trình tích hợp và tiếp nhận kết quả, thay vì trực tiếp chạy logic kiểm thử xâm nhập.

### 4.1. Security Gate

Security gate quyết định liệu một bản triển khai có được phép tiếp tục hay không.

Ví dụ, nếu severity threshold được cấu hình là `HIGH`, các finding có mức độ `HIGH` hoặc `CRITICAL` sẽ chặn quá trình promote. Những finding thấp hơn ngưỡng được ghi nhận nhưng không làm pipeline thất bại.

Hành vi này có thể cấu hình tùy theo yêu cầu bảo mật của từng đội ngũ.

### 4.2. Hiểu đúng kết quả của Pipeline

Pipeline thất bại không đồng nghĩa chắc chắn ứng dụng đã được xác nhận có lỗ hổng khai thác được. Cần xem xét kết quả kiểm thử và quyết định về phạm vi (scope decision).

Một số trường hợp quan trọng gồm:

- **Finding đạt hoặc vượt severity threshold:** Security gate thất bại và chặn promote.
- **Scoped out:** Thay đổi nằm trong phạm vi cấu hình nhưng không làm thay đổi attack surface liên quan cần kiểm thử mới. Pipeline được phép tiếp tục.
- **Scope conflict:** Thay đổi nằm ngoài phạm vi Pentest đã cấu hình. Pipeline xử lý theo chính sách dành cho trường hợp xung đột phạm vi.
- **Infrastructure error:** Bài kiểm thử không thể khởi chạy hoặc hoàn tất. Việc này có chặn pipeline hay không phụ thuộc vào cấu hình fail-on-infrastructure-error.

## 5. Cơ chế Scoping theo thay đổi

Một tính năng quan trọng của integration là khả năng giới hạn phạm vi kiểm thử theo những thay đổi của ứng dụng.

Thay vì kiểm thử toàn bộ ứng dụng sau mỗi lần deploy, AWS Continuum xác định khoảng commit giữa:

- **Head commit:** Commit đang được đánh giá.
- **Base commit:** Commit trước đó mà security gate đã xác nhận đạt điều kiện.

Integration sử dụng khoảng commit này để xác định những thay đổi cần được kiểm thử bảo mật.

Có ba quyết định về phạm vi cần phân biệt:

| Quyết định | Ý nghĩa |
|---|---|
| In scope | Thay đổi tác động đến attack surface được kiểm thử và được đánh giá bằng Pentest. |
| Scoped out | Thay đổi không tác động đến attack surface liên quan nên pipeline có thể tiếp tục mà không cần kiểm thử mới. |
| Scope conflict | Thay đổi nằm ngoài phạm vi Pentest đã cấu hình và chưa được kiểm thử. |

Cách tiếp cận này giúp giảm những lần kiểm thử không cần thiết, đồng thời duy trì bản ghi rõ ràng về các thay đổi đã được đánh giá.

## 6. Tích hợp AWS Continuum vào CI/CD

Quá trình tích hợp thường gồm ba bước chuẩn bị chính.

### 6.1. Cấu hình bài Penetration Testing

Trước tiên, cần cấu hình ứng dụng mục tiêu và phạm vi kiểm thử trong AWS Continuum.

Domain mục tiêu phải được xác minh. Bài Pentest cần được thiết lập với target, credentials và network scope phù hợp.

Sau đó, bật CI/CD mode cho bài kiểm thử và lưu lại Agent Space ID cùng penetration test ID.

Pipeline sử dụng cấu hình đã tồn tại này khi khởi chạy kiểm thử. Nó không tự động tạo target hoặc bài Pentest mới.

### 6.2. Cấu hình IAM và xác thực OIDC

Tiếp theo, cần cấu hình IAM OIDC identity provider và một IAM role để CI/CD workflow có thể đảm nhận.

Role cần hai chính sách quan trọng:

- **Trust policy:** Xác định những danh tính CI/CD nào được phép đảm nhận role.
- **Permissions policy:** Xác định workflow được phép thực hiện những hành động nào sau khi đảm nhận role.

Trust policy nên được giới hạn cho repository, branch hoặc deployment environment dự kiến trong phạm vi mà CI/CD provider hỗ trợ.

Việc sử dụng OIDC và temporary credentials giúp tránh phải lưu trữ AWS Access Keys dài hạn trong các biến CI/CD.

### 6.3. Thêm Security Gate vào Pipeline

Cuối cùng, thêm bước AWS Continuum penetration testing sau khi ứng dụng được deploy lên staging.

Job triển khai production cần phụ thuộc vào kết quả hoàn thành thành công của security gate.

Ví dụ, trong GitHub Actions, job triển khai production có thể khai báo job kiểm thử bảo mật là dependency thông qua thuộc tính `needs`.

Nhờ đó, production deployment sẽ bị bỏ qua khi security gate thất bại.

## 7. Triển khai theo từng giai đoạn: Report-only và Block

Việc áp dụng security gate với chế độ chặn ngay lập tức có thể ảnh hưởng đến quy trình phát triển hiện tại. Triển khai từng giai đoạn giúp đội ngũ hiểu rõ kết quả trước khi thực thi chính sách blocking.

### 7.1. Report-only Mode

Ở chế độ Report-only, bài Pentest vẫn được thực hiện và các finding vẫn được thu thập, công bố. Tuy nhiên, severity threshold được đặt thành `NONE` để finding không chặn pipeline.

Cách này giúp đội ngũ quan sát kết quả và hiểu cách integration hoạt động trước khi áp dụng ngưỡng blocking.

### 7.2. Block Mode

Sau khi xem xét kết quả, đội ngũ có thể cấu hình một ngưỡng như `HIGH`.

Khi đó, finding đạt hoặc vượt ngưỡng đã chọn sẽ khiến security gate thất bại và ngăn việc promote lên production.

Tuy nhiên, cần xem xét độc lập ba cấu hình:

- `severity-threshold`: Quyết định mức độ finding nào sẽ chặn pipeline.
- `on-scope-conflict`: Quyết định cách xử lý khi thay đổi nằm ngoài phạm vi kiểm thử.
- `fail-on-infrastructure-error`: Quyết định lỗi hạ tầng có làm pipeline thất bại hay không.

### 7.3. Lưu ý quan trọng về Baseline

Một lần chạy Report-only thành công vẫn có thể cập nhật commit baseline. Điều này có nghĩa là khi chuyển threshold sang `HIGH`, những commit đã được ghi nhận là cleared trước đó sẽ không tự động được kiểm thử lại.

Nếu cần đánh giá lại một khoảng commit cũ, có thể chỉ định rõ base và head commit SHA theo hướng dẫn của integration.

Đội ngũ cần hiểu hành vi này trước khi chuyển từ Report-only sang Block.

## 8. Những lưu ý bảo mật khi triển khai

Một số yếu tố cần được quan tâm khi tích hợp:

- **Triển khai có kiểm soát:** Quan sát kết quả Report-only trước khi áp dụng ngưỡng blocking.
- **Sử dụng OIDC:** Ưu tiên temporary credentials thay vì Access Keys dài hạn.
- **Áp dụng least privilege:** Giới hạn trust policy và permissions policy của IAM role.
- **Xem xét scope conflict:** Thay đổi chưa được kiểm thử không nên được xem tương đương với một bài Pentest thành công.
- **Xử lý lỗi hạ tầng có chủ đích:** Nếu production cần security sign-off, hãy cấu hình gate thất bại khi bài kiểm thử không thể chạy.
- **Bảo vệ cấu hình pipeline:** Giới hạn quyền chỉnh sửa workflow và các thiết lập security gate.
- **Cố định phiên bản integration:** Sử dụng release tag hoặc commit SHA bất biến khi được hỗ trợ để thay đổi từ upstream không âm thầm làm thay đổi integration.

## 9. Kết luận

AWS Continuum for Penetration Testing cung cấp một phương pháp thực tế để tích hợp hoạt động Pentest vào quy trình CI/CD hiện đại.

Thông qua việc kiểm thử các thay đổi sau triển khai, giới hạn phạm vi theo commit và sử dụng security gate có thể cấu hình, đội ngũ phát triển có thể đưa hoạt động xác thực bảo mật trở thành một phần của quy trình phát hành thay vì chỉ phụ thuộc vào những đợt đánh giá định kỳ.

Integration này không thay thế toàn bộ hoạt động bảo mật. Thay vào đó, nó bổ trợ cho automated scanning, secure development practices và các đợt đánh giá bảo mật tổng thể bằng cách bổ sung một giai đoạn xác thực trước khi triển khai production.

## Tài liệu tham khảo

1. [AWS Security Blog – Penetration Testing Every Deployment with AWS Continuum](https://aws.amazon.com/blogs/security/penetration-testing-every-deployment-with-aws-continuum/)
2. [AWS Documentation – Run Penetration Tests from Your CI/CD Pipeline](https://docs.aws.amazon.com/securityagent/latest/userguide/cicd-pentest.html)
3. [AWS Documentation – GitHub Actions Integration Tutorial](https://docs.aws.amazon.com/securityagent/latest/userguide/sample-cicd-github.html)
