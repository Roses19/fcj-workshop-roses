---
title: "Worklog Tuần 2"
date: 2026-01-01
weight: 1
chapter: false
pre: " <b> 1.2. </b> "
---

### Mục tiêu tuần 2:

* Nắm vững cách xử lý, làm sạch và tối ưu dữ liệu bằng AWS Glue và PySpark.
* Hiểu và áp dụng Monitoring, Cost Management và Security cho hệ thống Data Platform.
* Tìm hiểu các dịch vụ AWS Data & Analytics gồm S3, Glue, Athena và QuickSight.
* Xây dựng và quản trị hệ thống Data Lake và quy trình ETL trên AWS.
### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc                                                                                                                                                                                   | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                            |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ----------------------------------------- |
| 2   | - Viết ETL Transformation:<br>&emsp; + Xây dựng Job Glue (Pyspark) để làm sạch dữ liệu. <br>&emsp; + Chuyển đổi định dạng để tối ưu hiệu suất.                                                                                             | 22/12/2025   | 22/12/2025      |
| 3   | - **Tìm hiểu Monitoring & Cost Management** <br>&emsp; + CloudWatch logs & metrics <br>&emsp; + AWS Budgets <br>&emsp; + Cost Explorer                         | 23/12/2025   | 23/12/2025      | <https://000008.awsstudygroup.com/> |
| 4   | - **Tìm hiểu IAM & Security cho Data Platform** <br>&emsp; + IAM User vs Role vs Policy <br>&emsp; + IAM cho Glue, Athena, Redshift <br> &emsp;+ S3 Bucket Policy vs IAM Policy <br> &emsp; + KMS & mã hóa dữ liệu | 24/12/2025   | 24/12/2025      | <https://000033.awsstudygroup.com/> |
| 5   | - **Tìm hiểu Networking cho AWS Data Services** <br>&emsp; + VPC, Subnet, Internet Gateway <br>&emsp; + VPC Endpoint cho S3 & Glue <br>&emsp; + Public vs Private access <br>&emsp; + Data services giao tiếp với nhau thế nào <br>                  | 25/12/2025   | 26/12/2025      | <https://000003.awsstudygroup.com/vi/> |
| 6   | - Xây dựng Data Lake trên AWS: Tìm hiểu các dịch vụ AWS Glue, Athena, Quicksight. <br> - **Thực hành:** <br>&emsp;    + Tạo Role, policy phù hợp.             <br>&emsp; + Tạo S3 bucket, ETL jobs, crawler <br>&emsp; + Truy vấn SQL với Athena.                                                                          | 26/12/2025    | 26/12/2025       | <https://000035.awsstudygroup.com/vi>|

### Kết quả đạt được tuần 2:

* Đã xây dựng ETL Job bằng AWS Glue (PySpark) để:

  * Làm sạch dữ liệu Reddit

  * Chuẩn hóa schema cho phân tích

  * Chuyển dữ liệu từ Raw S3 → Processed S3
* Đã thiết lập Monitoring & Cost Management:
  * Bật CloudWatch Logs cho Glue Jobs

  * Thiết lập AWS Budgets để theo dõi chi phí

  * Sử dụng Cost Explorer để phân tích usage
* Đã cấu hình Security cho Data Platform:

  * IAM Roles cho Glue, Athena, S3

  * S3 Bucket Policy theo nguyên tắc least privilege

  * Bật KMS encryption cho dữ liệu

* Đã thiết kế và hiểu rõ Networking cho AWS Data Services:

  * VPC, Subnet, Internet Gateway

  * VPC Endpoint cho S3 & Glue

  * Phân biệt Public vs Private access

* Đã xây dựng AWS Data Lake:

  * Tạo S3 structure: raw/, processed/

  * Chạy Glue Crawler để tạo Data Catalog