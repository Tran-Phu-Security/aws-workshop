---
title: "Bản đề xuất"
date: 2026-09-14
weight: 2
chapter: false
pre: "<b> 2. </b>"
---

# AWS Phishing Detection Engine (Trust Check)

> Bản đề xuất trình bày mục tiêu, kiến trúc, phương pháp kỹ thuật, kế hoạch triển khai và kết quả kỳ vọng của dự án AWS Phishing Detection Engine (Trust Check).

## 1. Tổng quan dự án

**AWS Phishing Detection Engine (Trust Check)** là một nền tảng an ninh mạng trên nền tảng đám mây, được thiết kế nhằm hỗ trợ người dùng nhận diện các mối đe dọa lừa đảo trực tuyến (phishing) thông qua quá trình phân tích tự động URL, nội dung email và các tệp được tải lên.

Dự án giải quyết khó khăn trong việc nhận diện các hình thức phishing ngày càng tinh vi, đặc biệt đối với người dùng có kiến thức hạn chế về an ninh mạng. Thay vì chỉ đưa ra kết quả nhị phân an toàn hoặc không an toàn, Trust Check kết hợp nhiều kỹ thuật phát hiện và các nguồn tình báo mối đe dọa bên ngoài để cung cấp **Trust Score từ 0 đến 100**, kèm theo giải thích về kết quả đánh giá.

Nền tảng sử dụng kiến trúc serverless hướng sự kiện (event-driven serverless architecture) được xây dựng trên Amazon Web Services (AWS). Các dịch vụ cốt lõi bao gồm Amazon API Gateway, AWS Lambda, Amazon Simple Queue Service (SQS), Amazon DynamoDB và Amazon Simple Storage Service (S3).

Bằng cách tách biệt quá trình tiếp nhận yêu cầu khỏi các tác vụ phân tích bất đồng bộ, hệ thống hỗ trợ xử lý các loại tác vụ quét khác nhau, cải thiện hiệu quả sử dụng tài nguyên và giảm các hoạt động xử lý không cần thiết thông qua cơ chế caching.

## 2. Vấn đề và mục tiêu dự án

### 2.1. Vấn đề đặt ra

Phishing vẫn là một mối đe dọa an ninh mạng đáng kể. Kẻ tấn công sử dụng các website giả mạo, email lừa đảo, URL gây nhầm lẫn và tệp đính kèm độc hại nhằm đánh cắp thông tin nhạy cảm, chiếm quyền truy cập tài khoản và phát tán mã độc.

Việc nhận diện những mối đe dọa này ngày càng khó khăn do các kỹ thuật phishing liên tục phát triển. Những phương pháp phát hiện truyền thống chủ yếu dựa vào cơ sở dữ liệu uy tín hoặc các quy tắc được xác định trước có thể không nhận diện được các mối đe dọa mới xuất hiện.

Bên cạnh đó, các công cụ bảo mật không phải lúc nào cũng giải thích rõ lý do một nội dung bị đánh giá là đáng ngờ. Điều này gây khó khăn cho người dùng không có kiến thức chuyên môn trong việc hiểu kết quả phát hiện và đưa ra quyết định phù hợp.

Những thách thức trên cho thấy sự cần thiết của một nền tảng phát hiện phishing có khả năng tự động phân tích nhiều loại nội dung, kết hợp các kỹ thuật đánh giá khác nhau và cung cấp kết quả bảo mật dễ hiểu.

### 2.2. Mục tiêu dự án

Mục tiêu chính của dự án là xây dựng một nền tảng trên nền tảng đám mây nhằm hỗ trợ người dùng nhận diện các mối đe dọa phishing tiềm ẩn.

Các mục tiêu cụ thể bao gồm:

- **Phát hiện đa định dạng:** Hỗ trợ phân tích URL đáng ngờ, nội dung email và các tệp được tải lên thông qua những mô-đun phát hiện chuyên biệt.
- **Phương pháp phát hiện kết hợp:** Kết hợp các quy tắc heuristic, phân tích nội dung và metadata cùng các dịch vụ tình báo mối đe dọa bên ngoài.
- **Đánh giá rủi ro có giải thích:** Tạo Trust Score từ 0 đến 100, đi kèm phân loại mức độ an toàn và giải thích các dấu hiệu được phát hiện.
- **Kiến trúc đám mây Serverless:** Sử dụng các dịch vụ AWS được quản lý nhằm hỗ trợ thực thi hướng sự kiện, xử lý bất đồng bộ và giảm yêu cầu quản lý hạ tầng.
- **Tối ưu hóa sử dụng tài nguyên:** Áp dụng cơ chế caching nhằm hạn chế quét lặp lại và giảm các yêu cầu không cần thiết đến API bên ngoài.
- **Giao diện thân thiện với người dùng:** Cung cấp nền tảng web cho phép gửi nội dung cần kiểm tra, theo dõi tiến trình quét và xem kết quả phân tích.

## 3. Giải pháp và kiến trúc đề xuất

### 3.1. Tổng quan giải pháp

Trust Check cung cấp ba chức năng quét chính:

1. **Phát hiện URL:** Kiểm tra các đặc điểm của URL và những dấu hiệu liên quan đến tên miền, đồng thời sử dụng thông tin hỗ trợ từ các dịch vụ đánh giá uy tín bên ngoài như VirusTotal, Google Safe Browsing và URLhaus.
2. **Phát hiện Email:** Phân tích nội dung email, các liên kết được nhúng và những đặc điểm liên quan của thông điệp nhằm nhận diện dấu hiệu phishing.
3. **Phát hiện File:** Thực hiện phân tích tĩnh các tệp được tải lên, bao gồm đặc điểm tệp và các dấu hiệu mã độc, với MalwareBazaar là một nguồn tình báo bổ sung.

Sau quá trình phân tích, Scoring Engine tập trung đánh giá các dấu hiệu thu thập được và tạo ra Trust Score.

| Trust Score | Phân loại | Ý nghĩa |
|---|---|---|
| 80–100 | SAFE | Không phát hiện dấu hiệu đe dọa đáng kể bằng các phương pháp phân tích hiện có. |
| 50–79 | SUSPICIOUS | Phát hiện các rủi ro bảo mật tiềm ẩn; khuyến nghị kiểm tra thêm. |
| 0–49 | DANGEROUS | Phát hiện các dấu hiệu đe dọa đáng kể; người dùng nên tránh tương tác với nội dung đó. |

Điểm số được cung cấp kèm giải thích về những dấu hiệu đã phát hiện. Đây là kết quả đánh giá dựa trên các phương pháp phân tích hiện có, không phải sự bảo đảm tuyệt đối rằng nội dung đó an toàn.

### 3.2. Kiến trúc hệ thống

Hệ thống áp dụng kiến trúc serverless hướng sự kiện với ba lớp xử lý chính:

- **Lớp quản lý yêu cầu (Request Management Layer):** Tiếp nhận và xác thực dữ liệu người dùng gửi thông qua Amazon API Gateway và AWS Lambda.
- **Lớp phát hiện mối đe dọa (Threat Detection Layer):** Phân phối tác vụ quét thông qua Amazon SQS đến các worker chuyên biệt xử lý URL/email và file.
- **Lớp quản lý dữ liệu (Data Management Layer):** Sử dụng Amazon DynamoDB để lưu trữ thông tin tác vụ quét, kết quả phát hiện và dữ liệu cache đủ điều kiện. Amazon S3 hỗ trợ lưu trữ các tệp được tải lên và những dữ liệu phân tích liên quan.

Kiến trúc tách biệt quá trình xử lý URL và email khỏi quá trình xử lý file. Điều này cho phép hệ thống quản lý độc lập các loại tác vụ có yêu cầu xử lý khác nhau.

<a href="/aws-workshop/images/2-Proposal/trust_check_architecture.png" target="_blank">
  <img src="/aws-workshop/images/2-Proposal/trust_check_architecture.png"
       alt="Kiến trúc hệ thống Trust Check"
       style="width: 90vw; max-width: 1200px; height: auto; display: block; margin-left: 50%; transform: translateX(-50%); cursor: zoom-in;">
</a>
*Hình 2.1. Kiến trúc tổng quan của AWS Phishing Detection Engine.*

### 3.3. Quy trình hoạt động của hệ thống

Hệ thống áp dụng quy trình quét bất đồng bộ, bao gồm các giai đoạn sau:

1. **Gửi yêu cầu:** Người dùng gửi URL, nội dung email hoặc file thông qua giao diện web.
2. **Xác thực yêu cầu:** Amazon API Gateway chuyển tiếp yêu cầu đến AWS Lambda phù hợp để xác thực dữ liệu và khởi tạo tác vụ quét.
3. **Phân phối tác vụ:** Amazon SQS phân phối tác vụ quét đến hàng đợi xử lý tương ứng.
4. **Phát hiện mối đe dọa:** Các worker chuyên biệt phân tích nội dung được gửi lên và truy vấn những nguồn tình báo mối đe dọa phù hợp khi có sẵn.
5. **Tính toán Trust Score:** Scoring Engine đánh giá các dấu hiệu thu thập được và xác định điểm số cùng mức phân loại bảo mật.
6. **Lưu trữ kết quả:** Kết quả hoàn chỉnh được lưu trong Amazon DynamoDB. Những kết quả đủ điều kiện có thể được lưu vào cache để tái sử dụng.
7. **Truy xuất kết quả:** Frontend truy xuất trạng thái tác vụ và hiển thị kết quả phân tích hoàn chỉnh cho người dùng.

Đối với quá trình phân tích file, Amazon S3 hỗ trợ quy trình tải tệp lên thông qua presigned URL có thời hạn. Cơ chế này cho phép tải tệp lên một cách có kiểm soát mà không cần tiết lộ thông tin xác thực AWS cho phía client.

## 4. Triển khai kỹ thuật

### 4.1. Công nghệ phát triển

Dự án sử dụng các công nghệ và dịch vụ sau:

| Hạng mục | Công nghệ |
|---|---|
| Frontend | HTML, CSS, JavaScript |
| Backend | Python 3.12, AWS Lambda |
| Quản lý API | Amazon API Gateway |
| Xử lý bất đồng bộ | Amazon SQS |
| Cơ sở dữ liệu và Caching | Amazon DynamoDB |
| Lưu trữ tệp | Amazon S3 |
| Tình báo mối đe dọa | VirusTotal, Google Safe Browsing, URLhaus, MalwareBazaar |
| Tích hợp AWS | Boto3 |
| Kiểm thử | Python unittest |
| Công cụ phát triển | Visual Studio Code, AWS Management Console |
| Quản lý mã nguồn | Git và GitHub |

### 4.2. Phát hiện và đánh giá rủi ro

Detection Engine sử dụng những phương pháp phân tích khác nhau tùy theo loại nội dung được gửi lên:

- Phân tích URL tập trung kiểm tra các đặc điểm đáng ngờ của URL và những dấu hiệu liên quan đến tên miền.
- Phân tích Email tập trung vào nội dung thông điệp, các liên kết được nhúng và những đặc điểm có khả năng mang tính lừa đảo.
- Phân tích File đánh giá các thuộc tính liên quan của tệp và những dấu hiệu tĩnh có thể chỉ ra hoạt động độc hại.

Các dấu hiệu thu thập được sẽ được chuyển đến Scoring Engine để tính toán Trust Score và cung cấp giải thích đi kèm.

Các dịch vụ tình báo mối đe dọa bên ngoài được sử dụng tùy theo loại nội dung và khả năng tích hợp hiện có. Tính khả dụng, giới hạn phản hồi và điều kiện sử dụng của các dịch vụ này có thể ảnh hưởng đến mức độ đầy đủ của quá trình phân tích.

### 4.3. Xử lý bất đồng bộ và quản lý dữ liệu

Amazon SQS tách biệt quá trình gửi yêu cầu khỏi quá trình phát hiện thực tế. Điều này giúp các tác vụ quét có thời gian xử lý dài không làm chậm phản hồi API ban đầu một cách không cần thiết.

Amazon DynamoDB quản lý thông tin tác vụ quét và những kết quả đủ điều kiện được lưu cache. Trước khi bắt đầu một lượt quét mới, ứng dụng có thể kiểm tra xem đã tồn tại kết quả trước đó còn hợp lệ hay chưa.

Cơ chế caching giúp giảm việc phân tích lặp lại, các lần thực thi Lambda không cần thiết và số lượng yêu cầu đến các dịch vụ tình báo mối đe dọa bên ngoài. Thời hạn hết hiệu lực của dữ liệu cache cần được kiểm tra trước khi tái sử dụng kết quả đã lưu.

Amazon S3 hỗ trợ lưu trữ các tệp được tải lên và những dữ liệu phân tích liên quan.

## 5. Lộ trình triển khai và các mốc thời gian

Dự án được thực hiện trong khuôn khổ chương trình **First Cloud AI Journey (FCAJ) – Workforce Bootcamp 2026**, với thời gian tham gia từ **14/09/2026 đến 14/12/2026**.

Lộ trình dưới đây phân bổ các nhóm công việc dự kiến trong khoảng thời gian ba tháng:

| Thời gian | Hoạt động chính |
|---|---|
| 14/09 – 13/10/2026 | Xác định yêu cầu dự án, làm rõ mục tiêu phát hiện, thiết kế kiến trúc hệ thống và chuẩn bị hạ tầng AWS. |
| 14/10 – 13/11/2026 | Phát triển và tích hợp các mô-đun phát hiện URL, email và file; cấu hình xử lý bất đồng bộ, lưu trữ dữ liệu và tích hợp các nguồn tình báo mối đe dọa. |
| 14/11 – 14/12/2026 | Hoàn thiện tích hợp frontend và backend, thực hiện kiểm thử chức năng, xem xét kết quả phát hiện, ghi nhận các giới hạn và đánh giá chi phí cũng như khả năng tối ưu hóa AWS. |

Lịch trình này đóng vai trò là khung phân bổ công việc cho dự án. Tiến độ hoàn thành và kết quả đánh giá thực tế cần được ghi nhận dựa trên bằng chứng triển khai tương ứng.

## 6. Ước tính ngân sách

Dự án sử dụng các dịch vụ AWS được quản lý với mô hình tính phí dựa trên mức độ sử dụng. Chi phí hạ tầng phụ thuộc vào cấu hình dịch vụ, mức tiêu thụ tài nguyên, khu vực triển khai và khối lượng yêu cầu quét thực tế.

Báo cáo dự án đưa ra mức ước tính sơ bộ khoảng **7,68 USD mỗi tháng** đối với khối lượng giả định **10.000 yêu cầu quét** tại khu vực **Asia Pacific (Singapore)**.

Mức ước tính này dựa trên các cấu hình dịch vụ giả định và chưa tính các khoản ưu đãi Free Tier hoặc tín dụng khuyến mại nếu đủ điều kiện. Do đó, đây không phải mức phí hàng tháng được bảo đảm.

Các yếu tố chi phí chính bao gồm:

- Số lượt gọi AWS Lambda, cấu hình bộ nhớ và thời gian thực thi.
- Số lượng yêu cầu Amazon API Gateway.
- Quá trình xử lý thông điệp Amazon SQS.
- Dung lượng và năng lực xử lý của Amazon DynamoDB.
- Dung lượng lưu trữ và các yêu cầu liên quan đến Amazon S3.
- Hoạt động ghi log và giám sát thông qua Amazon CloudWatch.
- Chi phí sử dụng các dịch vụ tình báo mối đe dọa bên ngoài, nếu có.

Chi phí thực tế cần được đánh giá thông qua AWS Pricing Calculator và AWS Billing and Cost Management. AWS Cost Explorer có thể hỗ trợ phân tích xu hướng sử dụng dịch vụ và chi tiêu.

## 7. Đánh giá rủi ro

Dự án có một số rủi ro kỹ thuật và vận hành cần được xem xét trong quá trình triển khai và đánh giá.

| Rủi ro | Ảnh hưởng tiềm ẩn | Biện pháp giảm thiểu |
|---|---|---|
| Tình báo mối đe dọa không đầy đủ | Kết quả phát hiện có thể thiếu những dấu hiệu liên quan. | Kết hợp nhiều kỹ thuật phát hiện và thông báo rõ các giới hạn của quá trình phân tích. |
| Dịch vụ API bên ngoài không khả dụng | Dịch vụ bên thứ ba có thể ngừng hoạt động hoặc giới hạn số lượng yêu cầu. | Xử lý lỗi tích hợp và không xem việc thiếu dữ liệu tình báo là kết quả an toàn. |
| False Positive và False Negative | Nội dung có thể bị phân loại sai. | Đánh giá các dấu hiệu phát hiện, kiểm thử nhiều tình huống và giải thích cơ sở của từng điểm số. |
| Tác vụ trùng lặp hoặc thất bại | Tác vụ quét có thể được xử lý nhiều lần hoặc thất bại trong quá trình thực thi. | Áp dụng cấu hình retry, visibility timeout và Dead-Letter Queue phù hợp khi được thiết lập. |
| Tiêu thụ tài nguyên quá mức | Khối lượng yêu cầu lớn hoặc việc quét lặp lại có thể làm tăng chi phí AWS. | Sử dụng caching, phân tách workload, giám sát tài nguyên và cấu hình phù hợp. |
| Truy cập file trái phép | Các tệp được tải lên có thể bị lộ nếu quyền truy cập không được kiểm soát đúng cách. | Áp dụng quyền truy cập S3 phù hợp và sử dụng presigned URL tạm thời cho các lượt tải lên được cấp quyền. |
| Kết quả quét không hoàn chỉnh | Tác vụ thất bại hoặc bị gián đoạn có thể tạo ra đánh giá không đáng tin cậy. | Theo dõi trạng thái tác vụ và phân biệt kết quả thất bại hoặc chưa hoàn thành với kết quả đánh giá bảo mật hoàn chỉnh. |

Trust Score cần được xem là một công cụ hỗ trợ đánh giá bảo mật, không phải sự bảo đảm tuyệt đối về mức độ an toàn. Người dùng vẫn cần thận trọng khi tương tác với nội dung đáng ngờ.

## 8. Kết quả kỳ vọng

Dự án hướng đến việc xây dựng một nền tảng phân tích phishing trên nền tảng web với những kết quả sau:

- **Phát hiện đa định dạng:** Hỗ trợ phân tích URL, email và file thông qua các mô-đun phát hiện chuyên biệt.
- **Tích hợp nhiều kỹ thuật phát hiện:** Kết hợp phân tích heuristic, kiểm tra nội dung và metadata cùng các nguồn tình báo mối đe dọa bên ngoài khả dụng.
- **Trust Score có giải thích:** Hiển thị điểm số, phân loại bảo mật và những dấu hiệu phát hiện làm cơ sở cho kết quả đánh giá.
- **Kiến trúc AWS Serverless:** Thể hiện khả năng tích hợp API Gateway, Lambda, SQS, DynamoDB và S3.
- **Xử lý bất đồng bộ:** Tách biệt quá trình tiếp nhận yêu cầu khỏi quá trình phát hiện thông qua cơ chế xử lý dựa trên hàng đợi.
- **Tối ưu hóa tài nguyên:** Giảm việc quét lặp lại thông qua caching và phân bổ tài nguyên theo đặc điểm từng loại tác vụ.
- **Giao diện dễ tiếp cận:** Cho phép người dùng gửi nội dung đáng ngờ, theo dõi tiến trình quét và hiểu kết quả đánh giá bảo mật.

Nhìn chung, AWS Phishing Detection Engine (Trust Check) thể hiện cách ứng dụng các dịch vụ điện toán đám mây AWS để xây dựng một giải pháp an ninh mạng có cấu trúc mô-đun, khả năng giải thích kết quả và chú trọng hiệu quả sử dụng tài nguyên trong việc nhận diện các mối đe dọa phishing tiềm ẩn.