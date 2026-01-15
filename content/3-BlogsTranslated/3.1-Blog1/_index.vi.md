---
title: "Xây dựng và quản lý nền tảng dữ liệu hiện đại bằng dbt và AWS Glue thông qua dbt-glue – adapter dbt mới"
date: 2026-01-05
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---


Bài blog gốc: [AWS Big Data Blog – dbt & AWS Glue](https://aws.amazon.com/vi/blogs/big-data/build-and-manage-your-modern-data-stack-using-dbt-and-aws-glue-through-dbt-glue-the-new-trusted-dbt-adapter/)

## Giới thiệu

**dbt** là một công cụ mã nguồn mở, lấy SQL làm trung tâm, cho phép bạn viết các phép biến đổi dữ liệu có thể tái sử dụng và mở rộng bằng Python và SQL. dbt tập trung vào lớp biến đổi (transform) trong quy trình ELT/ETL trên các data warehouse và database thông qua các adapter tương ứng để đạt được khả năng trích xuất và tải dữ liệu. 

Việc này giúp data engineer, data scientist và analytics engineer định nghĩa logic nghiệp vụ bằng các câu lệnh SQL SELECT và loại bỏ nhu cầu viết thủ công các câu lệnh DML, DDL. dbt cũng giúp deploy code analytics nhanh và collaborative theo best practices của phát triển phần mềm như tính mô-đun, khả năng di chuyển, CI/CD cùng với tài liệu hóa.

**dbt** được sử dụng chủ yếu với các data warehouse (như Amazon Redshift) nơi mà khách hàng muốn tách biệt logic biến đổi dữ liệu khỏi storage và engine. 

Vì nhu cầu mở rộng sang cloud data lake, AWS đã giới thiệu adapter dbt-glue vào năm 2022 — một adapter AWS Glue cho dbt mã nguồn mở, đã được kiểm chứng (battle-tested), giúp data engineer dùng dbt cho cả data lake lẫn data warehouse, trả phí chỉ cho compute họ cần.
**Adapter dbt-glue** mở rộng quyền truy cập cho người dùng dbt tới data lake, và cho phép chạy workloads biến đổi dữ liệu trên cloud với serverless Spark của AWS Glue dễ dàng. AWS tiếp tục đầu tư vào dbt-glue để hỗ trợ thêm nhiều yêu cầu khác.

Ngày nay, adapter dbt-glue đã trở thành một adapter đáng tin cậy dựa trên sự hợp tác chiến lược với **dbt Labs**. Adapter đáng tin cậy là adapter không do dbt Labs duy trì nhưng được dbt Labs khuyến nghị dùng ở môi trường production.

Các tính năng chính của adapter dbt-glue:
- Chạy SQL dưới dạng Spark SQL trên phiên tương tác của AWS Glue.
- Quản lý định nghĩa bảng trên **Amazon SageMaker Lakehouse Catalog** với dữ liệu lưu trên Amazon S3
- Hỗ trợ các định dạng bảng mở như Apache Hudi, Delta Lake và Apache Iceberg
- Tương thích với **AWS Lake Formation permissions** để kiểm soát truy cập chi tiết

Ngoài ra, dbt-glue tối ưu hóa việc sử dụng tài nguyên bằng nhiều kỹ thuật mở rộng trên các phiên tương tác của AWS Glue.

## Trường hợp sử dụng phổ biến
**1. Đội analytics trong một tập đoàn**

Một đội analytics trung tâm cần theo dõi hiệu quả hoạt động. Họ ingest log ứng dụng vào các bảng Parquet ở tầng raw trong data lake trên S3. Họ cũng trích xuất dữ liệu có cấu trúc từ các hệ thống vận hành

Ví dụ: Schema tổ chức và chi phí của các thành phần khác nhau — lưu trong tầng raw dưới dạng Iceberg để giữ nguyên schema ban đầu, phục vụ cho nhiều truy cập dữ liệu.

Đội sử dụng dbt-glue để xây dựng mô hình gold đã được transform tối ưu cho BI — kết hợp technical logs với billing data và tổ chức số liệu theo từng business unit. Iceberg hỗ trợ mô hình kiểu warehouse giúp BI phân tích perform tốt. Kết hợp Iceberg và dbt-glue cho phép xây dựng data model sẵn sàng để sử dụng.

**2. Tạo data product tuân thủ GDPR**

Một đội analytics tại công ty châu Âu có data lake trên S3 và cần tạo data product mới để làm phong phú dữ liệu y tế — yêu cầu tuân thủ GDPR như quyền bị lãng quên và xóa dữ liệu. Họ dùng Iceberg để đáp ứng những yêu cầu này, và dbt để model dữ liệu trên data lake vì dbt-glue hỗ trợ Iceberg và đơn giản hoá việc sử dụng định dạng lưu trữ này.

##  Cách hoạt động của dbt và dbt-glue
- Các tính năng chính của dbt:
    + **Project** – cấu trúc cấp cao cho các staging, models, permissions và adapters. Có thể quản lý bằng Git.
    + **SQL** – dbt dùng SQL (với templating Jinja) để biểu diễn logic biến đổi một cách modular. Thay vì phải sao chép/dán mã SQL ở nhiều nơi, các kỹ sư dữ liệu có thể định nghĩa các phép biến đổi theo mô-đun và gọi chúng từ các vị trí khác trong dự án. Việc có một quy trình xử lý dữ liệu theo mô-đun giúp các kỹ sư dữ liệu cộng tác hiệu quả hơn trên cùng một dự án.
    + **Models** – mỗi dbt model là một SELECT statement lưu trong file .sql. Các kỹ sư dữ liệu định nghĩa các mô hình dbt cho cách biểu diễn dữ liệu của họ.
    + **Materializations** – cách định nghĩa persist model trong đích lưu (table, view, incremental,ephemeral, and materialized view).
    + **Data lineage** – theo dõi dòng dữ liệu và phân tích tác động khi thay đổi.

- Luồng dữ liệu tổng quan: 

    1. Data engineer ingest dữ liệu từ nguồn vào các bảng raw và định nghĩa chúng.

    2. Viết các dbt model bằng SQL template.

    3. dbt adapter chuyển các dbt model thành SQL tương thích với Data Warehouse.

    4. Hệ thống thực thi các SQL đó để tạo intermediate hoặc final tables/views/materialized views.

Sơ đồ sau mô tả kiến trúc.
![architecture](/images/3-Blogstranslated/image.png)
- Cách dbt-glue hoạt động với AWS Glue

    1. dbt-glue chuyển dbt models sang SQL tương thích với Spark SQL.

    2. AWS Glue interactive sessions thực thi các SQL này để tạo bảng intermediate/final hoặc views.

    3. dbt-glue hỗ trợ các định dạng: ```csv```, ```parquet```, ```hudi```, ```delta```, ```iceberg```.

    4. Với adapter này, materialization thường dùng là table hoặc incremental. Tùy chiến lược incremental, merge strategy yêu cầu ```hudi```, ```delta```, hoặc ```iceberg```. Với ```append``` hoặc ```insert_overwrite```, bạn có thể dùng bất kỳ định dạng kể trên.

Ví dụ minh họa
![diagram illustrates](/images/3-Blogstranslated/image-1.png)

## Trường hợp ví dụ

Trong bài này, AWS lấy dữ liệu từ dataset [**New York City Taxi Records**](https://registry.opendata.aws/nyc-tlc-trip-records-pds/) trong Registry of [Open Data on AWS (RODA)](https://registry.opendata.aws/). Dữ liệu raw Parquet lưu các thông tin chuyến đi taxi. 

- Mục tiêu là tạo 3 bảng metrics từ bảng raw:
    + **silver_avg_metrics** – metrics cơ bản cho năm 2016
    + **gold_passengers_metrics** – metrics theo passenger
    + **gold_cost_metrics** – metrics theo cost

Kết quả cuối cùng là các bảng gold định dạng Iceberg để query tương tác qua **Amazon Athena**.

## Các bước chuẩn bị

+ Chuẩn bị **IAM Role** với quyền chạy **Glue interactive session** và **dbt-glue**
+ Tạo Glue database + table cho dataset Taxi
+ Tạo S3 bucket để lưu dữ liệu output
+ Cấu hình Athena để thăm dò dữ liệu
+ Dùng CloudFormation để deploy toàn bộ hạ tầng nhanh chóng

Với những bước chuẩn bị này, chúng ta mô phỏng tình huống các Data Engineers đã nhập dữ liệu từ các nguồn dữ liệu vào các bảng thô và đã định nghĩa các bảng cho các bảng thô đó. Để dễ sử dụng, chúng tôi đã chuẩn bị một mẫu CloudFormation. 

Mẫu này triển khai tất cả cơ sở hạ tầng cần thiết. Để tạo các tài nguyên này, hãy chọn Khởi chạy Stack trong Vùng ```us-east-1```.
## Các bước làm chi tiết: 
**1. Cài dbt và dbt CLI** với dòng lệnh sau: ```$ pip3 install --no-cache-dir dbt-core ```
**2.  Cài đặt adapter dbt-glue** với ```$ pip3 install --no-cache-dir dbt-glue```
**3. Khởi tạo dbt project (dbt init)** với ```$ dbt init``` và nhập tên ```dbt_glue_demo```. Chọn database ```glue```. Bây giờ dự án trống đã được tạo: 
```
$ cd dbt_glue_demo 
$ tree .
.
├── README.md
├── analyses
├── dbt_project.yml
├── macros
├── models
│   └── example
│       ├── my_first_dbt_model.sql
│       ├── my_second_dbt_model.sql
│       └── schema.yml
├── seeds
├── snapshots
└── tests
```
**4. Tạo source table**: Trong ```models/``` tạo file ```source_tables.yml``` với nội dung:
```
version: 2

sources:
  - name: data_source
    schema: nyctaxi

    tables:
      - name: records
```
Source table được định nghĩa tương ứng với AWS Glue table ```nyctaxi.records```, đây là bảng mà chúng ta đã tạo ở CloudFormation stack.

**5. Tạo models**

Ở bước này, chúng ta tạo một mô hình dbt biểu diễn các giá trị trung bình cho thời gian chuyến đi, số lượng hành khách, quãng đường chuyến đi và tổng chi phí. Hoàn thành các bước sau:
- Tạo thư mục **silver** trong thư mục **models**, sau đó tạo file **silver_avg_metrics.sql** với nội dung sau: 
```
WITH source_avg as ( 
    SELECT avg((CAST(dropoff_datetime as LONG) - CAST(pickup_datetime as LONG))/60) as avg_duration 
    , avg(passenger_count) as avg_passenger_count 
    , avg(trip_distance) as avg_trip_distance 
    , avg(total_amount) as avg_total_amount
    , year
    , month 
    , type
    FROM {{ source('data_source', 'records') }} 
    WHERE year = "2016"
    AND dropoff_datetime is not null 
    GROUP BY year, month, type
) 
SELECT *
FROM source_avg
```
- Tạo file **schema.yml** với nội dung sau:
```
version: 2

models:
  - name: silver_avg_metrics
    description: This table has basic metrics based on NYC Taxi Open Data for the year 2016

    columns:
      - name: avg_duration
        description: The average duration of a NYC Taxi trip

      - name: avg_passenger_count
        description: The average number of passenger per NYC Taxi trip

      - name: avg_trip_distance
        description: The average NYC Taxi trip distance

      - name: avg_total_amount
        description: The avarage amount of a NYC Taxi trip

      - name: year
        description: The year of the NYC Taxi trip

      - name: month
        description: The month of the NYC Taxi trip 

      - name: type
        description: The type of the NYC Taxi 
```
- Tạo thư mục **gold** trong models, bên trong có file **gold_cost_metrics.sql** với nội dung sau: 
```
{{ config(
    materialized='incremental',
    incremental_strategy='merge',
    unique_key=["year", "month", "type"],
    file_format='iceberg',
    iceberg_expire_snapshots='False',
    table_properties={'format-version': '2'}
) }}
SELECT (avg_total_amount/avg_trip_distance) as avg_cost_per_distance
, (avg_total_amount/avg_duration) as avg_cost_per_minute
, year
, month 
, type 
FROM {{ ref('silver_avg_metrics') }}
```
- Tạo file **models/gold/gold_passengers_metrics.sql** với nội dung: 
```
{{ config(
    materialized='incremental',
    incremental_strategy='merge',
    unique_key=["year", "month", "type"],
    file_format='iceberg',
    iceberg_expire_snapshots='False',
    table_properties={'format-version': '2'}
) }}
SELECT (avg_total_amount/avg_passenger_count) as avg_cost_per_passenger
, (avg_duration/avg_passenger_count) as avg_duration_per_passenger
, (avg_trip_distance/avg_passenger_count) as avg_trip_distance_per_passenger
, year
, month 
, type 
FROM {{ ref('silver_avg_metrics') }}
```
- Tạo **models/gold/schema.yml** với nội dung: 
```
version: 2

models:
  - name: gold_cost_metrics
    description: This table has metrics per cost based on NYC Taxi Open Data

    columns:
      - name: avg_cost_per_distance
        description: The average cost per distance of a NYC Taxi trip

      - name: avg_cost_per_minute
        description: The average cost per minute of a NYC Taxi trip

      - name: year
        description: The year of the NYC Taxi trip

      - name: month
        description: The month of the NYC Taxi trip

      - name: type
        description: The type of the NYC Taxi

  - name: gold_passengers_metrics
    description: This table has metrics per passenger based on NYC Taxi Open Data

    columns:
      - name: avg_cost_per_passenger
        description: The average cost per passenger for a NYC Taxi trip

      - name: avg_duration_per_passenger
        description: The average number of passenger per NYC Taxi trip

      - name: avg_trip_distance_per_passenger
        description: The average NYC Taxi trip distance

      - name: year
        description: The year of the NYC Taxi trip

      - name: month
        description: The month of the NYC Taxi trip 

      - name: type
        description: The type of the NYC Taxi
```
- Cuối cùng, xóa **models/example/** vì đây là folder ví dụ khi chạy ```dbt init```.

**6. Cấu hình dbt project**

- File **dbt_project.yml** bao gồm nội dung:
```
models:
  dbt_glue_demo:
    # Config indicated by + and applies to all files under models/example/
    example:
      +materialized: view
```
- Vì ta muốn hiện thực hóa các mô hình dưới lớp silver dưới dạng Parquet. ta cần thay thế nội dung trong file **dbt_project.yml** như sau:
```
models:
  dbt_glue_demo:
    silver:
      +materialized: table

```
**7. Cấu hình dbt profile**


**dbt profile** là một cấu hình quy định cách kết nối với một cơ sở dữ liệu cụ thể và được định nghĩa trong tệp **profiles.yml** trong dự án dbt. 

Thực hiện các bước sau để cấu hình dbt profile: 

1. Tạo thư mục ```profiles```. 
    
2. Tạo tệp ```profiles/profiles.yml``` với nội dung sau:
    ```
    dbt_glue_demo:
    target: dev
    outputs:
        dev:
        type: glue
        query-comment: demo-nyctaxi
        role_arn: "{{ env_var('DBT_ROLE_ARN') }}"
        region: us-east-1
        workers: 5
        worker_type: G.1X
        schema: "dbt_glue_demo_nyc_metrics"
        database: "dbt_glue_demo_nyc_metrics"
        session_provisioning_timeout_in_seconds: 120
        location: "{{ env_var('DBT_S3_LOCATION') }}"
      ```
3. Tạo thư mục ```profiles/iceberg/```
4. Tạo file ```profiles/iceberg/profiles.yml``` với nội dung:

    ```
    dbt_glue_demo:
    target: dev
    outputs:
        dev:
            type: glue
            query-comment: demo-nyctaxi
            role_arn: "{{ env_var('DBT_ROLE_ARN') }}"
            region: us-east-1
            workers: 5
            worker_type: G.1X
            schema: "dbt_glue_demo_nyc_metrics"
            database: "dbt_glue_demo_nyc_metrics"
            session_provisioning_timeout_in_seconds: 120
            location: "{{ env_var('DBT_S3_LOCATION') }}"
            datalake_formats: "iceberg"
            conf: spark.sql.catalog.glue_catalog=org.apache.iceberg.spark.SparkCatalog --conf spark.sql catalog.glue_catalog.warehouse={{ env_var('DBT_S3_LOCATION') }}warehouse/ --conf spark.sql.catalog.glue_catalog.catalog-impl=org.apache.iceberg.aws.glue.GlueCatalog --conf spark.sql.catalog.glue_catalog.io-impl=org.apache.iceberg.aws.s3.S3FileIO --conf spark.sql.extensions=org.apache.iceberg.spark.extensions.IcebergSparkSessionExtensions
      ```
Hai dòng cuối cùng được thêm vào để thiết lập cấu hình Iceberg trên các phiên tương tác AWS Glue.

**8. Chạy dbt project**

Bây giờ là lúc chạy dự án dbt. Hoàn thành các bước sau:

1. Để chạy  project dbt, bạn phải ở trong thư mục dự án: ``$ cd dbt_glue_demo```
2. Dự án yêu cầu bạn đặt các biến môi trường để chạy trên tài khoản AWS:
```
$ export DBT_ROLE_ARN="arn:aws:iam::$(aws sts get-caller-identity --query "Account" --output text):role/GlueInteractiveSessionRole"
$ export DBT_S3_LOCATION="s3://aws-dbt-glue-datalake-$(aws sts get-caller-identity --query "Account" --output text)-us-east-1"
```
3. Đảm bảo cấu hình được thiết lập chính xác từ dòng lệnh:
```
$ dbt debug --profiles-dir profiles
...
05:34:22 Connection test: [OK connection ok]
05:34:22 All checks passed!
```
Nếu bạn gặp bất kỳ lỗi nào, hãy kiểm tra xem bạn đã cung cấp đúng IAM role ARN và vị trí S3 ở Bước 2 chưa. 
4. Chạy các mô hình với đoạn mã sau:
```
$ dbt run -m silver --profiles-dir profiles
$ dbt run -m gold --profiles-dir profiles/iceberg/
```
Giờ đây, các bảng đã được tạo thành công trong SageMaker Lakehouse Catalog và dữ liệu được hiện thực hóa ở Amazon S3 location.

Bạn có thể xác minh các bảng đó bằng cách mở bảng điều khiển AWS Glue, chọn **Databases** trong ngăn điều hướng và mở ```dbt_glue_demo_nyc_metrics```.
![check db](/images/3-Blogstranslated/image-2.png)

## Truy vấn các bảng được cụ thể hóa thông qua Athena

Hãy truy vấn bảng mục tiêu bằng cách sử dụng Athena để xác minh các bảng được cụ thể hóa. Hoàn thành các bước sau:

1. Trên bảng điều khiển Athena, chuyển nhóm làm việc sang ```athena-dbt-glue-aws-blog```.

2. Nếu hộp thoại cài đặt ```athena-dbt-glue-aws-blog``` của nhóm làm việc xuất hiện, hãy chọn Acknowledge.

3. Sử dụng truy vấn sau để khám phá các số liệu được tạo bởi dự án dbt:
```
SELECT cm.avg_cost_per_minute
    , cm.avg_cost_per_distance
    , pm.avg_cost_per_passenger
    , cm.year
    , cm.month
    , cm.type
FROM "dbt_glue_demo_nyc_metrics"."gold_passengers_metrics" pm
LEFT JOIN "dbt_glue_demo_nyc_metrics"."gold_cost_metrics" cm
    ON cm.type = pm.type
    AND cm.year = pm.year
    AND cm.month = pm.month
WHERE cm.type = 'yellow'
    AND cm.year = '2016'
    AND cm.month = '6'
```
Ảnh chụp màn hình sau đây hiển thị kết quả của truy vấn này.
![result-query](/images/3-Blogstranslated/image-3.png)

## Xem lại tài liệu dbt

Hãy hoàn tất các bước sau để xem lại tài liệu của bạn:

1. Tạo các tài liệu sau cho dự án:
```
$ dbt docs generate --profiles-dir profiles/iceberg
11:41:51  Running with dbt=1.7.1
11:41:51  Registered adapter: glue=1.7.1
11:41:51  Unable to do partial parsing because profile has changed
11:41:52  Found 3 models, 1 source, 0 exposures, 0 metrics, 478 macros, 0 groups, 0 semantic models
11:41:52  
11:41:53  Concurrency: 1 threads (target='dev')
11:41:53  
11:41:53  Building catalog
11:43:32  Catalog written to /Users/username/Documents/workspace/dbt_glue_demo/target/catalog.json
```

2. Chạy lệnh sau để mở tài liệu trên trình duyệt của bạn:
```$ dbt docs serve --profiles-dir profiles/iceberg```
![open doc](/images/3-Blogstranslated/image-4.png)

3. Trong ngăn điều hướng, chọn ```gold_cost_metrics``` trong ```dbt_glue_demo/models/gold```.
Bạn có thể xem chế độ xem chi tiết của mô hình ```gold_cost_metrics```, như minh họa trong ảnh chụp màn hình sau.
![model](/images/3-Blogstranslated/image-5.png)

4. Để xem biểu đồ dòng, hãy chọn biểu tượng hình tròn ở dưới cùng bên phải.

![circle icon](/images/3-Blogstranslated/image-6.png)

## Dọn dẹp

Để làm sạch môi trường của bạn, hãy hoàn thành các bước sau:

1. Xóa cơ sở dữ liệu được tạo bởi dbt:
```$ aws glue delete-database —name dbt_glue_demo_nyc_metrics```

2. Xóa tất cả dữ liệu đã tạo:
```
$ aws s3 rm s3://aws-dbt-glue-datalake-$(aws sts get-caller-identity —query "Account" —output text)-us-east-1/ —recursive
$ aws s3 rm s3://aws-athena-dbt-glue-query-results-$(aws sts get-caller-identity —query "Account" —output text)-us-east-1/ —recursive
```

3. Xóa ngăn xếp CloudFormation:
```$ aws cloudformation delete-stack —stack-name dbt-demo```
## Kết luận

Bài viết này đã trình bày cách bộ chuyển đổi dbt-glue hỗ trợ khối lượng công việc của bạn và cách bạn có thể xây dựng một ngăn xếp dữ liệu hiện đại bằng dbt và AWS Glue sử dụng bộ chuyển đổi dbt-glue. Bạn đã học được các thao tác từ đầu đến cuối và luồng dữ liệu để các kỹ sư dữ liệu xây dựng và quản lý ngăn xếp dữ liệu bằng dbt và bộ chuyển đổi dbt-glue.
