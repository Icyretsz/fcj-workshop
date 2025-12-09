---
title : "Tích hợp Frontend"
date :  "2025-09-15" 
weight : 8 
chapter : false
pre : " <b> 5.2.8 </b> "
---

## Tổng quan

Ở phần này, bạn sẽ kết hợp tất cả các thành phần backend đã xây dựng để kết nối với frontend CloudFront đã thiết lập ở phần trước.

**Bạn sẽ đạt được:**
- Cấu hình Lambda function để thêm CORS headers
- Cấu hình CORS cho API Gateway
- Cấu hình Cognito callback URL và login URL trỏ về CloudFront endpoint
- Kiểm thử các thao tác CRUD của ứng dụng

**Thời gian ước tính**: 30 phút

## Bước 1: Cấu hình Lambda function
Vì chúng ta gọi API từ một origin khác (CloudFront), cần cấu hình CORS headers đúng cách.

1. Vào Lambda console, chọn **workshop-lambda-sm-rds**
2. Xem hàm `response`, hàm này sẽ thêm các CORS headers phù hợp vào phản hồi
3. Đảm bảo trường `Access-Control-Allow-Origin` được đặt là CloudFront endpoint của bạn

```javascript
function respond(statusCode, payload) {
    return {
        statusCode,
        headers: {
            "Content-Type": "application/json",
            "Access-Control-Allow-Headers": "Content-Type",
            "Access-Control-Allow-Origin": "https://[cloudfront-id].cloudfront.net", //thêm CloudFront endpoint của bạn tại đây
            "Access-Control-Allow-Methods": "OPTIONS,POST,GET,DELETE"
        },
        body: JSON.stringify(payload),
    };
}
```
- Đảm bảo API chỉ cho phép gọi từ origin đáng tin cậy (CloudFront frontend của bạn)

## Bước 2: Cấu hình CORS cho các resources của API Gateway

- Đảm bảo đã cấu hình CORS đúng cho các resources `/users` và `/{id}`. Việc này đã thực hiện ở [5.2.6 - API Gateway setup]({{< relref "/5-Workshop/5.2-Serverless-backend/5.2.6-API-Gateway" >}}#29-enable-cors-on-each-resources)

## Bước 3: Cấu hình Cognito callback URL và login URL trỏ về CloudFront endpoint

- Việc này đã thực hiện ở [5.2.7 - Cognito]({{< relref "/5-Workshop/5.2-Serverless-backend/5.2.7-Cognito" >}}#72-configure-allowed-callback-urls)

## Bước 4: Kiểm thử ứng dụng

1. Truy cập CloudFront endpoint của bạn

![Home page](/images/5-Workshop/5.2-Serverless/5.2.8-Frontend-integration/2.png)

2. Nhấp **Sign in**
3. Bạn sẽ được chuyển hướng đến giao diện đăng nhập hosted login UI

![login](/images/5-Workshop/5.2-Serverless/5.2.8-Frontend-integration/3.png)

4. Đăng nhập thành công, bạn sẽ được chuyển về trang chủ.
5. Trang chủ sẽ hiển thị thông tin và token của bạn, cùng danh sách người dùng lấy từ RDS

![homepage](/images/5-Workshop/5.2-Serverless/5.2.8-Frontend-integration/4.png)

6. Bạn có thể kiểm thử các thao tác CRUD

## Kết thúc workshop
