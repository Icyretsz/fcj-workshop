---
title: "Bản đề xuất"
date: "2025-09-15"
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Nền tảng Thử thách Gõ phím Thời gian Thực

<h2 style="text-align:center;">TypeRush</h2>

## Kiến trúc do AWS vận hành cho một ứng dụng gõ phím nhiều người chơi

### 1. Tóm tắt điều hành
TypeRush là một ứng dụng web tương tác tái hiện trải nghiệm cốt lõi của Monkeytype đồng thời giới thiệu chức năng nhiều người chơi cho các phiên gõ phím cạnh tranh trực tiếp. Được thiết kế như một bản trình diễn các công nghệ web thời gian thực và kiến trúc đám mây có khả năng mở rộng, nền tảng kết hợp trải nghiệm frontend hiện đại với backend serverless chạy trên AWS.

Người dùng có thể tạo hoặc tham gia phòng gõ phím, thi đấu theo thời gian thực và xem các chỉ số hiệu suất như Words Per Minute (WPM), độ chính xác và xếp hạng. Ứng dụng tận dụng React cho frontend, AWS Lambda và API Gateway (WebSocket) cho đồng bộ thời gian thực, Amazon RDS cho dữ liệu người chơi và Amazon ElastiCache cho dữ liệu phiên. Ứng dụng cũng sử dụng Amazon Bedrock để tạo thử thách hằng ngày. Amazon Cognito bảo đảm truy cập và xác thực người dùng. Giải pháp minh họa cách các dịch vụ cloud-native có thể cung cấp các ứng dụng thời gian thực nhanh, có khả năng mở rộng và chi phí hiệu quả mà không cần quản lý máy chủ truyền thống.

### 2. Tuyên bố vấn đề
### Vấn đề là gì?
Các nền tảng gõ phím hiện có thường bị giới hạn ở chế độ một người chơi, thiếu tương tác nhiều người chơi theo thời gian thực hoặc ví dụ triển khai mở. Hầu hết các giải pháp nhiều người chơi yêu cầu máy chủ chuyên dụng hoặc hạ tầng phức tạp, khiến chi phí cao và khó bảo trì. Đối với nhà phát triển và người đam mê, không có ví dụ serverless dễ tiếp cận nào thể hiện đồng bộ thời gian thực, cập nhật theo sự kiện và quản lý người dùng an toàn trong một hệ thống tích hợp.

### Giải pháp
Dự án này cung cấp một nền tảng gõ phím thời gian thực theo kiểu serverless cho phép nhiều người dùng kết nối, gõ đồng thời và thấy phản hồi tức thì về tiến độ của họ. Nó được thiết kế nhẹ, chi phí hiệu quả và có khả năng mở rộng.

### Lợi ích và Hoàn vốn (ROI)
Dự án vừa là nền tảng học tập cho nhà phát triển vừa là ứng dụng hướng tới người dùng. Nó trình diễn các thực tiễn tốt nhất trong kiến trúc hướng sự kiện, triển khai WebSocket và khả năng mở rộng dựa trên đám mây với chi phí vận hành tối thiểu. Đối với người dùng, nó mang lại trải nghiệm gõ phím mang tính xã hội, lôi cuốn. Đối với nhà phát triển, nó cung cấp tài liệu tham khảo giáo dục để xây dựng các hệ thống serverless thời gian thực.

Nền tảng không cần bảo trì máy chủ liên tục, bảo đảm:
* Tính bền vững dài hạn
* Khả năng mở rộng nhanh
* Chi phí vận hành thấp

### 3. Kiến trúc giải pháp
Nền tảng sử dụng kiến trúc AWS serverless để quản lý một ứng dụng thử thách gõ phím thời gian thực. Dữ liệu được xử lý và lưu trữ bằng nhiều dịch vụ AWS, và frontend là một giao diện hiện đại, đáp ứng. Kiến trúc chi tiết bên dưới:
![TypeRush architecture](/images/2-Proposal/typerush_diagram.png)

### Dịch vụ AWS sử dụng
- **Amazon S3**: Lưu trữ frontend tĩnh.
- **CloudFront**: Cung cấp CDN cho frontend và API.
- **AWS WAF**: Hoạt động như tường lửa cho CloudFront.
- **Route 53**: Quản lý tên miền và định tuyến DNS.
- **Amazon Cognito**: Xử lý đăng nhập và đăng ký người dùng.
- **API Gateway**: Cổng vào cho các microservice và kết nối tới ECS riêng tư qua VPC Link.
- **AWS Lambda**: Vận hành microservice record và text, và WebSocket API cho microservice game.
- **Amazon RDS**: Lưu trữ bản ghi và bảng xếp hạng.
- **DynamoDB**: Lưu trữ dữ liệu văn bản.
- **AWS Bedrock (Claude Sonnet 3)**: Hoạt động như mô hình LLM để tạo văn bản.
- **Amazon SNS**: Quản lý thông báo và cảnh báo.
- **AWS CloudWatch**: Xử lý logging và monitoring.
- **AWS Secrets Manager**: Lưu trữ bí mật cho cơ sở dữ liệu và API.
- **Amazon ECS (Fargate)**: Lưu trữ backend cốt lõi của game.
- **Elastic Load Balancer**: Cân bằng tải cho dịch vụ game.
- **ElastiCache (Redis)**: Cache dữ liệu game thời gian thực.
- **CodePipeline**: Điều phối build và triển khai.
- **CodeBuild**: Build image Docker và dịch vụ Lambda.
- **ECR (Elastic Container Registry)**: Lưu trữ image Docker.
- **GitLab**: Kích hoạt pipeline build.


### Thiết kế thành phần
- **Frontend Layer**: UI hiện đại, đáp ứng xây dựng bằng React, lưu trữ trên Amazon S3 và phục vụ qua Amazon CloudFront với AWS WAF và Amazon Route 53 để nâng cao hiệu năng, phân phối nội dung toàn cầu và bảo mật mạnh mẽ.
- **API & Giao tiếp thời gian thực**: AWS API Gateway đóng vai trò cổng hợp nhất cho cả REST và WebSocket API, triển khai rate limiting, xác thực và kiểm tra yêu cầu. WebSocket API cung cấp tương tác nhiều người chơi theo thời gian thực.
- **Compute & Business Logic**: AWS Lambda xử lý các tác vụ serverless như lưu trữ dữ liệu và tạo văn bản dùng AI. Amazon ECS với AWS Fargate lưu trữ các dịch vụ backend dạng container, bao gồm server trò chơi. VPC PrivateLink kết nối API Gateway với các container ECS Fargate, và Elastic Load Balancer phân phối lưu lượng.
- **Data Layer**: Amazon RDS lưu dữ liệu quan hệ như hồ sơ người dùng và bảng xếp hạng. Amazon DynamoDB quản lý dữ liệu phi quan hệ như văn bản tạo động. Amazon ElastiCache (Redis) cung cấp cache trong bộ nhớ cho dữ liệu truy cập thường xuyên.
- **Xác thực & Quản lý người dùng**: Amazon Cognito xử lý đăng ký, xác thực và ủy quyền an toàn, hỗ trợ đăng nhập xã hội và liên kết. Amazon Secrets Manager bảo mật lưu trữ các bí mật như khóa API.
- **CI/CD & DevOps**: AWS CodeBuild và AWS CodePipeline cho phép tích hợp liên tục và triển khai liên tục (CI/CD) trên tất cả các thành phần ứng dụng, tự động hóa build, test và triển khai.

### 4. Triển khai kỹ thuật
#### Các giai đoạn triển khai
Dự án này có hai phần — thiết lập hạ tầng đám mây và xây dựng ứng dụng — mỗi phần theo 4 giai đoạn:
- **Xây dựng lý thuyết và vẽ kiến trúc:** Nghiên cứu và thiết kế kiến trúc AWS serverless.
- **Tính giá và kiểm tra tính thực tiễn:** Sử dụng AWS Pricing Calculator để ước tính chi phí và điều chỉnh khi cần.
- **Sửa kiến trúc cho phù hợp chi phí/giải pháp:** Tinh chỉnh thiết kế để giữ chi phí hiệu quả và dễ dùng.
- **Phát triển, kiểm thử và triển khai:** Lập trình ứng dụng và các dịch vụ AWS, sau đó kiểm thử và phát hành lên production.

#### Yêu cầu kỹ thuật
- **Frontend:** Kiến thức thực hành về React.
- **Backend:** Kinh nghiệm với các dịch vụ AWS bao gồm Lambda, API Gateway, ECS, Fargate, RDS, DynamoDB và ElastiCache.
- **CI/CD:** Quen thuộc với CodePipeline, CodeBuild và GitLab.
- **Hạ tầng như Mã (IaC):** Sử dụng AWS CDK/SDK để mã hóa tương tác.
### 5. Dòng thời gian & Các mốc
**Dòng thời gian dự án**
- **Tháng 1:** Học tất cả về AWS và các dịch vụ AWS.
- **Tháng 2:** Bắt đầu lập kế hoạch cho dự án và các dịch vụ AWS sẽ triển khai.
- **Tháng 3:** Phát triển, triển khai và ra mắt.

### 6. Ước tính ngân sách

---> [Tệp Ước tính Ngân sách](/attachments/costs.pdf) <---

#### Chi phí hạ tầng

* Dịch vụ AWS:
    * Amazon Route 53: $0.90
    * AWS Web Application Firewall (WAF): $9.03
    * Amazon CloudFront: $0.40 ($0 với free tier)
    * S3 Standard: $0.11 ($0 với free tier)
    * Truyền dữ liệu: $0.00 ($0 với free tier)
    * Amazon API Gateway: $15.92 ($0 với free tier)
    * Amazon Cognito: $0.00
    * Network Load Balancer: $18.49
    * AWS Fargate: $8.88
    * AWS Lambda: $0.02 ($0 với free tier)
    * Amazon ElastiCache: $16.06
    * Amazon RDS for PostgreSQL: $39.37 ($0 với free tier)
    * AWS Bedrock (Workload 1): $2.63
    * DynamoDB: $0.03 ($0 với free tier)
    * AWS Secrets Manager: $4.00
    * AWS CodePipeline: $1.00 ($0 với free tier)
    * AWS CodeBuild: $5.00 ($0 với free tier)

- Với tài khoản free tier: khoảng $60/tháng
- Với tài khoản trả phí: khoảng $122/tháng
> Cả hai mức đều giả định dịch vụ chạy 24/7; thực tế có thể thấp hơn nhiều.

### 7. Đánh giá rủi ro
#### Ma trận rủi ro
- Tích hợp kỹ thuật: Ảnh hưởng trung bình, xác suất trung bình.
- Hiệu năng và Khả năng mở rộng: Ảnh hưởng cao, xác suất trung bình.
- Quản lý chi phí: Ảnh hưởng trung bình, xác suất trung bình.
- Bảo mật: Ảnh hưởng cao, xác suất thấp.
- Độ tin cậy dữ liệu: Ảnh hưởng trung bình, xác suất thấp.
- Rủi ro vận hành: Ảnh hưởng thấp, xác suất trung bình.
- Đường cong học tập: Ảnh hưởng trung bình, xác suất cao.

#### Chiến lược giảm thiểu
- Tích hợp kỹ thuật: Rủi ro này được giảm thiểu bằng triển khai theo giai đoạn và tuân thủ các thực tiễn tốt nhất của AWS.
- Hiệu năng và Khả năng mở rộng: Tối ưu giao tiếp WebSocket, tận dụng cache Redis và giám sát với CloudWatch để duy trì độ phản hồi.
- Quản lý chi phí: Kiểm soát qua AWS Budgets, cảnh báo sử dụng và chính sách auto-scaling.
- Bảo mật: Giảm thiểu bằng AWS WAF, Cognito, Secrets Manager và chính sách IAM chặt chẽ.
- Độ tin cậy dữ liệu: Ranh giới sở hữu dữ liệu rõ ràng và ghi chép giao dịch bảo đảm tính nhất quán.
- Rủi ro vận hành: Rủi ro trong pipeline CI/CD được giảm bằng triển khai theo giai đoạn và cấu hình rollback.
- Đường cong học tập: Được giải quyết thông qua học tập có chủ đích và thực hành trong giai đoạn đầu của dự án.

#### Kế hoạch dự phòng
- Nếu dịch vụ gặp sự cố: Một trang bảo trì sẽ hiển thị cho người dùng.
- Nếu chế độ nhiều người chơi quá chậm: Tạm thời vô hiệu hóa chế độ nhiều người chơi, nhưng chế độ một người chơi vẫn hoạt động.
- Nếu chi phí tăng đột biến: Thay đổi gây ra tăng chi phí sẽ được đảo ngược ngay lập tức.
- Nếu bản cập nhật mới làm hỏng trang: Hệ thống sẽ tự động quay lại phiên bản hoạt động trước đó.

### 8. Kết quả kỳ vọng
#### Cải tiến kỹ thuật:
Sau khi hoàn tất, dự án sẽ cung cấp một nền tảng gõ phím nhiều người chơi thời gian thực có khả năng mở rộng, an toàn và đầy đủ chức năng, được xây dựng hoàn toàn trên AWS.

#### Giá trị dài hạn:
Dự án sẽ minh họa các thực tiễn tốt nhất trong kiến trúc serverless, thiết kế hướng sự kiện và tự động hóa CI/CD. Với người dùng, nó mang lại trải nghiệm gõ phím nhiều người chơi mượt mà, hấp dẫn. Với chúng tôi, nó là mô hình tham chiếu thực hành đầu tiên để xây dựng các ứng dụng web serverless thời gian thực trên AWS với chi phí và bảo trì tối thiểu.
