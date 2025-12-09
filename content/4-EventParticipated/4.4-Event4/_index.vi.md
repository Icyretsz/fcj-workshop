---
title: "Generative AI với Amazon Bedrock"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: " <b> 4.4. </b> "
---

# Bài thu hoạch

### Mục tiêu Sự kiện

- Cung cấp một lộ trình chiến lược về Generative AI, từ các nguyên lý nền tảng của Large Language Models đến nghệ thuật nâng cao của Prompt Engineering.
- Khai mở sức mạnh của Retrieval Augmented Generation (RAG) để xây dựng AI được neo (grounded) vào dữ liệu riêng, cập nhật theo thời gian thực.
- Giới thiệu bước tiến hóa tiếp theo của AI: các Autonomous, goal‑oriented Agents có khả năng reasoning và hành động.
- Ra mắt **Amazon Bedrock AgentCore**, một nền tảng toàn diện được thiết kế để thu hẹp khoảng cách giữa các AI prototype và hệ thống production vừa bảo mật vừa có khả năng mở rộng.

### Diễn giả

- **Lâm Tuấn Kiệt** – Sr. DevOps Engineer, FPT Software
- **Danh Hoàng Hiếu Nghị** – AI Engineer, Renova Cloud
- **Đinh Lê Hoàng Anh** – Cloud Engineer Trainee, First Cloud AI Journey

### Những Điểm Nhấn Chính

#### Cái Nhìn Thoáng Qua về Tương Lai

*"The journey of AI is an evolution in autonomy."* – “Hành trình của AI là sự tiến hóa trong mức độ tự chủ.” Chủ đề trung tâm này định hình toàn bộ workshop, vẽ nên lộ trình từ các AI assistant đơn giản chỉ làm theo luật đến các hệ thống Agentic AI hoàn toàn tự chủ có thể reasoning, lên kế hoạch và thực thi các workflow phức tạp với rất ít sự can thiệp của con người.

#### Phần 1: Nắm Vững Nền Tảng với Amazon Bedrock

- **The Foundation Model Revolution:** Phiên chia sẻ bắt đầu bằng việc so sánh các mô hình Machine Learning truyền thống với **Foundation Models (FMs)** – các mô hình tổng quát, đa dụng. Amazon Bedrock được giới thiệu như cổng vào an toàn để truy cập danh mục đa dạng các Foundation Model tiên tiến.
- **The Art of the Prompt:** Prompt Engineering được phân tích như kỹ năng giao tiếp hiệu quả với AI. Phiên chia sẻ bao quát một dải kỹ thuật từ **Zero‑Shot**, **Few‑Shot** đến **Chain‑of‑Thought** prompting nâng cao.
- **Retrieval Augmented Generation (RAG): Grounding AI in Reality:** RAG được trình bày như công nghệ then chốt cho Enterprise AI. Bằng cách sử dụng **Embedding Models** như Amazon Titan để chuyển đổi dữ liệu riêng thành vector representation, RAG cho phép AI truy xuất thông tin liên quan, cập nhật *trước khi* sinh câu trả lời, từ đó giảm hallucination và tinh chỉnh phản hồi cho từng bối cảnh business cụ thể.

#### Phần 2: Tiến Hóa Thành Autonomous Agents

- **Bridging the Prototype‑to‑Production Chasm:** Các diễn giả chỉ ra những rào cản quan trọng ngăn các AI prototype đầy hứa hẹn trở thành công cụ business thực sự có giá trị: **Performance, Scalability, Security và Governance.**
- **The Rise of Agentic AI:** Workshop sau đó chuyển trọng tâm sang tương lai: AI Agents. Đây không chỉ là chatbots; chúng là những hệ thống được thiết kế để đạt được mục tiêu cụ thể bằng cách tự động hóa cả workflow, tận dụng các open‑source framework như **LangChain, LlamaIndex và Crew.AI**.

#### Phần 3: Giới Thiệu Amazon Bedrock AgentCore

- **The Production‑Ready Platform for Agents:** Điểm nhấn của sự kiện là phần giới thiệu **Amazon Bedrock AgentCore**, một nền tảng toàn diện được thiết kế để giải quyết “khoảng cách lên production”. AgentCore cung cấp đầy đủ các dịch vụ nền tảng cần thiết để vận hành agents một cách bảo mật và có khả năng scale:
    - **Runtime & Identity:** Thực thi và quản lý hoạt động của agent một cách an toàn.
    - **Memory:** Cung cấp cho agents khả năng ghi nhớ ngữ cảnh để tương tác mạch lạc.
    - **Tools:** Trang bị cho agents các công cụ như **Browser Tool** (truy cập dữ liệu web theo thời gian thực) và **Code Interpreter** (thực hiện tính toán, chạy code).
    - **Gateway:** Cổng vào bảo mật để agents tương tác với các company API.
    - **Observability:** Cung cấp cái nhìn sâu về hiệu năng, chi phí và hành vi của agent trong vận hành.

### Từ Lý Thuyết đến Thực Tiễn: Live Demo và Code Blueprint

Workshop không dừng lại ở lý thuyết mà gắn mọi khái niệm với phần thực hành, demo code cụ thể, tham chiếu đến hai GitHub repository chính làm tài nguyên cực kỳ giá trị cho người tham dự.

#### The Foundational Cookbook: `aws-samples/amazon-bedrock-samples`

Official AWS repository này được giới thiệu như một “cookbook” không thể thiếu để làm chủ các thành phần cốt lõi của Bedrock. Các demo cho thấy bộ sưu tập Jupyter Notebook và code sample này đóng vai trò như một “flight simulator” cho việc phát triển AI, cho phép kỹ sư:

- **Experiment with Prompting:** Thử nghiệm hàng loạt ví dụ về Zero‑Shot, Few‑Shot và Chain‑of‑Thought prompting trên nhiều model khác nhau như Claude và Llama.
- **Build a RAG Pipeline from Scratch:** Các hướng dẫn từng bước minh họa cách sử dụng Amazon Titan để tạo embeddings, lưu trữ trong vector database và xây dựng một RAG pipeline hoàn chỉnh cho bài toán Q&A trên tài liệu riêng.
- **Master the APIs:** Cung cấp các code snippet rõ ràng, có thể tái sử dụng để tương tác với hầu hết mọi tính năng của dịch vụ Bedrock, từ text generation đến image creation.

#### The Capstone Project: `ihatesea69/Bedrock_AgentCore`

Repository này đóng vai trò như bài demo “capstone project”, minh họa cách lắp ghép các thành phần riêng lẻ từ “cookbook” thành một AI Agent phức tạp, hoạt động thực tế dựa trên nền tảng AgentCore mới. Phần live demo đi qua repository này để trình diễn cách:

- **Define an Agent:** Định nghĩa identity, instructions và goals của agent trong code.
- **Equip the Agent with Tools:** Cấp cho agent khả năng thực hiện các hành động vượt ra ngoài việc sinh text, như gọi external APIs hoặc chạy code.
- **Orchestrate a Mission:** Kết nối tất cả trong một ví dụ live, nơi agent nhận một yêu cầu phức tạp, tự chủ chọn đúng tools, truy xuất thông tin cần thiết và thực thi một kế hoạch nhiều bước để đạt được mục tiêu. Điều này khiến khái niệm trừu tượng về “Agentic AI” trở nên cụ thể và khả thi.

### Những Điều Rút Ra Chính (Key Takeaways)

#### Blueprint Khởi Động Nhanh: Ứng Dụng vào Công Việc

1. **Xây dựng một RAG‑powered Expert:** Clone repository `amazon-bedrock-samples` và tùy biến các RAG notebook để kết nối với internal documentation của bạn, tạo nên một internal search engine mạnh, có ngữ cảnh.
2. **Prototype Agent Đầu Tiên của Bạn:** Sử dụng repository `Bedrock_AgentCore` như template. Định nghĩa một agent đơn giản tự động hóa một business process nhiều bước đặc thù của team, ví dụ: tạo daily sales report bằng cách gọi internal API rồi tóm tắt kết quả.
3. **Refine Prompts với Cookbook:** Lấy một AI workflow sẵn có và sử dụng các ví dụ đa dạng trong repository `amazon-bedrock-samples` để nâng cấp prompts từ chỉ dẫn đơn giản lên Few‑Shot tinh tế, cải thiện đáng kể chất lượng output.
4. **Khám Phá AgentCore Tools:** Tìm hiểu cách các công cụ trong Bedrock AgentCore, như Browser Tool hoặc Code Interpreter, có thể giải quyết các bài toán business cần dữ liệu thời gian thực hoặc tính toán động.

### Trải Nghiệm Sự Kiện

Workshop là một hành trình hấp dẫn xuyên suốt bức tranh toàn diện của AI hiện đại. Việc đính kèm các GitHub repository chi tiết đã biến buổi học từ mang tính lý thuyết thành một “actionable masterclass”. Người tham dự không chỉ mang về kiến thức mà còn có cả code blueprint cụ thể để bắt đầu xây dựng thế hệ ứng dụng AI‑driven tiếp theo ngay lập tức. Việc giới thiệu Amazon Bedrock AgentCore, đi kèm demo thực tế, mang lại một roadmap rõ ràng, thuyết phục để vượt qua giai đoạn AI prototype, hướng tới các Enterprise Agent bảo mật, có khả năng mở rộng và thực sự tự chủ.

#### Một số hình ảnh sự kiện

{{< image-grid >}}
<img src="/images/event4.1.jpeg" alt="Hình ảnh sự kiện 1">
<img src="/images/event4.2.jpeg" alt="Hình ảnh sự kiện 2">
<img src="/images/event4.3.jpeg" alt="Hình ảnh sự kiện 3">
<img src="/images/event4.4.jpeg" alt="Hình ảnh sự kiện 4">
{{< /image-grid >}}