---
title : "Thu thập dữ liệu"
date : 2026-01-01 
weight : 3
chapter : false
pre : " <b> 5.3. </b> "
---

#### Sử dụng Airflow

Trong giai đoạn thu thập dữ liệu, hệ thống sử dụng Apache Airflow làm nền tảng điều phối để tự động hóa quá trình lấy dữ liệu từ Reddit API và đưa vào hệ thống Data Lake.

Toàn bộ môi trường Airflow được triển khai trong Docker, bao gồm các thành phần chính: Webserver, Scheduler, Workers, PostgreSQL và Celery.
![overview](/images/5-Workshop/5.3-Ingestion/ingestion.png)

#### Nội dung

- [Cấu hình Docker](5.3.1-Docker-Configuration/)
- [Quy trình thu thập dữ liệu](5.3.2-ingestion-pipeline/)