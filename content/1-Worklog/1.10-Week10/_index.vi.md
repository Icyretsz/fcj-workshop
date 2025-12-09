---
title: "Worklog tuần 10"
date: "2025-09-15"
weight: 1
chapter: false
pre: " <b> 1.10. </b> "
---

### Mục tiêu tuần 10:

* Hoàn thành phần backend deployment của serverless workshop
* Tích hợp frontend với các dịch vụ backend
* Kiểm tra chức năng end-to-end của ứng dụng serverless

### Các nhiệm vụ cần thực hiện trong tuần này:
| Ngày | Nhiệm vụ                                                                             | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo                                                                                                                            |
| --- |----------------------------------------------------------------------------------|------------|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------|
| 2   | - Thiết lập VPC và Network<br/>- Cấu hình RDS Database | 03/11/2025 | 03/11/2025 | <https://docs.aws.amazon.com/vpc/><br/><https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/> |
| 3   | - Cấu hình AWS Secrets Manager<br/>- Phát triển Lambda Functions | 04/11/2025 | 04/11/2025 | <https://docs.aws.amazon.com/secretsmanager/><br/><https://docs.aws.amazon.com/lambda/> |
| 4   | - Thiết lập API Gateway<br/>- Cấu hình Amazon Cognito | 05/11/2025 | 05/11/2025 | <https://docs.aws.amazon.com/apigateway/><br/><https://docs.aws.amazon.com/cognito/> |
| 5   | - Tích hợp Frontend với Backend<br/>- Kiểm tra end-to-end | 06/11/2025 | 06/11/2025 | |
| 6   | - Hoàn thiện tài liệu workshop<br/>- Clean up và xem xét tối ưu chi phí | 07/11/2025 | 07/11/2025 | |


### Thành tựu tuần 10:

**Part 2: Backend Deployment - Thiết lập VPC và Network**
* Thiết kế và triển khai kiến trúc VPC an toàn:
    * Tạo VPC với CIDR block phù hợp (ví dụ: 10.0.0.0/16).
    * Cấu hình public và private subnets trên nhiều Availability Zones.
    * Thiết lập Internet Gateway cho public subnet internet access.
    * Tạo NAT Gateway trong public subnet cho private subnet outbound traffic.
* Routing và bảo mật mạng:
    * Cấu hình route tables cho public và private subnets.
    * Liên kết subnets với route tables phù hợp.
    * Tạo security groups cho Lambda, RDS và các resources khác.
    * Tài liệu hóa security group rules và best practices.
* VPC endpoints cho các dịch vụ AWS:
    * Tạo VPC endpoints cho S3 (Gateway endpoint).
    * Thiết lập VPC endpoints cho Secrets Manager và các dịch vụ khác (Interface endpoints).
    * Cấu hình security groups và route tables cho endpoint access.
    * Kiểm tra kết nối riêng tư đến các dịch vụ AWS.

**Thiết lập RDS Database**
* Provisioning RDS database instance:
    * Chọn database engine phù hợp (ví dụ: PostgreSQL, MySQL).
    * Chọn instance class dựa trên yêu cầu workload.
    * Cấu hình Multi-AZ deployment cho high availability.
    * Đặt RDS trong private subnets cho bảo mật.
* Cấu hình database:
    * Thiết lập database subnet group bao trùm nhiều AZs.
    * Cấu hình security group chỉ cho phép truy cập từ Lambda.
    * Bật automated backups với retention period phù hợp.
    * Cấu hình encryption at rest sử dụng AWS KMS.
* Khởi tạo database:
    * Kết nối đến RDS instance từ bastion host hoặc Cloud9.
    * Tạo database schema và tables.
    * Thiết lập database users với permissions phù hợp.
    * Chèn sample data để kiểm tra.
* RDS monitoring và optimization:
    * Bật Enhanced Monitoring và Performance Insights.
    * Cấu hình CloudWatch alarms cho CPU, memory và connections.
    * Thiết lập automated snapshots và backup verification.
    * Tài liệu hóa database connection parameters.

**Cấu hình AWS Secrets Manager**
* Triển khai quản lý credential an toàn:
    * Tạo secrets cho RDS database credentials.
    * Lưu trữ API keys và cấu hình nhạy cảm khác.
    * Cấu hình secret rotation cho database passwords.
    * Thiết lập IAM policies cho Lambda để truy cập secrets.
* Cấu trúc và tổ chức secret:
    * Tổ chức secrets với quy ước đặt tên rõ ràng.
    * Lưu trữ connection strings và environment-specific configs.
    * Tài liệu hóa secret ARNs cho Lambda environment variables.
    * Triển khai least-privilege access policies.
* Tích hợp với Lambda và RDS:
    * Cấu hình Lambda functions để retrieve secrets tại runtime.
    * Triển khai caching strategy cho secret values.
    * Thiết lập automatic secret rotation với Lambda rotation function.
    * Kiểm tra secret retrieval và connection establishment.

**Phát triển Lambda Functions**
* Phát triển các serverless backend functions:
    * Tạo Lambda functions cho các CRUD operations.
    * Triển khai database connection pooling cho hiệu quả.
    * Viết handler functions cho các API endpoints khác nhau.
    * Cấu hình runtime phù hợp (Node.js, Python, v.v.).
* Cấu hình và tối ưu hóa Lambda:
    * Đặt memory allocation và timeout values.
    * Cấu hình environment variables cho configuration.
    * Gắn Lambda vào VPC để truy cập RDS.
    * Thiết lập Lambda layers cho shared dependencies.
* Error handling và logging:
    * Triển khai error handling toàn diện.
    * Cấu hình CloudWatch Logs cho debugging.
    * Thêm structured logging với correlation IDs.
    * Thiết lập CloudWatch alarms cho function errors.
* Lambda security best practices:
    * Tạo IAM execution role với minimal permissions.
    * Cấp quyền truy cập Secrets Manager, RDS và CloudWatch.
    * Bật X-Ray tracing cho performance monitoring.
    * Cấu hình VPC security groups cho Lambda functions.

**Thiết lập API Gateway**
* Tạo REST API trong API Gateway:
    * Thiết kế RESTful API với resources và methods phù hợp.
    * Cấu hình Lambda proxy integration cho backend functions.
    * Thiết lập path parameters và query string handling.
    * Triển khai proper HTTP status code responses.
* Cấu hình bảo mật API Gateway:
    * Cấu hình Cognito User Pool làm authorizer.
    * Thiết lập authorization trên các protected endpoints.
    * Triển khai request validation schemas.
    * Cấu hình throttling và rate limiting.
* Cấu hình CORS:
    * Bật CORS cho frontend domain.
    * Cấu hình allowed origins, headers và methods.
    * Thiết lập OPTIONS method cho preflight requests.
    * Kiểm tra cross-origin requests từ frontend.
* API deployment và stages:
    * Tạo deployment stages (dev, staging, prod).
    * Cấu hình stage variables cho environment-specific settings.
    * Bật CloudWatch Logs cho API Gateway.
    * Thiết lập custom domain name cho API (tùy chọn).

**Cấu hình Amazon Cognito**
* Thiết lập Cognito User Pool:
    * Tạo user pool cho authentication.
    * Cấu hình sign-up và sign-in attributes.
    * Đặt password policies và MFA options.
    * Cấu hình email verification và user confirmation.
* Tùy chỉnh User Pool:
    * Tùy chỉnh email templates cho verification và welcome messages.
    * Thiết lập hosted UI để kiểm tra authentication flow.
    * Cấu hình password recovery process.
    * Thêm custom attributes cho user profiles.
* Cấu hình App client:
    * Tạo app client cho frontend application.
    * Cấu hình OAuth 2.0 flows và grant types.
    * Đặt callback URLs và sign-out URLs.
    * Generate client ID và cấu hình scopes.
* Tích hợp Cognito với API Gateway:
    * Cấu hình API Gateway Cognito authorizer.
    * Thiết lập token validation và claims mapping.
    * Kiểm tra authenticated API requests với JWT tokens.
    * Tài liệu hóa authentication flow cho frontend developers.

**Tích hợp Frontend với Backend**
* Tích hợp frontend với API Gateway:
    * Cập nhật frontend code để gọi API endpoints.
    * Triển khai API client với proper error handling.
    * Thêm loading states và user feedback.
    * Cấu hình API base URL trong frontend configuration.
* Tích hợp Authentication:
    * Tích hợp AWS Amplify hoặc Cognito SDK trong frontend.
    * Triển khai sign-up, sign-in và sign-out flows.
    * Thêm authentication state management.
    * Cấu hình token storage và refresh mechanism.
* API request authentication:
    * Triển khai JWT token extraction từ Cognito.
    * Thêm Authorization header vào API requests.
    * Xử lý token expiration và refresh.
    * Triển khai logout và session management.
* Cải tiến User interface:
    * Tạo protected routes yêu cầu authentication.
    * Thêm user profile display với Cognito data.
    * Triển khai forms cho data submission đến backend.
    * Thêm data display từ database qua API.

**Kiểm tra End-to-End**
* Kiểm tra ứng dụng toàn diện:
    * Kiểm tra complete user registration và authentication flow.
    * Xác minh API calls với authenticated và unauthenticated users.
    * Kiểm tra CRUD operations thông qua frontend interface.
    * Xác thực data persistence trong RDS database.
* Kiểm tra bảo mật:
    * Xác minh rằng unauthenticated requests bị chặn.
    * Kiểm tra JWT token validation và expiration handling.
    * Xác nhận cấu hình CORS hoạt động đúng.
    * Xác thực SQL injection protection và input sanitization.
* Kiểm tra hiệu suất:
    * Kiểm tra Lambda cold start times và warm execution.
    * Đo API response times từ frontend.
    * Xác minh CloudFront caching của static assets.
    * Kiểm tra ứng dụng dưới simulated load.
* Kiểm tra error scenarios:
    * Kiểm tra error handling cho invalid inputs.
    * Xác minh graceful degradation khi services không khả dụng.
    * Kiểm tra network error handling và retry logic.
    * Xác thực user-friendly error messages.

**Hoàn thiện tài liệu Workshop**
* Hoàn thành hướng dẫn workshop toàn diện:
    * Viết hướng dẫn từng bước chi tiết cho tất cả các components.
    * Bao gồm architecture diagrams và service interaction flows.
    * Thêm phần troubleshooting cho các vấn đề phổ biến.
    * Tạo cleanup guide để xóa tất cả resources.
* Code samples và templates:
    * Cung cấp Lambda function code templates.
    * Bao gồm CloudFormation templates cho infrastructure (tùy chọn).
    * Tạo sample frontend application với comments.
    * Chia sẻ API request examples và authentication code.
* Tài liệu hóa tối ưu chi phí:
    * Tài liệu hóa estimated costs cho việc chạy workshop.
    * Cung cấp chiến lược để minimize costs trong quá trình học.
    * Bao gồm cleanup checklist để tránh ongoing charges.
    * Đề xuất sử dụng AWS Free Tier nếu có thể.

**Clean Up và quản lý Resources**
* Tạo quy trình cleanup toàn diện:
    * Tài liệu hóa thứ tự xóa resources từng bước.
    * Nhấn mạnh tầm quan trọng của việc xóa S3 buckets và RDS instances.
    * Bao gồm CloudFormation stack deletion nếu sử dụng.
    * Cung cấp verification steps để đảm bảo tất cả resources đã được xóa.
* Monitoring chi phí:
    * Thiết lập billing alerts cho unexpected charges.
    * Xem xét AWS Cost Explorer cho workshop expenses.
    * Tài liệu hóa best practices cho cost control.
    * Chia sẻ tips để ngăn chặn accidental resource provisioning.

**Những điểm rút ra chính và bài học**
* Hiểu biết kỹ thuật:
    * Tầm quan trọng của proper VPC design cho bảo mật và hiệu suất.
    * Lambda cold starts và optimization strategies.
    * Database connection management trong serverless architectures.
    * JWT token handling và security best practices.
* Quyết định kiến trúc:
    * Đánh đổi giữa serverless và traditional architectures.
    * Khi nào sử dụng RDS so với DynamoDB cho các use cases khác nhau.
    * API Gateway so với Application Load Balancer considerations.
    * Multi-region deployment strategies cho global applications.
* Cải tiến workshop đã xác định:
    * Thêm ví dụ cho các use cases phổ biến.
    * Video tutorials để bổ sung tài liệu viết.
    * Infrastructure as Code templates cho setup nhanh hơn.
    * Advanced topics để học thêm.

**Cải tiến Workshop trong tương lai**
* Các bổ sung đã lập kế hoạch cho các phiên bản tương lai:
    * CI/CD pipeline setup với AWS CodePipeline.
    * Monitoring và observability với X-Ray và CloudWatch.
    * Advanced security với AWS Shield và security headers.
    * Multi-environment deployment strategies.
    * WebSocket API implementation với API Gateway.
    * GraphQL API alternative với AWS AppSync.
    * Containerized deployments với ECS/Fargate.
    * Infrastructure as Code với Terraform hoặc CDK.