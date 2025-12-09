---
title: "Workshop"
date: "2025-09-15"
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Xây dựng và Triển khai Ứng dụng Fullstack Serverless trên AWS

## Tổng quan Workshop

Chào mừng bạn đến với workshop thực hành toàn diện về xây dựng và triển khai ứng dụng fullstack serverless sẵn sàng cho production trên AWS. Qua ba phần, bạn sẽ học cách thiết kế kiến trúc, bảo mật và tự động hóa triển khai một ứng dụng web hiện đại sử dụng các dịch vụ serverless của AWS.

## Bạn sẽ xây dựng gì

Kết thúc chuỗi workshop này, bạn sẽ có một ứng dụng serverless hoàn chỉnh với các tính năng:

- **Frontend bảo mật**: Website tĩnh hiệu năng cao, phân phối toàn cầu qua CloudFront, bảo vệ bởi AWS WAF
- **Backend mở rộng**: RESTful APIs sử dụng Lambda, truy cập database an toàn
- **Xác thực người dùng**: Quản lý đăng ký, đăng nhập, phân quyền đầy đủ
- **Triển khai tự động**: CI/CD pipeline tự động build và deploy ứng dụng

## Cấu trúc Workshop

### Phần 1: Triển khai Frontend với CloudFront, WAF và S3
Học cách triển khai và bảo vệ ứng dụng frontend tĩnh bằng mạng phân phối nội dung của AWS. Bạn sẽ cấu hình CloudFront để phân phối toàn cầu, sử dụng S3 cho lưu trữ và bảo vệ ứng dụng với các quy tắc AWS WAF.

**Chủ đề chính:**
- Cấu hình S3 bucket cho website tĩnh
- Thiết lập và tối ưu CloudFront distribution
- Cấu hình quy tắc bảo mật AWS WAF
- Quản lý custom domain và SSL certificate

### Phần 2: Triển khai Backend với API Gateway, Lambda và RDS
Xây dựng hạ tầng backend bảo mật, mở rộng. Bạn sẽ tạo RESTful APIs, phát triển Lambda function, thiết lập database quản lý và tích hợp xác thực người dùng.

**Chủ đề chính:**
- Thiết kế và triển khai API Gateway REST API
- Phát triển và cấu hình Lambda function
- Thiết lập và kết nối RDS database
- Quản lý thông tin xác thực với AWS Secrets Manager
- Xác thực người dùng với Amazon Cognito
- Bảo vệ API bằng Cognito authorizer

## Yêu cầu trước khi bắt đầu

**Kiến thức cần có:**
- Hiểu cơ bản về kiến trúc ứng dụng web
- Quen với JavaScript/Node.js hoặc Python
- Biết sử dụng command line/terminal
- Hiểu về HTTP và REST APIs

**Công cụ cần thiết:**
- Tài khoản AWS với quyền admin
- Đã cài đặt và cấu hình AWS CLI
- Text editor hoặc IDE (nên dùng VS Code)
- Đã cài đặt Git

**Khuyến nghị:**
- Biết cơ bản về SQL
- Quen với JSON
- Có kinh nghiệm sử dụng Git

## Tổng quan Kiến trúc

![Diagram]( /images/5-Workshop/diagram.png)

## Kết quả học tập

Hoàn thành chuỗi workshop này, bạn sẽ:
- Thiết kế và triển khai kiến trúc serverless trên AWS
- Bảo mật ứng dụng web theo best practice
- Xây dựng luồng xác thực và phân quyền người dùng
- Quản lý secrets và thông tin xác thực database an toàn
- Xây dựng và duy trì pipeline tự động triển khai
- Tối ưu hiệu năng và chi phí
- Khắc phục các lỗi phổ biến khi triển khai serverless

## Cân nhắc chi phí

Workshop sử dụng các dịch vụ AWS có thể phát sinh chi phí. Chúng ta sẽ ưu tiên dùng các dịch vụ nằm trong Free Tier, nhưng bạn nên:
- Theo dõi billing dashboard thường xuyên
- Xóa tài nguyên sau mỗi phần nếu không tiếp tục
- Thiết lập billing alerts để tránh phát sinh phí ngoài ý muốn

Ước tính chi phí hoàn thành toàn bộ workshop: $5-$15 (nếu chưa dùng Free Tier trước đó)

## Hỗ trợ trong quá trình học

Trong suốt workshop, bạn sẽ có:
- Hướng dẫn từng bước kèm hình ảnh minh họa
- Mẫu code và template cấu hình
- Mẹo khắc phục lỗi phổ biến
- Link tài liệu AWS để tìm hiểu sâu hơn

## Sẵn sàng bắt đầu?

Hãy bắt đầu với Phần 1: Triển khai Frontend để xây dựng nền tảng cho ứng dụng serverless của bạn!

#### Nội dung

1. [Phần 1: Triển khai Frontend với CloudFront, WAF và S3](5.1-Frontend-deployment)
1. [Phần 2: Serverless Backend: Triển khai Backend với API Gateway, Lambda, RDS và Cognito](5.2-Serverless-backend)