---
title: "Week 3 Worklog"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.3. </b> "
---

### Week 3 Objectives:

* Build and manage the Data Lake system and ETL processes on AWS.
* Deploy a data warehouse and perform high-performance analytics with Redshift Serverless.
* Design professional interactive BI dashboards using Amazon QuickSight.

### Tasks to be carried out this week:
| Day | Task                                                                                                                                                                                                   | Start Date | Completion Date | Reference Material                        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 2   | - Building a Data Lake on AWS: Explore AWS Glue, Athena, and QuickSight. <br> - **Practice:** <br>&emsp; + Create appropriate Roles and Policies.         <br>&emsp; + Create S3 buckets, ETL jobs, and crawlers. <br>&emsp; + Perform SQL querying with Athena.                                                                                        | 29/12/2025 | 29/12/2025     |<hhttps://000035.awsstudygroup.com/vi/>|
| 3   | - **Set up and operate AWS Redshift:** <br>&emsp; + Initialize Redshift Serverless (Namespace & Workgroup). <br>&emsp; + Configure IAM Roles for S3 data access. <br> &emsp; + Create Tables and execute the COPY command to load data from S3. <br> &emsp; + Perform analytical SQL querying and visualization with Query Editor v2. | 30/12/2025 | 30/12/2025      | <https://docs.aws.amazon.com/redshift/>|
| 4   | - Explore Amazon QuickSight core components: SPICE, Dataset, Analysis, and Dashboard. <br> - **Practice:** <br>&emsp; + Prepare data and set up the environment in the Singapore Region. <br>&emsp; + Create visualizations. <br>&emsp; + Optimize UI and set up interactivity. <br>&emsp; + Publish the Dashboard and clean up resources.                          | 31/12/2025 | 31/12/2025     | <https://000073.awsstudygroup.com/> |


### Week 3 Achievements:

* Data Lake & ETL:
  * Established IAM Roles and security Policies for data services.
  * Created S3 buckets and configured Glue Crawlers for automated schema discovery and Data Catalog updates.
  * Successfully performed direct data querying on S3 using Amazon Athena.

* AWS Redshift Serverless:
  * Initialized the Namespace/Workgroup environment and successfully loaded data from S3 into tables via the COPY command.
  * Leveraged Query Editor v2 for in-depth SQL analytics and performance tuning on the data warehouse.

* Amazon QuickSight:
  * Optimized data processing performance using the SPICE in-memory calculation engine. 
  * Implemented various visualization types, including Line charts, KPI & Insights, Pie charts, and Pivot Tables. 
  * Configured flexible dashboard interactivity through Filters and Navigation Actions. 
  * Successfully published the final Dashboard and executed resource cleanup to optimize operational costs.