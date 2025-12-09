---
title : "Thiết lập VPC và Mạng"
date :  "2025-09-15" 
weight : 2 
chapter : false
pre : " <b> 5.2.2 </b> "
---

## Tổng quan

Trong phần này, bạn sẽ tạo nền tảng mạng cho backend serverless. Bạn sẽ thiết lập một Virtual Private Cloud (VPC) với các private subnet để lưu trữ an toàn RDS database và Lambda function, đảm bảo lớp dữ liệu được cách ly khỏi truy cập trực tiếp từ internet.

**Bạn sẽ đạt được:**
- Hiểu kiến trúc VPC cho ứng dụng serverless
- Tạo custom VPC với cấu hình CIDR phù hợp
- Thiết lập các private subnet ở các Availability Zone khác nhau
- Cấu hình security group cho truy cập database
- Thiết lập kết nối internet cho Lambda function
- Chuẩn bị hạ tầng mạng cho triển khai RDS và Lambda

**Thời gian ước tính**: 30 phút

## Tại sao cần VPC cho Serverless Backend?

### Lợi ích bảo mật

**Private Subnets:**
- Database không thể truy cập từ internet
- Lambda function giao tiếp riêng tư với RDS
- Thêm một lớp phòng thủ chống tấn công

**Network Isolation:**
- Cách ly database khỏi internet công cộng
- Kiểm soát luồng traffic bằng security group
- Đáp ứng yêu cầu tuân thủ bảo vệ dữ liệu

### Mô hình kiến trúc
```
Internet
    ↓
NAT Gateway (cho Lambda outbound)
    ↓
Private Subnet (Lambda Functions)
    ↓
Private Subnet (RDS Database)
    └── Cách ly, không truy cập internet trực tiếp
```

{{% notice note %}}
**Lambda và VPC:**<br>
Mặc định, Lambda function chạy trong AWS-managed VPC với internet access. Khi bạn gắn Lambda vào custom VPC:<br>
Lambda có thể truy cập tài nguyên trong private subnet (như RDS)<br>
Lambda mặc định mất internet access<br>
Bắt buộc khi kết nối database trong VPC<br>
{{% /notice %}}

## Hiểu kiến trúc mạng

### Bạn sẽ xây dựng gì
```
VPC (10.0.0.0/16)
├── Private Subnet 1 (10.0.128.0/20) - AZ: ap-southeast-1a
│   └── Cho Lambda Functions
│
├── Private Subnet 2 (10.0.144.0/20) - AZ: ap-southeast-1b
│   └── Không sử dụng subnet này
│
├── Private Subnet 3 (10.0.160.0/20) - AZ: ap-southeast-1a
│   └── Cho RDS Database (yêu cầu multi-AZ)   
│
├── Private Subnet 4 (10.0.176.0/20) - AZ: ap-southeast-1b
│   └── Cho RDS Database (yêu cầu multi-AZ)  
```

### Vì sao cần 4 subnet ở 2 Availability Zone?
Private Subnet 1 dành cho Lambda function

Private Subnet 3 và 4 dành cho RDS instance.

Yêu cầu VPC phải có ít nhất hai subnet ở hai Availability Zone khác nhau trong cùng một AWS Region để triển khai DB instance.
Điều này đảm bảo tính sẵn sàng cao và cho phép RDS tạo Multi-AZ deployment hoặc tự động chuyển đổi dự phòng.

Chúng ta sẽ không dùng Private Subnet 2.

## Bước 1: Tạo VPC

### 1.1 Truy cập VPC Console

1. Đăng nhập AWS Management Console
2. Tìm kiếm "VPC"
3. Click **VPC** trong mục Services

![VPC Console](/images/5-Workshop/5.2-Serverless/5.2.2-VPC/1.png)

### 1.2 Tạo VPC

1. Click **Create VPC**

**VPC settings:** Chọn **VPC and more**

**Name tag:** `workshop-backend`

**IPv4 CIDR block:** `10.0.0.0/16`

**IPv6 CIDR block:** Không chọn

**Tenancy:** Default

**Number of Availability Zones (AZs)**: 2

**Number of public subnets**: 0

**Number of private subnets**: 4

**NAT gateways ($) - cập nhật**: None

**VPC Endpoints**: None

![VPC Settings](/images/5-Workshop/5.2-Serverless/5.2.2-VPC/2.png)

2. Kiểm tra cấu trúc VPC ở phần **Preview**

![VPC Preview](/images/5-Workshop/5.2-Serverless/5.2.2-VPC/100.png)

3. Click **Create VPC**

4. Đợi vài giây để hệ thống khởi tạo VPC. Khi xong, click **View VPC**

![VPC Finish](/images/5-Workshop/5.2-Serverless/5.2.2-VPC/4.png)

5. Bạn sẽ được chuyển đến console VPC mới

![VPC](/images/5-Workshop/5.2-Serverless/5.2.2-VPC/5.png)

## Bước 2: Tạo Security Groups

Security group là firewall ảo kiểm soát traffic vào/ra.

### 2.1 Tạo Security Group cho VPC endpoint

{{% notice note %}}
Security group này dành cho VPC endpoint để Lambda truy cập Secrets Manager lấy RDS secret.
Endpoint sẽ được tạo ở **5.2.4 AWS Secrets Manager Configuration**
{{% /notice %}}

1. Trong VPC console, chọn **Security Groups**
2. Click **Create security group**

**Security group name:** `workshop-endpoint-sm-sg`

**Description:** `Security group for Secret Manager endpoint`

**VPC:** Chọn `workshop-backend-vpc`

**Inbound rules:**
1. Click **Add rule**
    - **Type:** HTTPS
    - **Protocol:** TCP
    - **Port range:** 443
    - **Source:** Custom
    - **Source:** nhập CIDR của VPC `10.0.0.0/16`

**Outbound rules:**
- Giữ mặc định: All traffic (0.0.0.0/0) allowed

3. Click **Create security group**

### 2.1 Tạo Security Group cho Lambda

1. Trong VPC console, chọn **Security Groups**
2. Click **Create security group**

**Security group name:** `workshop-lambda-sg`

**Description:** `Security group for Lambda functions`

**VPC:** Chọn `workshop-backend-vpc`

**Inbound rules:**
1. Click **Add rule**
    - **Type:** HTTPS
    - **Protocol:** TCP
    - **Port range:** 443
    - **Source:** Custom
    - **Source:** Chọn security group endpoint: `workshop-endpoint-sm-sg`

**Outbound rules:**
- Giữ mặc định: All traffic (0.0.0.0/0) allowed
- Cho phép Lambda:
    - Kết nối RDS
    - Gọi AWS services (Secrets Manager, CloudWatch)

3. Click **Create security group**

![Lambda SG Config](/images/5-Workshop/5.2-Serverless/5.2.2-VPC/6.png)

### 6.2 Tạo Security Group cho RDS

1. Click **Create security group**

**Security group name:** `workshop-rds-sg`

**Description:** `Security group for RDS database`

**VPC:** Chọn `workshop-backend-vpc`

**Inbound rules:**
1. Click **Add rule**
    - **Type:** PostgreSQL
    - **Protocol:** TCP
    - **Port range:** 5432 (auto-filled)
    - **Source:** Custom
    - **Source:** Chọn security group Lambda: `workshop-lambda-sg`
        - Gõ "workshop" để lọc
        - Chọn SG ID (sg-xxxxxxxxxxxxx)

**Ý nghĩa:** Chỉ cho phép kết nối PostgreSQL (port 5432) từ tài nguyên có gắn security group Lambda.

**Outbound rules:**
- Giữ mặc định: All traffic allowed (cho bảo trì database)

3. Click **Create security group**

![RDS SG Config](/images/5-Workshop/5.2-Serverless/5.2.2-VPC/7.png)

{{% notice info %}}
**Best Practice cho Security Group:**<br>
Dùng tham chiếu security group thay vì IP:<br>
Linh hoạt hơn (không cần cập nhật khi Lambda đổi IP)<br>
Bảo mật hơn (chỉ Lambda có đúng SG mới truy cập được)<br>
Dễ quản lý (thêm Lambda mới không cần sửa rule)
{{% /notice %}}

## Bước 3: Kiểm tra cấu hình mạng

Xác nhận bạn đã có:
- [x] Đã tạo VPC với CIDR 10.0.0.0/16
- [x] Đã tạo 4 private subnet:
    - [x] Private subnet 1 (10.0.128.0/20) ở ap-southeast-1a
    - [x] Private subnet 2 (10.0.144.0/20) ở ap-southeast-1b
    - [x] Private subnet 3 (10.0.160.0/20) ở ap-southeast-1a
    - [x] Private subnet 4 (10.0.176.0/20) ở ap-southeast-1b
- [x] Đã tạo security group cho Lambda (outbound all traffic)
- [x] Đã tạo security group cho RDS (inbound PostgreSQL từ Lambda SG)

## Tóm tắt

Chúc mừng! Bạn đã:
-   Tạo custom VPC với 4 private subnet và cấu hình CIDR chuẩn
-   Thiết lập security group cho Lambda và RDS với rule phù hợp
-   Xây dựng nền tảng mạng an toàn cho backend serverless

## Bước tiếp theo

Chuyển sang **Phần 2: Thiết lập RDS Database** để tạo PostgreSQL database trong VPC an toàn này.

---

**Sẵn sàng tiếp tục?** Nền tảng mạng của bạn đã sẵn sàng để triển khai database và serverless compute an toàn! 🎉

