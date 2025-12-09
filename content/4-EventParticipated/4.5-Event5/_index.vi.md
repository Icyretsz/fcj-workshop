---
title: "DevOps trên AWS"
date: "`r Sys.Date()`"
weight: 5
chapter: false
pre: " <b> 4.5. </b> "
---

# Bài thu hoạch

### Mục tiêu Sự kiện

- Xây dựng tư duy DevOps, bao quát văn hóa, các nguyên tắc và những chỉ số hiệu năng quan trọng.
- Đi sâu vào cách xây dựng CI/CD pipelines sử dụng các AWS DevOps services native.
- Giới thiệu các nguyên tắc Infrastructure as Code (IaC) với AWS CloudFormation và AWS CDK.
- Khám phá hệ sinh thái container trên AWS, bao gồm Docker, Amazon ECR, ECS, EKS và App Runner.
- Minh họa cách triển khai monitoring và observability toàn diện sử dụng CloudWatch và AWS X-Ray.

### Diễn giả

- **Truong Quang Tinh** - Platform Engineer (TymeX), AWS Community Builder
- **Bao Huynh** - AWS Community Builder
- **Thinh Nguyen** - AWS Community Builder
- **Vi Tran** - AWS Community Builder
- **Van Hoang Kha** – Cloud Engineer, AWS Community Builder
- **Long Huynh** - AWS Community Builder
- **Quy Pham** - AWS Community Builder
- **Nghiem Le** - AWS Community Builder

### Những điểm nhấn chính

#### Từ Manual Operations đến Infrastructure as Code (IaC)

- Workshop nhấn mạnh những hạn chế của "ClickOps" (quản lý thủ công qua console), như chậm, dễ lỗi và khó tái lập.
- **AWS CloudFormation**: Được giới thiệu như giải pháp IaC native trên AWS, sử dụng YAML/JSON template để định nghĩa và quản lý AWS resources theo dạng "Stacks" và có khả năng phát hiện configuration drift.
- **AWS Cloud Development Kit (CDK)**: Được trình bày như một framework IaC thân thiện với developer, cho phép định nghĩa hạ tầng bằng các ngôn ngữ lập trình quen thuộc (ví dụ: Python, TypeScript), sử dụng các "Constructs" tái sử dụng để tăng tốc phát triển.

#### Xây dựng CI/CD Pipeline Hoàn Chỉnh

- Một pipeline tự động, end‑to‑end được demo sử dụng bộ AWS developer tools:
    - **AWS CodeCommit**: Dùng cho secure source control.
    - **AWS CodeBuild**: Dùng cho automated builds và testing.
    - **AWS CodeDeploy**: Dùng để quản lý các chiến lược deploy phức tạp như Blue/Green và Canary releases.
    - **AWS CodePipeline**: Dùng để orchestrate toàn bộ release process từ source đến deployment.

#### Containerization và Orchestration

- Phiên chia sẻ bao quát fundamentals của containerization với Docker và tầm quan trọng của container registry như **Amazon ECR** để lưu trữ và scan images.
- Một phần so sánh chi tiết giữa các dịch vụ orchestration được trình bày:
    - **Amazon ECS**: Dịch vụ native trên AWS, đơn giản hơn, tích hợp sâu với hệ sinh thái AWS, phù hợp với các team muốn giảm tối đa operational overhead.
    - **Amazon EKS**: Dịch vụ managed Kubernetes tuân theo chuẩn open‑source, mang lại tính linh hoạt cao và khả năng multi‑cloud portability, nhưng đi kèm độ phức tạp vận hành lớn hơn.
    - **AWS Fargate & App Runner**: Các tùy chọn serverless compute giúp không cần quản lý servers bên dưới cho containers, đơn giản hóa việc deploy và vận hành.

#### Monitoring và Observability

- Tầm quan trọng của full‑stack observability được nhấn mạnh để vận hành và debug các distributed systems.
- **Amazon CloudWatch**: Được dùng để thu thập metrics, logs và thiết lập alarms, dashboards.
- **AWS X-Ray**: Được demo cho distributed tracing nhằm phân tích và debug performance bottlenecks trong microservices architectures.

### Bài học Chính (Key Takeaways)

#### Tư duy Thiết kế (Design Mindset)

- **Automate Everything**: Chuyển từ "ClickOps" thủ công sang cách tiếp cận IaC tự động hóa hoàn toàn để đảm bảo tính nhất quán, tốc độ và độ tin cậy.
- **Infrastructure as Code is Non‑Negotiable**: IaC là nền tảng của DevOps hiện đại, cho phép cộng tác, versioning và tái lập môi trường một cách dễ dàng.
- **Choose the Right Tool for the Team**: Việc lựa chọn giữa CloudFormation, CDK, ECS và EKS cần dựa trên kỹ năng team, nhu cầu hệ sinh thái và mức độ cân bằng mong muốn giữa đơn giản và quyền kiểm soát.

#### Kiến trúc Kỹ thuật (Technical Architecture)

- **CI/CD Pipelines**: Mỗi project nên có một automated pipeline để xử lý code integration, testing và deployment, đảm bảo release nhanh và an toàn.
- **Container‑First cho Microservices**: Sử dụng containers để đóng gói ứng dụng và dependencies, cùng với một orchestrator (ECS hoặc EKS) để quản lý ở quy mô lớn.
- **Full‑Stack Observability**: Xây dựng chiến lược monitoring mạnh với metrics, logs (CloudWatch) và distributed tracing (X‑Ray) để có cái nhìn sâu về performance và sức khỏe ứng dụng.

#### Chiến lược Hiện đại hóa (Modernization Strategy)

- **Phased Adoption**: Áp dụng DevOps một cách tuần tự. Bắt đầu bằng việc chuyển đổi một quy trình thủ công sang IaC hoặc xây dựng một CI/CD pipeline cho một service duy nhất.
- **Leverage Serverless**: Tận dụng các tùy chọn serverless như AWS Fargate và App Runner để giảm độ phức tạp vận hành và cho phép team tập trung vào application logic thay vì quản lý hạ tầng.
- **Measure What Matters**: Tập trung vào các DevOps metrics quan trọng như Deployment Frequency, Lead Time for Changes và Mean Time to Recovery (MTTR) để thúc đẩy cải tiến liên tục.

### Ứng dụng vào Công việc

- **Tự động hóa một Deployment**: Chuyển một ứng dụng đang deploy thủ công sang sử dụng AWS CodePipeline workflow.
- **Mã hóa Hạ tầng (Codify Infrastructure)**: Định nghĩa một S3 bucket hoặc EC2 instance hiện có bằng một AWS CloudFormation template hoặc một ứng dụng CDK.
- **Containerize một Ứng dụng**: Tạo Dockerfile cho một web application và push image đó lên Amazon ECR.
- **Thử nghiệm Container Service**: Deploy một ứng dụng container hóa đơn giản bằng AWS App Runner hoặc ECS với Fargate launch type.
- **Cải thiện Observability**: Tạo một CloudWatch Dashboard cho một ứng dụng quan trọng và cấu hình alarms cho các metrics then chốt như CPU utilization và error rates.

### Trải nghiệm Sự kiện

Tham dự workshop "DevOps on AWS" mang lại giá trị rất lớn, cung cấp một hướng dẫn toàn diện và thực tiễn về cách triển khai DevOps hiện đại trên nền tảng cloud.

#### Học hỏi từ các diễn giả giàu kinh nghiệm

- Các AWS Community Builders chia sẻ kiến thức sâu sắc, thực tiễn, giúp chia nhỏ các chủ đề phức tạp thành những khái niệm dễ hiểu.

#### Trải nghiệm kỹ thuật hands‑on

- Nhiều live demo, bao gồm walkthrough một CI/CD pipeline hoàn chỉnh và deployment microservices trên ECS, giúp tôi thấy rõ ngữ cảnh thực tế cho các công cụ và dịch vụ được giới thiệu.

#### Tận dụng công cụ hiện đại

- Workshop cung cấp cái nhìn đầy đủ về bộ AWS DevOps toolkit hiện đại, từ IaC nâng cao với CDK đến serverless containers với App Runner.

#### Kết nối và thảo luận

- Phiên Q&A tạo cơ hội trao đổi về lộ trình nghề nghiệp và AWS certification roadmap, mang lại định hướng giá trị cho phát triển chuyên môn.

#### Bài học rút ra

- Việc áp dụng IaC là bước đòn bẩy quan trọng nhất để đạt đến một thực hành DevOps trưởng thành.
- AWS cung cấp một bộ công cụ tích hợp, đầy đủ để xây dựng một DevOps platform hiện đại, với các lựa chọn phù hợp cho đội ngũ ở nhiều quy mô và mức kỹ năng khác nhau.
- Monitoring và observability hiệu quả không phải là tùy chọn; chúng là yếu tố bắt buộc để vận hành các ứng dụng cloud đáng tin cậy và hiệu năng cao.

#### Một số hình ảnh sự kiện
{{< image-grid >}}
<img src="/images/event5.2.jpeg" alt="Hình ảnh sự kiện 1">
<img src="/images/event5.4.jpeg" alt="Hình ảnh sự kiện 2">
<img src="/images/event5.3.jpeg" alt="Hình ảnh sự kiện 3">
<img src="/images/event5.1.jpeg" alt="Hình ảnh sự kiện 4">
{{< /image-grid >}}
