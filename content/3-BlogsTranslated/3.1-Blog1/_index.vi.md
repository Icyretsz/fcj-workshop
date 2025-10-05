---
title: "Blog 1"
date: "2025-09-15"
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---
# SportsCapital ra mắt tính năng phát hiện sự kiện theo thời gian thực cho giao dịch thể thao được hỗ trợ bởi AWS

**Viết bởi:** James Lockwood và Asim Jalis | **Ngày:** 03/06/2025

*Bài blog này đồng tác giả bởi Aaron Riccio, nhà đồng sáng lập và CEO của SportsCapital, và Pravin Santhanam, CTO của SportsCapital*

---

## Thách thức trong giao dịch thể thao

Các nhà cái thể thao (sportsbook) phụ thuộc vào các đội ngũ giao dịch (trading teams) để quản lý tỷ lệ cược và ngăn chặn những người chơi chuyên nghiệp (sharp bettors) khai thác các điểm bất hợp lý trong việc định giá. Những sự kiện tin tức, chẳng hạn như thông tin về chấn thương của cầu thủ hoặc thay đổi đội hình, thường tạo ra sự biến động có thể làm thay đổi thị trường bất kỳ lúc nào và buộc các nhà giao dịch phải điều chỉnh tỷ lệ trong thời gian thực.

Bất kỳ sự chậm trễ nào trong quá trình này đều có thể khiến nhà cái phải chịu rủi ro lên tới hàng triệu đô la. Tuy nhiên, vì tin tức và các báo cáo trực tiếp vốn có cấu trúc không đồng nhất, việc phát hiện và sử dụng chúng một cách hiệu quả trong môi trường thời gian thực không hề đơn giản. Thách thức này càng trở nên lớn hơn do khối lượng thông tin khổng lồ được tạo ra từ hàng nghìn nguồn tin địa phương và quốc gia trên tất cả các giải đấu lớn và thể thao đại học.

## Giải pháp của SportsCapital

Hệ thống phát hiện sự kiện của SportsCapital, được xây dựng trên nền tảng Amazon Web Services (AWS) như Amazon SageMaker và Amazon Bedrock, mang đến một giải pháp sáng tạo. Hệ thống này sử dụng các pipeline xử lý dữ liệu dựa trên điện toán đám mây và AI tạo sinh (generative AI) để biến các nội dung thời gian thực thành các luồng dữ liệu có cấu trúc phục vụ cho mô hình định giá và ứng dụng giao dịch.

Các nhà cái hàng đầu đang sử dụng công nghệ này để nhận cảnh báo chấn thương theo thời gian thực, và gần đây hơn là để xây dựng các hệ thống giao dịch tự động.

> "Các cảnh báo của chúng tôi giúp các nhà giao dịch là những người đầu tiên biết về một sự kiện chấn thương trước khi phần còn lại của thị trường kịp phản ứng. Hiện nay, chúng tôi đang chứng kiến nhiều khoản đầu tư hơn từ các nhà cái vào tự động hóa giao dịch, nhưng điều đó chỉ khả thi nếu bạn có thể khai thác hiệu quả các bản tin này để điều chỉnh tỷ lệ và dự báo một cách linh hoạt."
>
> — **Aaron Riccio, CEO của SportsCapital**

## Lợi thế của điện toán đám mây

Cơ sở hạ tầng dựa trên đám mây của SportsCapital cho phép xử lý dữ liệu theo thời gian thực cho cả nội dung ngắn và dài trên môi trường trực tuyến. Hệ thống này chuyển đổi các tin tức trực tiếp từ nhiều định dạng truyền thông khác nhau thành metadata nâng cao, chẳng hạn như phân loại tin tức, liên kết thực thể (entity linking) và mức độ liên quan.

> "Các dịch vụ được quản lý (managed services) từ AWS là trung tâm của kiến trúc của chúng tôi, mang lại khả năng mở rộng và độ tin cậy cần thiết để xử lý hàng nghìn tin tức mỗi ngày mà vẫn đảm bảo yêu cầu độ trễ nghiêm ngặt. Chúng mang đến một giải pháp hiệu quả để chúng tôi cung cấp các dịch vụ thời gian thực cho khách hàng."
>
> — **Pravin Santhanam, CTO của SportsCapital**

![**Ảnh 1:** Phát hiện sự kiện theo thời gian thực trong hệ thống xây dựng với AWS của SportsCapital](/images/3-BlogsTranslated/SC-Image-1-1.png)
**Ảnh 1:** Phát hiện sự kiện theo thời gian thực trong hệ thống xây dựng với AWS của SportsCapital

### Kiến trúc hệ thống

Sử dụng Amazon Elastic Cloud Compute (Amazon EC2), hệ thống pipeline phát hiện sự kiện của SportsCapital liên tục giám sát các nguồn tin để phát hiện các sự kiện liên quan. Dữ liệu mới được lưu trữ trong cơ sở hạ tầng cơ sở dữ liệu của họ thông qua nền tảng dữ liệu đám mây dành cho nhà phát triển MongoDB Atlas chạy trên Amazon EC2 và được quản lý bằng Amazon Relational Database Service (Amazon RDS) cho PostgreSQL.

Khi các báo cáo mới đi vào hệ thống, database triggers sẽ kích hoạt quá trình xử lý serverless thông qua AWS Lambda. Các Lambda function này thực hiện việc làm sạch văn bản và liên kết thực thể (entity linking), chẳng hạn như xác định cầu thủ, đội bóng, nhân viên, hoặc người đại diện được nhắc đến trong nội dung.

Amazon SageMaker lưu trữ các mô hình machine learning (ML) do SportsCapital huấn luyện để thực hiện các tác vụ xử lý nâng cao này, cho phép họ mở rộng khả năng xử lý dựa trên nhu cầu thực tế. Các content triggers sẽ kết hợp với các thuật toán đối chiếu (matching algorithms) để định tuyến thông tin phù hợp đến đúng nơi cần thiết dựa trên các bộ lọc tùy chỉnh.

**Độ trễ hiện tại** — tức là thời gian từ khi một bản tin được xuất bản đến khi được xử lý hoàn chỉnh — trung bình khoảng **7 giây**.

### Xử lý nội dung dài

Đối với các nội dung dạng dài (longform), cần thêm bước xử lý bổ sung, và SportsCapital sử dụng mô hình ngôn ngữ lớn (LLMs) trong Amazon Bedrock để thực hiện. Các LLM này sẽ phân tích nội dung và chia thành nhiều đoạn nhỏ, mỗi đoạn chứa thông tin độc lập. Pipeline này cũng chuyển đổi âm thanh thành văn bản bằng công nghệ speech-to-text từ Amazon Transcribe. Cách phân đoạn này giúp họ duy trì quy trình xử lý thống nhất ở các giai đoạn sau, bất kể nguồn dữ liệu đến từ đâu.

## Hạ tầng AI nền tảng

Duy trì lợi thế cạnh tranh với AI đòi hỏi khả năng đổi mới nhanh chóng mà không bị cản trở bởi hạ tầng. AWS giúp SportsCapital giảm thiểu nhu cầu tự xây dựng hạ tầng phức tạp, đồng thời cung cấp độ linh hoạt và độ trễ thấp cần thiết để đáp ứng các yêu cầu xử lý dữ liệu thời gian thực của công ty.

> "Với Amazon SageMaker, chúng tôi có thể sử dụng các container image tùy chỉnh của riêng mình để triển khai pipeline suy luận (inference pipeline) chuyên biệt. Các khả năng ghi log toàn diện cũng mang lại khả năng giám sát hiệu suất hệ thống quan trọng, giúp chúng tôi nhanh chóng xác định và khắc phục sự cố. Hơn nữa, tính năng provisioned concurrency trong Amazon SageMaker giúp giảm đáng kể độ trễ xử lý — điều cực kỳ quan trọng trong lĩnh vực mà từng giây đều có giá trị."
>
> — **Pravin Santhanam, CTO của SportsCapital**

### Sử dụng Amazon Bedrock

SportsCapital cũng đang sử dụng Amazon Bedrock để thử nghiệm các mô hình nền tảng (foundation models), bao gồm cả LLM Anthropic Claude Sonnet 3.5. Dịch vụ generative AI này cung cấp cho đội ngũ một API thống nhất, giúp việc tích hợp mô hình nền tảng trở nên liền mạch hơn. Các tính năng ghi log tích hợp sẵn cho phép nhóm SportsCapital duy trì trải nghiệm vận hành thống nhất trên toàn bộ hệ thống ML.

Claude Sonnet 3.5 cũng đóng vai trò quan trọng trong việc giúp pipeline tóm tắt các nội dung dài. SportsCapital đã xây dựng một bộ dữ liệu huấn luyện để định lượng hiệu suất của tác vụ này và sẽ tiếp tục thử nghiệm thêm các mô hình mới để cải thiện các chỉ số đánh giá.

> "Amazon Bedrock giúp chúng tôi xử lý hạ tầng triển khai phức tạp của LLMs một cách vô cùng hiệu quả. Nhờ đó, chúng tôi có thể tích hợp các khả năng xử lý ngôn ngữ tự nhiên nâng cao (NLP) vào quy trình thời gian thực mà không cần lo lắng về hạ tầng phía dưới. Điều này sẽ tiếp tục đóng vai trò then chốt khi nhu cầu của chúng tôi thay đổi, và chúng tôi rất háo hức trước tiềm năng tinh chỉnh (fine-tune) các LLM tùy chỉnh thông qua Amazon Bedrock khi pipeline tiếp tục phát triển."
>
> — **Pravin Santhanam, CTO của SportsCapital**

## Bước tiếp theo

Trong tương lai, SportsCapital dự định mở rộng quy mô xử lý cho cả nguồn tin ngắn và dài, bao phủ toàn bộ mùa thể thao trong năm.

> "Đội ngũ của chúng tôi tập trung vào việc mở rộng khả năng thu thập (ingestion) từ hàng nghìn nguồn suốt cả năm và xử lý tin tức nóng với độ chính xác và tốc độ mà ngành này chưa từng thấy trước đây."
>
> — **Aaron Riccio, CEO của SportsCapital**

---

Hãy cập nhật những bước tiến mới nhất của SportsCapital và liên hệ với đại diện của AWS để tìm hiểu cách mà các dịch vụ AI tạo sinh như Amazon SageMaker và Amazon Bedrock có thể nâng cao các dự án AI của bạn.
