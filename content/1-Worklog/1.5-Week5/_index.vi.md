---
title: "Worklog tuần 5"
date: "2025-09-15"
weight: 1
chapter: false
pre: " <b> 1.5. </b> "
---

### Mục tiêu tuần 5:
* Tìm hiểu về AWS Lambda
* Tìm hiểu về Amazon Cognito và xác thực/phân quyền hiện đại trong ứng dụng web
* Tìm hiểu về API Gateway

### Các nhiệm vụ cần thực hiện trong tuần này:
| Ngày | Nhiệm vụ                                                                                  | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| --- |-------------------------------------------------------------------------------------------|------------|----------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2   | - Tìm hiểu về AWS Lambda                                                                  | 10/06/2025 | 10/06/2025     | <https://000078.awsstudygroup.com/> <https://aws.amazon.com/lambda/> <https://www.youtube.com/watch?v=e1tkFsFOBHA> <https://www.youtube.com/watch?v=jgpRAiar2LQ>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               
| 3   | - Tìm hiểu về xác thực/phân quyền hiện đại trong ứng dụng web<br/>- Tìm hiểu về Amazon Cognito | 10/07/2025 | 10/07/2025     | <https://dev.to/ifechukwuobiezue/mastering-modern-authentication-in-web-applications-oauth-20-demystified-2b8b> <https://aws.amazon.com/pm/cognito/?trk=246e27b6-9b79-45af-ba59-fdf3f8be64f8&sc_channel=ps&ef_id=CjwKCAiA86_JBhAIEiwA4i9Ju4vtraj9L5zlL1SabX8bG7JmTWk4rdIX4ehPV0jJd8BKZlIMymtVdhoCunwQAvD_BwE:G:s&s_kwcid=AL!4422!3!785574083501!e!!g!!amazon%20cognito!23300619811!193013719910&gad_campaignid=23300619811&gbraid=0AAAAADjHtp9IwNbHJlX1E1BUO5b4ZKeV_&gclid=CjwKCAiA86_JBhAIEiwA4i9Ju4vtraj9L5zlL1SabX8bG7JmTWk4rdIX4ehPV0jJd8BKZlIMymtVdhoCunwQAvD_BwE> <https://www.youtube.com/watch?v=tCNcG1lcCHY> <https://www.youtube.com/watch?v=UBUNrFtufWo> <https://www.youtube.com/watch?v=fyTxwIa-1U0> |
| 4   | - Tìm hiểu về API Gateway                                                                 | 10/08/2025 | 10/08/2025     | <https://www.youtube.com/watch?v=6ULyxuHKxg8> <https://www.youtube.com/watch?v=jgpRAiar2LQ>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| 5   | - Tiếp tục với Dự án cuối kỳ FCJ                                                         | 10/09/2025 | 10/09/2025     |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| 6   | - Tiếp tục với Dự án cuối kỳ FCJ                                                         | 10/10/2025 | 10/10/2025     |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |


### Thành tựu tuần 5:

* Đã học các kiến thức cơ bản về AWS Lambda:
    * Hiểu mô hình điện toán serverless và lợi ích của nó (không cần quản lý server, trả tiền theo sử dụng, tự động mở rộng quy mô).
    * Khám phá cấu trúc hàm Lambda: handler, event, context và môi trường runtime.
    * Tìm hiểu về môi trường thực thi Lambda và cách chúng hoạt động (cold starts so với warm starts).
    * Hiểu mô hình định giá Lambda: gói miễn phí (1 triệu requests + 400K GB-giây), sau đó trả tiền theo mỗi request và thời gian tính toán.
    * Khám phá các runtime Lambda khác nhau: Node.js, Python, Java, Go và custom runtimes.
* Thực hành với các hàm Lambda:
    * Tạo hàm Lambda đầu tiên sử dụng Node.js runtime.
    * Cấu hình các thiết lập hàm: phân bổ bộ nhớ, timeout và biến môi trường.
    * Kiểm tra các hàm Lambda trực tiếp trong console với các test events.
    * Khám phá logs Lambda trong CloudWatch để debug và giám sát việc thực thi.
    * Tìm hiểu về Lambda layers để chia sẻ code và dependencies giữa các hàm.

* Đạt được hiểu biết toàn diện về xác thực hiện đại:
    * Tìm hiểu các giao thức OAuth 2.0 và OpenID Connect và vai trò của chúng trong ứng dụng web hiện đại.
    * Hiểu sự khác biệt giữa xác thực (authentication - bạn là ai) và phân quyền (authorization - bạn có thể làm gì).
    * Khám phá cấu trúc JWT (JSON Web Tokens): header, payload, signature.
    * Tìm hiểu về các loại token: ID tokens (định danh), Access tokens (truy cập API), Refresh tokens (lấy token mới).
    * Hiểu luồng xác thực dựa trên token và các phương pháp bảo mật tốt nhất.
* Nắm vững Amazon Cognito cho quản lý người dùng:
    * Tạo và cấu hình Cognito User Pools cho thư mục người dùng và xác thực.
    * Thiết lập các thuộc tính người dùng: email, username, số điện thoại, thuộc tính tùy chỉnh.
    * Cấu hình chính sách mật khẩu và các thiết lập bảo mật (tùy chọn MFA, khôi phục tài khoản).
    * Triển khai luồng xác minh email cho việc đăng ký người dùng.
    * Tạo app clients và cấu hình các thiết lập OAuth 2.0 (grant types, scopes, callback URLs).
    * Thiết lập Cognito Hosted UI để kiểm tra xác thực nhanh chóng.
    * Tích hợp Cognito với API Gateway như một authorizer để bảo mật các API endpoints.
* Thực hành triển khai xác thực:
    * Đăng ký người dùng thử nghiệm thông qua Cognito Hosted UI.
    * Kiểm tra toàn bộ luồng xác thực: đăng ký, xác minh email, đăng nhập.
    * Lấy và giải mã JWT tokens để hiểu các user claims.
    * Cấu hình API Gateway Cognito authorizer để xác thực JWT tokens.
    * Kiểm tra các API calls đã xác thực với Bearer tokens trong Authorization headers.
    * Trích xuất thông tin người dùng từ JWT claims trong các hàm Lambda (`event.requestContext.authorizer.claims`).
    * Triển khai tự động điền dữ liệu người dùng (cognitoSub, email) từ các token đã xác thực.
* Hiểu các mẫu xác thực sẵn sàng cho production:
    * Tìm hiểu về cơ chế làm mới token để duy trì phiên người dùng mà không cần xác thực lại.
    * Khám phá xử lý hết hạn token và các tác động bảo mật.
    * Hiểu tầm quan trọng của HTTPS và lưu trữ token an toàn.
    * Tìm hiểu mô hình định giá Cognito: 50.000 MAU (Monthly Active Users) đầu tiên miễn phí.

* Đã học các kiến thức cơ bản về Amazon API Gateway:
    * Hiểu vai trò của API Gateway như "cửa trước" cho các dịch vụ backend.
    * Khám phá các loại API khác nhau: REST API, HTTP API, WebSocket API.
    * Tìm hiểu các khái niệm REST API: resources, methods, stages, deployments.
    * Hiểu Lambda proxy integration so với các mẫu custom integration.
    * Khám phá định giá API Gateway: $3.50 cho mỗi triệu requests (REST API).
* Xây dựng cơ sở hạ tầng REST API hoàn chỉnh:
    * Tạo REST API với nhiều resources và methods (GET, POST, PUT, DELETE).
    * Thiết kế các RESTful endpoints theo phương pháp tốt nhất (`/users`, `/users/{id}`).
    * Cấu hình Lambda proxy integration để gọi hàm Lambda một cách liền mạch.
    * Thiết lập path parameters cho định tuyến động (`{id}`).
    * Triển khai các mã trạng thái HTTP phù hợp cho các tình huống khác nhau (200, 201, 400, 404, 500).
* Cấu hình CORS (Cross-Origin Resource Sharing):
    * Hiểu CORS và lý do cần thiết cho các ứng dụng dựa trên trình duyệt gọi APIs từ các domain khác nhau.
    * Bật CORS trên các resources của API Gateway để cho phép tích hợp frontend.
    * Cấu hình allowed origins, headers và methods cho các requests cross-origin an toàn.
    * Thiết lập OPTIONS method cho các CORS preflight requests.
* Triển khai bảo mật và giám sát API:
    * Deploy API lên các stages (dev, prod) để quản lý môi trường.
    * Cấu hình request throttling để bảo vệ backend khỏi lạm dụng (rate: 1000 req/s, burst: 2000).
    * Bật CloudWatch Logs để ghi log request/response và debug.
    * Thiết lập các metrics CloudWatch chi tiết để giám sát hiệu suất API.
    * Tạo CloudWatch alarms cho lỗi 4XX, lỗi 5XX và độ trễ cao.
* Thực hành kiểm tra API:
    * Kiểm tra các API endpoints sử dụng tính năng test trong console API Gateway.
    * Thực hiện các API calls đã xác thực sử dụng curl với Bearer tokens.
    * Sử dụng Postman để tạo bộ sưu tập API hoàn chỉnh cho tất cả các thao tác CRUD.
    * Xác minh xử lý lỗi và mã trạng thái phù hợp cho các tình huống khác nhau.
    * Xuất tài liệu API ở định dạng OpenAPI/Swagger.

* Áp dụng kiến thức serverless vào dự án cuối kỳ:
    * Thiết kế và triển khai kiến trúc backend serverless cho yêu cầu dự án.
    * Tích hợp các thành phần xác thực, API Gateway, Lambda và database.
    * Kiểm tra các luồng công việc end-to-end và tinh chỉnh triển khai.
    * Tài liệu hóa các quyết định kiến trúc và chi tiết triển khai.
    * Chuẩn bị tài liệu trình bày và demo dự án.