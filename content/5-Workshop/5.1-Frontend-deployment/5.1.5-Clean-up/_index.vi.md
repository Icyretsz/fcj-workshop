---
title : "Dọn dẹp"
date :  "2025-09-15" 
weight : 5
chapter : false
pre : " <b> 5.1.5 </b> "
---

## Tổng quan

Phần này hướng dẫn cách dọn dẹp tài nguyên bạn đã tạo trong quá trình triển khai frontend. Dọn dẹp giúp tránh phát sinh chi phí AWS, đặc biệt với các tài nguyên có phí hàng tháng như WAF Web ACL.

**Bạn sẽ học:**
- Cách xóa tài nguyên an toàn, đúng thứ tự
- Hiểu về phụ thuộc giữa các tài nguyên
- Ảnh hưởng chi phí khi giữ hoặc xóa tài nguyên
- Cách lưu cấu hình để sử dụng lại
- Tùy chọn dọn dẹp một phần

**Thời gian ước tính**: 15-20 phút

## Có nên dọn dẹp không?

### Giữ lại tài nguyên nếu:
- ✅ Bạn sẽ tiếp tục ngay với Phần 2: Serverless Backend
- ✅ Muốn giữ frontend để tham khảo
- ✅ Dùng cho dự án thực tế
- ✅ Chi phí phù hợp với nhu cầu

### Dọn dẹp nếu:
- ✅ Đã hoàn thành workshop và không cần tài nguyên nữa
- ✅ Muốn giảm chi phí AWS
- ✅ Chỉ thực hành và sẽ tạo lại sau
- ✅ Gần hết hạn Free Tier

## Dọn dẹp hoàn toàn (theo thứ tự)

Làm theo thứ tự sau để tránh lỗi phụ thuộc:

### Bước 1: Disable và xóa CloudFront Distribution

CloudFront distribution phải được disable trước khi xóa.

#### 1.1 Disable Distribution

1. Vào CloudFront console
2. Chọn distribution của bạn (tick vào ô)
3. Click **Disable**
4. Xác nhận bằng cách click **Disable** trong modal

**Trạng thái thay đổi:**
- **Deploying**: Distribution đang được disable
- **Deployed**: Đã disable thành công

**Thời gian chờ:** 5-15 phút

#### 1.2 Chờ trạng thái "Deployed"

1. Ở trang CloudFront distributions
2. Refresh định kỳ (2-3 phút/lần)
3. Đợi đến khi cột **Status** hiển thị **Deployed**
4. Trường **Last modified** hiển thị ngày

![CloudFront Disabled](/images/5-Workshop/5.1-Frontend-deployment/5.1.5-Cleanup/2.png)

#### 1.3 Xóa Distribution

1. Chọn distribution đã disable (tick vào ô)
2. Click **Delete**
3. Xác nhận bằng cách click **Delete** trong modal

**Kết quả mong đợi:** Distribution biến mất khỏi danh sách

**Lưu ý:** Nếu bạn đã nâng cấp distribution lên Pro, phải đợi đến chu kỳ thanh toán tiếp theo mới xóa được.

### Bước 2: Xóa S3 Bucket

S3 bucket phải rỗng trước khi xóa.

#### 2.1 Làm rỗng Bucket

1. Vào S3 console
2. Click vào tên bucket: `workshop-frontend-[tên-bạn]-[random]`
3. Nếu có file, click **Empty**
4. Nhập `permanently delete` để xác nhận
5. Click **Empty**

![S3 Empty Bucket](/images/5-Workshop/5.1-Frontend-deployment/5.1.5-Cleanup/4.png)

#### 2.2 Xóa Bucket

1. Quay lại danh sách S3 buckets
2. Chọn bucket (tick vào ô)
3. Click **Delete**
4. Nhập tên bucket để xác nhận
5. Click **Delete bucket**

### Bước 3: Xóa SSL/TLS Certificate (Tùy chọn)

Chỉ thực hiện nếu bạn đã tạo custom SSL certificate trong ACM cho custom domains.

#### 3.1 Kiểm tra Certificate đang sử dụng

Trước khi xóa, kiểm tra certificate có đang dùng ở nơi khác không:

1. Vào Certificate Manager console
2. Đảm bảo bạn ở vùng **us-east-1**
3. Tìm certificate của bạn
4. Kiểm tra cột **In use?**

**Nếu "Yes"**: Không xóa (vẫn đang liên kết với tài nguyên khác)
**Nếu "No"**: Có thể xóa an toàn

#### 3.2 Xóa Certificate

1. Chọn certificate (tick vào ô)
2. Click **Delete**
3. Xác nhận bằng cách click **Delete** trong modal

![ACM Delete](/images/5-Workshop/5.1-Frontend-deployment/5.1.5-Cleanup/3.png)

**Kết quả mong đợi:** Certificate biến mất khỏi danh sách

{{% notice note %}}
**Dịch vụ miễn phí:**
ACM certificate là miễn phí, nên xóa không giúp tiết kiệm chi phí. Bạn có thể giữ lại certificate nếu:<br>
Bạn sẽ tạo lại distribution sau này<br>
Bạn dùng cùng domain cho dịch vụ AWS khác<br>
Quá trình xác thực mất nhiều thời gian (xóa sẽ phải xác thực lại)<br>
{{% /notice %}}

### Bước 4: Xóa CloudWatch Alarms

Chỉ thực hiện nếu bạn đã tạo CloudWatch alarms ở phần tùy chọn.

1. Vào CloudWatch console
2. Click **Alarms** ở menu bên trái
3. Chọn alarm: `WAF-High-Blocked-Requests`
4. Click **Actions** → **Delete**
5. Xác nhận xóa

![CloudWatch Delete Alarm](/images/5-Workshop/5.1-Frontend-deployment/5.1.5-Cleanup/5.png)

### Bước 5: Xóa CloudWatch log group

1. Click **Logs** -> **Log groups**
2. Chọn log group bạn đã tạo ở [Phần 3]({{< relref "/5-Workshop/5.1-Frontend-deployment/5.1.4-WAF" >}}#91-enable-logging-destination-in-waf)
`aws-waf-logs-workshop1` và chọn delete

### Bước 6: Xóa SNS Topics

1. Vào SNS console
2. Click **Topics** ở menu bên trái
3. Chọn topic: `Default_CloudWatch_Alarms_Topic`
4. Click **Delete**
5. Nhập `delete me` để xác nhận
6. Click **Delete**

## Tóm tắt

### Checklist hoàn thành dọn dẹp

**Nếu dọn dẹp hoàn toàn:**
- [ ] CloudFront distribution đã disable và xóa
- [ ] SSL certificate đã xóa (nếu có tạo)
- [ ] S3 bucket đã làm rỗng và xóa
- [ ] CloudWatch alarms đã xóa
- [ ] SNS topics đã xóa
- [ ] Đã kiểm tra không còn chi phí phát sinh trong Billing Dashboard

### Tiếp theo?

Nếu tiếp tục với Phần 2: Serverless backend:
- Giữ lại tài nguyên hiện có HOẶC
- Triển khai backend
- Backend sẽ tích hợp với hạ tầng frontend này

Nếu đã hoàn thành workshop:
- Đã dọn dẹp toàn bộ tài nguyên
- Không còn chi phí phát sinh
- Bạn đã có kiến thức và kỹ năng! 🎉

**Chúc mừng!** Bạn đã hoàn thành Phần 1: Triển khai Frontend, bao gồm cả dọn dẹp tài nguyên đúng cách. Bạn đã hiểu cách triển khai, bảo mật và quản lý frontend serverless trên AWS! 🎉
