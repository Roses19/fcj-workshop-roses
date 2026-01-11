---
title: "Worklog Tuần 3"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.3. </b> "
---
### Mục tiêu tuần 3:

* Xây dựng và quản trị hệ thống Data Lake và quy trình ETL trên AWS.
* Triển khai kho dữ liệu và phân tích hiệu năng cao với Redshift Serverless.
* Thiết kế bảng điều khiển BI tương tác chuyên nghiệp qua Amazon QuickSight.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc                                                                                                                                                                                   | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                            |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ----------------------------------------- |
| 2   | - Xây dựng Data Lake trên AWS: Tìm hiểu các dịch vụ AWS Glue, Athena, Quicksight. <br> - **Thực hành:** <br>&emsp;    + Tạo Role, policy phù hợp.             <br>&emsp; + Tạo S3 bucket, ETL jobs, crawler <br>&emsp; + Truy vấn SQL với Athena.                                                                          | 29/12/2025   | 29/12/2025      | <https://000035.awsstudygroup.com/vi/>          |
| 3   | - **Thiết lập và vận hành AWS Redshift:** <br>&emsp; + Khởi tạo Redshift Serverless (Namespace & Workgroup).<br>&emsp; + Cấu hình IAM Role cho phép truy cập dữ liệu từ S3. <br>&emsp;  + Tạo Table và thực hiện lệnh COPY để load dữ liệu từ S3. <br>&emsp;  + Truy vấn SQL phân tích và trực quan hóa bằng Query Editor v2.                      | 30/12/2025   | 30/12/2025      | <https://docs.aws.amazon.com/redshift/> |
| 4   | - Tìm hiểu Amazon QuickSight, các thành phần cốt lõi: SPICE, Dataset, Analysis và Dashboard <br> - **Thực hành:** <br>&emsp; + Chuẩn bị dữ liệu và thiết lập môi trường tại Region Singapore <br>&emsp; + Tạo các biểu đồ trực quan <br> &emsp; + Tối ưu giao diện và thiết lập tính tương tác <br> &emsp; + Xuất bản Dashboard và dọn dẹp tài nguyên | 31/12/2025   | 31/12/2025      | <https://000073.awsstudygroup.com/> |

### Kết quả đạt được tuần 3:

* Data Lake & ETL:
  * Thiết lập các IAM Role và Policy bảo mật cho dịch vụ dữ liệu.
  * Khởi tạo S3 bucket và cấu hình Glue Crawler để tự động hóa việc nhận diện schema và cập nhật Data Catalog.
  * Thực hiện truy vấn dữ liệu trực tiếp trên nền tảng S3 thông qua Amazon Athena.

* AWS Redshift Serverless:
  * Khởi tạo thành công môi trường Namespace/Workgroup và nạp dữ liệu từ S3 vào bảng bằng lệnh COPY.
  * Sử dụng Query Editor v2 để thực hiện các phân tích SQL chuyên sâu trên kho dữ liệu.

* Amazon QuickSight:
  * Tối ưu hóa hiệu suất xử lý dữ liệu nhờ công cụ tính toán trong bộ nhớ SPICE.
  * Triển khai đa dạng các loại hình trực quan hóa: Line chart, KPI & Insights, Pie chart và Pivot Table.
  * Thiết lập tính tương tác linh hoạt cho báo cáo thông qua bộ lọc (Filters) và các hành động điều hướng (Navigation Actions).
  * Xuất bản Dashboard thành công và thực hiện quy trình dọn dẹp tài nguyên để tối ưu hóa chi phí vận hành.
