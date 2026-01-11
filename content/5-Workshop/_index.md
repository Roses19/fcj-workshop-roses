---
title: "Workshop"
date: 2024-01-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Automating the ETL Pipeline for Reddit Data on AWS

#### Overview

This project builds an automated system for analyzing Reddit data on AWS with the following main components:

+ **Data collection and storage**: Data is automatically retrieved from the Reddit API using Apache Airflow and stored in Amazon S3 following the Data Lake model (Raw and Transformed).

+ **Data processing and management**: AWS Glue ETL Jobs are used to clean and transform the data, while AWS Glue Data Catalog and Crawler manage schemas and metadata.

+ **Analytics and querying**: Amazon Athena enables direct querying on the Data Lake, while Amazon Redshift serves as the Data Warehouse for analytical queries.

+ **Visualization**: Amazon QuickSight is used to build dashboards and display insights from the data.

The system ensures that Reddit data is continuously updated and analyzed automatically, supporting the tracking of trends and user behavior on the AWS platform.

#### Content

1. [Workshop overview](5.1-Workshop-overview)
2. [Prerequiste](5.2-Prerequiste/)
3. [Access S3 from VPC](5.3-S3-vpc/)
4. [Access S3 from On-premises](5.4-S3-onprem/)
5. [VPC Endpoint Policies (Bonus)](5.5-Policy/)
6. [Clean up](5.6-Cleanup/)