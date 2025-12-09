---
title: "Worklog tuần 9"
date: "2025-09-15"
weight: 1
chapter: false
pre: " <b> 1.9. </b> "
---

### Mục tiêu tuần 9:

* Thiết kế và lập kế hoạch workshop kiến trúc serverless toàn diện
* Bắt đầu triển khai các thành phần frontend deployment
* Tài liệu hóa hướng dẫn từng bước cho ứng dụng web serverless

### Các nhiệm vụ cần thực hiện trong tuần này:
| Ngày | Nhiệm vụ                                                                             | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo                                                                                                                            |
| --- |----------------------------------------------------------------------------------|------------|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------|
| 2   | - Lập kế hoạch workshop và thiết kế kiến trúc<br/>- Tạo đề cương và cấu trúc workshop | 27/10/2025 | 27/10/2025 | <https://docs.aws.amazon.com/whitepapers/latest/serverless-architectures-lambda/> |
| 3   | - Làm việc trên phần Prerequisites<br/>- Thiết lập S3 Static Website Hosting | 28/10/2025 | 28/10/2025 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteHosting.html> |
| 4   | - Cấu hình CloudFront Distribution<br/>- Thiết lập custom domain và SSL certificates | 29/10/2025 | 29/10/2025 | <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/> |
| 5   | - Triển khai AWS WAF Configuration<br/>- Kiểm tra security rules và rate limiting | 30/10/2025 | 30/10/2025 | <https://docs.aws.amazon.com/waf/latest/developerguide/> |
| 6   | - Xem xét và kiểm tra Part 1: Frontend Deployment<br/>- Tài liệu hóa các bước troubleshooting | 31/10/2025 | 31/10/2025 | |


### Thành tựu tuần 9:

**Lập kế hoạch và thiết kế kiến trúc Workshop**
* Hình thành workshop kiến trúc serverless toàn diện:
    * Thiết kế cấu trúc hai phần: Frontend Deployment và Backend Deployment.
    * Lập kế hoạch tích hợp các dịch vụ AWS chính: S3, CloudFront, WAF, API Gateway, Lambda, RDS, Cognito và Secrets Manager.
    * Tạo đề cương hướng dẫn từng bước chi tiết cho triển khai ứng dụng web serverless hoàn chỉnh.
    * Tổ chức workshop thành các phần logic với prerequisites và mục tiêu học tập rõ ràng.
* Xác định đối tượng mục tiêu và kết quả học tập của workshop:
    * Hướng đến các developers chuyển sang kiến trúc serverless.
    * Tập trung vào triển khai thực hành với cấu hình production-ready.
    * Nhấn mạnh các phương pháp bảo mật tốt nhất và tối ưu chi phí.

**Part 1: Frontend Deployment - Prerequisites và Planning**
* Thiết lập prerequisites cho workshop:
    * AWS account với quyền IAM phù hợp.
    * Hiểu biết cơ bản về web development (HTML, CSS, JavaScript).
    * Quen thuộc với AWS Console và các dịch vụ AWS cơ bản.
    * Code editor và Git cho version control.
* Chuẩn bị ứng dụng frontend mẫu:
    * Tạo static website đơn giản với HTML, CSS và JavaScript.
    * Thiết kế UI responsive cho mục đích demo.
    * Chuẩn bị assets và nội dung để kiểm tra deployment.

**Triển khai S3 Static Website Hosting**
* Cấu hình S3 bucket cho static website hosting:
    * Tạo S3 bucket với quy ước đặt tên phù hợp.
    * Bật tính năng static website hosting.
    * Upload các tệp website (HTML, CSS, JavaScript, images).
    * Cấu hình index document và error document.
* Thiết lập bucket policies cho public access:
    * Viết bucket policy để cho phép public read access cho các website objects.
    * Cấu hình CORS settings cho cross-origin requests.
    * Kiểm tra khả năng truy cập website qua S3 website endpoint.
* Triển khai versioning và lifecycle policies:
    * Bật S3 versioning cho khả năng rollback nội dung.
    * Tạo lifecycle rules cho quản lý object tự động.
    * Tài liệu hóa các phương pháp tốt nhất cho S3 website hosting.

**Thiết lập CloudFront Distribution**
* Cấu hình CloudFront distribution cho global content delivery:
    * Tạo CloudFront distribution với S3 bucket làm origin.
    * Cấu hình origin access identity (OAI) cho secure S3 access.
    * Cập nhật S3 bucket policy để hạn chế truy cập chỉ từ CloudFront.
    * Đặt default root object và custom error responses.
* Tối ưu hóa cài đặt CloudFront:
    * Cấu hình caching behaviors và TTL settings.
    * Bật compression để cải thiện hiệu suất.
    * Thiết lập query string forwarding và header configuration.
    * Cấu hình geographic restrictions nếu cần.
* Cấu hình SSL/TLS certificate:
    * Request SSL certificate qua AWS Certificate Manager (ACM).
    * Xác thực quyền sở hữu domain thông qua DNS hoặc email.
    * Liên kết certificate với CloudFront distribution.
    * Cấu hình HTTPS-only access với security policies.
* Thiết lập custom domain:
    * Cấu hình alternate domain names (CNAMEs) trong CloudFront.
    * Tạo Route 53 hosted zone cho domain management.
    * Thêm A record (alias) trỏ đến CloudFront distribution.
    * Kiểm tra custom domain access và SSL certificate validity.

**Cấu hình AWS WAF cho bảo mật**
* Triển khai AWS WAF cho web application protection:
    * Tạo Web ACL (Access Control List) cho CloudFront.
    * Cấu hình managed rule groups cho các mối đe dọa phổ biến:
        * Core Rule Set (CRS) cho OWASP Top 10 protection.
        * Known Bad Inputs rule set cho malicious request patterns.
        * SQL injection và XSS protection rules.
    * Thiết lập rate-based rules để ngăn chặn DDoS attacks:
        * Cấu hình rate limiting (ví dụ: 2000 requests mỗi 5 phút trên mỗi IP).
        * Tạo IP reputation rules sử dụng threat intelligence.
        * Thiết lập geographic blocking rules nếu cần.
* Triển khai custom WAF rules:
    * Tạo custom rules cho nhu cầu bảo mật đặc thù của ứng dụng.
    * Cấu hình URI path matching và header inspection rules.
    * Thiết lập allow/block rules dựa trên quốc gia xuất xứ.
    * Triển khai bot protection và challenge mechanisms.
* WAF monitoring và logging:
    * Bật WAF logging đến S3 hoặc CloudWatch Logs.
    * Cấu hình CloudWatch metrics cho rule match counts.
    * Thiết lập CloudWatch alarms cho unusual traffic patterns.
    * Tạo dashboard cho real-time WAF monitoring.

**Kiểm tra và xác thực Frontend Deployment**
* Kiểm tra toàn diện frontend đã deploy:
    * Xác minh khả năng truy cập website qua CloudFront URL và custom domain.
    * Kiểm tra HTTPS enforcement và SSL certificate validation.
    * Xác thực caching behavior và cache invalidation process.
    * Kiểm tra geographic access và edge location performance.
* Kiểm tra bảo mật:
    * Xác nhận WAF rules đang chặn các malicious requests.
    * Kiểm tra rate limiting bằng cách mô phỏng high request volumes.
    * Xác minh rằng direct S3 access bị chặn (chỉ CloudFront được phép).
    * Xác thực cấu hình CORS cho API integration readiness.
* Tối ưu hóa hiệu suất:
    * Phân tích CloudFront cache hit ratio và cơ hội tối ưu hóa.
    * Kiểm tra hiệu quả compression cho các loại file khác nhau.
    * Đo global latency từ nhiều vị trí địa lý.
    * Tối ưu hóa TTL settings dựa trên tần suất cập nhật nội dung.

**Tạo tài liệu và nội dung Workshop**
* Tạo tài liệu workshop chi tiết:
    * Viết hướng dẫn từng bước cho mỗi cấu hình.
    * Bao gồm screenshots và diagrams để hướng dẫn trực quan.
    * Tài liệu hóa các vấn đề phổ biến và các bước troubleshooting.
    * Chuẩn bị hướng dẫn cleanup để tránh chi phí không cần thiết.
* Tài liệu hóa best practices:
    * Best practices bảo mật cho S3, CloudFront và WAF.
    * Chiến lược tối ưu chi phí cho frontend deployment.
    * Khuyến nghị performance tuning.
    * Hướng dẫn monitoring và maintenance.

**Bài học và thách thức**
* Những hiểu biết chính từ triển khai frontend deployment:
    * Tầm quan trọng của chiến lược cache invalidation phù hợp.
    * Các cân nhắc bảo mật khi expose S3 content.
    * Đánh đổi giữa security rules và legitimate traffic.
    * Certificate validation timing và DNS propagation delays.
* Các thách thức đã giải quyết:
    * Thời gian deploy CloudFront distribution (15-20 phút).
    * Yêu cầu ACM certificate validation trong us-east-1 region.
    * WAF rule fine-tuning để tránh false positives.
    * Custom domain DNS propagation delays.

**Chuẩn bị cho Part 2: Backend Deployment**
* Lập kế hoạch các thành phần backend architecture:
    * VPC và network design cho secure backend resources.
    * RDS database setup cho application data storage.
    * Lambda functions cho serverless compute.
    * API Gateway cho RESTful API endpoints.
    * Cognito cho user authentication và authorization.
    * Secrets Manager cho secure credential storage.
* Phác thảo yêu cầu tích hợp backend:
    * API authentication flow với Cognito JWT tokens.
    * Database connection từ Lambda functions.
    * Environment variable management và secrets handling.
    * Frontend-backend communication qua API Gateway.