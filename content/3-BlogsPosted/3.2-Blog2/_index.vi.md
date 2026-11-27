
---
title: "Blog 2"
date: 2026-11-27
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# 🔐 TÌM HIỂU LỖ HỔNG PHP2SHELL: TỪ VIỆC AMAZON PHÁT HIỆN SỚM ĐẾN CÁCH GIẢM THIỂU BẰNG AWS WAF

PHP2Shell là một sự cố bảo mật nghiêm trọng liên quan đến hai lỗ hổng trong PHP, có thể cho phép thực thi mã từ xa mà không cần xác thực (Remote Code Execution – RCE). Điểm đáng chú ý của sự cố này là Amazon Threat Intelligence đã ghi nhận các nỗ lực khai thác thông qua hệ thống honeypot MadPot trước thời điểm công bố công khai bốn ngày.

Sự việc cho thấy tốc độ khai thác các lỗ hổng mới ngày càng nhanh và lý do các tổ chức cần kết hợp việc cập nhật bản vá kịp thời, giám sát liên tục và triển khai nhiều lớp kiểm soát bảo mật.

Trước khi đi sâu vào các lỗ hổng, chúng ta cần hiểu một số thuật ngữ quan trọng:

- **Remote Code Execution (RCE):** Lỗ hổng cho phép kẻ tấn công thực thi lệnh hoặc mã trên một hệ thống từ xa.
- **Honeypot:** Cơ chế bảo mật được thiết kế để thu hút và quan sát các hoạt động đáng ngờ trong một môi trường được kiểm soát.
- **Embargo:** Khoảng thời gian thông tin về lỗ hổng được hạn chế công bố công khai, nhằm tạo điều kiện cho các bên liên quan chuẩn bị bản cập nhật bảo mật.
- **Proof of Concept (PoC):** Bản minh chứng cho thấy một lỗ hổng có khả năng bị khai thác.
- **Web Application Firewall (WAF):** Giải pháp bảo mật giám sát và lọc lưu lượng HTTP/HTTPS nhằm hỗ trợ bảo vệ các ứng dụng web.

<figure>
  <a href="/aws-workshop/images/3-BlogsPosted/3.2-Blog2/php2shell-aws-waf.png" target="_blank">
    <img src="/aws-workshop/images/3-BlogsPosted/3.2-Blog2/php2shell-aws-waf.png" alt="Tổng quan lỗ hổng PHP2Shell và các biện pháp giảm thiểu bằng AWS WAF" style="display:block;width:90%;max-width:1100px;height:auto;margin:0 auto;" />
  </a>
  <figcaption style="text-align:center;">Tổng quan về các lỗ hổng PHP2Shell và biện pháp giảm thiểu bằng AWS WAF.</figcaption>
</figure>

## 1. Tổng quan về các lỗ hổng PHP2Shell

PHP2Shell là tên gọi chung cho hai lỗ hổng ảnh hưởng đến cách PHP xử lý các yêu cầu multipart/form-data:

| Lỗ hổng | Mô tả |
|---|---|
| CVE-2026-109618 | Lỗi liên quan đến hỏng bộ nhớ khi PHP gặp tình trạng hết bộ nhớ trong quá trình xử lý một yêu cầu. |
| CVE-2026-109619 | Dữ liệu cũ hoặc trạng thái toàn cục từ một yêu cầu trước đó có thể được tái sử dụng bởi yêu cầu tiếp theo được xử lý bởi cùng một worker. |

Cả hai lỗ hổng đều có khả năng dẫn đến thực thi mã từ xa mà không cần xác thực trong những điều kiện bị ảnh hưởng. Chúng được xếp hạng **Critical (Nghiêm trọng)** với điểm CVSS v4.0 là **9.2**.

Điều đáng lo ngại là hành vi xử lý yêu cầu bị ảnh hưởng diễn ra trước khi mã ứng dụng được thực thi. Vì vậy, các biện pháp kiểm tra ở cấp độ ứng dụng có thể không đủ để ngăn chặn việc khai thác.

## 2. Amazon phát hiện hoạt động khai thác trước khi công bố công khai

Một trong những phát hiện đáng chú ý nhất đến từ Amazon Threat Intelligence. Hệ thống honeypot MadPot của AWS đã ghi nhận hoạt động đáng ngờ trước khi các lỗ hổng được công bố công khai.

Dưới đây là dòng thời gian tổng hợp từ báo cáo của AWS:

| Thời gian (UTC) | Hoạt động được ghi nhận |
|---|---|
| Ngày 17/10/2026 | MadPot ghi nhận một nỗ lực khai thác xuất phát từ mạng ẩn danh thương mại, sớm hơn bốn ngày so với thời điểm công bố công khai. |
| Ngày 21/10, khoảng 02:00 | Các tài liệu Proof of Concept đầu tiên xuất hiện công khai. |
| Ngày 21/10, 02:00–08:00 | Nhiều nỗ lực khai thác khác được ghi nhận từ hạ tầng ẩn danh. Sự khác biệt giữa các payload cho thấy có khả năng liên quan đến nhiều tác nhân khác nhau. |
| Ngày 21/10, 14:00 | Thời gian embargo kết thúc và các bản cập nhật bảo mật PHP được phát hành. |
| Sau khi công bố | Các tài liệu Proof of Concept lan truyền nhanh chóng, làm tăng nguy cơ xuất hiện những nỗ lực khai thác trên diện rộng. |

Cần phân biệt giữa một nỗ lực khai thác được quan sát và một trường hợp xâm nhập thành công. AWS cho biết vẫn chưa rõ tác nhân sớm nhất đã tiếp cận thông tin khai thác bằng cách nào, cũng như liệu những nỗ lực được ghi nhận có thể thành công trên các hệ thống thực tế hay không.

Phát hiện này cho thấy các tổ chức không nên mặc định rằng một lỗ hổng chưa bị khai thác chỉ vì ngày công bố công khai vẫn chưa đến.

## 3. Tại sao các lỗ hổng này đáng lo ngại?

Sự cố PHP2Shell phản ánh một số thách thức quan trọng trong lĩnh vực an toàn thông tin.

### 3.1. Hoạt động khai thác có thể diễn ra trước khi công bố công khai

Việc ghi nhận hoạt động vào ngày 17/10 cho thấy các nỗ lực khai thác có thể xuất hiện trong khi thông tin về lỗ hổng vẫn đang được giữ kín. Vì vậy, các tổ chức không nên chỉ dựa vào ngày công bố chính thức để xác định thời điểm bắt đầu điều tra những hoạt động đáng ngờ.

### 3.2. Khoảng thời gian từ khi có bản vá đến khi bị khai thác ngày càng ngắn

Sau khi thông tin kỹ thuật và Proof of Concept được công bố, kẻ tấn công có thể nhanh chóng điều chỉnh và phát triển các phương thức khai thác của riêng mình. AWS cũng nhấn mạnh rằng việc ứng dụng AI trong phân tích có thể tiếp tục đẩy nhanh quá trình này.

### 3.3. Lỗ hổng nghiêm trọng đòi hỏi phản ứng nhanh chóng

Đối với các hệ thống có thể truy cập từ Internet, việc chờ đợi nhiều ngày mới đánh giá hoặc triển khai bản cập nhật bảo mật quan trọng có thể tạo ra khoảng thời gian phơi nhiễm không cần thiết. Việc xử lý lỗ hổng cần được xem là một hoạt động vận hành khẩn cấp, đặc biệt khi các nỗ lực khai thác đã được ghi nhận.

## 4. Áp dụng các biện pháp giảm thiểu bằng AWS WAF

AWS đã công bố hướng dẫn sử dụng AWS WAF để triển khai các biện pháp giảm thiểu bổ sung đối với hai lỗ hổng PHP2Shell.

Các bản cập nhật của nhóm quy tắc quản lý bao gồm:

- **Phiên bản 2.3:** Bổ sung quy tắc `PHP2Shell_BODY` nhằm hỗ trợ giảm thiểu CVE-2026-109619.
- **Phiên bản 2.4:** Bổ sung quy tắc `PHP2Shell_BODY_Extended` nhằm cung cấp phạm vi bảo vệ bổ sung cho CVE-2026-109618. Phiên bản 2.4 trở thành phiên bản mặc định vào ngày 23/10/2026.

Những quy tắc này cung cấp thêm một lớp bảo vệ, nhưng không thể thay thế việc nâng cấp PHP.

### 4.1. Nâng cấp PHP lên phiên bản đã được vá lỗi

Biện pháp khắc phục chính là nâng cấp các hệ thống PHP bị ảnh hưởng lên phiên bản đã sửa lỗi.

AWS xác định các phiên bản PHP đã được vá như sau:

| Nhánh PHP | Phiên bản đã sửa lỗi |
|---|---|
| PHP 8.5 | 8.5.12 |
| PHP 8.4 | 8.4.27 |
| PHP 8.3 | 8.3.36 |
| PHP 8.2 | 8.2.35 |

Đối với các nhánh PHP cũ hơn, tổ chức cần kiểm tra hướng dẫn tương ứng từ nhà cung cấp và khả năng áp dụng các bản vá bảo mật được backport. Những hệ thống sử dụng phiên bản đã hết vòng đời hỗ trợ cần được đặc biệt chú ý.

### 4.2. Sử dụng AWS WAF Managed Rules

Các tổ chức có thể sử dụng nhóm quy tắc AWS Managed Rules cùng những quy tắc liên quan đến PHP2Shell để kiểm tra các yêu cầu gửi đến ứng dụng.

Một quy trình triển khai thận trọng có thể thực hiện như sau:

1. Kích hoạt nhóm quy tắc Managed Rules phù hợp.
2. Bắt đầu với chế độ **Count** để quan sát những yêu cầu có khả năng khớp với quy tắc.
3. Kiểm tra log và đánh giá các trường hợp có thể gây false positive.
4. Chuyển sang chế độ **Block** sau khi xác nhận rằng việc chặn không ảnh hưởng đến lưu lượng hợp lệ của ứng dụng.

Cách triển khai theo từng giai đoạn giúp giảm nguy cơ làm gián đoạn các chức năng bình thường của hệ thống.

### 4.3. Giới hạn kích thước request body

Việc giới hạn kích thước request body có thể giúp giảm mức độ phơi nhiễm trước một số tình huống cạn kiệt bộ nhớ liên quan đến CVE-2026-109618.

Tuy nhiên, đây chỉ là một biện pháp giảm thiểu một phần. AWS lưu ý rằng hiện tượng khuếch đại thông qua request header và các yếu tố khác có thể làm giảm hiệu quả của việc chỉ giới hạn kích thước body.

Do đó, giới hạn kích thước request body cần được kết hợp với việc cập nhật PHP và các biện pháp bảo vệ phù hợp ở phía máy chủ.

### 4.4. Hạn chế multipart upload không cần thiết

Nếu ứng dụng không yêu cầu chức năng tải tệp lên hoặc không cần các yêu cầu multipart/form-data, quản trị viên có thể cân nhắc chặn loại nội dung này thông qua AWS WAF.

Biện pháp này giúp giảm mức độ tiếp xúc với luồng xử lý yêu cầu bị ảnh hưởng. Tuy nhiên, cần kiểm tra cẩn thận vì một số ứng dụng vẫn phụ thuộc hợp lệ vào chức năng multipart upload.

### 4.5. Áp dụng Rate-Based Rules và giám sát yêu cầu

Các quy tắc giới hạn tốc độ có thể giúp giảm số lượng yêu cầu POST đáng ngờ được gửi lặp lại. Đây là một biện pháp kiểm soát bổ sung hữu ích, nhưng không thể đảm bảo ngăn chặn một yêu cầu khai thác thành công đơn lẻ.

Các tổ chức cũng nên bật logging của AWS WAF và kiểm tra những yêu cầu POST đáng ngờ, đặc biệt là các yêu cầu chứa tham số `PHP_SESSION_UPLOAD_PROGRESS`.

**Giới hạn quan trọng:** AWS WAF không thể kiểm tra toàn diện bộ nhớ của PHP worker hoặc thay thế các bản sửa lỗi trong cơ chế xử lý yêu cầu nội bộ của PHP. Những biện pháp bảo vệ bằng WAF chỉ đóng vai trò phòng thủ nhiều lớp, không phải giải pháp khắc phục hoàn toàn.

## 5. Khuyến nghị phản ứng bảo mật

Dựa trên những phát hiện của AWS, các tổ chức đang vận hành ứng dụng PHP nên cân nhắc thực hiện những hành động sau:

1. **Cập nhật các hệ thống bị ảnh hưởng:** Nâng cấp lên phiên bản PHP đã được vá phù hợp trong thời gian sớm nhất.
2. **Kiểm tra log lịch sử:** Điều tra các hoạt động đáng ngờ bắt đầu từ ngày 17/10/2026, thay vì chỉ tập trung vào ngày công bố công khai.
3. **Kiểm tra các yêu cầu POST đáng ngờ:** Tìm kiếm những yêu cầu multipart/form-data bất thường và tham số `PHP_SESSION_UPLOAD_PROGRESS`.
4. **Điều tra hoạt động bất thường:** Kiểm tra việc thực thi tiến trình ngoài dự kiến và những thay đổi tệp không được phép trên máy chủ bị ảnh hưởng.
5. **Đánh giá cẩn thận các địa chỉ IP đáng ngờ:** Hạ tầng ẩn danh thương mại có thể được sử dụng chung bởi cả người dùng hợp pháp. Vì vậy, cần điều tra hoạt động liên quan thay vì tự động xem mọi địa chỉ được ghi nhận là độc hại.
6. **Triển khai các biện pháp bảo vệ bằng WAF:** Sử dụng managed rules, logging và các giới hạn yêu cầu phù hợp như những lớp phòng thủ bổ sung.

## 6. Những bài học rút ra

Sự cố PHP2Shell mang đến một số bài học quan trọng cho những người làm công tác an toàn thông tin:

- **Embargo không đảm bảo rằng chưa có hoạt động khai thác:** Kẻ tấn công có thể tiếp cận hoặc tự phát hiện thông tin về lỗ hổng trước khi nó được công bố công khai.
- **Quản lý bản vá cần có khả năng phản ứng nhanh:** Những lỗ hổng nghiêm trọng ảnh hưởng đến dịch vụ có thể truy cập từ Internet đòi hỏi phải được đánh giá và khắc phục kịp thời.
- **Giám sát cần bao phủ cả hoạt động trong quá khứ:** Quá trình điều tra bảo mật phải tính đến khả năng các hoạt động đáng ngờ đã bắt đầu trước khi lỗ hổng được công bố.
- **Phòng thủ nhiều lớp vẫn rất cần thiết:** Cập nhật bản vá, quy tắc WAF, biện pháp bảo vệ phía máy chủ và giám sát liên tục cần phối hợp với nhau.
- **Đội ngũ bảo mật phải thích ứng với chu kỳ khai thác ngày càng nhanh:** Các kỹ thuật phân tích tự động và hỗ trợ bởi AI có thể rút ngắn khoảng thời gian từ khi phát hiện lỗ hổng đến khi nó bị khai thác.

## 7. Góc nhìn cá nhân và kết luận

Điều khiến tôi chú ý nhất ở sự cố PHP2Shell không chỉ là mức độ nghiêm trọng của các lỗ hổng, mà còn là thời điểm những nỗ lực khai thác được ghi nhận.

Việc Amazon MadPot phát hiện hoạt động đáng ngờ trước thời điểm công bố công khai bốn ngày cho thấy hoạt động giám sát bảo mật cần được duy trì liên tục, thay vì chỉ bắt đầu phản ứng sau khi một lỗ hổng được nhiều người biết đến.

Tôi cũng nhận thấy AWS WAF có thể cung cấp thêm một lớp bảo vệ hữu ích, nhưng không thể thay thế việc vá lỗi trong ứng dụng gốc. Các biện pháp bảo mật cần được lựa chọn dựa trên những giới hạn thực tế của công nghệ và nhu cầu của ứng dụng.

Đối với tôi, sự cố này củng cố một nguyên tắc quan trọng trong lĩnh vực an toàn thông tin: phòng thủ hiệu quả đòi hỏi khả năng phát hiện sớm, khắc phục kịp thời và nhiều lớp bảo vệ phối hợp chặt chẽ với nhau.

## Tài liệu tham khảo

1. Amazon Web Services. (2026, October 21). *Amazon Threat Intelligence identifies PHP2Shell exploitation prior to public disclosure in MadPot.*  
   https://aws.amazon.com/blogs/security/amazon-threat-intelligence-identifies-php2shell-exploitation-prior-to-public-disclosure-in-madpot/

2. Amazon Web Services. (2026, October 21; updated October 29, 2026). *Using AWS WAF to apply mitigations for PHP2Shell RCE vulnerabilities CVE-2026-109618 and CVE-2026-109619.*  
   https://aws.amazon.com/blogs/security/using-aws-waf-to-apply-mitigations-for-php2shell-rce-vulnerabilities-cve-2026-109618-and-cve-2026-109619/
