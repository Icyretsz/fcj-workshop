---
title : "Yêu cầu trước khi bắt đầu"
date :  "2025-09-15" 
weight : 1 
chapter : false
pre : " <b> 5.1.1 </b> "
---

### Kiến thức AWS cần thiết

- **AWS Console Navigation**: Khả năng điều hướng AWS Management Console và tìm kiếm các dịch vụ
- **Basic AWS Concepts**: Hiểu biết về AWS regions, availability zones và các tương tác dịch vụ cơ bản
- **Không cần kinh nghiệm trước với S3, CloudFront hoặc WAF** - chúng tôi sẽ hướng dẫn từng bước

### Kỹ năng kỹ thuật cần thiết

- **Basic Web Development**: Hiểu biết về HTML, CSS và JavaScript
- **File System Operations**: Khả năng tạo, chỉnh sửa và tổ chức các tệp và thư mục
- **Command Line Basics**: Thoải mái với việc chạy các lệnh terminal/command prompt cơ bản
- **Text Editing**: Quen thuộc với bất kỳ code editor hoặc IDE nào

### Thiết lập tài khoản AWS cần thiết

Trước khi bắt đầu workshop này, hãy đảm bảo bạn có:

1. **AWS Account**
    - Tài khoản AWS đang hoạt động với quyền truy cập quản trị
    - Thẻ tín dụng đã đăng ký (bắt buộc ngay cả với Free Tier)
    - MFA (Multi-Factor Authentication) được bật trên tài khoản root (khuyến nghị mạnh mẽ)

2. **IAM User** (Khuyến nghị)
    - IAM user với các quyền phù hợp thay vì sử dụng tài khoản root
    - Các quyền cần thiết:
        - `AmazonS3FullAccess`
        - `CloudFrontFullAccess`
        - `WAFv2FullAccess`
        - `AWSCertificateManagerFullAccess` (nếu sử dụng custom domain)
    - Access key và secret key đã được tạo (để truy cập CLI)

3. **Billing Alerts**
    - Thiết lập AWS Budgets hoặc billing alerts để theo dõi chi phí
    - Khuyến nghị: Đặt cảnh báo ở ngưỡng $10

### Công cụ và phần mềm cần thiết

Cài đặt các công cụ sau trên máy tính của bạn:

1. **Text Editor hoặc IDE**
    - VS Code (khuyến nghị): https://code.visualstudio.com/
    - Hoặc bất kỳ editor nào bạn chọn (Sublime Text, Atom, v.v.)

2. **Web Browser**
    - Trình duyệt hiện đại (Chrome, Firefox, Safari hoặc Edge)
    - Khuyến nghị nhiều tab để điều hướng console

3. **Git** (Tùy chọn nhưng khuyến nghị)
    - Tải về: https://git-scm.com/
    - Sử dụng cho version control và lấy mã nguồn mẫu

### Ứng dụng mẫu

Chúng tôi sẽ cung cấp một trang web tĩnh đơn giản cho workshop này.

### Tùy chọn: Thiết lập Custom Domain

Nếu bạn muốn sử dụng custom domain (ví dụ: `www.yoursite.com`):

- **Domain Name**: Tên miền đã đăng ký (có thể sử dụng Route 53 hoặc nhà đăng ký bên ngoài)
- **DNS Access**: Khả năng sửa đổi các bản ghi DNS cho tên miền của bạn
- **Lưu ý**: Đây là tùy chọn; bạn có thể hoàn thành workshop bằng cách sử dụng tên miền mặc định của CloudFront

### Dự kiến chi phí cho Part 1: Frontend Deployment

**Các dịch vụ đủ điều kiện Free Tier:**
- **S3**: 5GB lưu trữ, 20,000 GET requests, 2,000 PUT requests (12 tháng đầu)
- **CloudFront**: 1TB data transfer out, 10,000,000 HTTP/HTTPS requests (12 tháng đầu)
- **AWS WAF**: Không có Free Tier, nhưng chi phí tối thiểu cho các quy tắc cơ bản

**Chi phí ước tính** (nếu vượt quá Free Tier):
- S3 storage: $0.023 mỗi GB mỗi tháng
- CloudFront data transfer: $0.085 mỗi GB (thay đổi theo region)
- WAF: $5.00 mỗi tháng cho mỗi web ACL + $1.00 cho mỗi rule mỗi tháng
- **Tổng chi phí ước tính cho workshop này**: $0-$2 (trong Free Tier) hoặc $5-$10 (với WAF)

**Mẹo tiết kiệm chi phí:**
- Xóa tài nguyên ngay sau workshop nếu không tiếp tục
- Sử dụng các tệp mẫu nhỏ để giảm thiểu chi phí lưu trữ và truyền tải
- Bắt đầu với các quy tắc WAF cơ bản và mở rộng sau

## Sẵn sàng bắt đầu?

Sau khi hoàn thành tất cả các yêu cầu và xác minh thiết lập của bạn, bạn đã sẵn sàng để bắt đầu xây dựng cơ sở hạ tầng frontend an toàn, được phân phối toàn cầu!

Hãy chuyển sang **Part 1: S3 Static Website Hosting**.
