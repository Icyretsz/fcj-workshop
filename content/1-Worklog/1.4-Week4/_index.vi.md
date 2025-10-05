---
title: "Worklog Tuần 4"
date: "2025-09-15"
weight: 1
chapter: false
pre: " <b> 1.4. </b> "
---

### Mục tiêu tuần 4:

* Tiếp tục học thêm các dịch vụ của AWS
* Tiếp tục xây dựng dự án FCJ

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc                                                                                                                                                 | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                                                                                                                                                                           |
| --- |-----------------------------------------------------------------------------------------------------------------------------------------------------------| ------------ | --------------- |------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2   | - Tìm hiểu về AWS CloudWatch                                                                                                                              | 09/29/2025 | 09/29/2025      | <https://000008.awsstudygroup.com/1-introduction/> <https://docs.aws.amazon.com/cloudwatch/> <https://www.youtube.com/watch?v=ZCHwJLqPLj8> <https://www.youtube.com/watch?v=Yxl7e88cTAQ> 
| 3   | - Tìm hiểu về AWS ElastiCache                                                                                                                             | 09/30/2025 | 09/30/2025      | <https://docs.aws.amazon.com/elasticache/> <https://redis.io/> <https://www.youtube.com/watch?v=jgpVdJB2sKQ>                                                                             |
| 4   | - Tiếp tục phát triển dự án FCJ                                                                                                                           | 10/01/2025 | 10/01/2025      |                                                                                                                                                                                          |
| 5   | - Tham gia sự kiện AWS: [AWS GenAI Builder Club] AI-Driven Development Life Cycle: Reimagining Software Engineering <br> - Tiếp tục phát triển dự án FCJ  | 10/02/2025 | 10/02/2025      |                                                                                                                                                                                          |
| 6   | - Tiếp tục phát triển dự án FCJ                                                                                                                           | 10/03/2025 | 10/03/2025      |                                                                                                                                                                                          |


### Kết quả đạt được tuần 4:

* **Tìm hiểu về AWS CloudWatch**
    * Chức năng của CloudWatch: Thu thập **metrics**, **logs** và **events** từ các tài nguyên AWS và ứng dụng.
    * Khái niệm cốt lõi: **Metrics**, **Logs**, **Events**, **Dashboard**, **Alarms**, **CloudWatch Agent**
    * Thực hành:
        * Khởi tạo và theo dõi **metrics của EC2** (CPU, Memory, Network, Disk)
        * Tạo **alarm** khi CPU EC2 > 70% và liên kết với **SNS topic** để nhận thông báo qua email
        * Xem **log của EC2** trong CloudWatch Logs
        * Truy vấn log bằng **Logs Insight**
        * Thêm **widgets** hiển thị CPU Utilization, NetworkIn/Out vào **Dashboard**

* **Tìm hiểu về AWS ElastiCache**
    * Học về **khái niệm caching** trong các ứng dụng web
    * Tìm hiểu về **Redis**
    * Nắm các khái niệm chính trong ElastiCache: **nodes**, **clusters**, **shards**, **replication groups**
    * Thực hành:
        * Tạo **Redis Cluster** với **cluster mode disabled** trong VPC có EC2 instance
        * Chạy ứng dụng **Node.js nhỏ** trên EC2 để kiểm tra cache và gọi API khi cache miss
        * Giám sát bằng CloudWatch: xem các **metrics** như CPUUtilization, CurrConnections, Evictions
        * **Replication group & failover:** tạo replication group với **multi-AZ** và 1 **replica per shard**
        * Mô phỏng **failover** để quan sát node primary mới được kích hoạt

* **Phát triển dự án FCJ**
    * Hoàn thiện **logic trò chơi chính:**
        * Thêm **chế độ luyện tập (singleplayer)**
        * Thêm **dấu nháy của người chơi** với hoạt ảnh mượt khi di chuyển giữa các ký tự
        * Thêm **màu chỉ báo** cho từng ký tự
        * Thêm **thời lượng trò chơi**
        * Xử lý logic khi người chơi gõ **quá số ký tự cuối từ**
    * Giới thiệu **chế độ nhiều người chơi (multiplayer)** dựa trên **WebSocket**
        * Thêm **khái niệm phòng (room):**
            * Người chơi có thể **tạo phòng**, người khác **tham gia bằng roomId**
        * Thêm **dấu nháy cho từng người chơi** hiển thị **tiến độ gõ theo thời gian thực**

* **Tham gia sự kiện AWS**
    * Tham gia **AWS GenAI Builder Club: AI-Driven Development Life Cycle: Reimagining Software Engineering**
    * Học được các khái niệm về **AI trong phát triển phần mềm:**
        * Cách **ứng dụng AI vào chu trình phát triển** để nâng cao chất lượng phần mềm
        * **Tình hình hiện tại của AI** trong lĩnh vực phát triển phần mềm
        * Hiểu về **AI-driven development**
        * Làm quen với **Kiro IDE** thông qua các phần trình diễn demo


