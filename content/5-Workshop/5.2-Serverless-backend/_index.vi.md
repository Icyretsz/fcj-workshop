---
title: "Phần 2: Triển khai Backend với API Gateway, Lambda, RDS và Cognito"
date: "2025-09-15"
weight: 2
chapter: false
pre: " <b> 5.2 </b> "
---

## Giới thiệu

Chào mừng bạn đến với phần thứ hai trong chuỗi workshop xây dựng ứng dụng serverless! Trong buổi thực hành này, bạn sẽ xây dựng một hạ tầng backend hoàn chỉnh, bảo mật cho ứng dụng serverless sử dụng các dịch vụ quản lý của AWS.

Bạn sẽ học cách kết nối backend serverless với frontend đã triển khai ở Phần 1.

Các ứng dụng hiện đại cần hệ thống backend mạnh mẽ, mở rộng, xử lý logic nghiệp vụ, quản lý dữ liệu an toàn và xác thực người dùng. Trong workshop này, bạn sẽ tạo kiến trúc backend hoàn toàn serverless sử dụng AWS Lambda cho xử lý, Amazon RDS cho lưu trữ dữ liệu, API Gateway cho RESTful APIs, Amazon Cognito cho xác thực người dùng, và AWS Secrets Manager cho quản lý thông tin xác thực an toàn.

![Diagram]( /images/5-Workshop/diagram.png)

### Bạn sẽ xây dựng gì

Kết thúc phần này, bạn sẽ triển khai một hạ tầng backend hoàn chỉnh gồm:

- **RESTful APIs**: API Gateway xử lý HTTP request và chuyển tiếp đến Lambda function phù hợp
- **Serverless Computing**: Lambda function viết bằng Node.js/Python thực thi logic nghiệp vụ
- **Managed Database**: Amazon RDS PostgreSQL lưu trữ dữ liệu tin cậy
- **User Authentication**: Amazon Cognito user pool cho đăng ký, đăng nhập và quản lý người dùng
- **API Security**: Cognito authorizer bảo vệ các API endpoint. Ngoài ra, API Gateway chỉ nhận request từ CloudFront
Hơn nữa, hãy thực thi API Gateway để chỉ chấp nhận các yêu cầu có nguồn gốc từ CloudFront
- **Secrets Management**: AWS Secrets Manager lưu trữ thông tin xác thực database và sinh giá trị bảo mật để nhúng vào custom header của CloudFront nhằm giới hạn truy cập API Gateway
- **Network Security**: Cấu hình VPC cách ly database khỏi internet công cộng

### Tại sao chọn kiến trúc này?

Kiến trúc backend serverless này mang lại nhiều lợi ích:

- **Không quản lý server**: AWS tự động cấp phát, vá lỗi, mở rộng
- **Trả phí theo sử dụng**: Chỉ trả cho thời gian tính toán và lưu trữ thực tế
- **Tự động mở rộng**: Xử lý lưu lượng từ 0 đến hàng ngàn request
- **Bảo mật tích hợp**: Nhiều lớp bảo vệ gồm IAM, Cognito, VPC, secrets management
- **Tăng năng suất lập trình viên**: Tập trung vào code, không lo hạ tầng

### Thời lượng workshop

**Thời gian ước tính**: 3-4 tiếng

- Thiết lập VPC và mạng: 20 phút
- Tạo RDS database: 30 phút
- Cấu hình Secrets Manager: 15 phút
- Phát triển Lambda function: 45 phút
- Thiết lập API Gateway: 40 phút
- Cấu hình Cognito: 45 phút
- Tích hợp và kiểm thử: 45 phút

## Cấu trúc workshop

Workshop này gồm các phần sau:

### **Phần 1: Thiết lập VPC và mạng**
- Tạo VPC để cách ly database
- Cấu hình subnet (chỉ 1 Availability Zone cho workshop này)
- Thiết lập security group cho truy cập database

### **Phần 2: Cấu hình RDS Database**
- Tạo RDS PostgreSQL instance
- Cấu hình tham số database
- Tạo schema và bảng database
- Tạo user và phân quyền

### **Phần 3: Cấu hình AWS Secrets Manager**
- Lưu trữ thông tin xác thực database an toàn
- Cấu hình xoay vòng secret (tùy chọn)
- Cấp quyền Lambda truy cập secret
- Tạo VPC Endpoint để Lambda gọi Secrets Manager
- Tạo role cho Lambda truy cập RDS và Secrets Manager

### **Phần 4: Phát triển Lambda Functions**
- Tạo Lambda execution role với quyền phù hợp
- Viết Lambda function cho các thao tác CRUD
- Đóng gói và triển khai Lambda function
- Cấu hình biến môi trường
- Kết nối Lambda với RDS qua VPC
- Kiểm thử Lambda

### **Phần 5: Thiết lập API Gateway**
- Tạo REST API
- Định nghĩa resource và method
- Cấu hình Lambda proxy integration
- Thiết lập CORS cho tích hợp frontend
- Triển khai API ra một stage

### **Phần 6: Cấu hình Amazon Cognito**
- Tạo Cognito user pool
- Cấu hình thuộc tính và policy người dùng
- Thiết lập app client cho ứng dụng
- Cấu hình domain cho user pool
- Kiểm thử đăng ký và xác thực người dùng

### **Phần 7: Bảo mật API với Cognito**
- Tạo Cognito authorizer trong API Gateway
- Bảo vệ API endpoint bằng xác thực
- Kiểm thử API với token xác thực
- Xử lý token xác thực trong Lambda

### **Phần 8: Kiểm thử tích hợp**
- Kiểm thử endpoint không xác thực
- Kiểm thử endpoint có xác thực với token
- Kiểm thử thao tác database
- Kiểm thử xử lý lỗi
- (Tùy chọn) Tích hợp với frontend đã triển khai ở Phần 1

### **Phần 9: Giám sát và ghi nhật ký**
- Xem CloudWatch Logs của Lambda
- Giám sát chỉ số API Gateway
- Theo dõi hoạt động người dùng Cognito
- Thiết lập CloudWatch alarm

### **Phần 10: Dọn dẹp**
- Xóa tài nguyên đúng thứ tự
- Kiểm tra loại bỏ chi phí
- Lưu cấu hình cho lần sử dụng sau
