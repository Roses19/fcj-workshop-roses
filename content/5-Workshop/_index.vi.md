---
title: "Workshop"
date: 2024-01-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---




# Tự động hóa pipeline ETL cho dữ liệu Reddit trên AWS

#### Tổng quan

Dự án này xây dựng một hệ thống tự động hóa phân tích dữ liệu Reddit trên AWS với các thành phần chính sau:

+ **Thu thập và lưu trữ dữ liệu**: Dữ liệu được lấy tự động từ Reddit API bằng Apache Airflow và lưu trữ trên Amazon S3 theo mô hình Data Lake (Raw và Transformed).

+ **Xử lý và quản lý dữ liệu**: AWS Glue ETL Jobs được sử dụng để làm sạch và chuyển đổi dữ liệu, trong khi AWS Glue Data Catalog và Crawler quản lý schema và metadata.

+ **Phân tích và truy vấn**: Amazon Athena cho phép truy vấn trực tiếp trên Data Lake, còn Amazon Redshift đóng vai trò Data Warehouse cho các truy vấn phân tích.

+ **Trực quan hóa**: Amazon QuickSight được dùng để xây dựng dashboard và hiển thị insight từ dữ liệu.

Hệ thống giúp dữ liệu Reddit được cập nhật và phân tích hoàn toàn tự động, hỗ trợ theo dõi xu hướng và hành vi người dùng trên nền tảng AWS.
#### Nội dung

1. [Tổng quan về workshop](5.1-Workshop-overview/)
2. [Chuẩn bị](5.2-Prerequiste/)
3. [Thu thập dữ liệu](5.3-Ingestion/)
4. [Data Catalog](5.4-Data-Catalog/)
5. [Phân tích dữ liệu và báo cáo](5.5-Analytics/)
6. [Dọn dẹp tài nguyên](5.6-Cleanup/)