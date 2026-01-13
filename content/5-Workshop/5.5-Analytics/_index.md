---
title : "Analytics and Visualization"
date : 2026-01-01
weight : 5
chapter : false
pre : " <b> 5.5. </b> "
---


#### 1. Querying Data with Amazon Athena
- After the Glue Crawler creates tables in the Glue Data Catalog, data stored in the S3 Transformed Zone can be queried directly using Amazon Athena without moving the data.

- Athena uses metadata from the Glue Data Catalog to understand: Schema, data types, and file locations in S3

- Athena configuration

  + Database: reddit_db

  + Table: transformed

  + Query settings → query results are stored at: s3://amzn-s3-reddit-airflow-project/athena-script/

![athena](/images/5-Workshop/5.5-Analytics/athena.png)
- Example query:
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
- Result: 
![query](/images/5-Workshop/5.5-Analytics/result-query.png)
- In the early stage of analysis, Athena is used for rapid exploratory analysis, data inspection and validation of ETL quality.

#### 2. Loading Data from S3 into Amazon Redshift

- Although Athena is suitable for ad-hoc queries, advanced analytics such as BI dashboards, large joins, and high-performance queries require loading the data into Amazon Redshift (Data Warehouse).

- With the previously prepared configurations (namespace and workgroup), the data is queried using Query Editor v2:
![query](/images/5-Workshop/5.5-Analytics/redshift-serverless.png)

- The connection is established to the created workgroup using Federated user authentication, which eliminates the need for database passwords: 
![connect wg](/images/5-Workshop/5.5-Analytics/connectwg.png)
- Data is then loaded from S3 into the Data Warehouse:
![load data](/images/5-Workshop/5.5-Analytics/loaddata.png)
- The public schema is selected, a table name is assigned in the warehouse, and the default IAM role is used:
![schema](/images/5-Workshop/5.5-Analytics/schema.png)
- The Data Warehouse after a successful load:
![dwh](/images/5-Workshop/5.5-Analytics/dwh.png)
#### 3. Future Direction for Visualization and Advanced Analytics

- Although the current volume of Reddit data is not yet large enough to fully exploit Business Intelligence (BI) systems, the platform architecture has been designed to be scalable and future-ready.

- As data continues to be collected over time through daily ingestion via Airflow, Amazon Redshift will gradually evolve into a historical data warehouse with increasing temporal depth. At that stage, BI tools such as Amazon QuickSight can be integrated directly with Redshift to enable:

  + Visualization of post growth trends by subreddit

  + Analysis of engagement behavior (score, comments, ESS_updated) over time

  + Monitoring topic evolution and influence

  + Supporting data-driven decision making using both historical and near real-time data

Therefore, the current system not only supports data processing and querying, but also serves as a complete enterprise-grade analytics platform, ready to be extended with visualization and advanced analytics once the dataset reaches sufficient scale.