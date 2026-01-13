---
title: "Worklog Tuần 4"
date: 2026-01-01
weight: 1
chapter: false
pre: " <b> 1.4. </b> "
---

### Mục tiêu tuần 4:

* Mở rộng kiến thức về **kiến trúc Data Platform trên AWS**.
* Tìm hiểu cách các hệ thống dữ liệu lớn được **tự động hóa, giám sát và vận hành**.
* Thực hiện các **lab và bài thực hành** để làm quen với các dịch vụ nâng cao trên AWS.
* Đánh giá kiến trúc và chi phí của hệ thống Reddit Data Platform hiện tại..

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc                                                                                                                                                                                   | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                            |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ----------------------------------------- |
| 2,3 | **Tìm hiểu kiến trúc tự động hóa dữ liệu**  <br>&emsp; + Event-driven vs Scheduled pipelines  <br>&emsp; + AWS Lambda & EventBridge  <br>&emsp; + So sánh với Airflow | 05/01/2026 | 06/01/2026 |https://docs.aws.amazon.com/eventbridge/ |
| 4 | **Thực hành: Monitoring & Observability**  <br>&emsp; + CloudWatch logs & metrics  <br>&emsp; + Dashboard & Alert  <br>&emsp; + SLA cho pipeline | 07/01/2026 | 07/01/2026 | https://000008.awsstudygroup.com/vi/ |
| 5 | **Rà soát kiến trúc hệ thống**  <br>&emsp; + Kiểm tra luồng dữ liệu Reddit → S3 → Glue → Athena → Redshift → QuickSight  <br>&emsp; + Xác định bottleneck và rủi ro | 08/01/2026 | 08/01/2026 | |
| 6 | **Phân tích chi phí & hiệu năng**  <br>&emsp; + Glue, S3, Athena, Redshift, QuickSight  <br>&emsp; + Đề xuất phương án tối ưu | 09/01/2026  | 09/01/2026  |  |
### Kết quả đạt được tuần 4:

- Hiểu rõ các **mô hình tự động hóa pipeline** (event-driven và scheduled) và cách áp dụng trong hệ thống dữ liệu.  
- Thực hành **monitoring, logging và alerting** cho Data Platform bằng **CloudWatch**.  
- Đánh giá được **kiến trúc tổng thể** của Reddit Data Platform và các điểm nghẽn tiềm năng.  
- Phân tích **chi phí và hiệu năng** của các dịch vụ AWS đang sử dụng (Glue, S3, Athena, Redshift, QuickSight).  
- Xác định được các **hướng tối ưu và mở rộng** cho giai đoạn tiếp theo.