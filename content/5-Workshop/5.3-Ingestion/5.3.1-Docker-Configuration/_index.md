---
title : "Docker Configuration"
date : 2026-01-01 
weight : 1
chapter : false
pre : " <b> 5.3.1 </b> "
---


1. Building a Custom Airflow Image
+ A custom Airflow Docker image is built using the following Dockerfile::
```
FROM apache/airflow:2.8.4-python3.10

USER root

RUN apt-get update && apt-get install -y gcc python3-dev

USER airflow

COPY requirements.txt /opt/airflow/requirements.txt

RUN pip install --no-cache-dir -r /opt/airflow/requirements.txt

```
+ A dedicated Docker image is created based on: ```apache/airflow:2.8.4-python3.10```

+ This image ensures that all Airflow containers run in a consistent runtime environment with the required Python dependencies.


2 . Shared Configuration Definition
A shared configuration block (x-airflow-common) is defined to:
+ Use the same Airflow image
+ Share environment variables
+ Mount configuration, DAGs, logs, and pipeline directories

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
3. Deploying Core Services

+ PostgreSQL (Metadata Database): Stores DAG definitions, task states, and execution history.

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
+ Redis (Message Broker): Enables multiple Celery workers to process tasks in parallel and retry failed tasks.

```
redis:
    image: redis:latest
    ports:
      - "6379:6379"
```
+ Airflow Initialization (airflow-init): The airflow-init service initializes the metadata database and creates the admin account.

```
airflow-init:
    <<: *airflow-common
    command: >
      bash -c " pip install -r /opt/airflow/requirements.txt &&  airflow db migrate && airflow db upgrade && airflow db check && airflow users create --username admin --firstname admin --lastname admin --role Admin --email nguyenthihong112000@gmail.com --password admin"
    restart: "no"
```
+ After this step completes, the Airflow system is ready to run.

4. Deploying Airflow Components

+ Webserver: Provides the web interface for managing DAGs, monitoring tasks, and viewing logs.
```
airflow-webserver:
    <<: *airflow-common
    command: airflow webserver
    ports:
      - "8080:8080"
```

+ Scheduler: Schedules tasks and sends them to the execution queue.
```
airflow-scheduler:
    <<: *airflow-common
    command: airflow scheduler
```
+ Celery Worker: Executes tasks such as calling the Reddit API and uploading data to Amazon S3.
```
  airflow-worker:
    <<: *airflow-common
    container_name: airflow-worker
    command: airflow celery worker
    ports:
      - "8793:8793"
```
+ Triggerer: Handles asynchronous tasks and sensors.
```
  airflow-triggerer:
    <<: *airflow-common
    command: airflow triggerer
```