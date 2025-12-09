---
title: "Worklog tuần 6"
date: "2025-09-15"
weight: 1
chapter: false
pre: " <b> 1.6. </b> "
---

### Mục tiêu tuần 6:

* Tìm hiểu về AWS security best practices và VPC networking
* Nghiên cứu cho kỳ thi AWS Certified Cloud Practitioner
* Phát triển core game logic cho Dự án cuối kỳ FCJ (Type Rush)

### Các nhiệm vụ cần thực hiện trong tuần này:
| Ngày | Nhiệm vụ                                                                                                                           | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo                                                                                                                                                    |
| --- |--------------------------------------------------------------------------------------------------------------------------------|------------|----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2   | - Nghiên cứu về bảo mật trên AWS Cloud: <br> + Cách truy cập riêng tư vào S3 với VPC Endpoints <br> - Tiếp tục xây dựng dự án FCJ | 10/06/2025 | 10/06/2025     | <https://000111.awsstudygroup.com/>                                                                                                                                   
| 3   | - Tiếp tục về bảo mật: <br> + AWS WAF <br> - Nghiên cứu về AWS cloud thông qua các câu hỏi AWS Certified Cloud Practitioner         | 10/07/2025 | 10/07/2025     | <https://000026.awsstudygroup.com/1-introduction/> <https://www.udemy.com/course/6-tests-aws-certified-cloud-practitioner-clf-c02-2025/> <https://aws.amazon.com/waf/> |
| 4   | - Tiếp tục về bảo mật: <br> + AWS WAF <br> - Tiếp tục xây dựng dự án FCJ                                                    | 10/08/2025 | 10/08/2025     | <https://aws.amazon.com/waf/>                                                                                                                             |
| 5   | - Họp nhóm FCJ hàng tuần                                                                                                      | 10/09/2025 | 10/09/2025     |                                                                                                                             |
| 6   | - Tiếp tục xây dựng dự án FCJ                                                                                                | 10/10/2025 | 10/10/2025     |                                                                                                                             |


### Thành tựu tuần 6:

* Đã học về **Truy cập Private S3 Buckets qua VPC Endpoints**
    * **Các khái niệm đã học:**
        * Gateway và Interface Endpoints cho phép truy cập các dịch vụ AWS một cách riêng tư mà không cần đi qua internet công cộng.
        * **Gateway Endpoints**: Được sử dụng cho S3 và DynamoDB để định tuyến lưu lượng từ bên trong VPC đến dịch vụ một cách an toàn.
        * **Interface Endpoints (AWS PrivateLink)**: Cho phép kết nối riêng tư đến các dịch vụ AWS, dịch vụ đối tác hoặc dịch vụ tùy chỉnh bằng cách sử dụng các IP riêng tư bên trong VPC.
        * **AWS Direct Connect**: Cung cấp kết nối riêng tư chuyên dụng giữa các trung tâm dữ liệu on-premises và AWS, bỏ qua internet công cộng.
        * **AWS Site-to-Site VPN**: Thiết lập kết nối được mã hóa an toàn giữa on-premises và AWS VPCs qua internet công cộng (rẻ hơn và dễ hơn Direct Connect).
    * **Những điểm chính rút ra:**
        * Gateway endpoints chỉ xử lý **lưu lượng outbound từ bên trong VPC**.
        * Môi trường on-prem phải sử dụng **Interface Endpoints** kết hợp với **VPN hoặc Direct Connect** để truy cập S3 riêng tư.
        * Các dịch vụ được quản lý bởi AWS như **CloudFront, Bedrock, SNS, SQS và Lambda (non-VPC)** hoạt động **bên ngoài mặt phẳng VPC**, do đó chúng có thể giao tiếp với các dịch vụ như S3 mà không cần endpoints.
        * Các tài nguyên dựa trên VPC (ví dụ: EC2, ECS, VPC-attached Lambda) yêu cầu **VPC endpoints hoặc NAT gateway** để truy cập S3 một cách an toàn.
    * **Thực hành:**
        * Tạo private S3 bucket và cấu hình Gateway VPC Endpoint cho S3.
        * Xác minh các EC2 instances trong private subnet có thể truy cập S3 mà không sử dụng Internet Gateway.
        * Kiểm tra tải lên S3 từ ECS để xác nhận định tuyến đúng thông qua VPC endpoint.
        * Quan sát các kết nối thất bại khi S3 bucket và instance ở các regions khác nhau, khắc phục bằng cách căn chỉnh regions.
        * Xem xét các phương pháp CloudFront và pre-signed URL để phục vụ nội dung S3 an toàn trong các ứng dụng web.
        * Mô phỏng môi trường on-prem bằng cách thiết lập một VPC riêng biệt và kết nối nó với S3 bằng cách sử dụng AWS Direct Connect.
* Đã học về các khái niệm bảo mật ứng dụng web
    * **Thực hành**: 
        * Khám phá và khai thác các rủi ro bảo mật phổ biến trên ứng dụng web trên OWASP Juice Shop
        * Khám phá về AWS WAF
        * Nghiên cứu 66 câu hỏi của kỳ thi AWS Certified Cloud Practitioner
* **Phát triển dự án FCJ**
    * Hoàn thiện **logic trò chơi chính:**
        * Thêm các chế độ tính giờ: 15, 30, 60, vô hạn
        * Xây dựng logic tính giờ, hiển thị giờ đếm ngược và tính toán kết quả khi kết thúc trò chơi
    * Thảo luận thêm với nhóm về dự án
        * Chế độ nhiều người chơi: 
          * Type Race: đua gõ chữ
          * Wave Rush: đua gõ chữ với nhiều vòng