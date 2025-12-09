---
title: "Data Science trên AWS"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: " <b> 4.2. </b> "
---



# Bài thu hoạch

### Mục tiêu Sự kiện

- Cung cấp cái nhìn tổng quan toàn diện về việc xây dựng hệ thống Data Science hiện đại trên AWS.
- Giới thiệu end-to-end Data Science pipeline, từ data processing đến model deployment.
- Cung cấp hands-on với các dịch vụ chủ chốt như AWS Glue và Amazon SageMaker.
- Thảo luận các yếu tố thực tiễn như cost, performance, và lợi ích của cloud vs on-premise.

### Diễn giả

- **Văn Hoàng Kha** – Cloud Solutions Architect, AWS Community Builder
- **Bạch Doãn Vương** – Cloud Develops Engineer, AWS Community Builder

### Nội dung Nổi bật

#### End-to-End Data Science Pipeline trên AWS

Workshop mô tả toàn bộ hành trình data science trên cloud, sử dụng các dịch vụ cốt lõi:

- **Amazon S3**: Lưu trữ dữ liệu scalable.
- **AWS Glue**: Serverless data integration, ETL (Extract, Transform, Load), và data cleaning.
- **Amazon SageMaker**: Để build, train và deploy machine learning models at scale.

#### Trình diễn Thực hành

- **Demo 1: Data Processing với AWS Glue**: Trình diễn cách process và clean một real-world IMDb dataset, nhấn mạnh tầm quan trọng của data quality đối với model accuracy.
- **Demo 2: Model Training với SageMaker**: Minh họa quy trình train và deploy một Sentiment Analysis model, giúp các khái niệm ML deployment trở nên cụ thể.
- **Tích hợp Custom Models**: Trình bày cách leverage frameworks như TensorFlow và PyTorch trong SageMaker, sử dụng sample project từ một GitHub repository.

#### Mở rộng Năng lực AI/ML với Managed Services

Tổng quan về các pre-built AI services của AWS giúp tăng tốc phát triển:

- **Amazon Transcribe**: Speech-to-text conversion.
- **Amazon Comprehend**: Natural Language Processing cho sentiment analysis và topic extraction.
- **Amazon Rekognition**: Phân tích hình ảnh và video.
- **Amazon Personalize**: Xây dựng personalized recommendation systems.

### Những điểm nhấn chính

#### Tư duy Data-First

- **Business-first approach**: Luôn bắt đầu từ business context của dữ liệu, được nhấn mạnh qua nhu cầu feature engineering.
- **Data quality là tối quan trọng**: Độ chính xác của bất kỳ ML model nào phụ thuộc trực tiếp vào chất lượng input data.
- **Data như một tài sản**: Data collection, governance và security là các trụ cột nền tảng của một data-driven organization.

#### Kiến trúc Kỹ thuật

- **Modular Pipeline**: Kiến trúc tiêu chuẩn gồm pipeline từ S3 (storage) → AWS Glue (ETL) → Amazon SageMaker (ML), cho phép separation of concerns rõ ràng.
- **Tính linh hoạt**: AWS hỗ trợ cả low-code như SageMaker Canvas và code-intensive custom model training với TensorFlow/PyTorch.
- **Lợi ích serverless**: Dùng các services như AWS Glue giúp loại bỏ quản trị hạ tầng, cho phép teams tập trung vào data và models.

#### Chiến lược

- **Phased approach**: Bắt đầu với data collection và cleaning trước khi chuyển sang model training phức tạp.
- **Cloud vs On-premise**: Cloud mang lại lợi thế về scalability, pay-for-what-you-use cost models, và khả năng truy cập tài nguyên compute mạnh mẽ mà không cần đầu tư ban đầu.
- **Đo lường ROI**: Tận dụng lợi ích cloud để giảm development time và infrastructure overhead, rút ngắn time-to-market cho AI-powered features.

### Ứng dụng vào Công việc

- **Tự động hóa ETL**: Dùng AWS Glue để tạo các job data cleaning và preparation tự động cho analytics và ML.
- **Áp dụng SageMaker**: Pilot Amazon SageMaker cho training và deploying ML models để streamline MLOps lifecycle.
- **Triển khai Sentiment Analysis**: Ứng dụng các khái niệm từ demo để phân tích phản hồi khách hàng từ reviews hoặc support tickets.
- **Khai thác Pre-built AI**: Tích hợp các services như Amazon Rekognition cho content moderation hoặc Amazon Transcribe cho call center analytics.
- **Củng cố kiến thức**: Xây dựng một project nhỏ dựa trên hướng dẫn của workshop để củng cố các khái niệm đã học.

### Trải nghiệm Sự kiện

Tham dự workshop "Data Science on AWS" mang lại một hành trình hands-on giá trị về machine learning trên cloud. Một số trải nghiệm chính:

#### Học hỏi từ các diễn giả giàu kinh nghiệm
- Các diễn giả, đều là AWS Community Builders, chia sẻ practical insights và best practices từ kinh nghiệm thực tế.

#### Trải nghiệm kỹ thuật hands-on
- Các live demo về processing data với AWS Glue và training model với SageMaker giúp chuyển hóa lý thuyết thành thực tiễn rất hiệu quả.

#### Tận dụng công cụ hiện đại
- Khám phá hệ sinh thái AWS toàn diện cho data science, hiểu cách các dịch vụ kết hợp để tạo thành một cohesive pipeline.
- Tìm hiểu cả fully managed AI services và các tùy biến mạnh mẽ trong SageMaker.

#### Bài học rút ra
- Chiến lược data preparation vững chắc là điều không thể thiếu để thành công trong machine learning.
- AWS giúp hạ thấp đáng kể rào cản xây dựng và triển khai các hệ thống data science tiên tiến.
- Nền tảng cloud hiện đại mang lại sự linh hoạt lựa chọn giữa low-code để tăng tốc và custom code cho các yêu cầu phức tạp, đặc thù.

#### Một số hình ảnh sự kiện
<div style="display: flex; gap: 10px;">
  <img src="/images/event2pic1.jpg" alt="Event Photo 1" style="width: 50%;">
  <img src="/images/event2pic2.jpg" alt="Event Photo 2" style="width: 50%;">
</div>
<div style="text-align: center;">
  <img src="/images/event2pic3.jpeg" alt="Event Photo 3" style="width: 50%;">
</div>

> Tổng thể, workshop không chỉ mang lại kiến thức kỹ thuật mà còn trải nghiệm thực hành trong việc xây dựng end-to-end data science pipelines trên AWS, nhấn mạnh tầm quan trọng của data quality và sức mạnh của cloud-native ML services.
