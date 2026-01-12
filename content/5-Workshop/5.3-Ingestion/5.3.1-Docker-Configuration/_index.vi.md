---
title : "Cấu hình Docker "
date : 2026-01-01 
weight : 1
chapter : false
pre : " <b> 5.3.1 </b> "
---


1 . Tạo Airflow Image tùy chỉnh
+ Xây dựng Dockerfile:
```
FROM apache/airflow:2.8.4-python3.10

USER root

RUN apt-get update && apt-get install -y gcc python3-dev

USER airflow

COPY requirements.txt /opt/airflow/requirements.txt

RUN pip install --no-cache-dir -r /opt/airflow/requirements.txt

```
+ Một Docker image riêng được xây dựng từ Dockerfile: ```apache/airflow:2.8.4-python3.10```

+ Image này đảm bảo mọi container Airflow có cùng môi trường runtime.


2 . Định nghĩa cấu hình dùng chung
Một block cấu hình chung (x-airflow-common) được tạo ra để:
+ Dùng chung image.
+ Dùng chung biến môi trường.
+ Mount các thư mục config, dags, data, etls, logs, pipelines, plugins, tests,  utils.

```
x-airflow-common: &airflow-common
  build:
    context: .
    dockerfile: Dockerfile
  image: custom-airflow:2.8.4-python3.10
  env_file:
    - airflow.env
  volumes:
    - ./config:/opt/airflow/config
    - ./dags:/opt/airflow/dags
    - ./data:/opt/airflow/data
    - ./etls:/opt/airflow/etls
    - ./logs:/opt/airflow/logs
    - ./pipelines:/opt/airflow/pipelines
    - ./plugins:/opt/airflow/plugins
    - ./tests:/opt/airflow/tests
    - ./utils:/opt/airflow/utils
    - ./requirements.txt:/opt/airflow/requirements.txt
  depends_on:
    - postgres
    - redis

```
3 . Triển khai các dịch vụ:

+ PostgreSQL để lưu DAG, trạng thái task, lịch sử chạy: 

```
postgres:
    image: postgres:12
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DB: airflow_reddit
    ports:
      - "5432:5432"
```
+ Thiết lập Message Broker (Redis) để nhiều Worker xử lý song song và Retry Task khi bị lỗi

```
redis:
    image: redis:latest
    ports:
      - "6379:6379"
```
+ Khởi tạo hệ thống (airflow-init): Tạo schema database, tài khoản admin

```
airflow-init:
    <<: *airflow-common
    command: >
      bash -c " pip install -r /opt/airflow/requirements.txt &&  airflow db migrate && airflow db upgrade && airflow db check && airflow users create --username admin --firstname admin --lastname admin --role Admin --email nguyenthihong112000@gmail.com --password admin"
    restart: "no"
```
+ Sau khi hoàn tất, hệ thống Airflow sẵn sàng hoạt động.

7. Triển khai các thành phần Airflow

+ Webserver cung cấp giao diện để quản lý DAG, theo dõi trạng thái và xem log.
```
airflow-webserver:
    <<: *airflow-common
    command: airflow webserver
    ports:
      - "8080:8080"
```

+ Scheduler chịu trách nhiệm lập lịch và gửi task vào hàng đợi để thực thi.
```
airflow-scheduler:
    <<: *airflow-common
    command: airflow scheduler
```
+ Worker (Celery) thực hiện các task như gọi Reddit API và ghi dữ liệu vào S3.
```
  airflow-worker:
    <<: *airflow-common
    container_name: airflow-worker
    command: airflow celery worker
    ports:
      - "8793:8793"
```
+ Triggerer xử lý các tác vụ bất đồng bộ và sensor.
```
  airflow-triggerer:
    <<: *airflow-common
    command: airflow triggerer
```