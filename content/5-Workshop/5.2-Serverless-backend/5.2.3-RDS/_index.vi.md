---
title : "Cấu hình RDS Database"
date :  "2025-09-15"
weight : 3
chapter : false
pre : " <b> 5.2.3 </b> "
---

## Tổng quan

Trong phần này, bạn sẽ tạo một Amazon RDS PostgreSQL database instance bên trong VPC của mình. Database sẽ được triển khai trong một private subnet, khiến nó không thể truy cập được từ internet nhưng vẫn cho phép các Lambda function của bạn kết nối một cách an toàn.

**Những gì bạn sẽ đạt được:**
- Hiểu kiến trúc RDS và các tùy chọn cấu hình
- Tạo một DB subnet group cho việc triển khai RDS
- Cấu hình và khởi chạy một RDS PostgreSQL instance
- Thiết lập các tham số và tùy chọn database
- Tạo schema và các bảng database ban đầu
- Xác minh kết nối database
- Hiểu về bảo mật RDS và các cài đặt sao lưu

**Thời gian ước tính**: 35-40 phút (bao gồm 10-15 phút chờ tạo database)

## Tại sao chọn Amazon RDS?

### Lợi ích so với Database tự quản lý

**Dịch vụ được quản lý (Managed Service):**
- Tự động sao lưu và khôi phục tại một thời điểm (point-in-time recovery)
- Tự động cập nhật và vá lỗi phần mềm
- Tích hợp sẵn giám sát và các chỉ số (metrics)
- Tùy chọn tính sẵn sàng cao (High availability - Multi-AZ)
- Read replicas để mở rộng khả năng đọc

**Vận hành đơn giản:**
- Không cần cung cấp máy chủ hoặc quản lý hệ điều hành
- Dễ dàng mở rộng khả năng tính toán và lưu trữ
- Tự động chuyển đổi dự phòng (failover) cho triển khai Multi-AZ
- Tích hợp CloudWatch để giám sát

**Hiệu quả chi phí:**
- Chỉ trả tiền cho những gì bạn sử dụng
- Reserved instances cho khối lượng công việc sản xuất (production)
- Có sẵn tính năng tự động mở rộng lưu trữ (Storage auto-scaling)

## Hiểu về cấu hình RDS

### Cân nhắc về chi phí

Trong bài thực hành này, chúng ta sẽ sử dụng **db.t3.micro**:
- 2 vCPUs, 1 GB RAM
- Hiệu năng có thể tăng cường - Burstable performance (phù hợp cho dev/test)
- Đủ điều kiện cho Free Tier (750 giờ/tháng trong 12 tháng đầu)
- $0.017/giờ (~$12.41/tháng) nếu ngoài Free Tier
- Tổng cộng: $0 (free-tier) hoặc <$2 (nếu dọn dẹp ngay sau hội thảo)

### Single-AZ so với Multi-AZ

**Single-AZ (Thiết lập cho Workshop):**
- Database nằm trong một Availability Zone
- Chi phí thấp hơn
- Tốt cho phát triển/kiểm thử
- ~5 phút gián đoạn khi bảo trì

**Multi-AZ (Production):**
- Sao chép đồng bộ sang instance dự phòng ở AZ khác
- Tự động failover (~1-2 phút)
- Chi phí cao hơn (~gấp đôi)
- Tốt hơn cho khối lượng công việc production

### Cấu hình lưu trữ

**Loại lưu trữ:** General Purpose SSD (gp3)
- Cân bằng giữa giá cả và hiệu năng
- Burstable IOPS
- Phù hợp cho hầu hết các tác vụ

**Dung lượng được phân bổ:** 20 GB
- Mức tối thiểu cho PostgreSQL
- Free Tier bao gồm 20 GB
- Có thể tăng sau này mà không gây gián đoạn

## Bước 1: Tạo DB Subnet Group

RDS yêu cầu một DB subnet group để xác định các subnet mà database có thể được triển khai vào.

### 1.1 Truy cập RDS Console

1. Trong thanh tìm kiếm của AWS Console, nhập "RDS"
2. Nhấp vào **RDS** dưới mục Services
3. Ở thanh điều hướng bên trái, nhấp vào **Subnet groups**

![RDS SG Config](/images/5-Workshop/5.2-Serverless/5.2.3-RDS/1.png)

### 1.2 Tạo DB Subnet Group

1. Nhấp vào **Create DB subnet group**

**Chi tiết Subnet group:**

**Name:** `workshop-db-subnet-group`

**Description:** `Subnet group for workshop RDS database`

**VPC:** Chọn `workshop-backend-vpc`

### 1.3 Thêm Subnets

**Availability Zones:**
1. Chọn 2 availability zones mà bạn đã tạo các subnets trong phần thiết lập VPC ở [bài trước]({{< relref "/5-Workshop/5.2-Serverless-backend/5.2.2-VPC" >}}#12-create-vpc)
    - Chọn: `ap-southeast-1a` và `ap-southeast-1b`

**Subnets:**
- Chọn 2 private subnets, mỗi subnet từ một Availability Zone khác nhau:
    - `workshop-backend-subnet-private3-ap-southeast-1a`
    - `workshop-backend-subnet-private4-ap-southeast-1b`
    - ![RDS SG Config](/images/5-Workshop/5.2-Serverless/5.2.3-RDS/3.png)

![DB Subnet Group Subnets](/images/5-Workshop/5.2-Serverless/5.2.3-RDS/2.png)

**Tại sao cần hai Subnets?**

Mặc dù chúng ta đang triển khai database single-AZ, RDS yêu cầu subnet group phải có ít nhất hai subnets trong các Availability Zones khác nhau. Đây là yêu cầu của AWS nhằm:
- Cho phép dễ dàng di chuyển sang Multi-AZ sau này
- Mang lại sự linh hoạt cho read replicas
- Đảm bảo các thực hành cấu hình nhất quán

Database sẽ chỉ sử dụng một subnet (chúng ta sẽ chỉ định subnet nào trong quá trình tạo instance).


### 1.4 Tạo Subnet Group

1. Nhấp vào **Create**
2. Bạn sẽ thấy thông báo thành công
3. Subnet group xuất hiện trong danh sách với trạng thái **Complete**

![DB Subnet Group Created](/images/5-Workshop/5.2-Serverless/5.2.3-RDS/4.png)

## Bước 2: Tạo RDS PostgreSQL Instance

### 2.1 Bắt đầu tạo Database

1. Trong điều hướng bên trái của RDS console, nhấp vào **Databases**
2. Nhấp vào **Create database**

### 2.2 Chọn phương thức tạo Database

**Database creation method:**
- Chọn **Full configuration**
    - Cung cấp đầy đủ các tùy chọn cấu hình
    - Nhiều quyền kiểm soát hơn Easy create

![RDS Creation Method](/images/5-Workshop/5.2-Serverless/5.2.3-RDS/5.png)

### 2.3 Tùy chọn Engine

**Engine type:**
- Chọn **PostgreSQL**

**Engine Version:**
- Chọn **PostgreSQL 17.** (hoặc phiên bản mới nhất có sẵn)
- Sử dụng mặc định trừ khi bạn cần một phiên bản cụ thể

![RDS Engine Options](/images/5-Workshop/5.2-Serverless/5.2.3-RDS/6.png)


**Phiên bản PostgreSQL:**

Chúng tôi khuyến nghị PostgreSQL 17 hoặc mới hơn cho workshop này vì nó bao gồm:
- Hỗ trợ JSON tốt hơn
- Cải thiện hiệu năng
- Các tính năng bảo mật nâng cao

Đối với production, hãy chọn phiên bản LTS (Hỗ trợ dài hạn) và kiểm tra kỹ ứng dụng của bạn trước khi nâng cấp.


### 2.4 Mẫu (Templates)

**Templates:**
- Chọn **Sandbox** (hoặc **Free tier** nếu áp dụng)

**Availability and durability**
- Chọn **Single-AZ DB instance deployment (1 instances)**

![RDS Templates](/images/5-Workshop/5.2-Serverless/5.2.3-RDS/7.png)

{{% notice tip %}}
**Multi-AZ cho Production:**<br>
Đối với khối lượng công việc production, hãy bật triển khai Multi-AZ:<br>
Tự động failover sang instance dự phòng<br>
Sao chép đồng bộ<br>
Thời gian failover khoảng 1-2 phút<br>
Chi phí gấp đôi Single-AZ<br>
Có thể chuyển sang Multi-AZ sau này với thời gian gián đoạn tối thiểu thông qua việc sửa đổi database.
{{% /notice %}}

### 2.5 Cài đặt (Settings)

**DB instance identifier:** `workshop-postgres-db`
- Đây là tên của RDS instance của bạn
- Phải là duy nhất trong tài khoản AWS của bạn theo region

**Credentials Settings (Cài đặt thông tin xác thực):**

**Master username:** `postgres`
- Người dùng quản trị mặc định của PostgreSQL
- Không thể thay đổi sau khi tạo

**Credentials management:**
- Chọn **Managed in AWS Secrets Manager** - an toàn nhất
- Dưới mục **Select the encryption key**, chọn **aws/secretmanager(default)**

![RDS Settings](/images/5-Workshop/5.2-Serverless/5.2.3-RDS/8.png)

{{% notice note %}}
AWS Secrets Manager sẽ tạo và lưu trữ thông tin xác thực database của chúng ta để chúng ta không phải hardcode các bí mật trong Lambda function truy vấn database<br>
{{% /notice %}}

### 2.6 Cấu hình Instance

**DB instance class:**
- Chọn **Burstable classes (includes t classes)**
- Chọn **db.t3.micro**
    - 2 vCPUs, 1 GB RAM
    - Đủ điều kiện Free Tier
    - Đủ dùng cho workshop và các ứng dụng nhỏ

![RDS Instance Class](/images/5-Workshop/5.2-Serverless/5.2.3-RDS/9.png)

### 2.7 Lưu trữ (Storage)

**Storage type:**
- Chọn **General Purpose SSD (gp3)** (nếu có sẵn)
- Hoặc **General Purpose SSD (gp2)** (Mặc định cho Free Tier)

**Allocated storage:** `20` GB
- Tối thiểu cho PostgreSQL
- Free Tier bao gồm 20 GB

**Storage autoscaling:**
- Giữ chọn **Enable storage autoscaling**
- **Maximum storage threshold:** `100` GB
- Database tự động tăng dung lượng nếu cần (tính phí cho dung lượng thêm)

![RDS Storage](/images/5-Workshop/5.2-Serverless/5.2.3-RDS/10.png)

### 2.8 Kết nối (Connectivity)

**Compute resource:**
- Chọn **Don't connect to an EC2 compute resource**
- Chúng ta sẽ cấu hình cài đặt VPC thủ công

**Network type:**
- Giữ chọn **IPv4**

**Virtual private cloud (VPC):**
- Chọn `workshop-backend-vpc`

**DB subnet group:**
- Chọn `workshop-db-subnet-group`

**Public access:**
- Chọn **No**
- Database sẽ không thể truy cập từ internet
- Chỉ truy cập được từ bên trong VPC

**VPC security group:**
- Chọn **Choose existing**
- Loại bỏ security group mặc định
- Chọn `workshop-rds-sg` (đã tạo ở Phần 1)

**Availability Zone:**
- Chọn **ap-southeast-1a**
- Điều này khớp với nơi đặt `workshop-private-subnet-3`

{{% notice info %}}
**Tại sao chỉ định Availability Zone?**<br>
Mặc dù DB subnet group của chúng ta bao gồm cả hai AZ, chúng ta chọn rõ ràng **ap-southeast-1a** để đảm bảo database được tạo trong `workshop-private-subnet-3`. Điều này cung cấp:<br>
Vị trí có thể dự đoán để khắc phục sự cố<br>
Cùng availability zone với subnet của Lambda<br>
Tổ chức tài nguyên tốt hơn<br>
{{% /notice %}}

![RDS Connectivity](/images/5-Workshop/5.2-Serverless/5.2.3-RDS/11.png)

### 2.10 Xác thực Database

**Database authentication:**
- Giữ chọn **Password authentication**
- Chúng ta sẽ dùng username/password cho đơn giản

**Các tùy chọn khác (không dùng trong workshop):**
- **Password and IAM database authentication**: An toàn hơn, sử dụng IAM roles
- **Password and Kerberos authentication**: Dành cho tích hợp Active Directory doanh nghiệp

### 2.11 Giám sát (Monitoring)

**Database Insights**: chọn **standard**
- Lịch sử hiệu năng sẽ được giữ trong 7 ngày

**Turn on Performance Insights:**
- Giữ **Enabled** cho workshop này
- Tùy chọn giám sát này hiển thị nguồn gốc tải của database như các truy vấn SQL, giúp bạn tinh chỉnh câu lệnh SQL hoặc tăng tài nguyên hệ thống.

**Enable Enhanced monitoring:**
- Giữ **disabled** cho workshop này
- Cung cấp các metrics ở cấp độ hệ điều hành

![RDS Monitoring](/images/5-Workshop/5.2-Serverless/5.2.3-RDS/12.png)

**Cho production**: Bật cả hai để có khả năng quan sát tốt hơn.

### 2.12 Cấu hình bổ sung

Nhấp vào **Additional configuration** để mở rộng cài đặt.

**Database options:**

**Initial database name:** `workshopdb`
- Tạo một database ngay khi khởi tạo instance
- Nếu để trống, không có database nào được tạo (bạn sẽ phải tạo thủ công)

**DB parameter group:**
- Giữ mặc định: `default.postgres17`

**Option group:**
- Giữ mặc định: `default:postgres-17`

![RDS Database Options](/images/5-Workshop/5.2-Serverless/5.2.3-RDS/13.png)

**Backup:**

**Enable automated backups:**
- Giữ **enabled**
- Miễn phí trong thời gian lưu trữ

**Backup retention period:** `7` days
- Free Tier bao gồm sao lưu lên đến kích thước lưu trữ của DB instance
- Đủ cho phát triển/kiểm thử

**Backup window:**
- Chọn **No preference** (AWS chọn thời gian tối ưu)
- Hoặc chọn thời gian cụ thể nếu bạn muốn

**Enable Backup replication:**
- Giữ **disabled**
- Sao chép bản sao lưu sang vùng khác (chi phí bổ sung)

![RDS Backup](/images/5-Workshop/5.2-Serverless/5.2.3-RDS/14.png)

**Encryption:**

**Enable encryption:**
- Giữ **enabled** (được chọn mặc định)
- Sử dụng AWS KMS để mã hóa dữ liệu khi nghỉ (at rest)
- Không mất thêm phí (sử dụng AWS managed key)

**AWS KMS key:**
- Chọn `(default) aws/rds`
- Key do AWS quản lý, không cần quản lý key

{{% notice info %}}
**Thực hành tốt nhất về Mã hóa:**<br>
Luôn bật mã hóa cho các database chứa dữ liệu nhạy cảm:<br>
Mã hóa dữ liệu khi nghỉ<br>
Mã hóa các bản sao lưu tự động<br>
Mã hóa read replicas<br>
Không thể tắt sau khi tạo<br>
Ảnh hưởng hiệu năng tối thiểu<br>
{{% /notice %}}

**Maintenance (Bảo trì):**

**Enable auto minor version upgrade:**
- Giữ **enabled**
- Các bản cập nhật phiên bản phụ của PostgreSQL được áp dụng tự động
- Áp dụng trong cửa sổ bảo trì
- Khuyên dùng cho các bản vá bảo mật

**Maintenance window:**
- Chọn **No preference**
- Hoặc chọn thời gian cụ thể (ví dụ: cuối tuần cho production)

**Deletion protection:**
- Giữ **disabled** cho workshop này
- Ngăn chặn việc vô tình xóa
- **Hãy bật trong production**

### 2.13 Ước tính chi phí

Trước khi tạo, hãy xem lại chi phí ước tính hàng tháng:
- Nằm ở góc dưới bên phải của trang
- **Ước tính Free Tier**: $0 (trong vòng 750 giờ/tháng)
- **Ngoài Free Tier**: ~$20-25/tháng cho db.t3.micro

### 2.14 Tạo Database

1. Xem lại tất cả các cài đặt
2. Nhấp vào **Create database**
3. Bạn sẽ thấy biểu ngữ thành công

![RDS Creating](/images/5-Workshop/5.2-Serverless/5.2.3-RDS/15.png)

**Thời gian tạo Database:** 10-15 phút

**Tiến trình trạng thái:**
- Creating → Backing-up → Available

## Bước 3: Giám sát quá trình tạo Database

### 3.1 Kiểm tra trạng thái

1. Ở lại RDS console → Databases
2. Tìm database của bạn: `workshop-postgres-db`
3. Theo dõi cột **Status**

![RDS Status](/images/5-Workshop/5.2-Serverless/5.2.3-RDS/16.png)

**Các chỉ báo trạng thái:**
- **Creating**: Đang cung cấp tài nguyên ban đầu
- **Backing-up**: Đang thực hiện sao lưu tự động lần đầu
- **Available**: Sẵn sàng để sử dụng

### 3.2 Xem chi tiết Database

Khi trạng thái là **Available**:

1. Nhấp vào định danh database: `workshop-postgres-db`
2. Bạn sẽ thấy thông tin chi tiết

**Thông tin quan trọng cần lưu ý:**

**Endpoint & port:**
- **Endpoint**: `workshop-postgres-db.xxxxxxxxxx.[region].rds.amazonaws.com`
- **Port**: `5432`
- Sao chép endpoint - bạn sẽ cần nó cho kết nối Lambda

![RDS Endpoint](/images/5-Workshop/5.2-Serverless/5.2.3-RDS/17.png)

**Connectivity & security:**
- VPC: workshop-backend-vpc
- Subnets: Cả hai private subnets
- Security groups: workshop-rds-sg
- Public accessibility: No

**Configuration:**
- DB instance class: db.t3.micro
- Storage: 20 GB gp3 (hoặc gp2)
- Multi-AZ: No

## Các thực hành tốt nhất cho RDS (RDS Best Practices)

### Bảo mật

**Đã thực hiện trong workshop này:**
- Database trong private subnet (không có truy cập công khai)
- Security group hạn chế quyền truy cập chỉ cho Lambda
- Mã hóa dữ liệu khi nghỉ được bật
- Secrets Manager để lưu trữ thông tin xác thực database

**Cho production, hãy thực hiện thêm:**
- Xác thực database bằng IAM
- Secrets Manager với tính năng xoay vòng tự động (automatic rotation)
- Giám sát và ghi nhật ký nâng cao (Enhanced monitoring and logging)
- Các bản vá bảo mật thường xuyên (bật auto minor version upgrade)
- CloudWatch alarms cho các bất thường

### Hiệu năng

**Đã thực hiện:**
- Lớp instance phù hợp cho khối lượng công việc
- Lưu trữ gp3 cho hiệu năng cân bằng
- Indexes trên các cột thường xuyên được truy vấn

**Cho production, hãy cân nhắc thêm:**
- Read replicas cho các khối lượng công việc đọc nhiều
- Connection pooling (RDS Proxy)
- Query performance insights
- Tinh chỉnh cấu hình database phù hợp

### Tính sẵn sàng

**Đã thực hiện:**
- Sao lưu tự động (lưu trữ 7 ngày)
- DB subnet group trong nhiều AZs

**Cho production, hãy thực hiện thêm:**
- Triển khai Multi-AZ để tự động failover
- Cross-region read replicas cho DR (Thảm họa và phục hồi)
- Thời gian lưu trữ sao lưu lâu hơn (30 ngày)
- Sao chép bản sao lưu sang vùng khác

### Tối ưu hóa chi phí

**Đã thực hiện:**
- Free tier (nếu áp dụng)
- Instance kích thước phù hợp (db.t3.micro)
- Triển khai Single-AZ
- Thời gian lưu trữ sao lưu tiêu chuẩn

**Cho production, hãy cân nhắc thêm:**
- Reserved instances cho khối lượng công việc có thể dự đoán (tiết kiệm tới 69%)
- Dừng database instances trong giờ không làm việc (dev/test)
- Tự động mở rộng lưu trữ thay vì cung cấp dư thừa
- Xem xét thường xuyên các metrics CloudWatch

## Tóm tắt

Chúc mừng! Bạn đã thành công:
-   Tạo một DB subnet group cho triển khai RDS
-   Khởi chạy một RDS PostgreSQL database instance
-   Cấu hình database trong một private subnet
-   Thiết lập security groups cho truy cập Lambda
-   Bật mã hóa và sao lưu tự động
-   Chuẩn bị database schema cho ứng dụng
-   Lấy database endpoint cho các kết nối Lambda

## Bước tiếp theo

Tiến tới **Phần 3: Cấu hình AWS Secrets Manager** để lưu trữ và quản lý an toàn thông tin xác thực database của bạn.

---

**Sẵn sàng tiếp tục?** Database của bạn hiện đã sẵn sàng nhận kết nối từ các Lambda functions! 🎉