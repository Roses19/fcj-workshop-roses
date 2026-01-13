---
title : "Data Ingestion and Cataloging Pipeline"
date : 2026-01-01
weight : 4
chapter : false
pre : " <b> 5.4. </b> "
---

#### Overview

- After the data is collected by Airflow, the system loads it into the Data Lake on Amazon S3 and uses AWS Glue to automatically generate metadata.

    + **Amazon S3** serves as the central storage for Reddit data.

    +  **AWS Glue Crawler** scans the data and creates tables in the Glue Data Catalog.

- As a result, raw data files are transformed into queryable tables that can be accessed by Amazon Athena and Amazon Redshift.

- This process automates data ingestion, schema management, and provides the foundation for downstream analytics.
![pipeline](/images/5-Workshop/5.4-Data-Catalog/architecture.png)


