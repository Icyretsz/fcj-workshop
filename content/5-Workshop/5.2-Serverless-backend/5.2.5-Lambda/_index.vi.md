---
title : "Phát triển Lambda Functions"
date :  "2025-09-15"
weight : 5
chapter : false
pre : " <b> 5.2.5 </b> "
---

## Tổng quan

Trong phần này, bạn sẽ tạo các AWS Lambda function đóng vai trò là lớp logic nghiệp vụ (business logic layer) cho serverless backend của bạn. Các function này sẽ xử lý các yêu cầu API, truy xuất thông tin xác thực cơ sở dữ liệu từ Secrets Manager, tương tác với cơ sở dữ liệu RDS của bạn và trả về phản hồi cho API Gateway.

**Những gì bạn sẽ đạt được:**
- Hiểu kiến trúc Lambda function và quy trình thực thi
- Phát triển các Lambda function cho các hoạt động CRUD
- Kết nối Lambda với cơ sở dữ liệu RDS PostgreSQL
- Truy xuất thông tin xác thực một cách an toàn từ Secrets Manager
- Cấu hình cài đặt VPC để truy cập cơ sở dữ liệu
- Đóng gói và triển khai Lambda function cùng với các thư viện phụ thuộc (dependencies)
- Kiểm thử Lambda function trực tiếp
- Thiết lập CloudWatch Logs để gỡ lỗi (debugging)
- Khởi tạo schema cơ sở dữ liệu thông qua Lambda

**Thời gian ước tính**: 60-75 phút

## Kiến trúc Lambda Function

### Những gì chúng ta sẽ xây dựng
```
API Gateway
    ↓
Lambda Functions (trong VPC)
    ├── initDB - Khởi tạo schema cơ sở dữ liệu
    ├── createUser - Tạo người dùng mới
    ├── getUsers - Liệt kê tất cả người dùng
    ├── createTask - Tạo tác vụ (task) mới
    ├── getTasks - Lấy danh sách tác vụ của người dùng
    ├── updateTask - Cập nhật tác vụ
    └── deleteTask - Xóa tác vụ
    ↓
VPC Endpoint (Secrets Manager)
    ↓
Secrets Manager (lấy thông tin xác thực DB)
    ↓
RDS PostgreSQL (thực thi truy vấn)
```

## Cân nhắc về chi phí
### Bậc miễn phí (Free-tier):
- **1 triệu yêu cầu miễn phí mỗi tháng**
- **400,000 GB-giây thời gian tính toán mỗi tháng**
    - Áp dụng cho cả **x86** và **Graviton2** Lambda functions
- **100 GiB phát trực tuyến phản hồi HTTP (HTTP response streaming) mỗi tháng**
    - **6 MB đầu tiên cho mỗi yêu cầu luôn miễn phí**

### Bậc trả phí (Paid-tier)
1. Ngay cả chi phí bậc trả phí cũng rất nhỏ cho bài thực hành này
4. Tổng cộng: <$1 (nếu dọn dẹp ngay sau khi hoàn thành hội thảo)

## Bước 1: Chuẩn bị Môi trường Phát triển Lambda (tùy chọn)

Trong bước này, bạn sẽ thiết lập môi trường phát triển cục bộ (local) để xây dựng và đóng gói Lambda function của mình.
Tuy nhiên, bước này là tùy chọn. Nếu bạn không muốn thiết lập môi trường thủ công, bạn có thể bỏ qua bước này và tải xuống mã nguồn bên dưới.
Bạn có thể tải xuống mã nguồn cho phần này tại đây:
- Github repository: https://github.com/Icyretsz/fcj-workshop-serverless-backend-ver1
- Chỉ file zip cho lambda: https://fcj-workshop-files.s3.ap-southeast-1.amazonaws.com/userHandler.zip

### 1.1 Xem thư mục dự án

Mã nguồn được cung cấp trong git repository ở trên.

Cấu trúc dự án:
```
fcj-workshop-serverless-backend-ver1
└── backend/
    └── src/
        └── userHandler.ts
        └── types.ts
    ├── package.json
    ├── tsconfig.json
```

{{% notice note %}}
Bạn có thể chọn tổ chức các Lambda function của mình theo nhiều cách khác nhau. Trong môi trường production, nên tạo các Lambda function riêng biệt cho từng thao tác người dùng (CREATE, READ, UPDATE, DELETE) để cải thiện khả năng bảo trì, mở rộng và bảo mật.<br>
Tuy nhiên, để đơn giản hóa trong bài thực hành này, chúng ta sẽ đặt tất cả các thao tác liên quan đến người dùng vào một Lambda function duy nhất (userHandler.js).
{{% /notice %}}

#### Tệp userHandler.ts

**Các tính năng chính của handler này:**
- Khởi tạo DB (DB Initialization): tự động tạo bảng users và chèn dữ liệu giả lập nếu bảng chưa tồn tại.
- TypeScript types: sử dụng interface User và ApiResponse<T> của bạn.
- Secrets Manager: truy xuất thông tin xác thực cơ sở dữ liệu tại thời điểm chạy (runtime).
- Không dùng connection pool: an toàn cho các bài workshop, đủ đơn giản.
- Lambda function này xử lý các hoạt động CRUD cho người dùng trong cơ sở dữ liệu PostgreSQL. Nó được thiết kế để hoạt động với **API Gateway Lambda Proxy Integration**, cung cấp cấu trúc sự kiện cụ thể và mong đợi định dạng phản hồi cụ thể.

**Các thao tác được hỗ trợ:**
- `GET /users` - Lấy tất cả người dùng
- `GET /users/{id}` - Lấy một người dùng
- `POST /users` - Tạo người dùng mới
- `PUT /users/{id}` - Cập nhật người dùng
- `DELETE /users/{id}` - Xóa người dùng

Bạn có thể sửa đổi nó theo ý thích. Khi bạn đã sẵn sàng, hãy chuyển sang các bước tiếp theo để xây dựng và triển khai mã nguồn của chúng ta lên Lambda.

## Bước 2: Xây dựng Gói triển khai Lambda

### 2.1 Cài đặt dependencies

Từ thư mục gốc, chạy lệnh:

```bash
npm install
```

### 2.1 Build mã nguồn TypeScript

Build dự án:
```bash
npm run build
```

Lệnh này tạo thư mục `dist` trong thư mục gốc.

Bây giờ hãy tìm thư mục `node_modules` và sao chép nó vào thư mục `dist`.

Khi hoàn tất, chọn tất cả các tệp trong thư mục `dist`, nén chúng thành **zip** và đặt tên là userHandler.

![project structure](/images/5-Workshop/5.2-Serverless/5.2.5-Lambda/14.png)

Xác nhận nội dung của userHandler.zip:

![zip content](/images/5-Workshop/5.2-Serverless/5.2.5-Lambda/15.png)

## Bước 3: Triển khai Lambda Functions

Bây giờ chúng ta sẽ tạo Lambda function trong AWS.

### 3.1 Truy cập Lambda console

1. Truy cập vào Lambda console
2. Nhấp vào **Create function**

![Create Lambda](/images/5-Workshop/5.2-Serverless/5.2.5-Lambda/1.png)

### 3.2 Tạo Lambda function

1. **Function options:**
- Chọn **Author from scratch**

2. **Basic information:**
- **Function name:** `workshop-lambda-sm-rds`
- **Runtime:** Nodejs 24.x
- **Architecture:** x86_64
- Mở rộng **Change default execution role**
- Chọn **Use an existing role**
- Chọn `workshop-lamda-secretsmng-role`

![Create Lambda - Basic information](/images/5-Workshop/5.2-Serverless/5.2.5-Lambda/2.png)

3. **Advanced settings:**
- Mở rộng **Advanced settings**
- Tích vào **Enable VPC**

**VPC:**
- Chọn `workshop-backend-vpc`

**Subnets:**
- Chọn `workshop-private-subnet-1` (10.0.1.0/24)

**Security groups:**
- Chọn `workshop-lambda-sg`

![Lambda VPC Config](/images/5-Workshop/5.2-Serverless/5.2.5-Lambda/3.png)

3. Nhấp vào **Create function**
4. Chờ vài phút để hệ thống tạo Lambda function
5. Tạo thành công

![Lambda create success](/images/5-Workshop/5.2-Serverless/5.2.5-Lambda/4.png)

### 3.3 Tải lên Gói triển khai (Deployment Package)

1. Trong trang function, tại tab **Code**
2. Nhấp vào menu thả xuống **Upload from**
3. Chọn **.zip file**

![Lambda Upload Zip](/images/5-Workshop/5.2-Serverless/5.2.5-Lambda/9.png)

4. Nhấp vào **Upload**
5. Chọn `/fcj-serverless-workshop/backend/userHandler.zip`
6. Nhấp vào **Save**

![Lambda Code Upload](/images/5-Workshop/5.2-Serverless/5.2.5-Lambda/10.png)

7. Lambda sẽ bắt đầu nhập mã nguồn của chúng ta (userHandler.zip)
8. Kết quả: bạn sẽ thấy các tệp mã nguồn ở thanh bên trái

![Lambda Code Uploaded](/images/5-Workshop/5.2-Serverless/5.2.5-Lambda/16.png)

### 3.4 Cấu hình biến môi trường (environment variables)
1. Đi tới tab **Configuration**
2. Ở thanh bên trái, nhấp vào **Environment variables**
3. Nhấp vào **Edit**

![Lambda env](/images/5-Workshop/5.2-Serverless/5.2.5-Lambda/5.png)

4. Ở màn hình tiếp theo, nhấp vào **Add environment variable**
5. Thêm các biến sau:
    - RDS-HOST: RDS endpoint của bạn
    - DB_NAME: tên cơ sở dữ liệu RDS của bạn
    - SECRET_NAME: tên secret của RDS-managed secret (tham khảo phần 5.2.4)
    - REGION: AWS region hiện tại của bạn

![env](/images/5-Workshop/5.2-Serverless/5.2.5-Lambda/12.png)

6. Quay lại tab **Code**, nhấp vào **Deploy**

![deploy](/images/5-Workshop/5.2-Serverless/5.2.5-Lambda/13.png)

### 3.5 Kiểm thử Lambda function

Bây giờ hãy kiểm thử Lambda function. Chúng ta sẽ kiểm tra route lấy tất cả người dùng (get all users).
1. Trong tab **Code** của Lambda function, nhấp vào nút **Test** hoặc **Create new test event** ở thanh bên trái
2. Một ngăn kéo (drawer) bên phải sẽ xuất hiện
3. Cấu hình sự kiện kiểm thử:
    - Event name: `test-get-users`
    - Event JSON:
```JSON
{
    "httpMethod": "GET",
    "pathParameters": null,
    "body": null
}
```
4. Nhấp vào **Save**

![create test event](/images/5-Workshop/5.2-Serverless/5.2.5-Lambda/17.png)

5. Bạn sẽ thấy sự kiện kiểm thử mới của mình ở thanh bên trái, di chuột vào nó và sau đó nhấp vào nút play để bắt đầu kiểm tra

![event](/images/5-Workshop/5.2-Serverless/5.2.5-Lambda/18.png)

6. Nếu mọi thứ suôn sẻ, bạn sẽ thấy phản hồi với trạng thái 200 và phần thân (body) chứa danh sách người dùng của chúng ta

![response](/images/5-Workshop/5.2-Serverless/5.2.5-Lambda/19.png)

## Bước 4: Giám sát Lambda với CloudWatch Logs

### 4.1 Xem CloudWatch Logs

1. Trong Lambda console, nhấp vào tab **Monitor**
2. Nhấp vào **View CloudWatch logs**

![Lambda Monitor Tab](/images/5-Workshop/5.2-Serverless/5.2.5-Lambda/20.png)

3. Nhấp vào **Log stream** mới nhất

![CloudWatch Log Streams](/images/5-Workshop/5.2-Serverless/5.2.5-Lambda/21.png)

### 4.2 Phân tích các mục Log

Bạn sẽ thấy nhật ký chi tiết cho mỗi lần gọi (invocation):
```
2024-11-29T10:00:00.000Z INFO [users-handler] process start.
2024-11-29T10:00:00.100Z INFO Failed to get secret: Error: Could not retrieve secret
2024-11-29T10:00:00.150Z INFO Connected to RDS PostgreSQL successfully.
2024-11-29T10:00:00.200Z INFO Inserting demo users...
2024-11-29T10:00:00.250Z INFO Demo users inserted.
2024-11-29T10:00:00.300Z INFO [users-handler] process end.
```

**Những điểm chính cần quan sát:**
-   Kết nối cơ sở dữ liệu thành công
-   Khởi tạo bảng (Table initialization)
-   Chèn dữ liệu demo (chỉ lần chạy đầu tiên)
-   Phương thức và đường dẫn yêu cầu (Request method and path)
-   Thực thi truy vấn và kết quả

### 4.3 Hiểu kết quả Log

**Tạo người dùng thành công:**
```
START RequestId: abc-123-def
[INFO] [users-handler] process start.
[INFO] Connected to RDS PostgreSQL successfully.
[INFO] Demo users inserted.
END RequestId: abc-123-def
REPORT RequestId: abc-123-def Duration: 1250.34 ms Billed Duration: 1251 ms Memory Size: 512 MB Max Memory Used: 128 MB Init Duration: 2345.67 ms
```

**Ý nghĩa của từng chỉ số:**
- **Duration:** Thời gian thực thi thực tế (1250.34 ms)
- **Billed Duration:** Thời gian được làm tròn để tính phí (1251 ms)
- **Memory Size:** Bộ nhớ được phân bổ (512 MB)
- **Max Memory Used:** Bộ nhớ sử dụng cao nhất (128 MB)
- **Init Duration:** Thời gian khởi tạo nguội - Cold start (2345.67 ms, chỉ lần gọi đầu tiên)

**Ví dụ về lỗi:**
```
START RequestId: xyz-789-abc
[INFO] [users-handler] process start.
[ERROR] Failed to get secret: ResourceNotFoundException: Secret not found
[ERROR] RDS connection failed: Error: Connection timeout
[INFO] [users-handler] process end.
END RequestId: xyz-789-abc
REPORT RequestId: xyz-789-abc Duration: 5000.12 ms Billed Duration: 5001 ms Memory Size: 512 MB Max Memory Used: 95 MB
```

### 4.4 Lọc Log theo mẫu (Filter Logs by Pattern)

CloudWatch Logs Insights cho phép bạn truy vấn nhật ký bằng cú pháp giống SQL.

1. Trong CloudWatch Logs, nhấp vào **Logs Insights** ở điều hướng bên trái
2. Chọn log group của bạn: `/aws/lambda/workshop-lambda-sm-rds`
3. Nhập truy vấn
4. Nhấp vào **Run query**

**Các truy vấn ví dụ:**

**Tìm tất cả các lỗi:**
```sql
fields @timestamp, @message
| filter @message like /ERROR/
| sort @timestamp desc
| limit 20
```

**Tìm các yêu cầu chậm (> 2 giây):**
```sql
fields @timestamp, @duration
| filter @type = "REPORT"
| filter @duration > 2000
| sort @duration desc
| limit 20
```

**Đếm yêu cầu theo phương thức HTTP:**
```sql
fields @timestamp, @message
| filter @message like /httpMethod/
| parse @message '"httpMethod":"*"' as method
| stats count() by method
```

**Tìm lỗi kết nối cơ sở dữ liệu:**
```sql
fields @timestamp, @message
| filter @message like /RDS connection failed/
| sort @timestamp desc
| limit 20
```

**Lấy thời gian thực thi trung bình:**
```sql
fields @timestamp, @duration
| filter @type = "REPORT"
| stats avg(@duration) as avg_duration, max(@duration) as max_duration, min(@duration) as min_duration
```

## Tóm tắt

Chúc mừng! Bạn đã thành công:
-   Sao chép (clone) và khám phá dự án TypeScript Lambda
-   Xây dựng và đóng gói bản triển khai Lambda
-   Triển khai Lambda function lên AWS
-   Cấu hình VPC, security groups, và biến môi trường
-   Kiểm thử các hoạt động CRUD
-   Thiết lập giám sát và ghi nhật ký CloudWatch

### Những gì bạn đã xây dựng

Lambda function của bạn hiện cung cấp:
- **API CRUD hoàn chỉnh** để quản lý người dùng
- **Kết nối cơ sở dữ liệu** với khả năng khởi tạo tự động
- **Thông tin xác thực an toàn** thông qua Secrets Manager
- **Cô lập VPC** để bảo mật
- **Ghi nhật ký toàn diện** để gỡ lỗi
- **Giám sát hiệu năng** thông qua CloudWatch
- **Mã an toàn kiểu (Type-safe code)** với TypeScript

### Kiến trúc cho đến hiện tại
```
Client Request
    ↓
API Gateway (sẽ được tạo ở Phần 5)
    ↓
Lambda Function (workshop-userHandler)
    ├→ VPC Endpoint → Secrets Manager → Lấy thông tin xác thực DB
    └→ VPC Private Subnet → RDS PostgreSQL → Thực thi truy vấn
    ↓
CloudWatch Logs (giám sát & gỡ lỗi)
```

## Các bước tiếp theo

Tiến tới **Phần 5: Thiết lập API Gateway** để tạo các REST API endpoints sẽ kích hoạt Lambda function của bạn và hiển thị nó ra internet.

---

**Sẵn sàng tiếp tục?** Lambda function của bạn hiện đã hoạt động đầy đủ và sẵn sàng được hiển thị thông qua API Gateway! 🚀
