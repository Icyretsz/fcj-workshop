---
title : "Cài đặt API Gateway"
date :  "2025-09-15"
weight : 6
chapter : false
pre : " <b> 5.2.6 </b> "
---

## Tổng quan

Trong phần này, bạn sẽ tạo một Amazon API Gateway REST API đóng vai trò là điểm đầu vào (entry point) cho serverless backend của bạn. API Gateway sẽ nhận các yêu cầu HTTP từ client (máy khách), định tuyến chúng đến Lambda function của bạn và trả về phản hồi.

**Những gì bạn sẽ đạt được:**
- Hiểu các khái niệm về API Gateway và các loại tích hợp (integration types)
- Tạo một REST API trong API Gateway
- Định nghĩa các tài nguyên (resources) và phương thức (methods) cho các thao tác người dùng
- Cấu hình tích hợp Lambda proxy (Lambda proxy integration)
- Bật CORS để tích hợp với frontend
- Triển khai API đến một stage
- Kiểm thử các endpoint API với nhiều công cụ khác nhau
- Giám sát mức độ sử dụng và hiệu năng của API
- Hiểu về giá cả của API Gateway

**Thời gian ước tính**: 40-50 phút

## Kiến trúc API Gateway

### Những gì chúng ta sẽ xây dựng
```
Client (Trình duyệt/Mobile/Postman)
    ↓
HTTPS Request
    ↓
API Gateway REST API
    ├── POST   /users          → Lambda (Tạo User)
    ├── GET    /users          → Lambda (Lấy tất cả Users)
    ├── GET    /users/{id}     → Lambda (Lấy một User)
    ├── PUT    /users/{id}     → Lambda (Cập nhật User)
    └── DELETE /users/{id}     → Lambda (Xóa User)
    ↓
Lambda Function (workshop-userHandler)
    ↓
RDS PostgreSQL
```

### Cân nhắc về chi phí
## Bậc miễn phí (Free-tier):

1 triệu cuộc gọi REST API | 1 triệu cuộc gọi HTTP API | 1 triệu tin nhắn | 750,000 phút kết nối mỗi tháng

## Bậc trả phí (Paid-tier)
1. REST API
   | **Số cuộc gọi API (mỗi tháng)** | **Giá (trên mỗi triệu)** |
   |---------------------------|--------------------------|
   | 333 triệu đầu tiên        | $4.25                    |
   | 667 triệu tiếp theo       | $3.53                    |
   | 19 tỷ tiếp theo           | $3.00                    |
   | Trên 20 tỷ                | $1.91                    |

2. Caching: 0.5GB -> $0.028/giờ
3. Chi phí bổ sung: CloudWatch logs (đủ điều kiện Free-tier)
4. Tổng cộng: <$5 (hãy dọn dẹp ngay sau khi hoàn thành workshop)

### Các khái niệm API Gateway

**REST API:**
- API dựa trên tài nguyên (ví dụ: `/users`, `/users/{id}`)
- Hỗ trợ tất cả các phương thức HTTP (GET, POST, PUT, DELETE, v.v.)
- Chuyển đổi yêu cầu/phản hồi (Request/response transformation)
- Tích hợp sẵn tính năng điều tiết (throttling) và bộ nhớ đệm (caching)

**Resources (Tài nguyên):**
- Đường dẫn URL (ví dụ: `/users`)
- Có thể lồng nhau (ví dụ: `/users/{id}/tasks`)

**Methods (Phương thức):**
- Các hoạt động HTTP trên tài nguyên (GET, POST, PUT, DELETE)
- Mỗi method có thể có cấu hình tích hợp khác nhau

**Integration Types (Các loại tích hợp):**
- **Lambda Proxy**: Chuyển toàn bộ yêu cầu đến Lambda (khuyên dùng)
- **Lambda**: Ánh xạ yêu cầu/phản hồi tùy chỉnh
- **HTTP**: Proxy đến một HTTP endpoint
- **Mock**: Trả về phản hồi tĩnh
- **AWS Service**: Tích hợp trực tiếp với dịch vụ AWS

**Stages:**
- Môi trường triển khai (ví dụ: dev, staging, prod)
- Mỗi stage có URL duy nhất
- Có thể có các cài đặt khác nhau cho mỗi stage

## Bước 1: Tạo REST API

### 1.1 Truy cập API Gateway Console

1. Đi đến AWS Console
2. Tìm kiếm "API Gateway"
3. Nhấp vào **API Gateway** trong mục Services

![API Gateway Console](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/1.png)

### 1.2 Tạo API

1. Nhấp vào **Create an API**

Bạn sẽ thấy một số loại API:

- **HTTP API**: Đơn giản hơn, rẻ hơn, nhanh hơn (giảm 70% chi phí)
- **REST API**: Đầy đủ tính năng, chuyển đổi yêu cầu/phản hồi
- **WebSocket API**: Giao tiếp hai chiều thời gian thực
- **REST API (Private)**: Chỉ truy cập trong VPC

2. Dưới mục **REST API**, nhấp vào **Build**

![API Gateway REST API](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/2.png)

### 1.3 Cấu hình cài đặt API

1. **Choose the protocol:**
- Giữ chọn **REST**

2. **Create new API:**
- Chọn **New API**

**Chi tiết API:**

3. **API name:** `workshop-user-api`

4. **Description:** `REST API for user management in serverless workshop`

5. **Endpoint Type:**
- Chọn **Regional**
    - Được triển khai trong region hiện tại
    - Độ trễ thấp hơn cho người dùng trong cùng region
    - Có thể thêm CloudFront sau này để phân phối toàn cầu

{{% notice info %}}
**Các loại Endpoint:**<br>
**Regional**: Triển khai trong một region duy nhất, được khuyến nghị cho hầu hết các trường hợp sử dụng.<br>
**Edge Optimized**: Tự động phân phối qua CloudFront (tăng độ trễ cho lưu lượng truy cập trong region).<br>
**Private**: Chỉ có thể truy cập từ bên trong VPC.<br>
Đối với workshop này, **Regional** là tốt nhất. Bạn đã có CloudFront từ Phần 1: Frontend Deployment nếu bạn muốn phân phối toàn cầu.
{{% /notice %}}

6. **Security policy**
- Chọn **SecurityPolicy_TLS13_1_2_2021_06**
- Tùy chọn này bảo vệ dữ liệu đang truyền giữa máy khách và máy chủ bằng TLS 1.3

3. Nhấp vào **Create API**

![API Gateway Settings](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/3.png)

Bạn sẽ được đưa đến bảng điều khiển API Gateway hiển thị API mới của bạn.

![API Gateway Created](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/4.png)

## Bước 2: Tạo Resources và Methods

### 2.1 Tạo Resource /users

Một resource đại diện cho một đường dẫn endpoint của REST API.

1. Trong API Gateway console, chọn **Resources** ở thanh điều hướng bên trái (nếu chưa được chọn)
2. Nhấp vào **Create Resource**

**Proxy resource**:
- Về cơ bản là một resource bắt tất cả (catch-all)
- Một proxy resource (thường được tạo dưới dạng {proxy+}) là một loại resource đặc biệt trong API Gateway chuyển tiếp tất cả các yêu cầu đến một backend duy nhất (chẳng hạn như Lambda function) — bất kể đường dẫn URL hay phương thức HTTP nào.
- Chúng ta sẽ không sử dụng loại này cho workshop vì API của chúng ta đơn giản.

**Resource Path:** `/`

**Resource Name:** `users`
- Đây sẽ trở thành đường dẫn URL

**CORS:**
- Tích vào ô này
- Tự động thêm phương thức OPTIONS với các tiêu đề (headers) CORS
- Bật CORS trên tất cả các method con
- Bắt buộc đối với frontend dựa trên trình duyệt

![API Gateway Resource Settings](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/5.png)

4. Nhấp vào **Create Resource**

Bạn sẽ thấy `/users` xuất hiện trong cây resource.

![API Gateway Users Resource](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/6.png)

### 2.2 Tạo Resource /users/{id}

Tạo một resource con với tham số đường dẫn (path parameter) cho các thao tác trên một người dùng đơn lẻ.

1. Chọn resource `/users` (nhấp vào nó)
2. Nhấp vào **Create method**

**Resource con mới:**

**Resource Path:** `user`
- Số ít, đại diện cho một người dùng

**Resource Name:** `{id}`
- Dấu ngoặc nhọn biểu thị một tham số đường dẫn (path parameter)

**CORS:**
- Tích vào ô này

![API Gateway ID Resource](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/7.png)

3. Nhấp vào **Create Resource**

Cây resource của bạn bây giờ hiển thị:

![API Gateway Resource Tree](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/8.png)

### 2.3 Tạo Method POST trên /users

Methods định nghĩa các hoạt động HTTP trên resources.

1. Nhấp vào resource `/users`
2. Nhấp vào **Create Method**
3. Chọn **POST**

**Thiết lập - POST:**

**Integration type:**
- Chọn **Lambda Function**

**Use Lambda Proxy integration:**
- Tích vào ô này
- Chuyển toàn bộ yêu cầu đến Lambda nguyên trạng
- Lambda trả về phản hồi được định dạng theo API Gateway

**Lambda Region:**
- Chọn region của bạn (ví dụ: `ap-southeast-1`)

**Lambda Function:**
- Nhập: `workshop-lambda-sm-rds`
- Sẽ tự động gợi ý

**Permission prompt:**
Bạn sẽ thấy một cửa sổ bật lên: "Add Permission to Lambda Function"

Điều này cấp quyền cho API Gateway để gọi Lambda function của bạn.

7. Nhấp vào **Create method**

![API Gateway POST Setup](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/9.png)

### 2.4 Tạo Method GET trên /users

Lấy tất cả người dùng.

1. Nhấp vào resource `/users`
2. Nhấp vào **Create Method**
3. **Method**: **GET**

**Thiết lập - GET:**
- **Integration type:** Lambda Function
- **Use Lambda Proxy integration:** Đã tích
- **Lambda Function:** `workshop-lambda-sm-rds`

4. Nhấp vào **Create method**

### 2.5 Tạo Method GET trên /users/{id}

Lấy một người dùng.

1. Nhấp vào resource `/users/{id}`
2. Nhấp vào **Create Method**
3. **Method**: **GET**

**Thiết lập - GET:**
- **Integration type:** Lambda Function
- **Use Lambda Proxy integration:** Đã tích
- **Lambda Function:** `workshop-lambda-sm-rds`

4. Nhấp vào **Create method**

### 2.6 Tạo Method PUT trên /users/{id}

Cập nhật người dùng.

1. Nhấp vào resource `/users/{id}`
2. Nhấp vào **Create Method**
3. **Method**: **PUT**

**Thiết lập - PUT:**
- **Integration type:** Lambda Function
- **Use Lambda Proxy integration:** Đã tích
- **Lambda Function:** `workshop-lambda-sm-rds`

4. Nhấp vào **Create method**

### 2.7 Tạo Method DELETE trên /users/{id}

Xóa người dùng.

1. Nhấp vào resource `/users/{id}`
2. Nhấp vào **Create Method**
3. **Method**: **DELETE**

**Thiết lập - DELETE:**
- **Integration type:** Lambda Function
- **Use Lambda Proxy integration:** Đã tích
- **Lambda Function:** `workshop-lambda-sm-rds`

4. Nhấp vào **Create method**

### 2.8 Xác minh Cấu trúc Resource

Cấu trúc API của bạn bây giờ sẽ trông như sau:

![API Gateway Complete Structure](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/10.png)

### 2.9 Bật CORS trên từng resource
1. Nhấp vào resource **/users**
2. Nhấp vào **Enable CORS**
3. Chọn các methods: GET, POST
4. **Access-Control-Allow-Origin**: nhập CloudFront endpoint của bạn để giới hạn origin, hoặc `*` để debug
5. Nhấp vào **Save**

![API Gateway Complete Structure](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/26.png)

6. Làm tương tự cho resource **/{id}** (các methods: GET, PUT, DELETE)
7. Sau khi bật CORS cho hai resource đó, bạn sẽ thấy method OPTION trong mỗi resource
8. Nhấp vào method OPTION, đi tới **Integration response** để xem chi tiết

![API Gateway Complete Structure](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/27.png)

## Bước 3: Triển khai API

Các API phải được triển khai đến một stage trước khi chúng có thể truy cập được.

### 3.1 Tạo Deployment

1. Nhấp vào **Deploy API**

**Deployment stage:**
- Chọn **[New Stage]**

**Stage name:** `dev`
- Viết tắt của development
- Các tên phổ biến khác: `prod`, `staging`, `test`

**Stage description:** `Development stage for workshop`

**Deployment description:** `Initial deployment`

![API Gateway Deploy Settings](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/11.png)

3. Nhấp vào **Deploy**

### 3.2 Lấy API Endpoint

Sau khi triển khai, bạn sẽ thấy **Stage Editor**.

**Invoke URL** là URL cơ sở (base URL) của API:
```
https://abc123xyz.execute-api.[region].amazonaws.com/dev
```

![API Gateway Invoke URL](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/12.png)

## Bước 4: Kiểm thử API Endpoints

### 4.1 Kiểm thử với API Gateway Console

API Gateway cung cấp một công cụ kiểm thử tích hợp sẵn.

**Kiểm thử POST /users (Tạo User):**

1. Ở điều hướng bên trái, nhấp vào **Resources**
2. Nhấp vào `/users` → **POST** method
3. Đi đến tab **Test**

**Request Body:**
```json
{
  "cognitoSub": "test-sub-123",
  "username": "Test User",
  "email": "test@example.com",
  "role": "user",
  "phoneNumber": "1234567890"
}
```

![API Gateway Test POST](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/13.png)

4. Nhấp vào **Test**

**Phản hồi mong đợi (Expected Response):**

**Status:** 201

**Response Body:**
```json
{
  "success": true,
  "data": {
    "id": 3,
    "cognito_sub": "test-sub-123",
    "username": "Test User",
    "email": "test@example.com",
    "role": "user",
    "phone_number": "1234567890"
  }
}
```

**Response Headers:**
```
Content-Type: application/json
```

**Logs:**
Hiển thị nhật ký thực thi Lambda trực tiếp

![API Gateway Test Response](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/14.png)

**Kiểm thử GET /users (Lấy tất cả Users):**

1. Nhấp vào `/users` → **GET** method
2. Nhấp vào **Test**
3. Không cần request body
4. Nhấp vào **Test**

**Phản hồi mong đợi:**

**Status:** 200

**Response Body:**
```json
{
  "success": true,
  "data": [
    {
      "id": 1,
      "cognito_sub": "demo-sub-1",
      "username": "Alice",
      "email": "alice@example.com",
      "role": "admin",
      "phone_number": "1234567890"
    },
    {
      "id": 2,
      "cognito_sub": "demo-sub-2",
      "username": "Bob",
      "email": "bob@example.com",
      "role": "user",
      "phone_number": "0987654321"
    },
    {
      "id": 3,
      "cognito_sub": "test-sub-123",
      "username": "Test User",
      "email": "test@example.com",
      "role": "user",
      "phone_number": "1234567890"
    }
  ]
}
```

![API Gateway Test Response](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/15.png)

**Kiểm thử GET /users/{id} (Lấy một User):**

1. Nhấp vào `/users/{id}` → **GET** method
2. Nhấp vào **Test**

**Path Parameters:**
- **id:** `1`

![API Gateway Test Response](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/16.png)

3. Nhấp vào **Test**

**Phản hồi mong đợi:**

**Status:** 200

**Response Body:**
```json
{
  "success": true,
  "data": {
    "id": 1,
    "cognito_sub": "demo-sub-1",
    "username": "Alice",
    "email": "alice@example.com",
    "role": "admin",
    "phone_number": "1234567890"
  }
}
```

### 4.2 Kiểm thử với curl (Command Line)

Kiểm thử API đã triển khai của bạn từ terminal:

**Thiết lập URL API của bạn:**
```bash
API_URL="https://YOUR-API-ID.execute-api.[region].amazonaws.com/dev"
```

**Tạo User:**
```bash
curl -X POST "${API_URL}/users" \
  -H "Content-Type: application/json" \
  -d '{
    "cognitoSub": "curl-test-456",
    "username": "Curl User",
    "email": "curl@example.com",
    "role": "user",
    "phoneNumber": "5551234567"
  }'
```

**Lấy tất cả Users:**
```bash
curl -X GET "${API_URL}/users"
```

**Lấy một User:**
```bash
curl -X GET "${API_URL}/users/1"
```

**Cập nhật User:**
```bash
curl -X PUT "${API_URL}/users/1" \
  -H "Content-Type: application/json" \
  -d '{
    "username": "Updated Name",
    "email": "updated@example.com",
    "role": "admin",
    "phoneNumber": "9998887777"
  }'
```

**Xóa User:**
```bash
curl -X DELETE "${API_URL}/users/3"
```

### 4.3 Kiểm thử với Postman

Postman cung cấp giao diện thân thiện để kiểm thử API.

**Import API vào Postman:**

1. Trong API Gateway console, đi đến **Stages** → **dev**
2. Nhấp vào **Stage action** → **Export**
3. Trong modal **Export API**, để nguyên tất cả cài đặt mặc định. Nhấp vào **Export API**
4. Tải xuống tệp json
5. Mở Postman, nhấp vào **Import**
6. Trên modal mở ra, nhấp vào **files**

![Postman Import](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/17.png)

7. Trên modal tiếp theo, nhấp vào **Import**

![Postman Import](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/18.png)

8. Mở rộng **workshop-user-api**, chọn **GET /users** để kiểm thử route lấy tất cả người dùng
9. Nhấp vào **Send**
10. Xem phản hồi

![Postman response](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/19.png)

## Bước 5: Cấu hình cài đặt Stage

### 5.1 Bật ghi nhật ký CloudWatch

1. Trong API Gateway console, đi đến **Stages**
2. Nhấp vào stage **dev**
3. Dưới thẻ **Logs and racing**, nhấp vào **Edit**

**Cài đặt CloudWatch:**

**CloudWatch Logs:**
- Chọn **Errors and info logs**
- Bao gồm:
    - Yêu cầu nhận được
    - Phần thân yêu cầu (nếu được bật)
    - Yêu cầu tích hợp
    - Phản hồi tích hợp
    - Tóm tắt thực thi
    - Lỗi

**Data tracing:**
- Tích vào ô này (cho development)
- Ghi nhật ký phần thân của request/response
- Tắt trong production (có thể chứa dữ liệu nhạy cảm)

**Enable Detailed Metrics:**
- Tích vào ô này
- Cung cấp các metrics ở cấp độ method
- Chi phí bổ sung nhỏ ($0.50/tháng cho mỗi metric)

![API Gateway Stage Logging](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/20.png)

4. Nhấp vào **Save Changes**

**Cấp quyền cho API Gateway:**

Nếu đây là API đầu tiên của bạn có bật ghi nhật ký, bạn sẽ cần thiết lập một IAM role:

1. Trong **IAM** dashboard, tạo role với:
    - **Trusted entity type:** AWS Service
    - **Use case:** API Gateway
    - **Policy:** **AmazonAPIGatewayPushToCloudWatchLogs**
    - **Role name:** `api-gw-push-cloudwatch-logs`
2. Sao chép Role ARN
3. Quay lại **API Gateway** dashboard
4. Ở thanh bên trái, nhấp vào **Settings**
5. Trong phần **Logging**, nhấp vào **Edit**
6. Dán Role ARN vào ô văn bản
6. Nhấp vào **Save changes**

### 5.2 Cấu hình Throttling (Điều tiết)

Bảo vệ API của bạn khỏi bị lạm dụng bằng cách giới hạn tốc độ.

1. Vẫn trong cài đặt stage **dev**
2. Trong thẻ **Stage details**, nhấp vào **Edit**

**Throttling settings:**

**Enabled**

**Rate:** `1000` yêu cầu mỗi giây
- Khả năng chịu tải đột ngột (Burst capacity) cho các đợt tăng vọt tạm thời

**Burst:** `2000` yêu cầu
- Tổng số yêu cầu được phép trong một đợt burst

![API Gateway Throttling](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/22.png)


**Giới hạn Throttling:**

Khi vượt quá giới hạn:
- Client nhận được lỗi **429 Too Many Requests**
- Các yêu cầu bị từ chối trước khi đến Lambda
- Bảo vệ backend khỏi bị quá tải
- Không tốn phí Lambda cho các yêu cầu bị điều tiết

**Giới hạn mặc định của tài khoản AWS:**
- 10,000 yêu cầu mỗi giây mỗi region
- Có thể yêu cầu tăng thông qua phiếu hỗ trợ

Đối với workshop này, 1000 req/s là quá đủ.


### 5.3 Bật Caching (Tùy chọn)

API caching giúp giảm số lần gọi Lambda và cải thiện hiệu năng.

1. Trong stage **dev**, nhấp vào tab **Settings**
2. Cuộn đến **Cache Settings**

**Provision API cache:**
- Tích vào ô này

**Cache capacity:** `0.5 GB`
- Kích thước nhỏ nhất
- Đủ cho workshop

**Cache time-to-live (TTL):** `300` giây (5 phút)
- Thời gian phản hồi được lưu trong bộ nhớ đệm

**Per-key cache invalidation:**
- Vô hiệu hóa dữ liệu nhất định trong bộ nhớ đệm dựa trên một khóa thay vì toàn bộ bộ nhớ đệm

![API Gateway Throttling](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/21.png)

**Chi phí Caching:**

API Gateway caching tương đối đắt:
- 0.5 GB cache: $0.028/giờ (~$20.16/tháng)

**Đối với workshop này:** Bỏ qua caching để tránh chi phí. Nó được đề cập ở đây để đầy đủ thông tin.

**Khi nào nên sử dụng caching:**
- Lưu lượng đọc cao (>1000 req/phút)
- Dữ liệu không thay đổi thường xuyên
- Độ trễ là yếu tố quan trọng
- Chi phí gọi Lambda > chi phí cache

Đối với dữ liệu người dùng thay đổi thường xuyên, caching có thể không phù hợp.


## Bước 6: Giám sát Hiệu năng API

### 6.1 Xem Metrics của API Gateway

1. Trong API Gateway console, đi đến **Dashboard** (điều hướng bên trái)
2. Chọn API của bạn: `workshop-user-api`
3. Chọn stage: `dev`

**Các metrics được hiển thị:**

**API calls:**
- Tổng số yêu cầu trong khoảng thời gian
- Phân chia theo giờ/ngày

**Integration latency (Độ trễ tích hợp):**
- Thời gian dành cho Lambda function
- Không bao gồm thời gian xử lý của API Gateway

**Latency (Độ trễ tổng):**
- Tổng thời gian yêu cầu (API Gateway + Lambda)
- Bao gồm mạng, tích hợp và thời gian xử lý

**4XX errors:**
- Lỗi từ phía client (yêu cầu sai, không tìm thấy, v.v.)
- Nên thấp trong các API được thiết kế tốt

**5XX errors:**
- Lỗi từ phía server (lỗi Lambda, hết thời gian chờ, v.v.)
- Cần được giám sát chặt chẽ

![API Gateway Dashboard](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/23.png)

### 6.2 Xem CloudWatch Logs

1. Đi đến CloudWatch console
2. Nhấp vào **Log groups**
3. Tìm: `API-Gateway-Execution-Logs_{api-id}/dev`
4. Nhấp vào log group
5. Nhấp vào log stream mới nhất

**Mẫu mục nhật ký:**
```
(abc-123-def) Method request body before transformations: {
  "cognitoSub": "test-123",
  "username": "Test User",
  "email": "test@example.com",
  "role": "user",
  "phoneNumber": "1234567890"
}

(abc-123-def) Endpoint request URI: https://lambda.us-east-1.amazonaws.com/2015-03-31/functions/arn:aws:lambda:us-east-1:123456789012:function:workshop-userHandler/invocations

(abc-123-def) Endpoint response body before transformations: {
  "statusCode": 201,
  "headers": {
    "Content-Type": "application/json"
  },
  "body": "{\"success\":true,\"data\":{\"id\":1,...}}"
}

(abc-123-def) Method response body after transformations: {
  "success": true,
  "data": {
    "id": 1,
    "cognito_sub": "test-123",
    "username": "Test User",
    "email": "test@example.com",
    "role": "user",
    "phone_number": "1234567890"
  }
}

(abc-123-def) Method completed with status: 201
```

![API Gateway CloudWatch Logs](/images/5-Workshop/5.2-Serverless/5.2.6-API-Gateway/24.png)

## Tóm tắt

Chúc mừng! Bạn đã thành công:
-   Tạo REST API trong API Gateway
-   Định nghĩa resources và methods cho các thao tác CRUD
-   Cấu hình tích hợp Lambda proxy
-   Bật CORS để tương thích với frontend
-   Triển khai API đến stage dev
-   Kiểm thử endpoints với nhiều công cụ
-   Cấu hình ghi nhật ký và giám sát
-   Thiết lập throttling để bảo vệ API

### Những gì bạn đã xây dựng

API Gateway của bạn hiện cung cấp:
- **RESTful endpoints** cho tất cả các thao tác người dùng
- **Bảo mật HTTPS** theo mặc định
- **Hỗ trợ CORS** để tích hợp frontend
- **Request throttling** để bảo vệ hệ thống
- **Ghi nhật ký CloudWatch** để gỡ lỗi
- **Metrics** để giám sát

## Bước tiếp theo

Tiến tới **Phần 6: Cấu hình Amazon Cognito** để bảo mật các API endpoints với xác thực được cung cấp bởi **AWS Cognito**.

---

**Sẵn sàng tiếp tục?** Các API endpoints của bạn hiện đã hoạt động đầy đủ và sẵn sàng! 🚀
