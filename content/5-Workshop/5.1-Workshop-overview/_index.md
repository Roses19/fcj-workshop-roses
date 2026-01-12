---
title : "Introduction"
date : 2026-01-01 
weight : 1 
chapter : false
pre : " <b> 5.1. </b> "
---

#### Data Platform
A Data Platform is a system that enables the collection, storage, processing, and analysis of large-scale data in an automated and scalable manner. In this architecture, data is organized into layers (raw, transformed, analytics) to ensure that the original data is preserved while optimizing it for analysis and reporting.

#### Workshop overview
In this workshop, Reddit data is analyzed on AWS based on a Data Lake architecture combined with a Data Warehouse.

The system includes:

+ **Apache Airflow** running in Docker to collect data from the Reddit API and orchestrate the pipeline.

+ **Amazon S3** to store data in two layers: Raw and Transformed.

+ **AWS Glue & Data Catalog** to automatically discover schemas and process data.

+ **Amazon Athena** to query data directly from S3.

+ **Amazon Redshift** to store analytical data.

+ **Amazon QuickSight** to build dashboards and visualize data.

![overview](/images/5-Workshop/5.1-Workshop-overview/architecture.png)