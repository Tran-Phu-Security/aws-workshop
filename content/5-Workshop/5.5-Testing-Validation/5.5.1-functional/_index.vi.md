---
title : "Kiểm thử chức năng"
date : 2026-11-27
weight : 1
chapter : false
pre : " <b> 5.5.1. </b> "
---

Mục này kiểm tra ba luồng quét của hệ thống bằng các mẫu vô hại: link, email và file. Giao diện web chạy ở mục 5.4.9 được dùng cho phần chính. Phần cuối kiểm tra trực tiếp qua API.

#### Chuẩn bị mẫu thử

Link và email mẫu đã có sẵn trong mã nguồn (`backend\samples`). Hai file mẫu cần được tạo bằng PowerShell, trong một thư mục tùy chọn:

```powershell
mkdir workshop-samples
cd workshop-samples
Set-Content -Path ghi_chu.txt -Value "Bien ban hop nhom. Noi dung: ke hoach workshop." -Encoding ascii
Set-Content -Path hoa_don_thang_11.pdf -Value "MZ This is an inert text file used only for testing the file scanner. It is not a program." -Encoding ascii
```

+ `ghi_chu.txt` là văn bản thuần, dùng làm mẫu an toàn.
+ `hoa_don_thang_11.pdf` thực chất là file văn bản vô hại nhưng bắt đầu bằng ký tự `MZ`, là dấu hiệu nhận dạng của chương trình thực thi trên Windows. File mô phỏng trường hợp đuôi file giả mạo.

#### Cách đọc kết quả

| Điểm | Mức rủi ro |
|---|---|
| 80 trở lên | `SAFE` |
| 50 đến 79 | `SUSPICIOUS` |
| dưới 50 | `DANGEROUS` |

Kết quả còn có `confidence` (`HIGH`, `MEDIUM`, `LOW`) cho biết bao nhiêu nguồn bên ngoài đã trả lời. Kết quả `SAFE` với `confidence` là `LOW` chỉ có nghĩa là chưa thấy dấu hiệu xấu, chưa được đối chiếu với nguồn bên ngoài. Khi các khóa API ở mục 5.4.6 hoặc 5.4.8 đã được cấu hình, `confidence` thường cao hơn.

#### Kiểm thử quét link

1. Mở `http://localhost:8000/index.html`, chọn tab **Link**.
2. Dán `http://paypal-security-update-fake.com/login` và gửi.
3. Đợi kết quả hiển thị (vài giây đến khoảng một phút).

Kết quả mong đợi: mức rủi ro `DANGEROUS`. Các lý do thường gặp: tên miền chứa tên "paypal" nhưng không phải trang chính thức, chứa từ khóa nhạy cảm (login, update, security), tên miền có quá nhiều dấu gạch ngang, không dùng HTTPS.

![Kết quả quét link](/aws-workshop/images/5-Workshop/5.5-Testing-Validation/5.5.1-functional/url-result.png)

Mẫu đối chứng: quét `https://example.com`. Kết quả mong đợi là `SAFE`.

#### Kiểm thử quét email

1. Mở file `backend\samples\email_lua_dao.txt` bằng Visual Studio Code, chọn toàn bộ nội dung và sao chép.
2. Ở giao diện web, chọn tab **Email**, dán nội dung và gửi.

Kết quả mong đợi: mức rủi ro `DANGEROUS`. Các lý do thường gặp: tên miền người gửi giả mạo thương hiệu, tên hiển thị giả danh, email vi phạm chính sách xác thực DMARC, có cụm từ gây áp lực, địa chỉ trả lời khác tên miền người gửi.

![Kết quả quét email](/aws-workshop/images/5-Workshop/5.5-Testing-Validation/5.5.1-functional/email-result.png)

Mẫu đối chứng: quét nội dung file `backend\samples\email_binh_thuong.txt`. Kết quả mong đợi là `SAFE`.

#### Kiểm thử quét file

1. Ở giao diện web, chọn tab **File**.
2. Chọn file `hoa_don_thang_11.pdf` và gửi.

Kết quả mong đợi: mức rủi ro `SUSPICIOUS`, với lý do tệp mang đuôi `.pdf` nhưng thực chất là chương trình thực thi.

![Kết quả quét file](/aws-workshop/images/5-Workshop/5.5-Testing-Validation/5.5.1-functional/file-result.png)

Mẫu đối chứng: quét file `ghi_chu.txt`. Kết quả mong đợi là `SAFE`.

#### Kiểm thử trực tiếp qua API

Phần này kiểm tra luồng bất đồng bộ mà không dùng giao diện. Thay `<INVOKE_URL>` bằng địa chỉ ghi ở mục 5.4.7.

**Quét link và theo dõi trạng thái:**

```powershell
$base = "<INVOKE_URL>"
$job = Invoke-RestMethod -Uri "$base/scan" -Method Post -ContentType "application/json" `
  -Body '{"type":"link","payload":"http://paypal-security-update-fake.com/login"}'
$job

do {
  Start-Sleep 5
  $r = Invoke-RestMethod -Uri "$base/scan/$($job.job_id)"
  $r.status
} while ($r.status -ne "COMPLETED")

$r.result.risk_level
$r.result.confidence
$r.result.reasons
```

Trạng thái của job đi qua `PENDING`, `PROCESSING` rồi `COMPLETED`. Các giá trị số trong phản hồi của `StatusLambda` có thể ở dạng chuỗi (ví dụ `"45"`), cần ép kiểu khi dùng trong mã.

**Quét file (hai bước):** hệ thống trả về `upload_url` có hiệu lực 5 phút. Tải file lên địa chỉ đó ngay, sau đó hỏi kết quả như bình thường.

```powershell
$path = (Resolve-Path .\hoa_don_thang_11.pdf).Path
$job = Invoke-RestMethod -Uri "$base/scan" -Method Post -ContentType "application/json" `
  -Body (@{type="file"; payload=(Split-Path $path -Leaf)} | ConvertTo-Json)
Invoke-RestMethod -Uri $job.upload_url -Method Put -InFile $path -ContentType "application/octet-stream"

do {
  Start-Sleep 5
  $r = Invoke-RestMethod -Uri "$base/scan/$($job.job_id)"
  $r.status
} while ($r.status -ne "COMPLETED")
$r.result.risk_level
```

#### Kết quả

| Mẫu | Mức rủi ro mong đợi |
|---|---|
| Link `paypal-security-update-fake.com` | `DANGEROUS` |
| Link `example.com` | `SAFE` |
| Email `email_lua_dao.txt` | `DANGEROUS` |
| Email `email_binh_thuong.txt` | `SAFE` |
| File `hoa_don_thang_11.pdf` | `SUSPICIOUS` |
| File `ghi_chu.txt` | `SAFE` |

Điểm số cụ thể có thể chênh lệch tùy dữ liệu trả về từ các nguồn bên ngoài. Mức rủi ro là tiêu chí đối chiếu chính.