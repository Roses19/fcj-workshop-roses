---
title : "Truy vấn dữ liệu và trực quan hóa"
date : 2026-01-01
weight : 5
chapter : false
pre : " <b> 5.5 </b> "
---

#### 1. Truy vấn dữ liệu với Amazon Athena

- Sau khi Glue Crawler đã tạo bảng trong Glue Data Catalog, dữ liệu trong S3 (Transformed Zone) có thể được truy vấn trực tiếp bằng Amazon Athena mà không cần di chuyển dữ liệu.

- Athena sử dụng metadata từ Glue Catalog để hiểu: Schema, Data type, Location của file trong S3

- Cấu hình Athena
  + Chọn Database: reddit_db
  + Chọn bảng: transformed
  + Chọn Query settings -> gắn kết quả truy vấn được lưu tại: s3://amzn-s3-reddit-airflow-project/athena-script/
![athena](/images/5-Workshop/5.5-Analytics/athena.png)
- Ví dụ truy vấn:
```
SELECT 
    author,
    COUNT(*) AS total_posts,
    AVG(score) AS avg_score,
    SUM(num_comments) AS total_comments
FROM "AwsDataCatalog"."reddit_db"."transformed"
GROUP BY author
ORDER BY total_posts DESC;
```
- Đạt kết quả như sau: 
![query](/images/5-Workshop/5.5-Analytics/result-query.png)
- Ở phần đầu phân tích, Athena được sử dụng để phân tích nhanh, kiểm tra dữ liệu và Validate chất lượng ETL

#### 2. Nạp dữ liệu từ S3 vào Amazon Redshift

- Mặc dù Athena phù hợp cho truy vấn ad-hoc, nhưng để BI DashboardDashboard, Join lớnlớn, Performance cao thì dữ liệu cần được đưa vào Amazon Redshift (Data Warehouse).
- Với các cấu hình đã được thiết lập sẵn ở phần chuẩn bị, bao gồm tạo namespace và workgroup, ta cần chọn query data - > Query in query Editor v2:
![query](/images/5-Workshop/5.5-Analytics/redshift-serverless.png)

- Sau đó connect tới workgroup đã tạo, sử dụng Federated user để không cần dùng password cho database: 
![connect wg](/images/5-Workshop/5.5-Analytics/connectwg.png)
- Tiếp đó load dữ liệu từ S3 sang Data Warehouse:
![load data](/images/5-Workshop/5.5-Analytics/loaddata.png)
- Chọn tiếp schema **public**, đặt tên bảng trong kho dữ liệu và chọn IAM role mặc định:
![schema](/images/5-Workshop/5.5-Analytics/schema.png)
- Kho dữ liệu sau khi được load thành công:
![dwh](/images/5-Workshop/5.5-Analytics/dwh.png)
#### 3. Định hướng trực quan hóa và phân tích nâng cao

- Mặc dù ở giai đoạn hiện tại khối lượng dữ liệu Reddit chưa đủ lớn để triển khai hiệu quả các hệ thống Business Intelligence (BI), kiến trúc nền tảng đã được thiết kế sẵn sàng cho việc mở rộng trong tương lai.

- Khi dữ liệu tiếp tục được thu thập theo thời gian (daily ingestion từ Airflow), kho dữ liệu Amazon Redshift sẽ dần hình thành một historical data warehouse với độ sâu theo thời gian. Tại thời điểm đó, các công cụ BI như Amazon QuickSight có thể được tích hợp trực tiếp vào Redshift để:

- Trực quan hóa xu hướng tăng trưởng bài viết theo subreddit

- Phân tích hành vi tương tác (score, comment, ESS_updated) theo thời gian

- Theo dõi sự thay đổi của chủ đề và mức độ ảnh hưởng

- Hỗ trợ ra quyết định dựa trên dữ liệu lịch sử và dữ liệu gần real-time

Như vậy, hệ thống hiện tại không chỉ phục vụ cho việc xử lý và truy vấn dữ liệu, mà còn đóng vai trò là một nền tảng phân tích dữ liệu doanh nghiệp hoàn chỉnh, sẵn sàng mở rộng sang lớp trực quan hóa và phân tích nâng cao khi dữ liệu đạt quy mô đủ lớn.