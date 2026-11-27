---
title : "Tạo API Gateway"
date : 2026-11-27
weight : 7
chapter : false
pre : " <b> 5.4.7. </b> "
---

Giao diện web gọi hệ thống qua một HTTP API của Amazon API Gateway với hai route:

| Phương thức | Route | Hàm Lambda |
|---|---|---|
| `POST` | `/scan` | `IngestLambda` |
| `GET` | `/scan/{job_id}` | `StatusLambda` |

HTTP API có chi phí thấp hơn REST API, và hai hàm Lambda ở mục 5.4.6 đã được viết theo định dạng sự kiện của HTTP API (payload version 2.0).

#### Tạo API và hai route

1. Mở dịch vụ **API Gateway** trên AWS Console, chọn Region **Asia Pacific (Singapore) ap-southeast-1**.
2. Ở mục **HTTP API**, chọn **Build**.
3. Ở **Integrations**, chọn **Add integration** rồi nhập:
   + **Integration type:** Lambda
   + **Lambda function:** `IngestLambda`
4. Chọn **Add integration** lần nữa và chọn hàm `StatusLambda`.
5. Nhập **API name:** `PhishingDetectionAPI`, chọn **Next**.
6. Ở **Configure routes**, tạo hai route:
   + **Method** `POST`, **Resource path** `/scan`, **Integration target** `IngestLambda`
   + **Method** `GET`, **Resource path** `/scan/{job_id}`, **Integration target** `StatusLambda`

   Xóa route mặc định `ANY /{proxy+}` nếu có.
7. Chọn **Next**. Ở **Configure stages**, giữ stage `$default` với **Auto-deploy** đang bật, rồi chọn **Next**.
8. Chọn **Create**.

Khi tạo tích hợp trên Console, API Gateway tự cấp quyền gọi hàm cho từng hàm Lambda. Không cần thêm quyền thủ công.

#### Cấu hình CORS

Trình duyệt gửi yêu cầu kiểm tra (`OPTIONS`) trước khi gọi `POST /scan` với nội dung JSON. API phải trả lời đúng yêu cầu này, nếu không giao diện web sẽ báo lỗi CORS.

1. Mở API vừa tạo, chọn **CORS** ở menu bên trái (mục Develop), rồi chọn **Configure**.
2. Nhập:
   + **Access-Control-Allow-Origin:** `*`
   + **Access-Control-Allow-Methods:** `GET`, `POST`, `OPTIONS`
   + **Access-Control-Allow-Headers:** `content-type`
3. Chọn **Save**.

Giá trị `*` cho phép mọi nguồn gọi API, phù hợp cho workshop. Khi triển khai thật, thay bằng địa chỉ cụ thể của giao diện web.

#### Giới hạn tốc độ (tùy chọn)

Giới hạn số yêu cầu giúp bảo vệ hệ thống và hạn mức các nguồn bên ngoài.

1. Chọn **Stages** ở menu bên trái, chọn stage `$default`.
2. Ở mục **Default route throttling**, chọn **Edit**.
3. Đặt **Rate** là `10` yêu cầu mỗi giây và **Burst** là `20`, rồi chọn **Save**.

#### Ghi lại địa chỉ API

Chọn **API details** (hoặc trang tổng quan của API), sao chép **Invoke URL** có dạng:

    https://<api-id>.execute-api.ap-southeast-1.amazonaws.com

Địa chỉ này được dùng ở mục 5.4.9 để cấu hình giao diện và ở mục 5.5 để kiểm thử.

#### Kiểm tra nhanh

Mở PowerShell, thay `<INVOKE_URL>` bằng địa chỉ vừa sao chép:

```powershell
$base = "<INVOKE_URL>"
Invoke-RestMethod -Uri "$base/scan" -Method Post -ContentType "application/json" `
  -Body '{"type":"link","payload":"http://paypal-security-update-fake.com/login"}'
```

Kết quả đúng là một đối tượng có `job_id` và `status` là `PENDING`.

#### Kết quả

+ API `PhishingDetectionAPI` có hai route: `POST /scan` và `GET /scan/{job_id}`.
+ CORS đã được cấu hình.
+ Đã ghi lại **Invoke URL**.
+ Lệnh kiểm tra nhanh trả về `job_id`.