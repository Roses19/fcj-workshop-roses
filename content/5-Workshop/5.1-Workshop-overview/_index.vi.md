---
title : "Giới thiệu"
date : 2026-01-01 
weight : 1
chapter : false
pre : " <b> 5.1. </b> "
---

#### Giới thiệu về Data Platform

Data Platform là hệ thống cho phép thu thập, lưu trữ, xử lý và phân tích dữ liệu ở quy mô lớn một cách tự động và có khả năng mở rộng. Trong kiến trúc này, dữ liệu được tổ chức theo các tầng (raw, transformed, analytics) nhằm đảm bảo dữ liệu gốc được bảo toàn, đồng thời tối ưu cho việc phân tích và báo cáo.

#### Tổng quan về workshop
Trong workshop này, dữ liệu Reddit được phân tích trên AWS dựa trên kiến trúc Data Lake kết hợp Data Warehouse.

Hệ thống bao gồm:

+ **"Apache Airflow"** chạy trong Docker để thu thập dữ liệu từ Reddit API và điều phối pipeline.
+ **"Amazon S3"** để lưu trữ dữ liệu ở hai lớp: Raw và Transformed.
+ **"AWS Glue & Data Catalog"** để tự động phát hiện schema và xử lý dữ liệu.
+ **"Amazon Athena"** để truy vấn dữ liệu trực tiếp trên S3.
+ **"Amazon Redshift"** để lưu trữ dữ liệu phân tích.
+ **"Amazon QuickSight"** để xây dựng dashboard và trực quan hóa dữ liệu.

![overview](/images/5-Workshop/5.1-Workshop-overview/architecture.png)
