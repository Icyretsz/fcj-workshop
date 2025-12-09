---
title : "Phần 1: Triển khai Frontend với CloudFront, WAF và S3"
date :  "2025-09-15" 
weight : 1 
chapter : false
pre : " <b> 5.1. </b> "
---

## Giới thiệu

Chào mừng bạn đến với workshop đầu tiên trong chuỗi ứng dụng serverless! Trong buổi thực hành này, bạn sẽ học cách triển khai một website tĩnh an toàn, hiệu suất cao sử dụng các dịch vụ AWS như CloudFront, S3 và WAF.

Các ứng dụng web hiện đại cần phân phối nội dung nhanh, đáng tin cậy và bảo mật cho người dùng toàn cầu. Workshop này giúp bạn xây dựng hạ tầng frontend cho ứng dụng serverless sẵn sàng cho production. Bạn sẽ cấu hình Amazon S3 để lưu trữ file website tĩnh, thiết lập Amazon CloudFront để phân phối nội dung toàn cầu với độ trễ thấp, và triển khai AWS WAF (Web Application Firewall) để bảo vệ ứng dụng khỏi các lỗ hổng web phổ biến.

![Diagram]( /images/5-Workshop/5.1-Frontend-deployment/5.1/diagram.png)

### Những gì bạn sẽ xây dựng

Kết thúc workshop, bạn sẽ triển khai được hạ tầng frontend gồm:

- **Static Website Hosting**: S3 bucket phục vụ file HTML, CSS, JavaScript
- **Global Content Delivery**: CloudFront distribution lưu cache và phân phối nội dung từ các edge location toàn cầu
- **Security Layer**: Quy tắc AWS WAF bảo vệ khỏi SQL injection, cross-site scripting (XSS), DDoS
- **HTTPS Security**: Cấu hình chứng chỉ SSL/TLS cho giao tiếp an toàn
- **Custom Domain** (Tùy chọn): Website truy cập qua tên miền tùy chỉnh

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
    - Xóa tài nguyên để tránh phí
    - Lưu cấu hình cho lần sau

### Thời lượng Workshop

**Thời gian ước tính**: 2-3 giờ

- Thiết lập và chuẩn bị: 15 phút
- Cấu hình S3: 30 phút
- Thiết lập CloudFront: 45 phút
- Triển khai WAF: 45 phút
- Kiểm tra và xác thực: 30 phút
