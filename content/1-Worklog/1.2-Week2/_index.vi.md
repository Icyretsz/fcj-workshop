---
title: "Worklog Tuần 2"
date: "2025-09-15"
weight: 1
chapter: false
pre: " <b> 1.2. </b> "
---

### Mục tiêu Tuần 2:

* Kết nối và làm quen với các thành viên trong First Cloud Journey.
* Hiểu các dịch vụ cơ bản của AWS, cách sử dụng Console & CLI.

### Các công việc cần triển khai trong tuần này:
| Ngày | Nhiệm vụ                                                                                                                                                                                 | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo                                                                                                                                                                                                       |
| ---- |------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------|-----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2    | - Gặp gỡ các thành viên trong nhóm để thảo luận về project đầu tiên                                                                                                                      | 09/15/2025   | 09/15/2025      |
| 3    | - Học về **AWS Networking** cơ bản <br> + VPC <br> + Subnet <br> + Security groups <br> + Gateways <br> + Endpoints <br> + Route tables                                                  | 09/16/2025   | 09/16/2025      | <https://docs.aws.amazon.com/vpc/?icmpid=docs_homepage_networking> <https://www.youtube.com/watch?v=2doSoMN2xvI&t=127s> <https://www.youtube.com/watch?v=7_NNlnH7sAg> <https://000003.awsstudygroup.com/>               |
| 4    | - Học về **Amazon EC2** cơ bản <br> + All about instances <br> + Managing security groups <br> + SSH methods to instance <br> + Session Manager <br> + AMI <br> + EBS Volume             | 09/17/2025   | 09/17/2025      | <https://docs.aws.amazon.com/ec2/?icmpid=docs_homepage_compute> <https://www.youtube.com/watch?v=-t148tYgnJU&pp=ygUDZWMy> <https://www.youtube.com/watch?v=eaicwmnSdCs&pp=ygUDZWMy> <https://000004.awsstudygroup.com/> |
| 5    | - **Practice:** <br> + Tạo fully-functional VPC <br> + Tạo EC2 instance chạy trên VPC vừa tạo <br> + Thử nhiều phương thức kết nối tới EC2 instance <br> + Dùng Reachability Analyzer     | 09/18/2025   | 09/18/2025      | <https://www.youtube.com/watch?v=-t148tYgnJU&pp=ygUDZWMy> <https://www.youtube.com/watch?v=eaicwmnSdCs&pp=ygUDZWMy> <https://000004.awsstudygroup.com/>                                                                 |
| 6    | - Học về **Amazon S3** cơ bản <br> + Data storage <br> + Static hosting <br> + Bucket security practices                                                                                 | 09/19/2025   | 09/20/2025      | <https://docs.aws.amazon.com/s3/?icmpid=docs_homepage_serverless> <https://000057.awsstudygroup.com/>                                                                                                                   |

### Kết quả đạt được tuần 2:

* Gặp gỡ teammates và thảo luận kế hoạch cho project đầu tiên.
    * Xác định chủ đề của project
    * Tạo team Figma
    * Thảo luận về kiến trúc hệ thống
    * Phân chia nhiệm vụ ban đầu
    * Khởi tạo Github repo

* Học kiến thức cơ bản về **AWS Networking**:
    * Hiểu vai trò của **VPC** trong việc cô lập và bảo mật tài nguyên.
    * Thiết lập và cấu hình **Subnets**.
    * Quản lý **Security groups** để kiểm soát inbound/outbound traffic.
    * Học về **Gateways**, **Endpoints**, **Route tables** và cách chúng điều hướng traffic trong và ngoài VPC.

* Nắm kiến thức chuyên sâu về **Amazon EC2**:
    * Hiểu **EC2 instances** là gì, tìm hiểu các **instance families** (General Purpose, Compute Optimized, Memory Optimized, …) và use case.
    * Học về **instance types** và sự khác biệt về vCPU, RAM, network performance.
    * Nắm mô hình chi phí: **On-Demand**, **Reserved Instances**, **Spot Instances**.
    * Thực hành quản lý **Security groups** để kiểm soát truy cập.
    * Tìm hiểu các phương pháp kết nối:
        - Sử dụng **SSH key pairs** để đăng nhập bảo mật.
        - Sử dụng **Session Manager** để truy cập trực tiếp từ AWS Console mà không cần mở port SSH.
    * Gán **Elastic IP** để giữ địa chỉ IP tĩnh cho EC2 instance.
    * Gắn và quản lý **EBS volumes** để mở rộng dung lượng lưu trữ.
    * Học về **AMIs (Amazon Machine Images)** để launch instance mới từ image có sẵn.

* Thực hành networking và compute:
    * Tạo một **fully-functional VPC**.
    * Launch và cấu hình một **EC2 instance** trong VPC.
    * Thử nhiều phương pháp kết nối đến EC2 instance.
    * Sử dụng **Reachability Analyzer** để phân tích và xử lý vấn đề kết nối.

* Học kiến thức cơ bản về **Amazon S3**:
    * Lưu trữ và quản lý data trong **S3 buckets**.
    * Thực hành **static website hosting** bằng S3.
    * Áp dụng **bucket security best practices** để bảo mật dữ liệu.

* Củng cố kiến thức về **networking, compute, và storage** trong AWS thông qua thực hành trực tiếp, tạo nền tảng vững chắc cho các dự án thực tế.  




