---
title: "Worklog Tuần 3"
date: "2025-09-15"
weight: 1
chapter: false
pre: " <b> 1.3. </b> "
---

### Mục tiêu Tuần 3:

* Học và thực hành các dịch vụ AWS ở mức trung cấp.
* Trải nghiệm trực tiếp với cơ sở dữ liệu được quản lý, tối ưu hóa tính toán, tự động mở rộng, và giám sát hệ thống.

### Các công việc cần triển khai trong tuần này:
| Ngày | Nhiệm vụ                                                                                                                                                                                                                                                        | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo                                                                                                                                                              |
|------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------|-----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2    | - Tìm hiểu về **Amazon RDS** và các hệ quản trị cơ sở dữ liệu được hỗ trợ (MySQL, PostgreSQL, v.v.) <br> - **Thực hành:** <br>&emsp; + Tạo RDS instance <br>&emsp; + Cấu hình security groups <br>&emsp; + Kết nối qua client <br>&emsp; + Tạo DB, chèn dữ liệu | 22/09/2025   | 22/09/2025      | <https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html> <https://www.youtube.com/watch?v=GvUaA9cygUk&pp=ygUHYXdzIHJkcw%3D%3D> <https://000045.awsstudygroup.com/> |
| 3    | - Tìm hiểu về **Amazon Lightsail** và sự khác biệt so với EC2 <br> - **Thực hành:** <br>&emsp; + Khởi chạy Lightsail instance (Linux hoặc WordPress) <br>&emsp; + Kết nối qua SSH <br>&emsp; + Triển khai website tĩnh <br>&emsp; + Khám phá giá thành          | 23/09/2025   | 23/09/2025      | <https://lightsail.aws.amazon.com/> <https://www.youtube.com/watch?v=COK3ko4np-0&t=305s&pp=ygUNYXdzIGxpZ2h0c2FpbA%3D%3D> <https://000045.awsstudygroup.com/>                    |
| 4    | - Tìm hiểu về khái niệm **Amazon EC2 Autoscaling** (launch templates, scaling groups, policies) <br> - **Thực hành:** <br>&emsp; + Tạo Launch Template <br>&emsp; + Tạo Auto Scaling Group <br>&emsp; + Cấu hình scaling policy <br>&emsp; + Stress test        | 24/09/2025   | 24/09/2025      | <https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html> <https://000045.awsstudygroup.com/>                                                |
| 5    | - Họp nhóm hàng tuần <br> - Bắt đầu xây dựng main game prototype cho dự án                                                                                                                                                                                      | 25/09/2025   | 25/09/2025      |                                                                                                                                                                                 |
| 6    | - Tiếp tục xây dựng prototype cho phần main game                                                                                                                                                                                                                | 26/09/2025   | 27/09/2025      |                                                                                                                                                                                 |

---

### Kết quả đạt được tuần 3:
* Họp nhóm hàng tuần:
    * Thiết kế giao diện người dùng (UI) của ứng dụng cùng nhau
    * Đưa ra thêm các quy tắc nghiệp vụ (business rules)
    * Thống nhất dịch vụ AWS sẽ sử dụng và ước tính chi phí
    * Thêm các chế độ trò chơi

* Xây dựng nguyên mẫu dự án cho phần main game (project prototype):
    * Cài đặt các thư viện: antd, socket.io, idv4...
    * Đi sâu vào cơ chế và logic của trò chơi
    * Xây dựng giao diện UI đơn giản
    * Xây dựng logic cơ bản cho trò chơi chính

* Hiểu sự khác biệt giữa **cơ sở dữ liệu được quản lý (RDS)** và cơ sở dữ liệu tự quản lý trên EC2.
* Triển khai thành công một ứng dụng trên **Amazon Lightsail**, so sánh chi phí và trường hợp sử dụng với EC2.
* Tạo thành công **EC2 Auto Scaling Group** với các scaling policy phản ứng theo tải.
* Xây dựng hệ thống giám sát và cảnh báo với **CloudWatch**, bao gồm metrics, alarms, và quản lý log.
* Nắm vững kỹ năng thực hành với các dịch vụ ngoài EC2 cơ bản, tiến gần hơn đến hạ tầng AWS ở mức production.  



