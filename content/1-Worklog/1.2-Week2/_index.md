---
title: "Week 2 Worklog"
date: 2026-01-01
weight: 1
chapter: false
pre: " <b> 1.2. </b> "
---

### Week 2 Objectives:

* Master data cleansing, transformation, and optimization using **AWS Glue and PySpark**.  
* Understand and apply **Monitoring, Cost Management, and Security** for a cloud data platform.  
* Learn core **AWS Data & Analytics services** including **S3, Glue, Athena, and QuickSight**.  
* Build and manage a **Data Lake and ETL pipeline on AWS**.
### Tasks to be carried out this week:
| Day | Task                                                                                                                                                                                                   | Start Date | Completion Date | Reference Material                        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 2 | - **ETL Transformation**:<br>&emsp; + Build Glue Jobs (PySpark) to clean data.<br>&emsp; + Convert data formats to optimize performance. | 22/12/2025 | 22/12/2025 | |
| 3 | - **Monitoring & Cost Management**<br>&emsp; + CloudWatch logs & metrics<br>&emsp; + AWS Budgets<br>&emsp; + Cost Explorer | 23/12/2025 | 23/12/2025 | https://000008.awsstudygroup.com/ |
| 4 | - **IAM & Security for Data Platform**<br>&emsp; + IAM User vs Role vs Policy<br>&emsp; + IAM for Glue, Athena, Redshift<br>&emsp; + S3 Bucket Policy vs IAM Policy<br>&emsp; + KMS & data encryption | 24/12/2025 | 24/12/2025 | https://000033.awsstudygroup.com/ |
| 5 | - **Networking for AWS Data Services**<br>&emsp; + VPC, Subnet, Internet Gateway<br>&emsp; + VPC Endpoints for S3 & Glue<br>&emsp; + Public vs Private access<br>&emsp; + How AWS data services communicate | 25/12/2025 | 26/12/2025 | https://000003.awsstudygroup.com/vi/ |
| 6 | - **Build an AWS Data Lake**: Learn AWS Glue, Athena, QuickSight.<br>- **Hands-on**:<br>&emsp; + Create appropriate IAM roles and policies.<br>&emsp; + Create S3 buckets, ETL jobs, and crawlers.<br>&emsp; + Query data using Athena SQL. | 26/12/2025 | 26/12/2025 | https://000035.awsstudygroup.com/vi |



### Week 2 Achievements:

- Built **AWS Glue ETL Jobs (PySpark)** to:
  - Clean Reddit data  
  - Normalize schemas for analytics  
  - Move data from **Raw S3 → Processed S3**

- Implemented **Monitoring & Cost Management**:
  - Enabled **CloudWatch Logs** for Glue Jobs  
  - Set up **AWS Budgets** to track spending  
  - Used **Cost Explorer** to analyze usage  

- Configured **Security for the Data Platform**:
  - IAM Roles for **Glue, Athena, and S3**  
  - S3 Bucket Policies following the **least-privilege principle**  
  - Enabled **KMS encryption** for data  

- Designed and understood **Networking for AWS Data Services**:
  - VPC, Subnets, and Internet Gateway  
  - VPC Endpoints for **S3 and Glue**  
  - Differences between **Public and Private access**  

- Built an **AWS Data Lake**:
  - Created S3 structure: `raw/`, `processed/`  
  - Ran **Glue Crawler** to generate the **Data Catalog**