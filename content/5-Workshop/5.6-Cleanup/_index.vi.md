---
title : "Dọn dẹp tài nguyên"
date : 2024-01-01
weight : 6
chapter : false
pre : " <b> 5.6. </b> "
---

#### Dọn dẹp tài nguyên

Do hệ thống Data Platform sử dụng nhiều dịch vụ compute (Glue, Redshift), nếu không kiểm soát, chi phí có thể tăng nhanh ngay cả khi không có người dùng.


#### 1.  Glue Crawler

Xóa lịch chạy tự động:  Chọn dịch vụ AWS Glue → Crawlers → reddit_crawler → Delete
![delete crawler](/images/5-Workshop/5.6-Cleanup/delete-crawler.png)


#### 2. Glue Job

Xóa job ETL: Glue → Jobs → reddit_glue_jobjob.py → Action → Delete job(s)
![delete job](/images/5-Workshop/5.6-Cleanup/delete-job.png)

#### 3. Glue Logs
Xóa các logs chạy Glue: CloudWatch → Log groups → /aws-glue/* → Delete group logs
![delete log](/images/5-Workshop/5.6-Cleanup/delete-log.png)

#### 4. Xóa các S3 bucket

+ Mở bảng điều khiển S3
+ Chọn bucket chúng ta đã tạo cho lab, bao gồm bucket lưu trữ tầng raw, transformed, và athena, glue. N
+ Nhấp chuột và xác nhận là empty. Nhấp Delete và xác nhận delete.

![delete s3](/images/5-Workshop/5.6-Cleanup/delete-s3.png)