---
title : "Triển khai Frontend với CloudFront, WAF và S3"
date :  "2025-09-15" 
weight : 1 
chapter : false
pre : " <b> 5.1. </b> "
---

## Giới thiệu

Chào mừng bạn đến với workshop đầu tiên trong chuỗi ứng dụng serverless của chúng tôi! Trong buổi thực hành này, bạn sẽ học cách triển khai một trang web tĩnh an toàn, hiệu suất cao sử dụng các dịch vụ phân phối nội dung và lưu trữ của AWS.

Các ứng dụng web hiện đại yêu cầu phân phối nội dung nhanh chóng, đáng tin cậy và an toàn cho người dùng trên toàn thế giới. Trong workshop này, bạn sẽ xây dựng cơ sở hạ tầng frontend tạo nền tảng cho một ứng dụng serverless sẵn sàng cho production. Bạn sẽ cấu hình Amazon S3 để lưu trữ các tệp trang web tĩnh, thiết lập Amazon CloudFront để phân phối nội dung của bạn trên toàn cầu với độ trễ thấp, và triển khai AWS WAF (Web Application Firewall) để bảo vệ ứng dụng của bạn khỏi các lỗ hổng web phổ biến.

![Diagram]( /images/5-Workshop/5.1-Frontend-deployment/5.1/diagram.png)

### Những gì bạn sẽ xây dựng

Khi kết thúc workshop này, bạn sẽ triển khai được một cơ sở hạ tầng frontend hoàn chỉnh bao gồm:

- **Static Website Hosting**: Một S3 bucket được cấu hình để phục vụ các tệp HTML, CSS và JavaScript của bạn
- **Global Content Delivery**: Một CloudFront distribution lưu cache và phân phối nội dung của bạn từ các edge location trên toàn thế giới
- **Security Layer**: Các quy tắc AWS WAF bảo vệ ứng dụng của bạn khỏi các mối đe dọa phổ biến như SQL injection, cross-site scripting (XSS) và các cuộc tấn công DDoS
- **HTTPS Security**: Cấu hình chứng chỉ SSL/TLS cho giao tiếp an toàn
- **Custom Domain** (Tùy chọn): Trang web của bạn có thể truy cập qua tên miền tùy chỉnh

### Tại sao chọn kiến trúc này?

Kiến trúc này mang lại một số lợi ích chính:

- **Performance**: Các edge location của CloudFront đảm bảo thời gian tải nhanh cho người dùng bất kể vị trí địa lý của họ
- **Scalability**: Tự động xử lý các đợt tăng lưu lượng truy cập mà không cần can thiệp thủ công
- **Cost-Effective**: Chỉ trả tiền cho những gì bạn sử dụng, không có máy chủ để quản lý
- **Security**: Nhiều lớp bảo vệ bao gồm WAF, bảo vệ DDoS và mã hóa
- **Reliability**: Được xây dựng trên cơ sở hạ tầng có tính khả dụng cao của AWS với SLA uptime 99.9%

## Cấu trúc Workshop

Workshop này được chia thành các phần sau:

1. **Part 1: S3 Static Website Hosting**
    - Tạo và cấu hình S3 bucket
    - Upload các tệp website
    - Cấu hình bucket cho static website hosting
    - Kiểm tra truy cập website cơ bản

2. **Part 2: CloudFront Distribution Setup**
    - Tạo CloudFront distribution
    - Cấu hình origin settings
    - Thiết lập cache behaviors
    - Kiểm tra global content delivery

3. **Part 3: AWS WAF Configuration**
    - Tạo Web ACL
    - Cấu hình security rules
    - Liên kết WAF với CloudFront
    - Kiểm tra security rules

4. **Part 4: Cleanup** (Tùy chọn)
    - Xóa các tài nguyên để tránh phí
    - Lưu cấu hình để sử dụng trong tương lai

### Thời lượng Workshop

**Thời gian ước tính**: 2-3 giờ

- Thiết lập và chuẩn bị: 15 phút
- Cấu hình S3: 30 phút
- Thiết lập CloudFront: 45 phút
- Triển khai WAF: 45 phút
- Kiểm tra và xác thực: 30 phút
