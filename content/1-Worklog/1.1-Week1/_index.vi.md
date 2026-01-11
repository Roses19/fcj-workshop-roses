---
title: "Worklog Tuần 1"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---


### Mục tiêu tuần 1:

* Thành thạo dịch vụ lưu trữ Amazon S3, các cơ chế bảo mật và tối ưu hóa phân phối qua CloudFront. 
* Thiết lập hạ tầng cốt lõi và phân quyền bảo mật cho dự án Reddit Data Pipeline (Airflow, S3, IAM).
* Xây dựng và vận hành thành công luồng ETL tự động để lấy dữ liệu từ Reddit về Data Lake. 

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc                                                                                                                                                                                   | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                            |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ----------------------------------------- |
| 2   | - Tìm hiểu Amazon S3 Essentials: Bucket, Object, Metadata và Key, các lớp lưu trữ <br>   - **Thực hành Static Website Hosting:** <br>&emsp; + Cấu hình Static Website Hosting, thiết lập Index & Error document.   <br>&emsp;+ Quản lý quyền truy cập: Block Public Access, IAM Policy và ACLs.                                                                                      | 15/12/2025   | 15/12/2025      |<https://000057.awsstudygroup.com/> |
| 3   | - **Quản lý dữ liệu & Tối ưu hóa:** <br>&emsp; + Kích hoạt Versioning để bảo vệ dữ liệu khỏi việc xóa nhầm. <br>&emsp; + Thiết lập Cross-Region Replication (CRR) để dự phòng thảm họa. <br>&emsp; + Tăng tốc website với CloudFront (CDN) và cấu hình Origin Access Control (OAC). | 16/12/2025   | 16/12/2025      | <https://000057.awsstudygroup.com/vi/7-cloudfront/> |
| 4   | - Khởi tạo hạ tầng & Bảo mật cho dự án: <br>&emsp; + Thiết lập Airflow.<br>&emsp; + Tạo S3 Buckets (phân vùng Raw, Transformed, Athena). <br>&emsp; + Thiết lập IAM Roles/Policies cho Airflow, S3 và Athena. <br>&emsp;+ Đăng ký Reddit App để lấy Client ID/Secret.                                                                                           | 17/12/2025   | 18/12/2025      |
| 5   | - Tìm hiểu dịch vụ AWS Glue, Athena. <br> - **Thực hành:** <br>&emsp;    + Tạo Role, policy phù hợp.             <br>&emsp; + Tạo S3 bucket, ETL jobs, crawler.                                                                          | 18/12/2025   | 18/12/2025      | <https://000035.awsstudygroup.com/vi/>          |
| 6   | - Data Discovery (Glue & Athena): <br>&emsp;+ Chạy Glue Crawler để quét schema dữ liệu thô trên S3. <br>&emsp; + Dùng Athena thực hiện truy vấn SQL kiểm tra dữ liệu vừa đẩy lên. <br> - Phát triển ETL Pipeline: <br>&emsp; + Viết Airflow DAG để crawl dữ liệu từ Reddit. <br>&emsp; + Thực hiện đẩy dữ liệu thô lên S3.| 19/12/2025   | 19/12/2025      | 

### Kết quả đạt được tuần 1:

* Quản trị lưu trữ và Phân phối nội dung:
  * Nắm vững kiến trúc Amazon S3 và triển khai thành công Static Website Hosting với đầy đủ cấu hình Index/Error document. 
  * Thiết lập các lớp bảo mật nghiêm ngặt thông qua Block Public Access và Bucket Policy; bảo vệ dữ liệu bằng Versioning và sao chép xuyên vùng (CRR). 
  * Tối ưu hóa hiệu suất website bằng cách tích hợp CloudFront CDN và sử dụng Origin Access Control (OAC) để bảo mật nguồn dữ liệu. 

* Thiết lập hạ tầng dự án Reddit:
  * Khởi tạo thành công môi trường điều phối Apache Airflow và đăng ký định danh Reddit API (Client ID/Secret).
  * Xây dựng cấu trúc lưu trữ phân tầng trên S3 (Raw, Transformed, Athena) bám sát mô hình kiến trúc Data Lake.
  * Cấu hình hệ thống IAM Roles/Policies chuyên biệt cho Airflow và Athena, tuân thủ nguyên tắc đặc quyền tối thiểu (Least Privilege).

* Data Discovery và Luồng ETL:
  * Vận hành AWS Glue Crawler để tự động quét schema và cập nhật Metadata cho dữ liệu thô trên S3.
  * Thực hiện thành công các truy vấn SQL ad-hoc trên Amazon Athena để kiểm tra tính toàn vẹn của dữ liệu vừa đẩy lên.
  * Phát triển hoàn chỉnh Airflow DAG đầu tiên, tự động hóa quy trình crawl dữ liệu từ Reddit và nạp vào vùng Raw của Data Lake.
