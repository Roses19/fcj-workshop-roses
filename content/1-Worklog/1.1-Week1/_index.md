---
title: "Week 1 Worklog"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---

### Week 1 Objectives:

* Master Amazon S3 storage services, security mechanisms, and distribution optimization through CloudFront.
* Establish core infrastructure and security permissions for the Reddit Data Pipeline project (Airflow, S3, IAM).
* Build and successfully operate an automated ETL flow to ingest data from Reddit into the Data Lake.

### Tasks to be carried out this week:
| Day | Task                                                                                                                                                                                                   | Start Date | Completion Date | Reference Material                        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 2   | - Explore Amazon S3 Essential. <br> - **Practice Static Website Hosting:**<br>&emsp; + Configure Static Website Hosting, set up Index & Error documents <br>&emsp; + Access Management: Block Public Access, Bucket Policies, and ACLs.                                                                                      | 15/12/2025   | 15/12/2025      |<https://000057.awsstudygroup.com/> |
| 3   | - Data Management & Optimization: <br>&emsp; + Enable Versioning to protect data from accidental deletion. <br>&emsp; + Set up CRR for disaster recovery. <br>&emsp; + Accelerate website with CDN and configure OAC.                                               | 16/12/2025   | 16/12/2025      | <https://000057.awsstudygroup.com/vi/7-cloudfront/> |
| 4   | - Project Infrastructure & Security Setup: <br>&emsp; + Initialize Airflow environment. <br>&emsp; + Create S3 Buckets . <br> &emsp; + Configure IAM Roles/Policies for Airflow, S3, and Athena. <br> &emsp; + Register Reddit App for Client ID/Secret. &emsp;  | 17/12/2025   | 18/12/2025      |
| 5   | - Explore AWS Glue and Athena services. <br> - **Practice:** <br>&emsp; + Create appropriate IAM Roles and Policies. <br>&emsp; + Create S3 buckets, ETL jobs, and Glue Crawlers.                             |18/12/2025   | 18/12/2025      | <https://000035.awsstudygroup.com/vi/>          |
| 6   | - Data Discovery (Glue & Athena): <br>&emsp; + Run Glue Crawler to scan raw data schemas on S3. <br>&emsp; + Use Athena to execute SQL queries and verify uploaded data. <br> - ETL Pipeline Development: <br>&emsp; + Write Airflow DAG to crawl data from Reddit API. <br>&emsp;   + Implement data ingestion flow to S3 Raw zone.                                                                                   | 19/12/2025   | 19/12/2025      | 


### Week 1 Achievements:

* Storage Management & Content Delivery:
  * Mastered Amazon S3 architecture and successfully deployed Static Website Hosting with complete Index and Error document configurations.
  * Implemented strict security layers via Block Public Access and Bucket Policies; protected data using Versioning and Cross-Region Replication (CRR).
  * Optimized website performance by integrating CloudFront CDN and utilizing Origin Access Control (OAC) to secure data sources.

* Reddit Project Infrastructure Setup:
  * Successfully initialized the Apache Airflow orchestration environment and registered Reddit API credentials (Client ID/Secret).
  * Built a tiered storage structure on S3 (Raw, Transformed, Athena) following the standard Data Lake architecture model.
  * Configured specialized IAM Roles and Policies for Airflow and Athena, adhering to the principle of least privilege.

* Data Discovery & ETL Workflow:
  * Operated AWS Glue Crawler to automatically scan schemas and update Metadata for raw datasets stored on S3.
  * Successfully executed ad-hoc SQL queries on Amazon Athena to verify the integrity and consistency of ingested data.
  * Developed a complete initial Airflow DAG, automating the process of crawling Reddit data and ingesting it into the Raw zone of the Data Lake.