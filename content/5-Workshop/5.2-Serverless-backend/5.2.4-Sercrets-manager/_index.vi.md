---
title : "Cấu hình AWS Secrets Manager"
date :  "2025-09-15" 
weight : 4 
chapter : false
pre : " <b> 5.2.4 </b> "
---

## Tổng quan

Trong phần này, bạn sẽ cấu hình AWS Secrets Manager để lưu trữ thông tin đăng nhập database một cách an toàn. Thay vì hardcode mật khẩu trong Lambda function, bạn sẽ lấy động từ Secrets Manager, tuân thủ best practice về bảo mật.

**Bạn sẽ đạt được:**
- Hiểu về AWS Secrets Manager và lợi ích
- Lưu thông tin đăng nhập RDS database dưới dạng secret
- Cấu hình cấu trúc secret cho kết nối database
- Thiết lập IAM permissions cho Lambda truy cập secret
- Kiểm thử việc lấy secret
- Hiểu về xoay vòng secret (tùy chọn)

**Thời gian ước tính**: 15-20 phút

## Tại sao dùng AWS Secrets Manager?

### Lợi ích bảo mật

**Không hardcode credentials:**
- Credentials được mã hóa khi lưu trữ (encrypted at rest)
- Truy cập kiểm soát qua IAM policies
- Có audit trail ai đã truy cập secret
- Không lưu credentials trong code hoặc biến môi trường

**Tự động xoay vòng (Automatic Rotation):**
- Xoay vòng định kỳ mà không downtime
- Giảm rủi ro lộ credentials
- Lambda tự động dùng credentials mới

**Quản lý tập trung:**
- Một nguồn duy nhất cho credentials
- Dễ cập nhật cho nhiều ứng dụng
- Lưu lịch sử các phiên bản giá trị secret

### Cân nhắc chi phí

**Pricing:**
- $0.40 mỗi secret mỗi tháng
- $0.05 cho mỗi 10,000 API calls
- VPC Endpoint (PrivateLink): $0.013/giờ

**Cho workshop này:**
- 1 secret cho database credentials
- API calls từ Lambda rất ít
- **Ước tính chi phí**: <$1 (giả sử dọn dẹp ngay sau workshop)

## Bước 1: Xem Database Secret

Ở phần trước - RDS Database Setup, khi [tạo database]({{< relref "/5-Workshop/5.2-Serverless-backend/5.2.3-RDS" >}}#25-settings), chúng ta đã chọn
**Managed in AWS Secrets Manager - most secure** và dùng mặc định (**aws/secretmanager(default)**). Tùy chọn này tạo
một RDS-managed secret cho chúng ta. Hãy xem nó

### 1.1 Truy cập Secrets Manager Console

1. Trong thanh tìm kiếm AWS Console, nhập "Secrets Manager"
2. Nhấp vào **Secrets Manager** dưới mục Services

![Secrets Manager Console](/images/5-Workshop/5.2-Serverless/5.2.4-Secrets-manager/1.png)

### 1.2 Xem Secret

1. Ở dashboard Secrets Manager, bạn sẽ thấy RDS-managed secret trong danh sách

![RDS Secret](/images/5-Workshop/5.2-Serverless/5.2.4-Secrets-manager/2.png)

### 1.3 Xem chi tiết Secret

1. Nhấp vào tên secret của bạn
2. Bạn sẽ thấy thông tin chi tiết

![RDS Secret](/images/5-Workshop/5.2-Serverless/5.2.4-Secrets-manager/3.png)

**Secret ARN:**
- **Sao chép ARN này** - bạn sẽ cần cho IAM policies

**Giá trị secret (đã mã hóa):**
- Nhấp **Retrieve secret value** để xem

![RDS Secret](/images/5-Workshop/5.2-Serverless/5.2.4-Secrets-manager/4.png)

### 1.4 Xem hành vi xoay vòng

1. Vào tab **Rotation**
2. Bạn sẽ thấy các thông tin xoay vòng
    - **Rotation status**: true - đã bật
    - **Rotation schedule**: 7 ngày - secret sẽ được xoay vòng mỗi 7 ngày
    - **Last rotated date** và **Next rotation date**: ngày xoay vòng gần nhất và tiếp theo
3. Nhấp **Edit rotation** để chỉnh hành vi

![Secret rotation](/images/5-Workshop/5.2-Serverless/5.2.4-Secrets-manager/5.png)

4. Trong modal **Edit rotation configuration**, bạn sẽ thấy các cài đặt:
    - **Automatic rotation**: Bật/tắt xoay vòng tự động. Khi bật, Secrets Manager sẽ xoay vòng secret theo lịch bạn chọn.
    - **Rotation schedule**: Chọn đơn vị thời gian và số lượng cho lịch xoay vòng
      - Có thể chọn **time unit** và lượng đơn vị thời gian để quay
      - Có thể chọn **Schedule expression** cho lịch phức tạp
    - **Windows duration - optional**: 
      - Thời gian (giờ) cho phép Secrets Manager thực hiện xoay vòng
      - Thường dùng để tránh giờ cao điểm hoặc bảo trì
    - **Rotate immediately when the secret is stored (checkbox)**
      - Nếu chọn, Secrets Manager sẽ xoay vòng ngay khi tạo hoặc lưu secret. Sau đó theo lịch xoay vòng bình thường.

![Secret rotation edit](/images/5-Workshop/5.2-Serverless/5.2.4-Secrets-manager/6.png)

## Bước 2: Tạo IAM Role cho Lambda truy cập

Lambda cần quyền đọc secret này. Chúng ta sẽ tạo một IAM role riêng cho Lambda function sử dụng.

### 2.1 Truy cập IAM Console

1. Trong thanh tìm kiếm AWS Console, nhập "IAM"
2. Nhấp vào **IAM** dưới mục Services
3. Ở điều hướng bên trái, nhấp **Roles**

![IAM](/images/5-Workshop/5.2-Serverless/5.2.4-Secrets-manager/7.png)

### 2.2 Tạo Role

1. Nhấp **Create role**

### 2.3 Chọn trusted entity

1. **Trusted entity type**: chọn **AWS service**
2. **Service or use case**: chọn **Lambda**
3. Nhấp **Next**

![role - trusted entity](/images/5-Workshop/5.2-Serverless/5.2.4-Secrets-manager/8.png)

### 2.4 Thêm permissions
1. **Permission policies:**
   - Tìm và chọn `AWSLambdaVPCAccessExecutionRole` và `SecretsManagerReadWrite`
2. Nhấp **Next**

### 2.5 Đặt tên, review và tạo
1. **Role name:** `workshop-lamda-secretsmng-role`
2. **Description:** `Cho phép Lambda gọi Secrets Manager để lấy secret.`

![role - review](/images/5-Workshop/5.2-Serverless/5.2.4-Secrets-manager/9.png)

### Hiểu về IAM Role

#### AWSLambdaVPCAccessExecutionRole
```json
{
  "Effect": "Allow",
  "Action": [
    "logs:CreateLogGroup",
    "logs:CreateLogStream",
    "logs:PutLogEvents",
    "ec2:CreateNetworkInterface",
    "ec2:DescribeNetworkInterfaces",
    "ec2:DeleteNetworkInterface",
    "ec2:AssignPrivateIpAddresses",
    "ec2:UnassignPrivateIpAddresses"
  ],
  "Resource": "arn:aws:logs:*:*:*"
}
```
- Tạo và ghi vào CloudWatch Log Groups
- Cần thiết cho debug và monitoring
- Tạo ENI (Elastic Network Interfaces) trong VPC
- Bắt buộc để Lambda kết nối RDS trong private subnet
- Quản lý kết nối mạng

#### SecretsManagerReadWrite

Policy này bao gồm tất cả quyền liên quan đến Secrets Manager và cho phép Lambda truy cập Secrets Manager để kết nối RDS

#### Lambda sử dụng các quyền này như thế nào
```
Lambda Function Execution
    ↓
1. Tạo ENI trong VPC (VPCAccessExecutionRole)
    ↓
2. Lấy DB credentials (WorkshopLambdaSecretsPolicy)
    ↓
3. Kết nối RDS qua ENI
    ↓
4. Thực thi truy vấn database
    ↓
5. Ghi log vào CloudWatch (BasicExecutionRole)
    ↓
6. Trả về kết quả
```

### Best Practice về bảo mật

 **Nguyên tắc quyền tối thiểu (Principle of Least Privilege):**
- Policy chỉ cấp quyền đọc (không cấp write/delete)
- Giới hạn theo ARN cụ thể (không phải tất cả secrets)
- Không dùng wildcard

 **Giới hạn theo resource:**
- Dùng pattern ARN cụ thể
- Không thể truy cập nhầm các secret khác
- Dễ audit và troubleshoot

## Bước 3: Tạo VPC endpoint cho Lambda function
Để Lambda trong private subnet truy cập Secrets Manager, cần đi qua VPC endpoint.
Chúng ta sẽ tạo VPC endpoint trong VPC console

### 3.1 Truy cập VPC Console
1. Trong thanh tìm kiếm AWS Console, nhập "VPC"
2. Nhấp vào **VPC** dưới mục Services
3. Ở điều hướng bên trái, dưới **PrivateLink and Lattice**, nhấp **Endpoints**

![navigate endpoint](/images/5-Workshop/5.2-Serverless/5.2.4-Secrets-manager/10.png)

4. Nhấp **Create Endpoint**

### 3.2 Tạo Endpoint
1.  **Name tag - optional:** `workshop-lambda-secretsmng-endpoint`
2. **Type:**: AWS services

![endpoint type](/images/5-Workshop/5.2-Serverless/5.2.4-Secrets-manager/11.png)

3. **Services**: tìm `secrets`.
   - Sẽ thấy service tên `com.amazonaws.[region].secretsmanager`. Chọn nó

![endpoint services](/images/5-Workshop/5.2-Serverless/5.2.4-Secrets-manager/12.png)

4. **Network settings:**
   - VPC: chọn `workshop-backend-vpc`
   - **Subnet:**
      - Chọn subnet cùng availability zone với subnet của RDS
      - Nhớ rằng ở [VPC and Network Setup]({{< relref "/5-Workshop/5.2-Serverless-backend/5.2.2-VPC" >}}#understanding-the-network-architecture))
      chúng ta đặt Lambda ở private subnet 1, RDS ở private subnet 3, đều ở ap-southeast-1a
      - Chọn `apse1-az2 (ap-southeast-1a)`
      - **Subnet ID:** chọn `workshop-backend-subnet-private1-ap-southeast-1a` 
   - **Security groups:** chọn security group **workshop-endpoint-sm-sg** đã tạo ở [VPC and Network Setup]({{< relref "/5-Workshop/5.2-Serverless-backend/5.2.2-VPC" >}}#21-create-security-group-for-vpc-endpoint)
   - **Policy**: full access. Cho phép Lambda dùng mọi thao tác Secrets Manager (đã cấp trong role vừa tạo) qua endpoint

5. Nhấp **Create endpoint**

### 3.3 Kiểm tra Endpoint

Khi trạng thái là **Available**:
1. Nhấp vào endpoint ID
2. Ghi lại **DNS names** - Lambda sẽ dùng tự động
3. Kiểm tra **Subnets** hiển thị workshop-private-subnet-1
4. Kiểm tra **Security groups** hiển thị workshop-lambda-sg

**Tiết kiệm chi phí với VPC Endpoint:**

Interface VPC Endpoint: ~$7.20/tháng + $0.01/GB data

**Không dùng VPC Endpoint:**
- Lambda → NAT Gateway → Internet → Secrets Manager
- NAT Gateway: ~$32/tháng + $0.045/GB

**Dùng VPC Endpoint:**
- Lambda → VPC Endpoint → Secrets Manager (private)
- Độ trễ thấp hơn, bảo mật hơn, tiết kiệm chi phí cho ứng dụng traffic lớn

Với workshop này traffic thấp, chênh lệch nhỏ, nhưng đây là best practice cho production.

## Tóm tắt

Chúc mừng! Bạn đã:
- Xem chi tiết RDS secret trong AWS Secrets Manager
- Tạo IAM role cho Lambda truy cập secret
- Tạo VPC endpoint cho Lambda đọc secret trong AWS Secrets Manager

## Bước tiếp theo

Chuyển sang **Phần 4: Phát triển Lambda Functions** để tạo Lambda và kết nối các thành phần (VPC endpoint, Lambda role) sử dụng secret này để kết nối RDS database.

---

**Sẵn sàng tiếp tục?** Credentials của bạn đã được lưu trữ an toàn và Lambda đã có quyền truy cập! 🔐

