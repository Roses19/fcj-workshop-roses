---
title : "Nạp dữ liệu vào Data Lake và Quản lý Metadata"
date : 2026-01-01 
weight : 4 
chapter : false
pre : " <b> 5.4. </b> "
---

#### Tổng quan

- Sau khi dữ liệu được thu thập bởi Airflow, hệ thống nạp dữ liệu vào Data Lake trên Amazon S3 và sử dụng AWS Glue để tự động tạo metadata. 
    +  **Amazon S3** đóng vai trò lưu trữ trung tâm cho dữ liệu Reddit.
    +  **Glue Crawler** quét dữ liệu và xây dựng các bảng trong Glue Data Catalog.
- Nhờ đó, dữ liệu từ dạng file thô được chuyển thành các bảng có thể truy vấn bằng Athena và Redshift. 
- Quy trình này giúp tự động hóa việc nạp dữ liệu, quản lý schema và tạo nền tảng cho các bước phân tích tiếp theo.
![pipeline](/images/5-Workshop/5.4-Data-Catalog/architecture.png)



