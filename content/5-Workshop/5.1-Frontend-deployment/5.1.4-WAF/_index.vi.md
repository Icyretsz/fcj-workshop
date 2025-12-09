---
title : "Cấu hình AWS WAF"
date :  "2025-09-15"
weight : 4
chapter : false
pre : " <b> 5.1.4 </b> "
---

## Tổng quan

Trong phần này, bạn sẽ cấu hình AWS WAF (Web Application Firewall) để bảo vệ CloudFront distribution của mình khỏi các khai thác web phổ biến và lưu lượng truy cập độc hại. WAF cung cấp một lớp bảo mật giúp kiểm tra các yêu cầu (requests) đến và chặn những yêu cầu khớp với các quy tắc đã xác định.

**Những gì bạn sẽ đạt được:**
- Hiểu các khái niệm AWS WAF và các loại quy tắc (rule types)
- Xem và hiểu cấu hình WAF đi kèm với CloudFront distribution của bạn
- Kiểm tra và xác minh khả năng bảo vệ của WAF
- Giám sát các yêu cầu bị chặn

**Thời gian ước tính**: 45 phút

## Tại sao nên dùng AWS WAF?

**Bảo vệ chống lại:**
- **SQL injection attacks**: Ngăn chặn việc xâm nhập cơ sở dữ liệu
- **Cross-site scripting (XSS)**: Chặn việc tiêm nhiễm mã độc (script injection)
- **Bot traffic**: Lọc các yêu cầu tự động và scrapers
- **DDoS attacks**: Giới hạn tốc độ (rate limiting) để ngăn chặn cạn kiệt tài nguyên
- **Geographic restrictions**: Chặn/cho phép lưu lượng từ các quốc gia cụ thể
- **Known bad inputs**: Bảo vệ chống lại các mẫu tấn công phổ biến

**Lợi ích:**
- Bảo vệ thời gian thực tại biên (CloudFront edge)
- Các quy tắc có khả năng tùy biến cao
- Ghi nhật ký (logging) và giám sát chi tiết
- Chỉ trả tiền cho những gì bạn sử dụng
- Không cần quản lý hạ tầng

## Cân nhắc về chi phí
### Bậc miễn phí (Free-tier):
- $0/tháng

### Bậc trả phí (Paid-tier)
| Loại tài nguyên | Giá                                   |
|----------------|------------------------------------------|
| Web ACL        | $5.00 mỗi tháng (tính theo giờ)        |
| Rule           | $1.00 mỗi tháng (tính theo giờ)        |
| Request        | $0.60 cho mỗi 1 triệu requests             |
- Tổng cộng: $0 hoặc < $1 nếu ở Bậc trả phí (giả sử bạn dọn dẹp ngay sau hội thảo)


## Hiểu các khái niệm WAF

### Web ACL (Access Control List)
Một Web ACL là tài nguyên cốt lõi chứa tất cả các quy tắc bảo mật của bạn. Nó được liên kết với CloudFront distribution để kiểm tra và lọc lưu lượng truy cập.

### Các loại quy tắc (Rule Types)

**1. Managed Rules (Quy tắc được quản lý)**
- Các bộ quy tắc được cấu hình sẵn, duy trì bởi AWS hoặc người bán trên AWS Marketplace
- Thường xuyên được cập nhật để đối phó với các mối đe dọa mới
- Dễ dàng triển khai (không cần cấu hình phức tạp)

**2. Custom Rules (Quy tắc tùy chỉnh)**
- Các quy tắc do bạn tạo cho các yêu cầu cụ thể
- Kiểm soát hoàn toàn các điều kiện khớp (match conditions) và hành động
- Hữu ích cho việc bảo vệ đặc thù của ứng dụng

**3. Rule Groups (Nhóm quy tắc)**
- Tập hợp các quy tắc liên quan
- Có thể là managed rules hoặc custom rule groups
- Được đánh giá theo thứ tự ưu tiên

### Các hành động của quy tắc (Rule Actions)

- **Allow**: Yêu cầu được phép đi qua
- **Block**: Yêu cầu bị chặn (trả về lỗi 403 Forbidden)
- **Count**: Yêu cầu được đếm nhưng vẫn cho phép đi qua (hữu ích để kiểm thử)
- **CAPTCHA**: Hiển thị thử thách CAPTCHA
- **Challenge**: Thực hiện thử thách trình duyệt ngầm (silent browser challenge)

### Đơn vị dung lượng quy tắc (WCU - Rule Capacity Units)

- Mỗi quy tắc tiêu thụ một số lượng WCU nhất định
- Tối đa 1,500 WCU cho mỗi Web ACL
- Quy tắc đơn giản: 1-5 WCUs
- Managed rules phức tạp: 50-100+ WCUs

## Bước 1: Truy cập AWS WAF Console

### 1.1 Vào dịch vụ WAF

1. Trong thanh tìm kiếm của AWS Console, nhập "WAF"
2. Nhấp vào **WAF & Shield** dưới mục Services

![WAF Console](/images/5-Workshop/5.1-Frontend-deployment/5.1.4-WAF/1.png)

### 1.2 Hiểu về Dashboard

Bạn sẽ thấy:
- **Web ACLs**: Cấu hình tường lửa của bạn
- **IP sets**: Danh sách địa chỉ IP có thể tái sử dụng
- **Regex pattern sets**: Các mẫu regex tái sử dụng
- **Rule groups**: Các bộ sưu tập quy tắc tùy chỉnh
- **Application integration**
- **Add-on protection**

## Bước 2: Xem một Web ACL

### 2.1 Xem Web ACL được tạo bởi CloudFront

1. Ở thanh điều hướng bên trái, nhấp vào **Protection packs (web ACLs)**
2. Bạn có thể thấy một Web ACL được tạo bởi CloudFront mà bạn đã thiết lập trong phần [Thiết lập CloudFront Distribution]({{< relref "/5-Workshop/5.1-Frontend-deployment/5.1.3-Cloudfront-setup" >}}#15-step-4-enable-security)

![WAF Web ACL](/images/5-Workshop/5.1-Frontend-deployment/5.1.4-WAF/2.png)

{{% notice note %}}
Cấu hình WAF này được bao gồm như một phần của gói CloudFront Free tier cho distribution mà chúng ta đã tạo trong phần Thiết lập CloudFront Distribution
{{% /notice %}}

3. Nhấp vào tên Web ACL để mở thanh bên xem các hành động
    - Bạn có thể xem CloudFront distribution nào đã tạo Web ACL này trong thanh bên

![WAF Web ACL Sidebar](/images/5-Workshop/5.1-Frontend-deployment/5.1.4-WAF/3.png)

4. Nhấp vào **Manage details**, tại đây chúng ta có thể xem các thuộc tính và hành vi của gói bảo vệ này
    - Nhấp vào **Download protection pack (web ACL) as JSON**. Sau đó mở nó để xem cấu trúc web ACL
    - Bạn có thể xem mẫu Web ACL JSON và giải thích chi tiết trong [phần này](#understanding-web-acl-json)
    - Dưới mục **Protection pack (web ACL) behavior**, bạn có thể sửa đổi **Default action** (Hành động mặc định). Nếu đặt là allow, nó sẽ **cho phép** tất cả các requests không bị chặn bởi các quy tắc của web ACL, và ngược lại
    - Mục **Challenge configurations** dùng để điều chỉnh hành vi của các thử thách (CAPTCHA) như thời gian miễn nhiễm mặc định (thời gian trước khi phải giải CAPTCHA tiếp theo) hoặc token domains
    - **Custom response**: tùy chỉnh nội dung trả về trong phản hồi khi request bị chặn

![WAF Web ACL Sidebar](/images/5-Workshop/5.1-Frontend-deployment/5.1.4-WAF/5.png)

5. Quay lại trang chính, nhấp vào **Manage rules**
    - Tại trang này, bạn có thể thấy các quy tắc hiện có được tạo cùng với CloudFront
    - ![Manage rules](/images/5-Workshop/5.1-Frontend-deployment/5.1.4-WAF/6.png)
    - Nhấp vào một quy tắc để quản lý:
        - **Inspection**: áp dụng quy tắc cho **All requests** (Tất cả yêu cầu) hoặc các yêu cầu khớp với **Match statement**
        - **All requests**: nếu chọn **Override rule groups**, toàn bộ nhóm quy tắc sẽ hoạt động chủ yếu ở chế độ Count. Các quy tắc ở chế độ **Block** sẽ ngừng chặn, thay vào đó, chúng chỉ đếm các yêu cầu khớp với tiêu chí
        - **Match statement**: xác định tiêu chí để chặn yêu cầu, chẳng hạn như chặn IP hoặc chặn theo vị trí địa lý (Geo-block)
    - Nhấp vào nút **Add rule** để thêm quy tắc
        - Bạn có thể chọn giữa **Custom rule** và **AWS Managed rule groups**
        - Trong custom rule, bạn có thể tạo các tiêu chí như **IP-based rule** (chặn IP), **Geo-based rule** (chặn Vị trí), **Rate-based rule** (Giới hạn tốc độ) và **Custom rule** (kết hợp với các toán tử logic)
        - Ví dụ, hãy tạo một **Rate-based rule**: nếu có hơn 1000 requests trong vòng 5 phút, hãy chặn tất cả các requests tiếp theo sau request thứ 1000

![Create rate rule](/images/5-Workshop/5.1-Frontend-deployment/5.1.4-WAF/8.png)
![Create rate rule](/images/5-Workshop/5.1-Frontend-deployment/5.1.4-WAF/9.png)


- Nhấp vào **Edit rule order** để sửa đổi thứ tự ưu tiên của các quy tắc
    - Các quy tắc ở trên cao hơn sẽ chặn tất cả các requests khớp với nó mà không cho phép truy cập xuống các quy tắc thấp hơn
    - Hãy sửa đổi Rate-based rule mới của chúng ta lên vị trí cao nhất
        - Kéo quy tắc mới lên đầu, sau đó nhấp **Save rule order**
        - Chúng ta sẽ kiểm tra quy tắc này trong bước tiếp theo (Bước 3)

![rate rule order](/images/5-Workshop/5.1-Frontend-deployment/5.1.4-WAF/10.png)

6. Quay lại trang chính, nhấp vào **Manage resources**
    - Trong phần này, chúng ta có thể sửa đổi các tài nguyên AWS mà web ACL này bảo vệ. Web ACL liên kết với CloudFront thuộc gói Free-tier không thể được gán cho các tài nguyên khác hoặc hủy gán khỏi CloudFront distribution đã chỉ định của nó.

7. Chúng ta sẽ đề cập đến các phần **View dashboard, logs and sampled requests** và **Configure logging and sampled requests** sau.

## Bước 3: Kiểm tra khả năng bảo vệ của WAF

### 3.1 Kiểm tra truy cập bình thường

1. Mở trang web của bạn: `https://[your-cloudfront-domain].cloudfront.net`
2. Điều hướng qua các trang khác nhau
3. Xác minh trang web hoạt động bình thường

**Kết quả mong đợi:** Trang web tải mà không có vấn đề gì

### 3.2 Kiểm tra bảo vệ SQL Injection

Thử truy cập trang web của bạn với mẫu SQL injection trong URL:
```
https://[your-cloudfront-domain].cloudfront.net/?id=1' OR '1'='1
```

**Kết quả mong đợi:**
- Lỗi **403 Forbidden**
- Request bị chặn bởi WAF

![WAF Blocked Request](/images/5-Workshop/5.1-Frontend-deployment/5.1.4-WAF/7.png)

### 3.3 Kiểm tra bảo vệ XSS

Thử truy cập với mẫu cross-site scripting:
```
https://[your-cloudfront-domain].cloudfront.net/?search=<script>alert('xss')</script>
```

**Kết quả mong đợi:**
- Lỗi **403 Forbidden**
- Request bị chặn bởi WAF

![WAF Blocked Request](/images/5-Workshop/5.1-Frontend-deployment/5.1.4-WAF/7.png)

### 3.4 Kiểm tra giới hạn tốc độ (Rate Limiting)

Bạn có thể kiểm tra quy tắc giới hạn tốc độ chúng ta đã tạo trước đó bằng một tập lệnh (script) hoặc công cụ đơn giản:

**Sử dụng curl (bash/terminal):**
```bash
$url = "https://d3b2qa4f4hqtdb.cloudfront.net/"
$count = 1100

# Loop from 1 to 1100
for ($i = 1; $i -le $count; $i++) {
    # Invoke-WebRequest sends the request
    # -Uri specifies the URL
    $response = Invoke-WebRequest -Uri $url -Method GET -MaximumRedirection 0 -TimeoutSec 10 -ErrorAction SilentlyContinue | Select-Object -First 1

    # Check if a response object was returned
    if ($response -ne $null) {
        # Output the HTTP Status Code
        $response.StatusCode
    } else {
        # Output a message if the request failed
        "Request failed or timed out"
    }
}
```

**Kết quả mong đợi:**
- Khoảng ~1,000 requests đầu tiên: HTTP 200
- Sau 1,000 requests: HTTP 403
- Giới hạn tốc độ được kích hoạt

![WAF Rate Limit](/images/5-Workshop/5.1-Frontend-deployment/5.1.4-WAF/11.png)

{{% notice note %}}
**Kiểm thử Rate Limits:**<br>
Giới hạn tốc độ đếm các requests trong một cửa sổ thời gian 5 phút. Sau khi bị chặn, hãy đợi 5 phút để bộ đếm đặt lại. Trong thực tế (production), hãy thiết lập các giới hạn phù hợp dựa trên mô hình lưu lượng truy cập dự kiến của bạn:<br>
Website công cộng: 2,000-10,000 requests mỗi 5 phút<br>
APIs: 100-1,000 requests mỗi 5 phút (tùy thuộc vào trường hợp sử dụng)<br>
Trang quản trị (Admin panels): 50-100 requests mỗi 5 phút<br>
{{% /notice %}}

### 3.5 Cách khác: Sử dụng Browser Developer Tools

Để kiểm tra đơn giản hơn mà không cần scripts:

1. Mở Developer Tools (F12)
2. Chuyển đến tab **Network**
3. Refresh trang liên tục (Giữ Ctrl+R hoặc Cmd+R)
4. Sau 2,000+ requests, bạn sẽ thấy phản hồi 403

## Bước 8: Giám sát hoạt động WAF

### 8.1 Xem tổng quan Web ACL

1. Đi đến AWS WAF -> Protection packs (web ACLs)
2. Nhấp vào **View** dưới cột **Dashboard**
3. Hoặc bạn có thể nhấp vào tên Web ACL và trong thanh bên, chọn **View dashboard, logs and sampled requests**
3. Bạn sẽ thấy **Dashboard**

**Các chỉ số hiển thị:**
- **Total**: tổng số requests nhận được
- **Allowed requests**: Requests đã vượt qua tất cả các quy tắc
- **Blocked requests**: Requests bị chặn bởi các quy tắc
- **CAPTCHA**: Requests khớp với quy tắc và được hiển thị câu đố hình ảnh hoặc âm thanh yêu cầu tương tác của con người để giải quyết.
- **Challenged**: Requests khớp với quy tắc và chịu sự kiểm tra ngầm của trình duyệt

![WAF Rate Limit](/images/5-Workshop/5.1-Frontend-deployment/5.1.4-WAF/12.png)

### 8.2 Phân tích hoạt động Protection pack (web ACL)

1. Cuộn xuống phần **Protection pack (web ACL) activity**
2. Bạn sẽ thấy biểu đồ hiển thị các requests được xử lý bởi mỗi quy tắc. Di chuột vào từng phần để xem số lượng chi tiết các requests được phép, bị chặn... của mỗi quy tắc

![WAF Protection pack (web ACL) activity](/images/5-Workshop/5.1-Frontend-deployment/5.1.4-WAF/13.png)

### 8.3 Phân tích tổng quan

1. Cuộn xuống dưới cùng, bạn có thể thấy các biểu đồ, bạn có thể chọn tiêu chí để lọc dữ liệu cho biểu đồ như:
- Vị trí yêu cầu (Request locations)
- Loại tấn công (Type of attacks)
- Thiết bị khách (Client devices)
- Đặc điểm quy tắc...

**Hữu ích cho:**
- Xác định các mẫu tấn công
- Hiểu xu hướng lưu lượng truy cập
- Thiết lập cảnh báo cho hoạt động bất thường

![WAF overview](/images/5-Workshop/5.1-Frontend-deployment/5.1.4-WAF/14.png)

## Bước 9: Tạo CloudWatch Alarms (Tùy chọn)

{{% notice note %}}
Để thực hiện phần này, bạn phải nâng cấp CloudFront của mình lên Pro-tier. Bạn sẽ bị tính phí $15/tháng trong Pro-tier và chỉ có thể chuyển lại về free-tier sau 5 ngày. Hơn nữa, bạn chỉ có thể xóa distribution Pro-tier sau chu kỳ thanh toán đầu tiên.
{{% /notice %}}

### 9.1 Bật đích ghi nhật ký (logging destination) trong WAF
1. Đi đến WAF console -> Protection packs (web ACLs)
2. Trong web ACL của bạn, dưới mục **Logging & metrics**, bạn sẽ thấy nó đang **Not enabled**, nhấp vào **Not enabled** -> **Configure** để cấu hình logging

![WAF logging configure](/images/5-Workshop/5.1-Frontend-deployment/5.1.4-WAF/15.png)

3. Ở màn hình tiếp theo, dưới mục **Logging**, nhấp **Enable**, sau đó chọn **Logging destination**
4. Trên thanh bên xuất hiện, dưới mục **Amazon Cloudwatch Logs log group**, bạn có thể sử dụng các nhóm log hiện có hoặc tạo mới. Chúng ta sẽ tạo và sử dụng một log group tên là `aws-waf-logs-workshop1`, sau đó nhấp **Create**
   ![Create log group](/images/5-Workshop/5.1-Frontend-deployment/5.1.4-WAF/18.png)
5. Quay lại thanh bên **Logging destination**, chọn log group bạn vừa tạo
5. Sau đó nhấp **Save**
   ![Create log group](/images/5-Workshop/5.1-Frontend-deployment/5.1.4-WAF/19.png)

### 9.1 Thiết lập cảnh báo Blocked Request

1. Đi đến CloudWatch console
2. Nhấp **Alarms** -> **All alarms** ở điều hướng bên trái
3. Nhấp **Create alarm**

**Cấu hình cảnh báo:**

**Chọn metric:**
1. Nhấp **Select metric**
2. Bạn sẽ thấy **WAFV2** trong danh sách, nhấp vào nó để hiện danh sách metrics
   ![CloudWatch Select Metric](/images/5-Workshop/5.1-Frontend-deployment/5.1.4-WAF/20.png)
3. Chọn bất kỳ metrics nào bạn muốn tạo cảnh báo. Tôi sẽ tạo một cái cho các cuộc tấn công XSS bị chặn: nhấp **ManagedRuleGroup, ManagedRuleGroupRule, WebACL**, sau đó chọn **AWSManagedRulesCommonRuleSet**, rồi nhấp **Select metric**
   ![Select XSS](/images/5-Workshop/5.1-Frontend-deployment/5.1.4-WAF/21.png)
5. Nhấp **Select metric**

**Chỉ định điều kiện (Specify conditions):**
- **Statistic**: Sum
- **Period**: 5 minutes
- **Threshold type**: Static
- **Whenever BlockedRequests is**: Greater than `2`
- Điều này kích hoạt khi có hơn 2 requests bị chặn trong 5 phút
  ![specify conditions](/images/5-Workshop/5.1-Frontend-deployment/5.1.4-WAF/22.png)

**Cấu hình hành động (Configure actions):**
1. Chọn tạo **In alarm**
2. Nhấp **Create new topic** (để gửi thông báo SNS)
3. **Topic name**: `Default_CloudWatch_Alarms_Topic`
4. **Email**: Nhập địa chỉ email của bạn
5. Nhấp **Create topic**

![CloudWatch Alarm Actions](/images/5-Workshop/5.1-Frontend-deployment/5.1.4-WAF/23.png)

6. Bây giờ bạn sẽ thấy topic của mình được chọn
7. Nhấp **Next**

**Tên cảnh báo:**
- Name: `WAF-High-Blocked-Requests`
- Description: `Alert when WAF blocks more than 100 requests in 5 minutes`

5. Nhấp **Create alarm**

### 9.2 Xác nhận đăng ký SNS

1. Kiểm tra email của bạn
2. Nhấp vào liên kết xác nhận trong email đăng ký SNS
3. Bạn sẽ bắt đầu nhận được cảnh báo khi alarm kích hoạt

![SNS Email](/images/5-Workshop/5.1-Frontend-deployment/5.1.4-WAF/24.png)

### 9.3 Kiểm tra cảnh báo

Bây giờ hãy mô phỏng một cuộc tấn công XSS thêm 2 lần nữa để kích hoạt cảnh báo

Thử truy cập với mẫu cross-site scripting:
```
https://[your-cloudfront-domain].cloudfront.net/?search=<script>alert('xss')</script>
```

## Bước 10: Tinh chỉnh quy tắc (Tùy chọn)

### 10.1 Xử lý dương tính giả (False Positives)

Nếu các requests hợp lệ bị chặn:

1. Đi đến WAF console
2. Nhấp vào Web ACL của bạn
3. Nhấp tab **Rules**
4. Tìm quy tắc gây ra dương tính giả (kiểm tra phần sampled requests)

**Các lựa chọn:**

**Lựa chọn 1: Đặt quy tắc sang chế độ Count**
1. Nhấp **Edit** trên rule group
2. Nhấp **Override all rule actions**
3. Chọn **Count**
4. Việc này sẽ ghi nhật ký các lần khớp mà không chặn (chế độ kiểm thử)

**Lựa chọn 2: Loại trừ các quy tắc cụ thể**
1. Nhấp **Edit** trên rule group
2. Mở rộng **Rules**
3. Tìm quy tắc gây vấn đề
4. Chọn **Override to Count** cho quy tắc cụ thể đó

**Lựa chọn 3: Thêm scope-down statement**
1. Nhấp **Edit** trên rule group
2. Thêm các điều kiện để thu hẹp phạm vi áp dụng quy tắc
3. Ví dụ: Chỉ áp dụng cho các đường dẫn (paths) hoặc tham số truy vấn (query parameters) cụ thể

## Khắc phục sự cố

### Vấn đề: Requests hợp lệ bị chặn (False Positives)

**Giải pháp:**
1. Kiểm tra sampled requests để xác định quy tắc nào đang chặn
2. Đặt quy tắc cụ thể đó sang chế độ Count tạm thời
3. Thêm các scope-down statements để thu hẹp áp dụng quy tắc
4. Hoặc loại trừ các quy tắc phụ (sub-rules) cụ thể gây ra vấn đề

### Vấn đề: WAF không chặn các cuộc tấn công thử nghiệm

**Nguyên nhân:**
- WAF vẫn đang triển khai đến các vị trí biên (đợi 5 phút)
- Thứ tự ưu tiên quy tắc không chính xác
- Quy tắc được đặt thành Count thay vì Block

**Giải pháp:**
1. Xác minh trạng thái Web ACL là Active
2. Kiểm tra các hành động của quy tắc được đặt thành Block
3. Xác minh liên kết CloudFront đã hoàn tất
4. Kiểm tra thứ tự ưu tiên của quy tắc
5. Xóa cache CloudFront và thử lại

### Vấn đề: Không thể thấy Web ACL trong CloudFront

**Giải pháp:**
- Đảm bảo bạn đã tạo WAF trong vùng Global (CloudFront)
- Regional WAF (cho ALB/API Gateway) sẽ không xuất hiện cho CloudFront
- Tạo lại Web ACL trong đúng vùng nếu cần

### Vấn đề: Giới hạn tốc độ không hoạt động

**Giải pháp:**
1. Xác minh quy tắc giới hạn tốc độ đã được bật và thứ tự ưu tiên chính xác
2. Kiểm tra bạn đang thử nghiệm từ cùng một IP (các IP khác nhau có bộ đếm riêng)
3. Nhớ rằng giới hạn tốc độ tính theo cửa sổ 5 phút
4. Thử nghiệm với đủ số lượng requests (ví dụ: 2,100+ cho giới hạn 2,000)

### Vấn đề: Chi phí WAF cao

**Giải pháp:**
1. Xem lại những managed rules nào bạn thực sự cần
2. Cân nhắc sử dụng ít rule groups hơn
3. Sử dụng custom rules thay vì nhiều managed rules nếu có thể
4. Xóa Web ACL khi không sử dụng tích cực

## Tóm tắt

Chúc mừng! Bạn đã thành công:
-  Tạo một AWS WAF Web ACL
-  Cấu hình các nhóm quy tắc được quản lý bởi AWS (AWS managed rule groups) để bảo vệ
-  Thiết lập các quy tắc giới hạn tốc độ tùy chỉnh
-  Liên kết WAF với CloudFront
-  Kiểm tra và xác minh khả năng bảo vệ của WAF
-  Học cách giám sát và khắc phục sự cố hoạt động của WAF
-  (Tùy chọn) Thiết lập logging và cảnh báo CloudWatch

### Những gì bạn đã đạt được

Ứng dụng của bạn hiện đã được bảo vệ chống lại:
- **SQL injection attacks**: Các nỗ lực khai thác cơ sở dữ liệu bị chặn
- **Cross-site scripting (XSS)**: Ngăn chặn tiêm nhiễm mã độc
- **Known bad inputs**: Các mẫu không hợp lệ và độc hại bị lọc
- **Rate-based attacks**: Giảm thiểu lạm dụng tự động và DDoS
- **IP reputation threats**: Các IP độc hại đã biết bị chặn

### Kiến trúc hoàn chỉnh
```
Người dùng Internet
      ↓
AWS WAF (Quy tắc bảo mật)
      ↓
CloudFront (HTTPS + Caching)
      ↓
S3 Bucket (Riêng tư, Nội dung tĩnh)
```

### Các lớp bảo mật hiện có

1. **Lớp mạng (Network Layer)**: CloudFront với bảo vệ DDoS (AWS Shield Standard)
2. **Lớp ứng dụng (Application Layer)**: AWS WAF với managed rules và giới hạn tốc độ
3. **Lớp vận chuyển (Transport Layer)**: Mã hóa HTTPS với SSL/TLS
4. **Lớp lưu trữ (Storage Layer)**: S3 bucket riêng tư với OAC

## Tóm tắt các thực hành tốt nhất (Best Practices)

**Cho Môi trường Production:**
-  Bắt đầu với managed rule groups để có sự bảo vệ cơ bản
-  Sử dụng chế độ Count để kiểm tra quy tắc trước khi chặn
-  Giám sát sampled requests thường xuyên để tìm dương tính giả
-  Thiết lập cảnh báo CloudWatch cho hoạt động bất thường
-  Bật logging để kiểm toán và tuân thủ
-  Xem xét và cập nhật quy tắc dựa trên mô hình lưu lượng truy cập
-  Sử dụng IP sets cho các danh sách allow/block động
-  Triển khai giới hạn tốc độ phù hợp cho trường hợp sử dụng của bạn
- Ghi lại các thay đổi quy tắc và các ngoại lệ

**Giám sát bảo mật:**
- Kiểm tra dashboard WAF hàng tuần
- Xem lại các requests bị chặn để tìm mẫu tấn công
- Điều tra sự gia tăng đột biến trong lưu lượng bị chặn
- Cập nhật quy tắc khi các mối đe dọa mới xuất hiện
- Giữ managed rules được bật để tự động cập nhật

## Bước tiếp theo

Bạn đã hoàn thành việc triển khai frontend! Trang web tĩnh của bạn hiện tại:
- Được phân phối toàn cầu với CloudFront
- Được bảo vệ bởi AWS WAF
- Phục vụ qua HTTPS
- Được bảo mật với quyền truy cập S3 riêng tư

**Tiến tới Hội thảo 2** để xây dựng backend:
- API Gateway cho RESTful APIs
- Lambda functions cho logic nghiệp vụ
- RDS cho lưu trữ cơ sở dữ liệu
- Cognito cho xác thực người dùng
- Secrets Manager cho thông tin xác thực

**Các lệnh hữu ích:**
```bash
# Lấy chi tiết Web ACL
aws wafv2 get-web-acl \
  --scope CLOUDFRONT \
  --id YOUR-WEBACL-ID \
  --name workshop-frontend-waf

# Liệt kê các Web ACLs
aws wafv2 list-web-acls \
  --scope CLOUDFRONT

# Lấy sampled requests
aws wafv2 get-sampled-requests \
  --web-acl-arn YOUR-WEBACL-ARN \
  --rule-metric-name RateLimitRule \
  --scope CLOUDFRONT \
  --time-window StartTime=1234567890,EndTime=1234567900 \
  --max-items 100

# Cập nhật hành động quy tắc thành Count (kiểm thử)
aws wafv2 update-web-acl \
  --scope CLOUDFRONT \
  --id YOUR-WEBACL-ID \
  --name workshop-frontend-waf \
  --default-action Allow={} \
  # ... (các tham số bổ sung)
```

**Đường dẫn WAF Dashboard:**
- Tổng quan Web ACL: `https://console.aws.amazon.com/wafv2/homev2/web-acl/workshop-frontend-waf/`
- CloudWatch Metrics: `https://console.aws.amazon.com/cloudwatch/home?region=us-east-1#metricsV2:query=~(metricName~'BlockedRequests)`

---

**Chúc mừng!** Bạn đã triển khai và bảo mật thành công ứng dụng frontend serverless sẵn sàng cho production trên AWS! 🎉

## Hiểu về Web ACL JSON
```JSON
    {
  "ARN": "arn:aws:wafv2:us-east-1:362324939369:global/webacl/CreatedByCloudFront-cf37def6/78a29ce4-287f-47a4-b108-886bfc3ae748",
  "Capacity": 925,
  "DefaultAction": {
    "Allow": {}
  },
  "Description": "",
  "Id": "78a29ce4-287f-47a4-b108-886bfc3ae748",
  "LabelNamespace": "awswaf:362324939369:webacl:CreatedByCloudFront-cf37def6:",
  "ManagedByFirewallManager": false,
  "Name": "CreatedByCloudFront-cf37def6",
  "OnSourceDDoSProtectionConfig": {
    "ALBLowReputationMode": "ACTIVE_UNDER_DDOS"
  },
  "RetrofittedByFirewallManager": false,
  "Rules": [
    {
      "Name": "AWS-AWSManagedRulesAmazonIpReputationList",
      "OverrideAction": {
        "None": {}
      },
      "Priority": 0,
      "Statement": {
        "ManagedRuleGroupStatement": {
          "Name": "AWSManagedRulesAmazonIpReputationList",
          "VendorName": "AWS"
        }
      },
      "VisibilityConfig": {
        "CloudWatchMetricsEnabled": true,
        "MetricName": "AWS-AWSManagedRulesAmazonIpReputationList",
        "SampledRequestsEnabled": true
      }
    },
    {
      "Name": "AWS-AWSManagedRulesCommonRuleSet",
      "OverrideAction": {
        "None": {}
      },
      "Priority": 1,
      "Statement": {
        "ManagedRuleGroupStatement": {
          "Name": "AWSManagedRulesCommonRuleSet",
          "VendorName": "AWS"
        }
      },
      "VisibilityConfig": {
        "CloudWatchMetricsEnabled": true,
        "MetricName": "AWS-AWSManagedRulesCommonRuleSet",
        "SampledRequestsEnabled": true
      }
    },
    {
      "Name": "AWS-AWSManagedRulesKnownBadInputsRuleSet",
      "OverrideAction": {
        "None": {}
      },
      "Priority": 2,
      "Statement": {
        "ManagedRuleGroupStatement": {
          "Name": "AWSManagedRulesKnownBadInputsRuleSet",
          "VendorName": "AWS"
        }
      },
      "VisibilityConfig": {
        "CloudWatchMetricsEnabled": true,
        "MetricName": "AWS-AWSManagedRulesKnownBadInputsRuleSet",
        "SampledRequestsEnabled": true
      }
    }
  ],
  "VisibilityConfig": {
    "CloudWatchMetricsEnabled": true,
    "MetricName": "CreatedByCloudFront-cf37def6",
    "SampledRequestsEnabled": true
  }
}
```

### ARN (Amazon Resource Name)
```json
"ARN": "arn:aws:wafv2:us-east-1:362324939369:global/webacl/CreatedByCloudFront-cf37def6/78a29ce4-287f-47a4-b108-886bfc3ae748"
```

**Nó là gì:** Một định danh duy nhất cho Web ACL của bạn trên toàn bộ AWS.

**Phân tích ARN:**
- `wafv2`: Dịch vụ (AWS WAF phiên bản 2)
- `us-east-1`: Vùng nơi metadata được lưu trữ (tất cả CloudFront WAF đều sử dụng us-east-1)
- `362324939369`: ID tài khoản AWS của bạn
- `global`: Phạm vi (Tài nguyên CloudFront là toàn cầu)
- `webacl`: Loại tài nguyên
- `CreatedByCloudFront-cf37def6`: Tên Web ACL
- `78a29ce4-287f-47a4-b108-886bfc3ae748`: ID Web ACL duy nhất

### Capacity (Dung lượng)
```json
"Capacity": 925
```

**Ý nghĩa:** Web ACL này đang sử dụng 925 trong tổng số tối đa 1,500 Web ACL Capacity Units (WCUs).

**Phân bổ dung lượng:**
- Amazon IP Reputation List: ~25 WCUs
- Common Rule Set: ~700 WCUs
- Known Bad Inputs: ~200 WCUs
- **Tổng cộng**: 925 WCUs
- **Dung lượng còn lại**: 575 WCUs (bạn có thể thêm nhiều quy tắc hơn)

{{% notice info %}}
**Về dung lượng WCU:**
Mỗi quy tắc và managed rule group tiêu thụ WCUs dựa trên độ phức tạp của nó. Giới hạn 1,500 WCU đảm bảo hiệu suất tối ưu. Nếu bạn cần thêm dung lượng, bạn có thể:<br>
Xóa các quy tắc không sử dụng<br>
Tạo nhiều Web ACLs cho các distributions khác nhau<br>
Sử dụng custom rule groups để tối ưu hóa việc sử dụng dung lượng<br>
{{% /notice %}}

### Default Action (Hành động mặc định)
```json
"DefaultAction": {
    "Allow": {}
  }
```

**Ý nghĩa:** Các requests không khớp với bất kỳ quy tắc nào sẽ được **cho phép (allowed)** theo mặc định.

**Đây là cách tiếp cận được khuyến nghị** bởi vì:
- Các quy tắc chặn rõ ràng lưu lượng độc hại
- Lưu lượng hợp lệ đi qua theo mặc định
- Giảm rủi ro chặn người dùng hợp lệ

**Lựa chọn thay thế:** Đặt hành động mặc định là "Block" sẽ yêu cầu các quy tắc Allow rõ ràng cho tất cả lưu lượng hợp lệ (không khuyến nghị cho hầu hết các trường hợp sử dụng).

### ID và Name
```json
"Id": "78a29ce4-287f-47a4-b108-886bfc3ae748",
"Name": "CreatedByCloudFront-cf37def6"
```

- **ID**: Định danh duy nhất được sử dụng trong các lệnh gọi API
- **Name**: Tên dễ đọc (tự động tạo nếu được tạo qua CloudFront console)

### Label Namespace
```json
"LabelNamespace": "awswaf:362324939369:webacl:CreatedByCloudFront-cf37def6:"
```

**Nó là gì:** Một tiền tố cho các nhãn (labels) được áp dụng bởi các quy tắc trong Web ACL này.

**Labels** là các thẻ mà quy tắc có thể thêm vào requests. Các quy tắc khác sau đó có thể khớp dựa trên các nhãn này cho logic nâng cao. Ví dụ:
- Một quy tắc gắn nhãn request là "suspicious" (đáng ngờ)
- Một quy tắc khác chặn tất cả các requests có nhãn "suspicious"

## Cài đặt Firewall Manager
```json
"ManagedByFirewallManager": false,
"RetrofittedByFirewallManager": false
```

**Ý nghĩa:** Web ACL này **không** được quản lý bởi AWS Firewall Manager.

**AWS Firewall Manager** là một dịch vụ để quản lý tập trung các chính sách bảo mật trên nhiều tài khoản AWS. Vì các giá trị này là `false`:
- Bạn có toàn quyền kiểm soát để sửa đổi Web ACL này
- Các thay đổi sẽ không bị ghi đè bởi các chính sách tổ chức
- Bạn đang quản lý bảo mật ở cấp độ tài khoản cá nhân

## Cấu hình bảo vệ DDoS
```json
"OnSourceDDoSProtectionConfig": {
    "ALBLowReputationMode": "ACTIVE_UNDER_DDOS"
  }
```

**Nó là gì:** Cấu hình cho cách WAF xử lý lưu lượng từ các nguồn có uy tín thấp trong các cuộc tấn công DDoS.

**Chế độ ACTIVE_UNDER_DDOS:**
- Trong hoạt động bình thường: Tất cả lưu lượng được xử lý bình thường
- Trong cuộc tấn công DDoS đang diễn ra: WAF áp dụng giám sát kỹ lưỡng hơn đối với các requests từ các IP có điểm uy tín thấp
- Điều này cung cấp thêm một lớp bảo vệ khi ứng dụng của bạn bị tấn công

{{% notice note %}}
**Về AWS Shield:**
Cấu hình này hoạt động cùng với AWS Shield Standard, được tự động bao gồm với CloudFront mà không mất thêm phí. Shield Standard cung cấp:<br>
Bảo vệ DDoS ở các lớp mạng và vận chuyển<br>
Tự động phát hiện và giảm thiểu<br>
Bảo vệ luôn bật<br>
Để tăng cường bảo vệ, bạn có thể nâng cấp lên AWS Shield Advanced (chi phí bổ sung).
{{% /notice %}}

## Cấu hình Quy tắc (Rules Configuration)

Web ACL của bạn chứa **3 nhóm quy tắc được quản lý**, được đánh giá theo thứ tự ưu tiên (0 → 1 → 2).

### Quy tắc 1: Amazon IP Reputation List (Ưu tiên 0)
```json
{
    "Name": "AWS-AWSManagedRulesAmazonIpReputationList",
    "Priority": 0,
    "Statement": {
        "ManagedRuleGroupStatement": {
            "Name": "AWSManagedRulesAmazonIpReputationList",
            "VendorName": "AWS"
        }
    },
    "OverrideAction": {
        "None": {}
    },
    "VisibilityConfig": {
        "CloudWatchMetricsEnabled": true,
        "MetricName": "AWS-AWSManagedRulesAmazonIpReputationList",
        "SampledRequestsEnabled": true
      }
}
```

**Nó làm gì:**
- Chặn các requests từ các địa chỉ IP được biết đến với hoạt động độc hại
- AWS duy trì danh sách các IP xấu được cập nhật liên tục dựa trên thông tin tình báo về mối đe dọa
- Bao gồm các IP liên quan đến:
    - Botnets
    - Các chiến dịch spam
    - Phân phối phần mềm độc hại (Malware)
    - Tấn công DDoS

**Priority 0:** Được đánh giá đầu tiên - nếu một request đến từ một IP xấu đã biết, nó sẽ bị chặn ngay lập tức mà không cần kiểm tra các quy tắc khác.

**OverrideAction: None:**
- Sử dụng hành động mặc định được xác định trong managed rule group (Block)
- Không áp dụng ghi đè nào
- Tất cả các quy tắc trong nhóm này vẫn hoạt động

**VisibilityConfig:**
- **CloudWatchMetricsEnabled: true** - Metrics được gửi đến CloudWatch
- **MetricName** - Được sử dụng trong CloudWatch để lọc/cảnh báo
- **SampledRequestsEnabled: true** - Lưu trữ các requests mẫu để phân tích trong WAF console

### Quy tắc 2: Common Rule Set (Ưu tiên 1)
```json
{
    "Name": "AWS-AWSManagedRulesCommonRuleSet",
    "Priority": 1,
    "Statement": {
        "ManagedRuleGroupStatement": {
            "Name": "AWSManagedRulesCommonRuleSet",
            "VendorName": "AWS"
        }
    },
    "OverrideAction": {
        "None": {}
    },
    "VisibilityConfig": {
        "CloudWatchMetricsEnabled": true,
        "MetricName": "AWS-AWSManagedRulesCommonRuleSet",
        "SampledRequestsEnabled": true
      }
}
```

**Nó làm gì:**
- Cung cấp bảo vệ rộng rãi chống lại các khai thác web phổ biến
- Dựa trên danh sách lỗ hổng OWASP Top 10
- Bảo vệ chống lại:
    - **Cross-Site Scripting (XSS)**: Tiêm nhiễm mã độc
    - **Local File Inclusion (LFI)**: Truy cập tệp trái phép
    - **Remote File Inclusion (RFI)**: Tải các tệp độc hại từ bên ngoài
    - **Command Injection**: Cố gắng thực thi lệnh hệ điều hành
    - **Path Traversal**: Tấn công duyệt thư mục (ví dụ: `../../etc/passwd`)
    - **SQL Injection**: Các mẫu SQL injection cơ bản
    - **Session Fixation**: Cố gắng chiếm đoạt phiên

**Priority 1:** Được đánh giá sau kiểm tra uy tín IP. Nếu request vượt qua kiểm tra IP, nó sẽ được kiểm tra các mẫu tấn công phổ biến.

**Tại sao nó quan trọng:**
- Đây là nhóm quy tắc toàn diện nhất
- Bao phủ phần lớn các cuộc tấn công web phổ biến
- Được cập nhật thường xuyên bởi đội ngũ bảo mật AWS
- Sử dụng 700 WCUs (nhóm quy tắc lớn nhất)

### Quy tắc 3: Known Bad Inputs (Ưu tiên 2)
```json
{
    "Name": "AWS-AWSManagedRulesKnownBadInputsRuleSet",
    "Priority": 2,
    "Statement": {
        "ManagedRuleGroupStatement": {
            "Name": "AWSManagedRulesKnownBadInputsRuleSet",
            "VendorName": "AWS"
        }
    },
    "OverrideAction": {
        "None": {}
    },
    "VisibilityConfig": {
        "CloudWatchMetricsEnabled": true,
        "MetricName": "AWS-AWSManagedRulesKnownBadInputsRuleSet",
        "SampledRequestsEnabled": true
      }
}
```

**Nó làm gì:**
- Chặn các requests có các mẫu được biết đến là không hợp lệ hoặc khai thác
- Tập trung vào các đầu vào dị dạng (malformed inputs) không bao giờ nên xuất hiện trong lưu lượng hợp lệ
- Bảo vệ chống lại:
    - **Malformed request patterns**: Requests vi phạm tiêu chuẩn HTTP
    - **Invalid characters**: Các ký tự đặc biệt ở những nơi không mong đợi
    - **Known exploit patterns**: Chữ ký của các lỗ hổng nổi tiếng
    - **CVE exploits**: Các mẫu khớp với các Lỗ hổng và Phơi nhiễm Phổ biến (Common Vulnerabilities and Exposures) đã công bố

**Priority 2:** Tuyến phòng thủ cuối cùng. Nếu một request vượt qua uy tín IP và các kiểm tra quy tắc phổ biến, nhóm quy tắc này sẽ bắt các mẫu độc hại đã biết còn lại.

**Tại sao nó hữu ích:**
- Tỷ lệ dương tính giả rất thấp (hiếm khi chặn lưu lượng hợp lệ)
- Bắt các nỗ lực khai thác nhắm vào các lỗ hổng cụ thể
- Bổ sung cho Common Rule Set với các mẫu cụ thể hơn

## Cách các quy tắc được đánh giá (Evaluation Flow)

### Luồng đánh giá
```
Request đến
      ↓
Priority 0: Kiểm tra uy tín IP (IP Reputation Check)
      ├─ Khớp (Match) → Chặn (403 Forbidden)
      └─ Không khớp → Tiếp tục
      ↓
Priority 1: Common Rule Set
      ├─ Khớp (Match) → Chặn (403 Forbidden)
      └─ Không khớp → Tiếp tục
      ↓
Priority 2: Known Bad Inputs
      ├─ Khớp (Match) → Chặn (403 Forbidden)
      └─ Không khớp → Tiếp tục
      ↓
Hành động mặc định: Cho phép (Allow)
      ↓
Request được chuyển tiếp đến CloudFront
```

### Điểm chính

1. **Khớp đầu tiên sẽ thắng (First match wins)**: Khi một quy tắc khớp, hành động của nó được thực hiện ngay lập tức (nếu là hành động Block)
2. **Thứ tự ưu tiên quan trọng**: Số thấp hơn được đánh giá trước (0 trước 1 trước 2)
3. **Managed rules hiệu quả**: Ngay cả với 700 WCUs, Common Rule Set đánh giá rất nhanh
4. **Hành động mặc định chỉ áp dụng nếu không có quy tắc nào khớp**: Hầu hết các requests sẽ khớp với một quy tắc hoặc đi đến hành động Allow mặc định

## Khả năng hiển thị và Giám sát
```json
"VisibilityConfig": {
    "CloudWatchMetricsEnabled": true,
    "MetricName": "CreatedByCloudFront-cf37def6",
    "SampledRequestsEnabled": true
}
```

### CloudWatch Metrics Enabled

**Metrics tự động gửi đến CloudWatch:**
- Tổng số requests
- Requests được phép (Allowed)
- Requests bị chặn (Blocked)
- Requests được đếm (Counted - nếu có quy tắc dùng hành động Count)
- Metrics theo từng quy tắc

**Truy cập metrics:**
- CloudWatch console → Metrics → WAF
- Namespace: `AWS/WAFV2`
- Dimensions: WebACL, Rule, Region

### Sampled Requests Enabled

**Nó thu thập gì:**
- Lên đến 100 requests gần đây cho mỗi quy tắc
- Bao gồm cả requests được phép và bị chặn
- Chi tiết request: IP, URI, headers, hành động đã thực hiện

**Xem sampled requests:**
1. WAF Console → Web ACL của bạn
2. Cuộn đến phần "Sampled requests"
3. Nhấp vào bất kỳ request nào để xem chi tiết đầy đủ

**Trường hợp sử dụng:**
- Khắc phục sự cố dương tính giả
- Hiểu các mẫu tấn công
- Xác minh hiệu quả của quy tắc