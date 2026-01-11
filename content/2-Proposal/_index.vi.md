---
title: "Bản đề xuất"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---


# Automating the ETL Pipeline for Reddit Data on AWS  
## Tự động hóa pipeline ETL cho dữ liệu Reddit trên AWS  

### 1. Tóm tắt điều hành  
Dự án được thiết kế nhằm xây dựng một hệ thống thu thập và xử lý dữ liệu mạng xã hội tự động, tối ưu hóa giữa tài nguyên có sẵn và hạ tầng đám mây. Hệ thống áp dụng mô hình kiến trúc lai (Hybrid): sử dụng Apache Airflow chạy trên máy chủ cục bộ vật lý (local server) để điều phối và các dịch vụ AWS Serverless để xử lý và lưu trữ dữ liệu quy mô lớn.

Quy trình này giải quyết bài toán tự động hóa hoàn toàn vòng đời dữ liệu Reddit: từ khâu trích xuất (Ingestion), chuẩn hóa schema tự động bằng Glue Crawler, đến biến đổi dữ liệu Spark ETL sang định dạng Parquet giúp tiết kiệm chi phí truy vấn đáng kể.3 Hệ thống tận dụng sức mạnh của Amazon Redshift Serverless với cấu hình tối thiểu (4 RPU) để thực hiện phân tích hiệu năng cao và trình diễn qua Amazon QuickSight Standard, giúp giảm thiểu tối đa chi phí vận hành hàng tháng mà vẫn đảm bảo khả năng xử lý dữ liệu linh hoạt. 

### 2. Tuyên bố vấn đề  
***Vấn đề hiện tại*** 

Quy trình khai thác dữ liệu từ các nền tảng mạng xã hội hiện nay đối mặt với nhiều thách thức :

+ **Hạ tầng rời rạc**: Việc cào dữ liệu (scraping) và phân tích thường không được kết nối, dẫn đến dữ liệu bị cũ hoặc mất mát khi thực hiện thủ công.
+ **Lãng phí tài nguyên tính toán**: Duy trì một kho dữ liệu (24/7) cho các truy vấn không thường xuyên gây tốn kém ngân sách lớn, không phù hợp với các dự án vừa và nhỏ.
+ **Chi phí API và Rate Limit**: Reddit áp dụng các hạn chế nghiêm ngặt về tốc độ truy cập (100 QPM), đòi hỏi hệ thống điều phối phải có cơ chế quản lý thông minh để tránh bị khóa tài khoản. 

***Giải pháp***  
Xây dựng một đường ống dữ liệu hiện đại hóa :
+ **Local Orchestration**: Tận dụng phần cứng có sẵn để chạy Airflow trong Docker, giảm chi phí thuê máy chủ cloud cho khâu điều phối.
+ **Serverless Ingestion & ETL**: Sử dụng Amazon S3 và AWS Glue để xử lý dữ liệu phi cấu trúc khối lượng lớn một cách linh hoạt mà không cần quản lý máy chủ.
+ **Optimized Analytics**: Triển khai Redshift Serverless cấu hình $4$ RPU để thực hiện SQL analytics chỉ khi có yêu cầu, đảm bảo hiệu năng cao với chi phí cực thấp.

***Lợi ích và hoàn vốn đầu tư (ROI)***  
Giải pháp Hybrid giúp cắt giảm khoảng 30-40% chi phí cố định so với việc chạy toàn bộ trên cloud. Chi phí vận hành thực tế chỉ phát sinh khi dữ liệu được xử lý trên AWS (khoảng $25 - $30/tháng). Thời gian từ lúc có tin tức nóng hổi trên Reddit đến khi xuất hiện trên báo cáo của QuickSight được rút ngắn xuống tính bằng giờ, thay vì hàng ngày.17  

### 3. Kiến trúc giải pháp  
Hệ thống áp dụng kiến trúc hướng sự kiện (Event-driven) trên nền tảng Serverless, kết hợp với bộ điều phối Container chạy tại máy chủ vật lý cục bộ.  

![Social Media Big Data Pipeline Architecture](/images/2-Proposal/architecture.png)

*Dịch vụ AWS sử dụng*  
- *AWS S3*: Đóng vai trò là Data Lake, lưu trữ dữ liệu thô (Raw) và dữ liệu đã qua xử lý (Transformed).   
- *AWS Glue Crawler*: Tự động quét các tập tin trên S3 để nhận diện cấu trúc (schema) và cập nhật metadata. 
- *AWS Glue Data Catalog*: Kho lưu trữ metadata trung tâm, cho phép các dịch vụ phân tích truy cập dữ liệu S3 một cách thống nhất.  
- *AWS Glue ETL*: Dịch vụ xử lý dữ liệu Spark serverless, dùng để chuyển đổi định dạng JSON sang Parquet và làm sạch dữ liệu.  
- *Amazon Athena*: Dịch vụ truy vấn SQL serverless trực tiếp trên S3 phục vụ các phân tích tức thời.
- *Amazon Redshift Serverless*: Kho dữ liệu (Data Warehouse) với khả năng tự động co giãn, thực hiện các truy vấn phân tích hiệu năng cao.  
- *Amazon QuickSight Standard*: Nền tảng BI serverless để xây dựng các Dashboard trực quan hóa. 

*Thiết kế thành phần*  
 
Luồng dữ liệu được thực hiện qua 3 giai đoạn chính:
- *Trích xuất*: Airflow (Local Docker) gọi Reddit API và lưu dữ liệu JSON thô vào Amazon S3.    
- *Xử lý*: AWS Glue Crawler cập nhật schema , sau đó Glue Spark ETL chuẩn hóa và chuyển đổi dữ liệu sang định dạng Parquet lưu tại S3.    
- *Phân tích*: Dữ liệu từ S3 được nạp vào Redshift Serverless và hiển thị trực quan qua QuickSight Dashboard.
### 4. Triển khai kỹ thuật  
*Các giai đoạn triển khai*  
 
1. *Thiết lập Local Environment*: Cấu hình Docker Compose cho Airflow. Thiết lập kết nối an toàn từ máy chủ cục bộ lên AWS thông qua IAM User/Access Key.
2. *Phát triển Reddit Extractor*: Viết script Python sử dụng thư viện PRAW. Triển khai logic "Exponential Backoff" để tuân thủ giới hạn API của Reddit.ư
3. *Xây dựng Glue Pipeline*: Thiết lập Crawler quét S3 và viết job Spark ETL để chuyển đổi, chuẩn hóa dữ liệu Reddit sang Parquet phân vùng theo ngày.
4. *Thiết lập Kho dữ liệu*: Khởi tạo Redshift Serverless với namespace và workgroup, cấu hình mức 4 RPU cơ bản.
5. *Thiết kế Dashboard*: Kết nối QuickSight Standard tới Redshift để tạo các biểu đồ theo dõi từ khóa và xu hướng thảo luận.

*Yêu cầu kỹ thuật*  
- *Cấu hình Reddit API*: Sử dụng tài khoản Developer loại "script" để thu thập dữ liệu đảm bảo tuân thủ giới hạn 100 QPM của Reddit.
- *Hạ tầng điều phối (Compute)*: Máy chủ vật lý cục bộ chạy Docker/Airflow cần cấu hình tối thiểu 8 GB RAM để vận hành ổn định các worker Celery.
- *Lưu trữ (Storage)*: Sử dụng Amazon S3 với định dạng Parquet, giúp giảm tới 90\% dung lượng lưu trữ và chi phí quét dữ liệu so với JSON thô.
- *Amazon Redshift Serverless*: Thiết lập mức dung lượng tối thiểu 4 RPU để tối ưu chi phí cho các tác vụ phân tích vừa và nhỏ.
- *Amazon QuickSight Standard*: Sử dụng phiên bản Standard để tạo các báo cáo BI cơ bản với chi phí cố định thấp nhất.
### 5. Lộ trình & Mốc triển khai  
- *Trước thực tập (Tháng 0)*: 1 tháng lên kế hoạch và học tập các dịch vụ cơ bản AWS, kiến trúc ETL.  
- *Thực tập*:  
    - Tuần 1-2: Học AWS và nâng cấp phần cứng.  
    - Tuần 3: Thiết kế và điều chỉnh kiến trúc.  
    - Tuần 4-5: Triển khai, kiểm thử, đưa vào sử dụng.  
- *Sau triển khai*: Nghiên cứu thêm trong vòng 6 tháng để nâng cấp hệ thống.  

### 6. Ước tính ngân sách  
Có thể xem chi phí trên [AWS Pricing Calculator](https://calculator.aws/#/estimate?id=13f55200468bb2d49c9498816417a4f1c7a393a4)  

*Chi phí hạ tầng*  
- AWS Lambda: 0,00 USD/tháng (1.000 request, 512 MB lưu trữ).  
- S3 Standard: 0,15 USD/tháng (6 GB, 2.100 request, 1 GB quét).  
- Truyền dữ liệu: 0,02 USD/tháng (1 GB vào, 1 GB ra).  
- AWS Amplify: 0,35 USD/tháng (256 MB, request 500 ms).  
- Amazon API Gateway: 0,01 USD/tháng (2.000 request).  
- AWS Glue ETL Jobs: 0,02 USD/tháng (2 DPU).  
- AWS Glue Crawlers: 0,07 USD/tháng (1 crawler).  
- MQTT (IoT Core): 0,08 USD/tháng (5 thiết bị, 45.000 tin nhắn).  

*Tổng*: 0,7 USD/tháng, 8,40 USD/12 tháng  
- *Phần cứng*: 265 USD một lần (Raspberry Pi 5 và cảm biến).  

### 7. Đánh giá rủi ro  
*Tính sẵn sàng cục bộ:* Máy chủ local mất kết nối sẽ làm gián đoạn việc thu thập.
- Giảm thiểu: Cấu hình cơ chế "Catch-up" trong Airflow để tự động chạy lại task bị lỡ.

*API Rate Limit:* Vượt ngưỡng 100 QPM có thể bị Reddit chặn. 
- Giảm thiểu: Giới hạn 5-10 subreddit và sử dụng hàng chờ Celery để điều phối lưu lượng request.

*Schema Drift:*  Cấu trúc dữ liệu Reddit thay đổi bất ngờ làm hỏng script ETL.
- Giảm thiểu: Sử dụng Glue Crawler để tự phát hiện thay đổi schema tự động.

### 8. Kết quả kỳ vọng  
- Hệ thống thu thập dữ liệu Reddit tự động 100%, không cần can thiệp thủ công.
- Tối ưu hóa chi phí: Tận dụng tài nguyên sẵn có để giữ mức phí AWS dưới $30/tháng.
- Khả năng mở rộng: Dễ dàng thêm subreddit chỉ bằng cách cập nhật cấu hình Airflow local.
- Dashboard trực quan cung cấp cái nhìn chi tiết về xu hướng thảo luận mạng xã hội theo thời gian thực.