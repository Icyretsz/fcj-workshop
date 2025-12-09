---
title : "Dọn dẹp"
date :  "2025-09-15" 
weight : 9 
chapter : false
pre : " <b> 5.2.9 </b> "
---

## Tổng quan

Phần này hướng dẫn cách dọn dẹp tài nguyên bạn đã tạo trong Phần 2: Serverless Backend. Dọn dẹp giúp tránh phát sinh chi phí AWS, đặc biệt với các tài nguyên có phí hàng tháng như RDS.

**Bạn sẽ học:**
- Cách xóa tài nguyên an toàn, đúng thứ tự
- Hiểu về phụ thuộc giữa các tài nguyên
- Ảnh hưởng chi phí khi giữ hoặc xóa tài nguyên
- Cách lưu cấu hình để sử dụng lại
- Tùy chọn dọn dẹp một phần

**Thời gian ước tính**: 15-20 phút

## Có nên dọn dẹp không?

### Giữ lại tài nguyên nếu:
-   Bạn sẽ tiếp tục ngay với Phần 2: Serverless Backend
-   Muốn giữ frontend để tham khảo
-   Dùng cho dự án thực tế
-   Chi phí phù hợp với nhu cầu

### Dọn dẹp nếu:
-   Đã hoàn thành workshop và không cần tài nguyên nữa
-   Muốn giảm chi phí AWS
-   Chỉ thực hành và sẽ tạo lại sau
-   Gần hết hạn Free Tier

## Dọn dẹp hoàn toàn (theo thứ tự)

Làm theo thứ tự sau để tránh lỗi phụ thuộc:

## Bước 1: Xóa RDS instance và subnet group

### 1.1 Xóa RDS instance
1. Vào dashboard **Aurora and RDS**
2. Ở thanh bên trái, nhấp **Databases**
3. Chọn `workshop-posgresql-db`
4. Ở bên phải, nhấp nút **Action**, sau đó nhấp **Delete**
5. Bỏ chọn **Create final snapshot** và **Retain automated backup** vì hai lựa chọn này sẽ phát sinh phí ngay cả khi bạn đã xóa RDS instance
6. Chọn **I acknowledge that upon instance deletion, automated backups, including system snapshots and point-in-time recovery, will no longer be available.**
7. Nhập `delete me` khi được yêu cầu
8. Nhấp **Delete**

![Delete RDS](/images/5-Workshop/5.2-Serverless/5.2.9-Clean-up/1.png)

### 1.2 Xóa subnet group
1. Vẫn ở dashboard **Aurora and RDS**, ở thanh bên trái, nhấp **Subnet group**
2. Chọn **workshop-db-subnet-group**
3. Ở bên phải, nhấp **Delete**
4. Nhấp **Delete** trong modal
5. Đợi xóa instance hoàn tất rồi tiếp tục xóa subnet group

![Delete Subnet Group](/images/5-Workshop/5.2-Serverless/5.2.9-Clean-up/2.png)

## Bước 2: Xóa Lambda function
1. Vào dashboard **Lambda**
2. Chọn `workshop-lambda-sm-rds`
3. Ở bên phải, nhấp nút **Action**
4. Nhấp **Delete** trong dropdown
5. Nhập `delete` khi được yêu cầu và nhấp **Delete**

![Delete Lambda](/images/5-Workshop/5.2-Serverless/5.2.9-Clean-up/3.png)

## Bước 3: Xóa secret trong AWS Secrets Manager
1. Vào dashboard **Secrets Manager**
2. Trong danh sách secrets, nhấp vào tên secret của RDS
3. Ở bên phải, nhấp nút **Action**, sau đó nhấp **Delete** trong dropdown
4. Một modal xuất hiện, chọn **Waiting period**, sau thời gian này secret sẽ bị xóa
5. Nhấp **Schedule deletion**

![Delete secret](/images/5-Workshop/5.2-Serverless/5.2.9-Clean-up/4.png)

## Bước 4: Xóa tài nguyên API Gateway
1. Vào dashboard **API Gateway**
2. Ở thanh bên trái, nhấp **APIs**
3. Chọn `workshop-user-api`
4. Ở bên phải, nhấp **Delete**
5. Nhập `confirm` khi được yêu cầu
6. Nhấp **Delete**

![Delete API gateway](/images/5-Workshop/5.2-Serverless/5.2.9-Clean-up/5.png)

## Bước 5: Xóa Cognito user pool
1. Vào dashboard **Cognito**
2. Ở thanh bên trái, nhấp **User pools**
3. Trong danh sách, chọn `User pool - id`
4. Chọn hai checkbox, sau đó nhập tên Cognito user pool khi được yêu cầu
5. Nhấp **Delete**

![Delete User Pool](/images/5-Workshop/5.2-Serverless/5.2.9-Clean-up/6.png)

## Bước 6: Xóa VPC và VPC endpoint

### 6.1 Xóa VPC endpoint
1. Vào dashboard **VPC**
2. Ở thanh bên trái, nhấp **Endpoints**
3. Chọn `workshop-lambda-secretsmng-endpoint`
4. Ở bên phải, nhấp nút **Action**, sau đó chọn **Delete** trong dropdown
5. Nhập `delete` khi được yêu cầu và nhấp **Delete**
6. Đợi xóa hoàn tất trước khi tiếp tục xóa VPC

![Delete VPC endpoint](/images/5-Workshop/5.2-Serverless/5.2.9-Clean-up/7.png)

### 6.2 Xóa VPC
1. Ở thanh bên trái, nhấp **Your VPCs**
2. Chọn `workshop-backend-vpc`
3. Ở bên phải, nhấp nút **Action**, sau đó nhấp **Delete VPC**

![Delete VPC ](/images/5-Workshop/5.2-Serverless/5.2.9-Clean-up/8.png)

## Bước 7: Xóa CloudWatch log groups
1. Vào dashboard **CloudWatch**
2. Ở thanh bên trái, nhấp **Log groups**
3. Xóa tất cả log groups của các tài nguyên workshop

![Delete log groups ](/images/5-Workshop/5.2-Serverless/5.2.9-Clean-up/9.png)

## Bước 8: Xóa roles
1. Vào dashboard **IAM**
2. Ở thanh bên trái, nhấp **Roles**
3. Xóa các roles sau:
- `api-gw-push-cloudwatch-logs`
- `workshop-lamda-secretsmng-role`

## Tóm tắt

### Checklist hoàn thành dọn dẹp

**Nếu dọn dẹp hoàn toàn:**
- [ ] RDS: Không còn database hoặc subnet group
- [ ] Lambda: Không còn function `workshop-lambda-sm-rds`
- [ ] Secrets Manager: Secret đã lên lịch xóa
- [ ] API Gateway: Không còn `workshop-user-api`
- [ ] Cognito user pool
- [ ] VPC Endpoints: Không còn `workshop-secretsmng-endpoint`
- [ ] VPC: Không còn `workshop-backend-vpc`
- [ ] CloudWatch: Không còn log groups của workshop
- [ ] IAM: Không còn roles/policies của workshop
