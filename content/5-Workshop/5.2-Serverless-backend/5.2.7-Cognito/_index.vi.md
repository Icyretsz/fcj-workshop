---
title : "Cấu hình Amazon Cognito"
date :  "2025-09-15"
weight : 7
chapter : false
pre : " <b> 5.2.7 </b> "
---

## Tổng quan

Trong phần này, bạn sẽ cấu hình Amazon Cognito để xử lý việc xác thực (authentication) và ủy quyền (authorization) người dùng cho ứng dụng serverless của mình. Cognito sẽ quản lý việc đăng ký, đăng nhập, khôi phục mật khẩu và tạo ra các JWT tokens để bảo mật các API endpoints của bạn.

**Những gì bạn sẽ đạt được:**
- Hiểu các khái niệm và kiến trúc của Amazon Cognito
- Tạo một Cognito User Pool để quản lý người dùng
- Cấu hình các thuộc tính người dùng và chính sách mật khẩu
- Thiết lập app client cho ứng dụng của bạn
- Kiểm thử luồng đăng ký và xác thực người dùng
- Tích hợp Cognito với API Gateway như một authorizer (trình xác thực)
- Bảo mật API endpoints bằng cách xác thực JWT token
- Kiểm thử các lệnh gọi API đã được xác thực

**Thời gian ước tính**: 50-60 phút

## Kiến trúc Cognito

### Những gì chúng ta sẽ xây dựng
```
User (Trình duyệt/Mobile)
    ↓
Đăng ký / Đăng nhập
    ↓
Amazon Cognito User Pool
    ├── Quản lý người dùng (đăng ký, đăng nhập, xác minh)
    ├── Tạo Token (ID Token, Access Token, Refresh Token)
    └── Thuộc tính người dùng (email, sđt, thuộc tính tùy chỉnh)
    ↓
JWT Token (ID Token)
    ↓
API Gateway (với Cognito Authorizer)
    ├── Xác thực JWT Token
    ├── Trích xuất claims của người dùng (sub, email, v.v.)
    └── Chuyển đến Lambda nếu hợp lệ
    ↓
Lambda Function (workshop-lambda-sm-rds)
    ├── Nhận thông tin người dùng từ authorizer
    └── Thực hiện các hoạt động được ủy quyền
    ↓
RDS PostgreSQL
```

## Cân nhắc về chi phí
- Miễn phí cho tối đa 10,000 người dùng hoạt động hàng tháng (MAUs)
- Tổng cộng: $0 (giả định bạn dọn dẹp tài nguyên ngay sau khi hoàn thành workshop)

### Các khái niệm Cognito

**User Pool:**
- Danh mục người dùng cho ứng dụng của bạn
- Xử lý xác thực (đăng ký, đăng nhập)
- Quản lý hồ sơ và thuộc tính người dùng
- Cấp phát JWT tokens sau khi xác thực thành công

**App Client:**
- Cấu hình cách ứng dụng của bạn kết nối với user pool
- Định nghĩa các luồng xác thực
- Kiểm soát thời gian hết hạn của token

**JWT Tokens:**
- **ID Token**: Chứa danh tính và thuộc tính người dùng (được dùng để ủy quyền API)
- **Access Token**: Dùng để truy cập các API của Cognito user pool
- **Refresh Token**: Dùng để lấy ID/Access tokens mới mà không cần đăng nhập lại

**Hosted UI:**
- Các trang đăng ký/đăng nhập được dựng sẵn
- Có thể tùy chỉnh thương hiệu
- Xử lý các luồng OAuth 2.0
- Giải pháp thay thế cho việc tự xây dựng giao diện xác thực tùy chỉnh

**Cognito Authorizer:**
- Tính năng của API Gateway
- Tự động xác thực JWT tokens
- Từ chối các yêu cầu có token không hợp lệ hoặc hết hạn
- Chuyển các claims (thông tin xác thực) của người dùng đến Lambda

## Bước 1: Tạo Cognito User Pool

### 1.1 Truy cập Cognito Console

1. Đi đến AWS Console
2. Tìm kiếm "Cognito"
3. Nhấp vào **Amazon Cognito** trong mục Services

![Cognito Console](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/1.png)

![Cognito Page](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/2.png)

### 1.2 Tạo danh mục người dùng (User directory)

1. Nhấp vào **Get started for free in less than five minutes**

Bạn sẽ đi qua một trình hướng dẫn cấu hình từng bước.

## Thiết lập Cognito user directory

### 1.3 Định nghĩa ứng dụng của bạn

1. **Application type**
- Vì chúng ta sử dụng Cognito để xác thực người dùng trong frontend đã triển khai ở Phần 1, hãy chọn **Single-page application (SPA)**

2. **Name you application**
- Nhập tên: `workshop-cognito-SPA`

![Define application](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/3.png)

3. Nhấp vào **Next**

### 1.4 Cấu hình tùy chọn

1. **Options for sign-in identifiers**
- Thuộc tính bắt buộc để người dùng đăng nhập. Chọn **Email**

2. **Self-registration**
- **Enabled** (mặc định)
- Tùy chọn này cho phép người dùng tự khởi tạo đăng ký. Nếu chọn **Disable**, chỉ admin có quyền truy cập Cognito mới có thể tạo tài khoản mới.

3. **Required attributes for sign-up**
- Chọn **name** và **phone_number**
- Đây là các thuộc tính bổ sung bắt buộc khi đăng ký

![Configure options](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/4.png)

### 1.5 Thêm return URL (tùy chọn)
- Khi chỉ định một return URL (URL trả về), ứng dụng của chúng ta sẽ chuyển hướng người dùng trở lại URL đó sau khi đăng nhập thành công.
- Để trống lúc này. Chúng ta sẽ cấu hình sau khi tích hợp ứng dụng front-end.

5. Nhấp vào **Create user directory**

### 1.6 Xem tài nguyên
Sau khi bạn nhấp vào **Create user directory** ở bước trước, bạn sẽ được chuyển hướng đến một trang khác.

Trên trang này, bạn có thể xem:
- Trang đăng nhập và đăng ký tích hợp sẵn bằng cách nhấp vào **View login page**
- Hướng dẫn thiết lập nhanh (Quick setup guide)
    - Cung cấp cho nhà phát triển hướng dẫn cách tích hợp xác thực vào ứng dụng Front-end

Chúng ta sẽ rời trang này bây giờ và quay lại sau khi tích hợp front-end.

![Configure options](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/5.png)

Nhấp vào **Go to overview** ở cuối trang.

### 1.7 Xem chi tiết User Pool

Sau khi tạo, bạn sẽ thấy tổng quan về user pool của mình.

**Lưu lại các thông tin quan trọng này:**

**User pool ID:**
- Định dạng: `[region]_abcd1234`

**User pool ARN:**
- Định dạng: `arn:aws:cognito-idp:us-east-1:123456789012:userpool/[region]_abcd1234`

![Cognito Pool Details](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/6.png)

**Lấy App client ID:**
1. Ở thanh bên trái, nhấp vào **App clients** dưới mục **Applications**
2. Sao chép **Client ID**
    - Định dạng: `1234567890abcdefghijklmnop`

![Cognito App Client ID](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/7.png)

3. Ở giữa màn hình, bạn có thể thấy:
- **Authentication flow session duration**: thời gian tối đa khi người dùng khởi tạo xác thực
- **Refresh token expiration**: thời gian trước khi refresh token hết hạn
- **Access token expiration**: thời gian trước khi access token hết hạn và người dùng được yêu cầu (hoặc không, tùy cài đặt) đăng nhập lại
- Để chỉnh sửa các khoảng thời gian này và hơn thế nữa, nhấp vào **Edit** trong cùng thẻ ở bên phải

4. Chỉnh sửa thông tin app client
- **Authentication flow**: các luồng xác thực mà ứng dụng của bạn sẽ hỗ trợ
    - **Choice-based sign-in: ALLOW_USER_AUTH**: cho phép nhiều tùy chọn đăng nhập cho người dùng (sinh trắc học, OTP...)
    - **Sign in with secure remote password (SRP): ALLOW_USER_SRP_AUTH**: đăng nhập với tên người dùng và mật khẩu
    - **Get new user tokens from existing authenticated sessions: ALLOW_REFRESH_TOKEN_AUTH**: khi access token hết hạn, refresh token được sử dụng để gia hạn và lấy access token mới mà người dùng không cần khởi tạo đăng nhập lại
- **Duration and expiration times**: bạn cũng có thể sửa đổi thời lượng và thời gian hết hạn tại đây

5. Bạn cũng có thể xem một số cấu hình đáng chú ý trên trang app client
- **Quick setup guide**: cung cấp hướng dẫn cho nhà phát triển cách tích hợp Cognito vào ứng dụng Front-end
- **Attribute permissions**: xem và chỉnh sửa quyền đối với các thuộc tính mà app client này có thể đọc và ghi
    - Bạn có thể thấy rằng **email** không thể ghi (not writeable) vì chúng ta đã chọn **email** là thuộc tính đăng ký và đăng nhập bắt buộc
- **Login pages**: tùy chỉnh hành vi đăng nhập và đăng xuất
- **Threat protection**: Cấu hình bảo vệ khỏi mối đe dọa cho xác thực thích ứng và thông tin xác thực bị xâm phạm.
- **Analytics**

## Bước 2: Kiểm thử Đăng ký Người dùng

### 2.1 Truy cập Hosted UI

1. Trong Cognito console, đi đến user pool của bạn
2. Ở thanh bên trái, nhấp vào **App clients** dưới mục **Applications**
3. Nhấp vào **View login page**

Thao tác này sẽ mở trang xác thực do Cognito lưu trữ (Cognito-hosted) trong một tab mới.

![Cognito View Hosted UI](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/8.png)

### 2.2 Đăng ký Người dùng Mới

1. Trên trang Hosted UI, nhấp vào **Create an account**

2. Điền vào biểu mẫu đăng ký:

**Email:** Địa chỉ email thực của bạn
- Bạn sẽ nhận được mã xác minh tại đây

**Name:** Tên đầy đủ của bạn (chữ và số, phân biệt hoa thường)

**Phone number:** `+1234567890`
- Định dạng: `+[mã quốc gia][số điện thoại]`
- Ví dụ: `+84 0123456789` cho Việt Nam

**Password:**
- Phải đáp ứng chính sách mật khẩu:
    - Ít nhất 8 ký tự
    - Chữ in hoa
    - Chữ thường
    - Số
    - Ký tự đặc biệt

**Confirm password**

![Cognito Sign Up Form](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/9.png)

3. Nhấp vào **Sign up**

### 2.3 Xác minh Địa chỉ Email

1. Kiểm tra hộp thư đến email của bạn
2. Bạn sẽ nhận được email từ `no-reply@verificationemail.com`
3. **Chủ đề:** "Your verification code"
4. **Nội dung:** Chứa mã xác minh 6 chữ số
    - Ví dụ: `123456`

5. Quay lại trang đăng nhập
6. Nhập mã 6 chữ số vào trường **Confirmation code**
7. Nhấp vào **Confirm Account**

![Cognito Verification Email](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/11.png)


**Không nhận được email?**

1. Kiểm tra thư mục spam/rác
2. Đợi 2-3 phút (có thể bị trễ)
3. Nhấp vào "Resend code" trên trang xác minh
4. Xác minh địa chỉ email chính xác
5. Kiểm tra hạn ngạch gửi email của Cognito (giới hạn 50/ngày)

Nếu vẫn không được:
- Đi đến Cognito console → Users
- Tìm user của bạn (trạng thái: UNCONFIRMED)
- Nhấp vào Actions → Confirm account (admin override)

Sau khi xác minh thành công, bạn sẽ được chuyển hướng đến trang return URL.

![Cognito Sign In Page](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/12.png)

Chúng ta sẽ thiết lập return URL sau để chuyển hướng người dùng đến trang chủ front-end của chúng ta.

### 2.4 Đăng nhập

1. Đóng trang trình duyệt
2. Quay lại trang app client của chúng ta, sau đó nhấp vào **View login page** lần nữa
3. Bạn sẽ thấy như sau
    - Điều này là do bạn đã đăng nhập trước đó sau khi đăng ký
    - Sau khi đăng nhập thành công, Cognito sẽ lưu trữ httpOnly cookies chứa access token và refresh token
    - Nếu chúng chưa hết hạn, bạn sẽ không phải khởi tạo đăng nhập lại (nhập email và mật khẩu)
4. Nhấp vào **Sign in as [email của bạn]**, bạn sẽ được chuyển hướng đến return URL
5. Nếu bạn muốn nhập email và mật khẩu, chỉ cần nhấp vào **Sign in as a different user?**

![Cognito Sign In Page](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/13.png)

## Bước 4: Xem Người dùng trong Cognito Console

### 4.1 Xem Danh sách Người dùng

1. Trong Cognito console, chọn user pool
2. Nhấp vào **Users** ở điều hướng bên trái
3. Bạn sẽ thấy người dùng mới tạo của mình

![Cognito Users List](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/14.png)

### 4.2 Xem Chi tiết Người dùng

1. Nhấp vào username: `testuser`
2. Xem thông tin chi tiết:

**User attributes:**
- `sub`: UUID (định danh duy nhất)
- `email`: Email của bạn
- `email_verified`: true
- `name`: Tên của bạn
- `phone_number`: +1234567890
- `phone_number_verified`: false (chúng ta chưa xác minh số điện thoại)

![Cognito User Details](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/15.png)

**Group memberships:**
- None (chúng ta chưa tạo nhóm nào)

## Bước 5: Xem Authentication Tokens

Chúng ta sẽ xem một access token trông như thế nào thông qua CloudShell.

### 5.1 Lấy access token qua CloudShell

1. Ở góc dưới cùng bên phải, tìm CloudShell

![Cognito User Details](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/18.png)

2. Khi nhấp vào **CloudShell**, một ngăn chứa CLI sẽ xuất hiện từ phía dưới

![CloudShell](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/19.png)

3. Tạo một tệp json trên máy cục bộ của bạn với nội dung sau:

```JSON
{
    "UserPoolId": "{your-user-pool-id}",
    "ClientId": "{your-client-id}",
    "AuthFlow": "ADMIN_NO_SRP_AUTH",
    "AuthParameters": {
        "USERNAME": "admin@example.com",
        "PASSWORD": "password123"
    }
}
```

Ví dụ:

![json](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/20.png)

- **UserPoolId** có thể tìm thấy trong trang user pool của bạn
- **ClientId** có thể tìm thấy trong trang app client của bạn
- **username** và **password** của tài khoản mới bạn vừa tạo

4. Lưu tệp json dưới tên `auth.json`
5. Quay lại **CloudShell** CLI, nhấp vào **Action** ở bên phải, nhấp vào **Upload file**

![upload](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/21.png)

6. Chọn tệp `auth.json` của bạn để tải lên
7. Sau đó trong CLI, chạy lệnh `ls`. Bạn sẽ thấy tệp `auth.json` của mình

![json in CLI](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/22.png)

8. Chạy lệnh sau
   `aws cognito-idp admin-initiate-auth --region {your-aws-region} --cli-input-json file://auth.json`
9. Nếu bạn gặp lỗi này `An error occurred (InvalidParameterException) when calling the AdminInitiateAuth operation: Auth flow not enabled for this client`
    - Đi đến trang app client của bạn
    - Trong thẻ **App client information**, nhấp vào **Edit**
    - ![app client card](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/23.png)
    - Dưới mục **Authentication flows**, tích chọn **Sign in with server-side administrative credentials: ALLOW_ADMIN_USER_PASSWORD_AUTH**
    - Lưu thay đổi

10. Chạy lại lệnh, bạn sẽ nhận được phản hồi sau

```JSON
{
    "ChallengeParameters": {},
    "AuthenticationResult": {
        "AccessToken": "eyJraWQiOiJTNVwvTmlNSnorR0VQUzlVNis2dlBjRCttQ2tLZDNOdFFCcEp1NEhRbXhoUT0iLCJhbGciOiJSUzI1NiJ9.eyJzdWIiOiI4OTNhNzVmYy02MGYxLTcwOTYtNWJiYi1mNDNlMDA5ZjJiM2IiLCJpc3MiOiJodHRwczpcL1wvY29nbml0by1pZHAuYXAtc291dGhlYXN0LTEuYW1hem9uYXdzLmNvbVwvYXAtc291dGhlYXN0LTFfVHV1dGRSVExkIiwiY2xpZW50X2lkIjoiNTZtc2RjdHMwcjJ1YWhrdDZjMzBsdWxiZWgiLCJvcmlnaW5fanRpIjoiNWNmOWFiMWUtNDdjZC00MzZlLTk2OWYtZmU5ZDE3YTgxOGNhIiwiZXZlbnRfaWQiOiJkMTM5Y2Y3Zi02YmE3LTQzYzQtYjNiZS0xYWJmMmEyNjNkMzkiLCJ0b2tlbl91c2UiOiJhY2Nlc3MiLCJzY29wZSI6ImF3cy5jb2duaXRvLnNpZ25pbi51c2VyLmFkbWluIiwiYXV0aF90aW1lIjoxNzY0NDg3MDI1LCJleHAiOjE3NjQ0OTA2MjUsImlhdCI6MTc2NDQ4NzAyNSwianRpIjoiNGZhN2RkZjctZmJiYS00MjM3LWE2MzctYTkyMWE1NDZlODlmIiwidXNlcm5hbWUiOiI4OTNhNzVmYy02MGYxLTcwOTYtNWJiYi1mNDNlMDA5ZjJiM2IifQ.bUHsq_dNyxJ2GBARcP5IRY8Qi-AZbpTItD_BkH4uy7b-BYLZxNx03peI68DFmhd2mDpI_exw6DigB2I6hFGeQ2VbUlH0kKxvA0gDXqQNj4bnDnU2KCIzPbj_pIydab_tjxuU6FQi4l5tTxM6ygKciIUowqX3GLgSvyXPWprZzZtQoYgyMETT-rae4EWBhUoT1Em76YAvlXwqxHp0RJi8vwABWaB6Q34buCO1wM4SWzs5ZNfLSy28FK0xLEdGuAXwtXRnu2Myn_wmcePK_K-kiFi7aMX4wEB4Vl7zoPqUhgzI2unSLBCDNHX-svTSRy6sk9IaXZkzp_TbH4bfflFLPA",
        "ExpiresIn": 3600,
        "TokenType": "Bearer",
        "RefreshToken": "eyJjdHkiOiJKV1QiLCJlbmMiOiJBMjU2R0NNIiwiYWxnIjoiUlNBLU9BRVAifQ.pHOj9o4WG_bH5rw2E2tF5Oxpx4WbVEOMZb2oVduNH6Wxsl8NE_UfAhS6IVi6rcaGZkdgC1e1DX8Z-zqSwrequpT7eIKsUeb5WDmcu4my1RVfx2vrmmKKCuyHs3fCG8eyopjAe2-9vKnVJI6T-nr_Sg9YJ54EzWie8sHO2_pdLMOkcNgxn4Hhv8f8J0PsrIyovaI62O6uomTul1lMqUQ3Q4AHTRwROZEVqCQlzEYmMnsFBUbVTzHuA0hbEy4v8hRx8jqLjNwcvOf9qqYgyusZt0wiErnMGR11p00AsOgIcJ6A_484DoH4TPu2eqAy7UIZF5g-ak66iTncSedMQermXw.uA8Bb7gC4JfnFcCx.oFSb-D6NM1q2KbP8MGDqcnDOq8OD921MSb-oyirVPIu1taU-iPizZ08IvdGSc_kV-bRSep2-vmD-jKQePXNI4aZ0S6YxTV09XwLJlRkU8DMeiH5Z1LY8_ZMf2KBNCHpfAONb3l3_xQj3hHGgIpc2W2lHX-e9YaKadDKSlU4BTs5P2BOrTq4fkVGRfjp5sC_FlcsSOULCzgM8afHQTCnONzNmMrGHn6k9famKFFDUVX7W9tTI5YwndJ9eW_vU0ZYIETIP_dF4X0DateJb108NtgLfRs5U8eerBVCUh9RnExR9tuPSzqqP0nDCD9JN1QQE_Z-zmQFngVsEvL040d19serbQ7Fjyikzmm2udX5SHgYtQlGWHaFjp3hOQKotJhlFpYR-CmC9StB2BzLl2fA2Wol5FIi0VhrnWOOmnxlgN1m7k1kLAWC7S_hieJgbCw4kSpeG7QLk_9BYllT3dNKjgzyBS7ppYYAaRk1wXtm7f52Yf8nwD0qO-9aksa6rQFRJ5xzYUfdNyZwLbJvHDcTfyxedgcUGEGtRf-wAU7bxmzmKiFtnFr4nrTNsgkfWoDoepc23PzuQqsHZ_J7AYZHzBJmrlxz5NhYG9s6c7WkeEXTogXly7-hJdDen8NFXgEZOW9Aed1W6aQNjdzd726WIVNT_r5UcgvlAQs5y43tiLnsMjKmGpwW7PuWC8VGG4w1UhWCUH_5jZjgAuI4PDeZF2a7w3-O3jDsZhs4u0yBeDxFXtx_NESDA8Uvh_j5IAip8Gm_aO_pACelyGwhKb9aWVaQtixI7GBRLIwY8RptjnRVaLKxkGMsqfvYSOZHugQIeMCADoVMkl1COHv7bHy4289JGGpIPYVOfer-TIS6NzHf3xCqxNWSn0cBuYznJTrzjpwIv6L5t302WleAH5EvF-gyEfNU7oeiZp4q4-e11kppLoP6yFg4LOA8BxPiJBsDMA0qIn3MyfFG7UQmChLxOZyMkQCEWgTQey-WMtwKqAb3En_aoCfW_-IN2b9v0pdwq86B7NTVtrxGPLbhdFmVpF42kPxYTOasFrWymN6QeDgy4DBMcqKpBf3Tz3wGeSYZfsgD8AjyhaE6N56uARAIM23i-eVaNvd-b8-SSG5IOSC0z1kg9nARQmaC89Q-F9tsFeLQfQOGgIPbc9x24VeNAaUBE9TqtKHKofLX2Ea1xsotRK8bliZn1vceLJgrH1ixueFiqVo7UPIuKOw7_v390p1Rg0Jh8CL0iA2uC9jSTfT09-M_wkVmROZPh8Ac4hNlWe-p-N5061f956fxnoGuqcQ_m0fmW0s4K45N94Gevs1nB3JrVTB-llS3fGE5vPR_hB8qTwbJx0-ev.4IY2Y2G57kBAuuYXqaNucA",
        "IdToken": "eyJraWQiOiJWdjVBZXc1bHpZQUNvdWxaNVwvN3ZPSEEwUXBycTB3Sm1peXZQaFI1bVBYbz0iLCJhbGciOiJSUzI1NiJ9.eyJzdWIiOiI4OTNhNzVmYy02MGYxLTcwOTYtNWJiYi1mNDNlMDA5ZjJiM2IiLCJlbWFpbF92ZXJpZmllZCI6dHJ1ZSwiaXNzIjoiaHR0cHM6XC9cL2NvZ25pdG8taWRwLmFwLXNvdXRoZWFzdC0xLmFtYXpvbmF3cy5jb21cL2FwLXNvdXRoZWFzdC0xX1R1dXRkUlRMZCIsInBob25lX251bWJlcl92ZXJpZmllZCI6ZmFsc2UsImNvZ25pdG86dXNlcm5hbWUiOiI4OTNhNzVmYy02MGYxLTcwOTYtNWJiYi1mNDNlMDA5ZjJiM2IiLCJvcmlnaW5fanRpIjoiNWNmOWFiMWUtNDdjZC00MzZlLTk2OWYtZmU5ZDE3YTgxOGNhIiwiYXVkIjoiNTZtc2RjdHMwcjJ1YWhrdDZjMzBsdWxiZWgiLCJldmVudF9pZCI6ImQxMzljZjdmLTZiYTctNDNjNC1iM2JlLTFhYmYyYTI2M2QzOSIsInRva2VuX3VzZSI6ImlkIiwiYXV0aF90aW1lIjoxNzY0NDg3MDI1LCJuYW1lIjoiVHJhbiBNaW5oIFRoaWVuIiwicGhvbmVfbnVtYmVyIjoiKzg0MDc4MzQ3NjM0MSIsImV4cCI6MTc2NDQ5MDYyNSwiaWF0IjoxNzY0NDg3MDI1LCJqdGkiOiIyODY4NjgwNy03YTg0LTQ5MDctOTE3OC00YTNhMDZiYjM3NzAiLCJlbWFpbCI6InRoaWVuLnRtMjcyN0BnbWFpbC5jb20ifQ.rplRmMWbQFNyVXswjDecit4hbg3niMgxti0Xr6uFgjy0NBScI1t_vcd2M2N262KWnQzzki-ovYE8YKA14RUAdbz5lo9Xllqn0OHxTqWSTQnjqf4tYT7_SjWbH3_bSgarAzxT89-sK0dAMfZZYPrGfbRZeptCWWwyJxeEtffk09Pc7p8FAxszTFKro9pTnmlHYMUjADRW6ULfzdI7wNoBsRo1MNtIjXfxq9rtVPeVNQ4MvFraRzP9Fv9oZ9H_hgH1mC3XRXYyy5YyraY4bPFxBqLMrhTHRmhMrkUB8HRuOOiaYRmsf40aHp3qpsSpR0zy7WkiUE8W6FahBCgXoTSY5g"
    }
}
```

11. Lưu lại **AccessToken** và **IdToken** của bạn, chúng ta sẽ sử dụng chúng để xác thực.

### 5.2 Giải mã JWT Token (Hiểu nội dung bên trong)

Để hiểu thông tin nào có trong token:

1. Đi đến https://jwt.io/
2. Dán **AccessToken** của bạn vào ô **Encoded value** (bên trái)
3. Xem **Payload** đã giải mã (bên phải):
```json
{
  "sub": "893a75fc-60f1-7096-5bbb-f43e009f2b3b",
  "iss": "https://cognito-idp.ap-southeast-1.amazonaws.com/ap-southeast-1_TuutdRTLd",
  "client_id": "56msdcts0r2uahkt6c30lulbeh",
  "origin_jti": "5cf9ab1e-47cd-436e-969f-fe9d17a818ca",
  "event_id": "d139cf7f-6ba7-43c4-b3be-1abf2a263d39",
  "token_use": "access",
  "scope": "aws.cognito.signin.user.admin",
  "auth_time": 1764487025,
  "exp": 1764490625,
  "iat": 1764487025,
  "jti": "4fa7ddf7-fbba-4237-a637-a921a546e89f",
  "username": "893a75fc-60f1-7096-5bbb-f43e009f2b3b"
}
```
4. Dán **IdToken** của bạn
```JSON
{
  "sub": "893a75fc-60f1-7096-5bbb-f43e009f2b3b",
  "email_verified": true,
  "iss": "https://cognito-idp.ap-southeast-1.amazonaws.com/ap-southeast-1_TuutdRTLd",
  "phone_number_verified": false,
  "cognito:username": "893a75fc-60f1-7096-5bbb-f43e009f2b3b",
  "origin_jti": "5cf9ab1e-47cd-436e-969f-fe9d17a818ca",
  "aud": "56msdcts0r2uahkt6c30lulbeh",
  "event_id": "d139cf7f-6ba7-43c4-b3be-1abf2a263d39",
  "token_use": "id",
  "auth_time": 1764487025,
  "name": "Tran Minh Thien",
  "phone_number": "+840783476341",
  "exp": 1764490625,
  "iat": 1764487025,
  "jti": "28686807-7a84-4907-9178-4a3a06bb3770",
  "email": "Your email here"
}
```

![JWT decode](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/26.png)

5. Bạn có thể thấy thông tin của user của mình

**Giải thích các claims chính:**
- `sub`: Định danh duy nhất của người dùng (UUID) - **sử dụng cái này làm `cognito_sub` trong cơ sở dữ liệu của bạn**
- `email`: Địa chỉ email của người dùng
- `email_verified`: Trạng thái xác minh email (true/false)
- `name`: Tên đầy đủ của người dùng
- `cognito:username`: Username đã chọn khi đăng ký
- `phone_number`: Số điện thoại của người dùng
- `exp`: Thời gian hết hạn (Unix timestamp) - token hết hạn sau thời điểm này
- `iat`: Thời điểm phát hành (Unix timestamp) - khi token được tạo
- `aud`: Audience (App client ID của bạn)
- `iss`: Issuer (Cognito user pool URL)

Thông tin này sẽ có sẵn cho Lambda function của bạn khi các yêu cầu được xác thực.

## Bước 6: Tích hợp Cognito với API Gateway

Bây giờ hãy bảo mật API của bạn bằng cách yêu cầu các JWT token hợp lệ từ Cognito.

**Cách thức hoạt động:**
- Sau khi đăng nhập, access token sẽ được lưu trữ dưới dạng httpOnly cookies.
- Khi khởi tạo các lệnh gọi API, chúng ta sẽ gửi access token này trong header của request.
- API Gateway sẽ xác minh access token này để ủy quyền cho người dùng.

### 6.1 Tạo Cognito Authorizer trong API Gateway

1. Đi đến API Gateway console
2. Chọn API của bạn: `workshop-user-api`
3. Nhấp vào **Authorizers** ở điều hướng bên trái
4. Nhấp vào **Create an authorizer**

![API Gateway Authorizers](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/16.png)

**Tạo Authorizer:**

**Name:** `CognitoUserPoolAuthorizer`

**Type:** Chọn **Cognito**

**Cognito User Pool:**
- Nhấp vào trường này
- Chọn region của bạn
- Chọn user pool của bạn: `workshop-user-pool`

**Token Source:** `Authorization`
- Đây là HTTP header nơi token sẽ được gửi
- Định dạng: `Authorization: Bearer <id_token>`

**Token Validation:**
- Để trống (biểu thức chính quy tùy chọn để xác thực định dạng token)

![API Gateway Authorizer Settings](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/17.png)

5. Nhấp vào **Create authorizer**

### 6.2 Kiểm thử Authorizer

Trước khi áp dụng cho các methods, hãy kiểm thử xem nó có hoạt động không:

1. Trong trang authorizer
2. Trong trường **Token value**, dán ID token của bạn từ Bước 5
    - Chuỗi JWT đầy đủ bắt đầu bằng `eyJ...`
3. Nhấp vào **Test authorizer**

**Kết quả mong đợi:**

Bạn sẽ thấy thông tin giống như trình giải mã jwt ở trên

![API Gateway Test Authorizer](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/27.png)

**Thành công!** Authorizer đã xác thực token của bạn và trích xuất các claims.

Các claims này sẽ được chuyển đến Lambda function của bạn trong `event.requestContext.authorizer.claims`.


**Kiểm thử Thất bại?**

**Lỗi: "Unauthorized"**

**Nguyên nhân:**
- Token hết hạn (token tồn tại trong 1 giờ)
- Chọn sai user pool
- Token từ user pool khác
- Token bị lỗi định dạng (kiểm tra xem bạn đã sao chép toàn bộ chuỗi chưa)

**Giải pháp:**
1. Chạy lại lệnh lấy token để có token mới
2. Xác minh User pool ID khớp
3. Đảm bảo bạn đã sao chép toàn bộ token (không có khoảng trắng, không ngắt dòng)
4. Kiểm tra token chưa hết hạn (giải mã tại jwt.io và kiểm tra claim `exp`)

### 6.3 Áp dụng Authorizer cho API Methods

Bây giờ hãy bảo mật các API endpoints của bạn với authorizer.

**Bảo mật GET /users:**

1. Nhấp vào **Resources** ở điều hướng bên trái
2. Mở rộng resource `/users`
3. Nhấp vào **GET** method
4. Nhấp vào tab **Method Request**

5. Nhấp vào nút **Edit** trong thẻ **Method request settings**

![API Gateway edit authorizer](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/28.png)

6. Chọn **CognitoUserPoolAuthorizer** từ danh sách thả xuống
7. Bạn sẽ thấy "Authorization: CognitoUserPoolAuthorizer" được hiển thị.

![API Gateway Apply Authorizer](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/29.png)

| Phần | Mục đích |
|--------|---------|
| Authorization | Yêu cầu Cognito JWT |
| Scopes | Hạn chế truy cập theo phạm vi OAuth |
| Validator | Xác thực đầu vào (body/params/headers) |
| API Key | Yêu cầu x-api-key nếu được bật |
| Operation Name | Nhãn cho logs/metrics |
| Query Params | Xác thực tham số URL |
| Headers | Xác thực các trường header |
| Request Body | Xác thực JSON body |

**Lặp lại cho tất cả các methods khác:**

Áp dụng cùng một authorizer cho:
-  **POST** `/users` (Tạo user)
-  **GET** `/users/{id}` (Lấy một user)
-  **PUT** `/users/{id}` (Cập nhật user)
-  **DELETE** `/users/{id}` (Xóa user)

**Method nào cần bảo mật?**

**Cho workshop này:** Bảo mật TẤT CẢ methods để minh họa xác thực đầy đủ.

**Cho môi trường production:** Cân nhắc yêu cầu của bạn:
- **Luôn bảo mật:** POST, PUT, DELETE (thao tác ghi)
- **Đôi khi công khai:** GET (thao tác đọc có thể là dữ liệu công khai)
- **Logic ủy quyền:** Ngay cả khi đã xác thực, hãy kiểm tra xem người dùng có quyền không (sẽ đề cập sau)

Ví dụ:
- Công khai: GET /users (liệt kê tất cả người dùng - danh bạ công khai)
- Đã xác thực: POST /users (tạo người dùng - phải đăng nhập)
- Được ủy quyền: PUT /users/{id} (cập nhật người dùng - phải là chủ sở hữu hoặc admin)
- Được ủy quyền: DELETE /users/{id} (xóa người dùng - phải là chủ sở hữu hoặc admin)


### 6.4 Triển khai API với Xác thực

1. Nhấp vào **Deploy API**
2. **Deployment stage:** `dev`
3. **Deployment description:** `Added Cognito authentication`
4. Nhấp vào **Deploy**

**API của bạn hiện đã được bảo mật!**

Tất cả các yêu cầu phải bao gồm một JWT token hợp lệ trong header `Authorization`.

## Bước 7: Kiểm thử API Calls đã Xác thực

### 7.1 Kiểm thử Không có Token (Sẽ Thất bại)

Thử gọi API trong Postman mà không có xác thực:

![Test API authorizer](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/30.png)

**Phản hồi mong đợi:**
```json
{
  "message": "Unauthorized"
}
```

**Mã trạng thái:** `401 Unauthorized`

**Hoàn hảo!** API đã từ chối yêu cầu không được xác thực.

### 7.2 Kiểm thử Có Token Hợp lệ (Sẽ Thành công)

Gọi API với ID token của bạn:
1. Đi đến tab **Authorization**
2. **Auth type**: chọn **Bearer Token**
3. **Token**: dán IdToken của bạn
4. Nhấp vào **Send** lần nữa

![Test API authorizer](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/31.png)

5. Bây giờ bạn sẽ thấy tất cả người dùng được lấy về với trạng thái 200

![Test API authorizer](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/32.png)

## Bước 8: Cấu hình Cài đặt App Client

Bây giờ chúng ta sẽ chỉnh sửa cài đặt App Client.

### 8.1 Cập nhật App Client

1. Quay lại trang app client của bạn trong Amazon Cognito
2. Đi đến tab **Login pages**
3. Nhấp vào **Edit** trong thẻ **Managed login pages configuration**

![Cognito Edit App Client](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/33.png)

### 8.2 Cấu hình Allowed Callback URLs

Sau khi xác thực thành công, Cognito chuyển hướng người dùng đến các URL này.

Nếu bạn có frontend đã triển khai (từ [5.1.3-Cloudfront-setup]({{< relref "/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup" >}}#step-3-test-your-cloudfront-distribution)):
```
https://d1234abcd.cloudfront.net/callback
```

![Cognito callback url](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/34.png)

**Giải thích về Callback URLs:**

Sau khi xác thực thành công, Cognito chuyển hướng người dùng đến callback URL với các token xác thực trong phân đoạn URL (URL fragment).

**Định dạng:**
```
https://yourdomain.com/callback#id_token=eyJraWQi...&access_token=eyJraWQi...
```

**Ví dụ:**
- Development: `http://localhost:3000/callback`
- Production: `https://app.yourdomain.com/callback`
- Cloudfront: `https://d1234abcd.cloudfront.net/callback`

Bạn có thể thêm nhiều callback URL cho các môi trường khác nhau.

### 8.3 Cấu hình Allowed Sign-out URLs

URL nơi người dùng được chuyển hướng sau khi đăng xuất.

Nếu bạn có frontend đã triển khai:
```
https://d1234abcd.cloudfront.net
```

![Cognito singout url](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/35.png)

### 8.4 Cấu hình OAuth 2.0 Settings

**Identity providers:**
-   Giữ chọn **Cognito user pool**

**OAuth 2.0 grant types:**
-   **Authorization code grant**
-   **Implicit grant** (chỉ bật nếu bạn muốn token lộ ra trong callback URL, không khuyến nghị)

**OpenID Connect scopes:**
-   **OpenID**
-   **Email**
-   **Phone**

4. Nhấp vào **Save changes**

### 8.5 Kiểm thử callback URL

1. Vẫn trong trang app client, nhấp vào **View login page**
2. Trong tab mới, đăng nhập với user bạn đã tạo
3. Nếu đăng nhập thành công, bạn sẽ được chuyển hướng đến frontend mà bạn đã thiết lập ở phần 5.1

## Bước 9: Truy cập Thông tin User trong Lambda

### 9.1 Hiểu Context của Authorizer

Khi API Gateway xác thực một token với Cognito authorizer, nó tự động chuyển các claims của người dùng đến Lambda function của bạn.

**Nơi tìm claims:**
```typescript
event.requestContext.authorizer.claims
```

**Các claims có sẵn:**
```typescript
{
  sub: "12345678-1234-1234-1234-123456789012",
  email: "test@example.com",
  email_verified: "true",
  name: "Test User",
  "cognito:username": "testuser",
  phone_number: "+1234567890",
  phone_number_verified: "false",
  ...
}
```

### 9.2 Ví dụ: Ghi log User đã Xác thực

Thêm ghi log để xem ai đang thực hiện yêu cầu:
```typescript
export const handler = async (event: APIGatewayEvent): Promise<LambdaResponse> => {
  console.log('Received event:', JSON.stringify(event, null, 2));

  // Log authenticated user
  const claims = event.requestContext.authorizer?.claims;
  if (claims) {
    console.log('Authenticated user:', {
      cognitoSub: claims.sub,
      username: claims['cognito:username'],
      email: claims.email
    });
  } else {
    console.log('Unauthenticated request (authorizer not configured or bypassed)');
  }

  // ... rest of handler logic
};
```

### 9.3 Tự động điền cognitoSub từ Token

Thay vì yêu cầu `cognitoSub` trong request body, hãy trích xuất nó từ người dùng đã xác thực:
Sửa đổi mã lambda của bạn:
```javascript
async function createUser(body) {
    const client = await connectToRds();
    try {
        const data = JSON.parse(event.body);

        // Get authenticated user's cognito sub from token
        const claims = event.requestContext.authorizer?.claims;

        if (!claims || !claims.sub) {
            return createResponse(401, {
                success: false,
                error: 'Authentication required'
            });
        }

        const cognitoSub = claims.sub; // From JWT token
        const email = claims.email; // From JWT token
        const username = claims['cognito:username']; // From JWT token
        const role = data.role || 'user'; // Default to 'user' role
        const phoneNumber = claims.phone_number; // From JWT token

        const result = await client.query(`INSERT INTO users (cognito_sub, username, email, role, phone_number)
       VALUES ($1, $2, $3, $4, $5)
       RETURNING *`, [cognitoSub, username, email, role, phoneNumber]);
        return respond(201, { success: true, data: result.rows[0] });
    }
    catch (err) {
        return respond(400, { success: false, error: err.message });
    }
    finally {
        await client.end();
    }
}
```

**Cập nhật handler để truyền event:**
```javascript
const handler = async (event) => {
    log("[users-handler] process start.");
    const method = event.httpMethod;
    const id = event.pathParameters?.id;
    try {
        switch (method) {
            case "POST":
                return createUser(event); //update event here
            case "GET":
                return id ? getUser(id) : getAllUsers();
            case "PUT":
                if (!id)
                    return respond(400, { success: false, error: "Missing user ID" });
                return updateUser(id, event.body);
            case "DELETE":
                if (!id)
                    return respond(400, { success: false, error: "Missing user ID" });
                return deleteUser(id);
            default:
                return respond(400, { success: false, error: `Unsupported HTTP method: ${method}` });
        }
    }
    catch (err) {
        return respond(500, { success: false, error: err.message });
    }
    finally {
        log("[users-handler] process end.");
    }
};
```

Bạn có thể tải xuống mã nguồn hoạt động đầy đủ bao gồm các thay đổi trên tại đây:
- Github repository: https://github.com/Icyretsz/fcj-workshop-serverless-backend-ver2
- Chỉ tệp zip cho lambda: https://fcj-workshop-files.s3.ap-southeast-1.amazonaws.com/userHandler-final.zip
- Lưu ý rằng repo này khác với repo trong phần 5.2.5
- Sau khi tải zip lên Lambda, bạn nên tìm hàm response và sửa đổi Access-Control-Allow-Origin từ `*` thành CloudFront endpoint của bạn để chỉ cho phép các yêu cầu từ CloudFront của bạn.

![Cognito singout url](/images/5-Workshop/5.2-Serverless/5.2.7-Cognito/36.png)

## Tóm tắt

Chúc mừng! Bạn đã thành công:
-   Tạo Amazon Cognito User Pool
-   Cấu hình cài đặt đăng nhập, đăng ký và xác minh
-   Thiết lập Cognito hosted UI
-   Đăng ký và xác minh người dùng thử nghiệm
-   Lấy JWT tokens từ CLI
-   Tạo Cognito authorizer trong API Gateway
-   Bảo mật API endpoints với xác thực
-   Kiểm thử các lệnh gọi API đã xác thực
-   Trích xuất thông tin người dùng từ JWT tokens trong Lambda
-   Hiểu mô hình giá của Cognito

### Những gì bạn đã xây dựng

Ứng dụng của bạn hiện có:
- **Xác thực người dùng**: Đăng ký, đăng nhập, xác minh email
- **Tạo JWT Token**: Token bảo mật để truy cập API
- **Ủy quyền API**: API Gateway tự động xác thực tokens
- **Danh tính người dùng**: Lambda function biết ai đang thực hiện yêu cầu
- **Xác thực có thể mở rộng**: Xử lý hàng triệu người dùng với Cognito
- **Bảo mật mặc định**: Không lưu trữ mật khẩu trong cơ sở dữ liệu của bạn
