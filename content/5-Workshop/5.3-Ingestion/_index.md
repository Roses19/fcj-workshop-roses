---
title : "Ingestion"
date : 2026-01-01
weight : 3
chapter : false
pre : " <b> 5.3. </b> "
---

#### Using Airflow

During the data ingestion phase, the system uses **Apache Airflow** as the orchestration platform to automate the process of collecting data from the Reddit API and loading it into the Data Lake.

The entire Airflow environment is deployed using Docker, and consists of the main components: **Webserver, Scheduler, Workers, PostgreSQL, and Celery**.

![overview](/images/5-Workshop/5.3-Ingestion/ingestion.png)

#### Content

- [Docker Configuration](3.1-Docker-Configuration/)
- [Test gateway endpoint](3.2-test-gwe/)