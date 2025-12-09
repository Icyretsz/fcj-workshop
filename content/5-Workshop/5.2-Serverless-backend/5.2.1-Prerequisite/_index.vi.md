---
title : "Yêu cầu chuẩn bị"
date :  "2025-09-15" 
weight : 1 
chapter : false
pre : " <b> 5.2.1 </b> "
---

### Kiến thức AWS cần thiết

- **Phần 1: Hoàn thành triển khai Frontend**: Biết cách sử dụng AWS Console, các dịch vụ cơ bản; Có sẵn ứng dụng frontend để kết nối
- **HTTP/REST APIs**: Hiểu các phương thức HTTP (GET, POST, PUT, DELETE) và nguyên lý REST
- **Database Basics**: Hiểu về cơ sở dữ liệu quan hệ, bảng, và truy vấn SQL
- **JSON**: Quen thuộc với định dạng JSON cho request/response API
- **Basic Networking**: Hiểu các khái niệm như VPC, subnet, security group
- **IAM**: Quen với IAM roles và policies. Áp dụng best practice về least-privileges cho tài nguyên

### Kỹ năng kỹ thuật cần thiết

- **Lập trình**: Kiến thức trung cấp về một trong các ngôn ngữ:
    - **Node.js** (JavaScript/TypeScript) - Khuyến nghị cho workshop này
- **SQL Queries**: Biết sử dụng SELECT, INSERT, UPDATE, DELETE cơ bản
- **API Testing**: Sử dụng các công cụ như Postman hoặc curl
- **Command Line**: Thoải mái với các lệnh terminal
- **Environment Variables**: Hiểu về quản lý cấu hình qua biến môi trường

### Thiết lập tài khoản AWS cần có

Trước khi bắt đầu, hãy đảm bảo bạn có:

1. **Tài khoản AWS**
    - Tài khoản AWS đang hoạt động với quyền admin hoặc đủ quyền cho:
        - Lambda
        - API Gateway
        - RDS
        - Cognito
        - Secrets Manager
        - VPC
        - IAM
        - CloudWatch Logs

2. **IAM User/Role Permissions**
    - Các managed policy cần thiết:
        - `AWSLambda_FullAccess`
        - `AmazonAPIGatewayAdministrator`
        - `AmazonRDSFullAccess`
        - `AmazonCognitoPowerUser`
        - `SecretsManagerReadWrite`
        - `AmazonVPCFullAccess`
        - `IAMFullAccess` (tạo Lambda execution roles)
        - `CloudWatchLogsFullAccess`

3. **Cấu hình Billing Alerts**
    - Thiết lập AWS Budgets hoặc billing alerts
    - Ngưỡng khuyến nghị: $20-30 cho workshop này
    - RDS có thể phát sinh chi phí cao hơn so với workshop trước

### Công cụ và phần mềm cần cài đặt

Cài đặt các công cụ sau trên máy tính cá nhân:

1. **AWS CLI (Version 2) (tùy chọn)**
    - Tải: https://aws.amazon.com/cli/
    - Kiểm tra: `aws --version`
    - Đảm bảo đã cấu hình credentials: `aws configure`

2. **Node.js và npm** (nếu dùng Node.js cho Lambda)
    - Tải: https://nodejs.org/ (nên dùng bản LTS)
    - Phiên bản tối thiểu: Node.js 18.x trở lên
    - Kiểm tra: `node --version` và `npm --version`

3. **Python** (nếu dùng Python cho Lambda)
    - Tải: https://www.python.org/
    - Phiên bản tối thiểu: Python 3.9 trở lên
    - Kiểm tra: `python --version` hoặc `python3 --version`
    - Đảm bảo có pip: `pip --version`

4. **Git**
    - Tải: https://git-scm.com/
    - Kiểm tra: `git --version`

5. **Text Editor hoặc IDE**
    - VS Code (khuyến nghị): https://code.visualstudio.com/
    - Extension khuyến nghị:
        - AWS Toolkit
        - ESLint (cho JavaScript)
        - Python (nếu dùng Python)

6. **API Testing Tool**
    - **Postman** (khuyến nghị): https://www.postman.com/downloads/
    - Hoặc **Insomnia**: https://insomnia.rest/download
    - Hoặc **curl** (command line)

7. **Database Client** (Tùy chọn, hữu ích)
    - **DBeaver** (miễn phí, hỗ trợ PostgreSQL): https://dbeaver.io/
    - Hoặc **pgAdmin**: https://www.pgadmin.org/
    - Hoặc **psql** command line tool

### Sample Application Code

Chúng tôi sẽ cung cấp mã mẫu Lambda function và script SQL:

**Cách 1**: Clone repository workshop
```bash
git clone https://github.com/your-workshop/serverless-app-backend.git
cd serverless-app-backend
```

**Cách 2**: Viết code từ đầu theo hướng dẫn
- Tất cả code snippet sẽ có trong tutorial
- Phù hợp để học và hiểu từng thành phần

### Tùy chọn: Đã hoàn thành Phần 1: Frontend Deployment

Không bắt buộc, nhưng nếu đã hoàn thành Phần 1 sẽ giúp bạn:
- Hiểu cách frontend sử dụng các API này
- Có ứng dụng end-to-end hoàn chỉnh

**Nếu bạn bỏ qua Phần 1:**
- Vẫn có thể hoàn thành workshop này độc lập
- Sẽ test API bằng Postman thay vì frontend
- Có thể tích hợp với bất kỳ frontend nào sau này

### Ước tính chi phí cho Workshop 2

**Ước tính chi phí:**

**Trong Free Tier (12 tháng đầu):**
- RDS (single-AZ, giới hạn giờ): $0-5
- Lambda: $0
- API Gateway: $0
- Cognito: $0
- Secrets Manager: $0 (30 ngày trial)
- **Tổng**: $0-5

### Checklist trước khi bắt đầu

Trước khi bắt đầu, hãy xác nhận bạn đã:

- [ ] Có tài khoản AWS với đủ quyền
- [ ] Đã cài đặt và cấu hình AWS CLI
- [ ] Đã cài Node.js/Python (tùy chọn)
- [ ] Đã cài Git
- [ ] Đã cài code editor/IDE
- [ ] Đã cài công cụ test API (nên dùng Postman)
- [ ] Đã cấu hình billing alerts ở mức $20
- [ ] Đã clone repo code mẫu (hoặc sẵn sàng viết từ đầu)
- [ ] Có ít nhất 3-4 tiếng tập trung cho workshop
- [ ] Hiểu về REST API và database

### Bạn sẽ học được gì?

Hoàn thành workshop này, bạn sẽ:

- Thiết kế và triển khai kiến trúc API serverless
- Tạo và cấu hình Lambda function với quyền phù hợp
- Thiết lập và bảo mật RDS database trong VPC
-   Xây dựng API Gateway REST API với nhiều endpoint
-   Cấu hình Cognito user pool và luồng xác thực
-   Tích hợp Cognito authorizer với API Gateway
-   Quản lý secrets an toàn với AWS Secrets Manager
- Hạn chế truy cập API Gateway bằng Lambda và Secrets Manager
-   Kết nối Lambda với RDS database
-   Xử lý lỗi và logging đúng chuẩn
-   Test API với authentication token

## Sẵn sàng bắt đầu?

Khi đã hoàn thành các bước chuẩn bị và xác nhận setup, bạn đã sẵn sàng xây dựng hạ tầng serverless backend an toàn, mở rộng!

### Kết quả mong đợi

Kết thúc workshop, bạn sẽ có:
- Một REST API hoàn chỉnh với nhiều endpoint
- Xác thực và phân quyền người dùng an toàn
- Lưu trữ dữ liệu với database
- Logging và monitoring chuyên nghiệp
- Thực hành bảo mật production-ready
- Backend tích hợp với frontend đã triển khai ở Phần 1 (nếu có)

### Bước tiếp theo

Chuyển sang **Phần 1: Thiết lập VPC và mạng** để tạo nền tảng mạng bảo mật cho database và Lambda function.

---

**Cùng xây dựng serverless backend nào!** 🚀
