---
title: "Worklog Tuần 3"
date: 2026-01-01
weight: 1
chapter: false
pre: " <b> 1.3. </b> "
---
### Mục tiêu tuần 3:


* Triển khai kho dữ liệu và phân tích hiệu năng cao với Redshift Serverless.
* Thiết kế bảng điều khiển BI tương tác chuyên nghiệp qua Amazon QuickSight.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc                                                                                                                                                                                   | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                            |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ----------------------------------------- |
| 2   | **Thiết lập & làm quen Amazon Redshift** <br>&emsp;    + Tổng quan Data Warehouse & Redshift Serverless             <br>&emsp; + Tạo Namespace & Workgroup <br>&emsp; + Cấu hình IAM Role truy cập S3  | 29/12/2025   | 30/12/2025      | <https://docs.aws.amazon.com/redshift/>          |
| 3   | - **Nạp & phân tích dữ liệu trong Redshift** <br>&emsp; + Tạo bảng (Schema, Tables) <br>&emsp; + Sử dụng lệnh COPY load dữ liệu từ S3 <br>&emsp;  + Tạo Table và thực hiện lệnh COPY để load dữ liệu từ S3. <br>&emsp;  + Viết SQL phân tích bằng Query Editor v2                     | 30/12/2025   | 30/12/2025      | <https://docs.aws.amazon.com/redshift/> |
| 4   | - Tìm hiểu Amazon QuickSight, các thành phần cốt lõi: SPICE, Dataset, Analysis và Dashboard <br> - **Thực hành:** <br>&emsp; + Chuẩn bị dữ liệu và thiết lập môi trường tại Region Singapore <br>&emsp; + Tạo các biểu đồ trực quan <br> &emsp; + Tối ưu giao diện và thiết lập tính tương tác <br> &emsp; + Xuất bản Dashboard và dọn dẹp tài nguyên | 31/12/2025   | 31/12/2025      | <https://000073.awsstudygroup.com/> |

### Kết quả đạt được tuần 3:


* AWS Redshift Serverless:
  * Khởi tạo thành công môi trường Namespace/Workgroup và nạp dữ liệu từ S3 vào bảng bằng lệnh COPY.
  * Sử dụng Query Editor v2 để thực hiện các phân tích SQL chuyên sâu trên kho dữ liệu.

* Amazon QuickSight:
  * Tối ưu hóa hiệu suất xử lý dữ liệu nhờ công cụ tính toán trong bộ nhớ SPICE.
  * Triển khai đa dạng các loại hình trực quan hóa: Line chart, KPI & Insights, Pie chart và Pivot Table.
  * Thiết lập tính tương tác linh hoạt cho báo cáo thông qua bộ lọc (Filters) và các hành động điều hướng (Navigation Actions).
  * Xuất bản Dashboard thành công và thực hiện quy trình dọn dẹp tài nguyên để tối ưu hóa chi phí vận hành.
