---
title : "CloudFront Distribution Setup"
date :  "2025-09-15" 
weight : 3 
chapter : false
pre : " <b> 5.1.3 </b> "
---

## Tổng quan

Phần này bạn sẽ cấu hình Amazon CloudFront làm Content Delivery Network (CDN) phía trước S3 bucket. CloudFront sẽ cache nội dung tại các edge location trên toàn thế giới, giúp website tải nhanh hơn cho người dùng ở mọi nơi.

**Bạn sẽ thực hiện:**
- Tạo CloudFront distribution
- Cấu hình S3 bucket làm origin
- Thiết lập cache behaviors và tối ưu hóa
- Cấu hình SSL/TLS với HTTPS
- Cập nhật S3 bucket để hạn chế truy cập trực tiếp
- Kiểm tra CloudFront distribution

**Thời gian ước tính**: 45 phút

## Tại sao dùng CloudFront?

**Lợi ích so với truy cập trực tiếp S3:**
- **Performance**: Nội dung được phục vụ từ edge location gần người dùng (hơn 200 địa điểm toàn cầu)
- **Security**: Hỗ trợ HTTPS, bảo vệ DDoS, tích hợp với AWS WAF
- **Cost**: Giảm chi phí truyền dữ liệu S3 nhờ caching
- **Scalability**: Tự động xử lý tăng lưu lượng
- **Custom Domains**: Dùng tên miền riêng với chứng chỉ SSL

## Lưu ý về chi phí
### Free-tier:
- $0/tháng

### Paid-tier
1. $15/tháng
4. Tổng: <$0 hoặc <$1 nếu dùng bản Pro (xóa ngay sau khi hoàn thành workshop)

## Bước 1: Tạo CloudFront Distribution

### 1.1 Điều hướng đến CloudFront Console

1. Trong AWS Console, tìm "CloudFront"
2. Click vào **CloudFront** trong Services
3. Click **Create distribution**

![CloudFront Console](/images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/1.png)

### 1.2 Bước 1: Chọn gói dịch vụ
- Với workshop này, chúng ta sẽ dùng gói miễn phí.
- Click **Next**

![Choose a plan](/images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/2.png)

### 1.3 Bước 2: Bắt đầu cấu hình
- Ở bước này, bạn sẽ đặt tên cho distribution

**Distribution name**:
- Đặt là: `workshop-frontend-cf`

**Route 53 managed domain (optional)**:
- Nếu bạn đã có domain quản lý bởi Route 53, có thể nhập vào để dùng domain riêng thay vì domain mặc định của CloudFront

- Giữ các thiết lập còn lại mặc định
- Click **Next**

![Distribution name](/images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/3.png)

### 1.4 Bước 3: Chỉ định origin

**Origin type:**
- Chọn **Amazon S3** làm origin

**Origin:**
1. Click nút **Browse S3**
2. Trong modal hiện ra, chọn S3 bucket bạn đã tạo ở phần S3 Static Website Hosting. Sau đó click **Choose**

![S3 origin](/images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/4.png)

3. Sẽ có cảnh báo hiện ra do bucket đã cấu hình cho static website hosting. Có thể bỏ qua vì sẽ tắt **S3 static website hosting** ở bước sau

![S3 origin warning](/images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/5.png)

{{% notice note %}}
**Giải thích cảnh báo**:<br>
Ở [S3 Static Website Hosting]({{< relref "/5-Workshop/5.1-Frontend-deployment/5.1.2-S3-Hosting" >}}#23-configure-bucket-settings),
bucket đã được cấu hình public để dùng **S3 static website hosting**. CloudFront phát hiện cấu hình này nên khi click **Use website endpoint**, URL sẽ có dạng: `[your-bucket-name].s3-website-[region].amazonaws.com` (**S3 website endpoint**).<br>
Tuy **S3 static website hosting** cho phép truy cập trực tiếp, nhưng cần quyền public cho bucket, khiến nội dung bị lộ ra internet. Cách bảo mật hơn là giữ bucket private và chỉ cho CloudFront truy cập qua **Origin Access Control (OAC)**. Khi dùng OAC, origin URL sẽ là **bucket endpoint** dạng: `[your-bucket-name].s3.[region].amazonaws.com`
{{% /notice %}}

**Vì sao OAC tốt hơn:**
- S3 bucket luôn private (không public)
- Nội dung chỉ truy cập qua CloudFront
- Bảo mật tốt hơn
- Chức năng tương tự nhưng bảo vệ tốt hơn

Chúng ta sẽ cấu hình bảo mật này bằng cách chuyển từ S3 website endpoint sang OAC, rồi tắt quyền public cho bucket.

4. Xác nhận URL **S3 origin** có dạng:
    - Dạng: `[your-bucket-name].s3.[region].amazonaws.com `
    - Ví dụ: `workshop-frontend-thien-bucket.s3.ap-southeast-1.amazonaws.com`

5. **Origin path:** Để trống

**Settings**:
- Đảm bảo chọn **Allow private S3 bucket access to CloudFront - Recommended** để bật OAC
- Giữ các thiết lập còn lại mặc định

Click **Next**

![Origin settings](/images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/6.png)

### 1.5 Bước 4: Bật bảo mật

Mặc định, **Web Application Firewall (WAF)** đã được bật. Giữ nguyên các thiết lập ở bước này.
Chúng ta sẽ cấu hình WAF chi tiết hơn ở phần sau của workshop.

Click **Next**

![WAF](/images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/7.png)

### 1.6 Bước 5: Kiểm tra và tạo mới

1. Kiểm tra lại các thiết lập CloudFront
2. Click **Create distribution** khi đã sẵn sàng
3. Bạn sẽ được chuyển đến trang kết quả

**⏱️ Thời gian chờ**: CloudFront mất 5-15 phút để deploy. Trong lúc chờ, hãy cấu hình S3 Bucket để hỗ trợ OAC

![Creation successful](/images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/8.png)

## Bước 2: Bảo mật S3 Bucket (Chặn truy cập trực tiếp)

Sau khi CloudFront đã phục vụ nội dung, cần ngăn người dùng truy cập trực tiếp S3 mà không qua CloudFront.

### 2.1 Truy cập S3 Bucket

1. Vào S3 console
2. Click tên bucket
3. Vào tab **Permissions**

### 2.2 Chặn truy cập public

1. Cuộn đến **Block public access (bucket settings)**
2. Click **Edit**
3. **Chọn** cả 4 tuỳ chọn:
    - Block public access to buckets and objects granted through new access control lists (ACLs)
    - Block public access to buckets and objects granted through any access control lists (ACLs)
    - Block public access to buckets and objects granted through new public bucket or access point policies
    - Block public access to buckets and objects granted through any public bucket or access point policies
4. Click **Save changes**
5. Nhập `confirm` khi được hỏi
6. Click **Confirm**

![S3 Block Public Access](/images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/9.png)

### 2.3 Cập nhật Bucket Policy

1. Cuộn đến **Bucket policy**
2. Sẽ thấy policy cho phép CloudFront truy cập S3 đã được tạo tự động. Policy này cho phép CloudFront truy cập bucket

![S3 Block Public Access](/images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/10.png)

3. Bây giờ hãy xoá quyền **PublicReadGetObject** đã tạo ở
[S3 Static Website Hosting]({{< relref "/5-Workshop/5.1-Frontend-deployment/5.1.2-S3-Hosting" >}}#52-add-policy-json)

2. Click **Edit**
3. Xoá đoạn sau:

```json
 {
  "Sid": "PublicReadGetObject",
  "Effect": "Allow",
  "Principal": "*",
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::[your-bucket-name]/*"
}
```

5. Click **Save changes**

![S3 CloudFront Policy](/images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/11.png)

### 2.4 Tắt S3 Static Website Hosting

1. Trong bucket, vào tab **Properties**
2. Cuộn xuống phần **Static website hosting**, click **Edit**
3. Ở **Static website hosting**, chọn **Disable**
4. Click **Save changes**

![S3 Static Website Hosting](/images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/12.png)

## Bước 3: Kiểm tra CloudFront Distribution

Sau khi CloudFront hoàn tất việc deploy, bạn có thể kiểm tra và xác nhận mọi thứ hoạt động đúng.

### 3.1 Truy cập CloudFront distribution

1. Vào CloudFront console
2. Click vào distribution vừa tạo

![CloudFront distributions](/images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/13.png)

3. Trong tab **General**, tìm **Domain Name**. Đây là endpoint của CloudFront distribution, có dạng: `[your-distribution-id].cloudfront.net`

![CloudFront domain name](/images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/14.png)

### 3.2 Kiểm tra nội dung

1. Mở trình duyệt, nhập URL: `http://[your-distribution-id].cloudfront.net/[your-object-key]`
    - Thay thế `[your-distribution-id]` bằng CloudFront Domain Name
    - Thay thế `[your-object-key]` bằng tên file bạn đã upload lên S3

2. Nếu mọi thứ được cấu hình đúng, bạn sẽ thấy nội dung file. Nếu không, kiểm tra lại các bước cấu hình.

### 3.3 Kiểm tra bảo mật

1. Truy cập lại URL S3 trực tiếp (không qua CloudFront)
2. Bạn sẽ thấy lỗi truy cập bị từ chối (Access Denied), vì S3 bucket đã được cấu hình hạn chế truy cập trực tiếp.

![Access Denied](/images/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup/15.png)

## Tài nguyên tham khảo

- [AWS CloudFront Documentation](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Introduction.html)
- [AWS Pricing Calculator](https://calculator.aws/#/)

---
