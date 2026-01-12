---
title: "Proposal"
date: 2026-01-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# AWS Reddit ETL Platform
## Automating the ETL Pipeline for Reddit Data on AWS

### 1. Executive Summary
This project is designed to build an automated social media data collection and processing system that optimizes the balance between available on-premise resources and cloud infrastructure. The system adopts a Hybrid Architecture, where **Apache Airflow** runs on a physical local server to handle orchestration, while AWS Serverless services are used to process and store large-scale data.

The solution fully automates the Reddit data lifecycle—from data ingestion, automatic schema discovery using **AWS Glue Crawlers**, to Spark-based ETL transformations that convert data into Parquet format, significantly reducing query costs. The system leverages **Amazon Redshift Serverless** with a minimal configuration (4 RPUs) for high-performance analytics and visualization through **Amazon QuickSight** Standard, minimizing monthly operating costs while maintaining analytical flexibility.

### 2. Problem Statement
### What’s the Problem?
Extracting and analyzing data from social media platforms faces several key challenges:

- Fragmented infrastructure – Data scraping and analytics are often disconnected, causing stale or lost data due to manual handling.

- Inefficient compute utilization – Running a 24/7 data warehouse for infrequent queries results in unnecessary costs, especially for small and medium-sized projects.

- API rate limits and costs – Reddit enforces strict API limits (100 QPM), requiring intelligent orchestration to avoid account suspension.

### The Solution
A modernized data pipeline is proposed:

- Local orchestration – Airflow runs in Docker on an on-premise server to eliminate cloud compute costs for orchestration.

- Serverless ingestion & ETL – Amazon S3 and AWS Glue handle large-scale unstructured data processing without server management.

- Optimized analytics – Redshift Serverless with 4 RPUs executes SQL analytics only when needed, delivering high performance at very low cost.
    
### Benefits and Return on Investment
This hybrid solution reduces fixed infrastructure costs by approximately 30–40% compared to a fully cloud-based deployment. Operational expenses occur only when AWS services are used, averaging USD 25–30 per month. The time from breaking Reddit news to visualization in QuickSight is reduced from days to hours, enabling near-real-time insights.

### 3. Solution Architecture
The system applies an event-driven, serverless architecture combined with a container-based local orchestration layer.
![Social Media Big Data Pipeline Architecture](/images/2-Proposal/architecture.png)

### AWS Services Used

- **Amazon S3** – Serves as the Data Lake for both raw and transformed data
- **AWS Glue Crawler** – Automatically detects schema and updates metadata
- **AWS Glue Data Catalog** – Centralized metadata repository
- **AWS Glue ETL** – Serverless Spark processing for data cleansing and Parquet conversion
- **Amazon Athena** – Serverless SQL querying directly on S3
- **Amazon Redshift Serverless** – Auto-scaling data warehouse for high-performance analytics
- **Amazon QuickSight Standard** – Serverless BI platform for dashboarding
### Component Design

- **Ingestion** – Airflow (Local Docker) pulls Reddit data via API and stores raw JSON in S3

- **Processing** – Glue Crawler updates schema, and Glue Spark ETL converts JSON to partitioned Parquet

- **Analytics** – Data is queried in Redshift Serverless and visualized in QuickSight dashboards

### 4. Technical Implementation
**Implementation Phases**

- Local Environment Setup – Configure Docker Compose for Airflow and establish secure IAM access to AWS
- Reddit Extractor Development – Implement Python scripts using PRAW with exponential backoff to respect API limits
- Glue Pipeline – Configure Crawlers and Spark ETL jobs to clean and partition Parquet data by date
- Data Warehouse Setup – Create Redshift Serverless namespace and workgroup with 4 RPUs
- Dashboard Design – Connect QuickSight to Redshift and build analytics dashboards 

**Technical Requirements**
- Reddit API – Script-type developer account with 100 QPM rate limit
- Compute (Orchestration) – Local server with at least 8 GB RAM for Docker & Celery workers
- Storage – Parquet format in S3 reduces storage and query costs by up to 90% compared to JSON
- Redshift Serverless – Minimal 4 RPU configuration for cost-efficient analytics
- QuickSight Standard – Low-cost BI for basic reporting

### 5. Timeline & Milestones
**Project Timeline**
- Pre-internship (Month 0) – AWS fundamentals and ETL architecture training
- Internship Phase: 
    - Weeks 1–2: AWS learning and hardware upgrade
    - Week 3: Architecture design and tuning
    - Weeks 4–5: Deployment, testing, and production rollout
- Post-deployment – 6-month research phase for system enhancement
### 6. Budget Estimation
You can find the budget estimation on the [AWS Pricing Calculator](https://calculator.aws/#/estimate?id=1412e308aca85b914fe80db814739f567edfbb29).

### Infrastructure Costs
- AWS Services:
    - Amazon S3 (Data Lake): USD 0.17/month (6 GB, 5,000 requests, 1 GB scanned)
    - Data Transfer: USD 0.54/month (6 GB inbound, 6 GB outbound)
    - AWS Glue ETL Jobs: USD 3.09/month (2 DPUs, Spark and Python Shell jobs)
    - AWS Glue Crawlers: USD 0.66/month (1 crawler)
    - AWS Glue Data Catalog: USD 0.01/month
    - Amazon Athena: USD 0.74/month (10 queries/day, 0.5 GB per query)
    - Amazon Redshift Serverless: USD 7.32/month (4 RPUs, ~0.16 hours/day)

**Total**: $15.38/month ≈ $ 184.56/year

- Amazon QuickSight (demo only): USD 9–12/month (1 Author, no SPICE, deleted after demo)

### 7. Risk Assessment

-  Local server availability – Failure may interrupt ingestion
    - Mitigation: Enable Airflow catch-up to rerun missed tasks

-  API rate limits – Exceeding 100 QPM may block access
    - Mitigation: Limit to 5–10 subreddits and use Celery queues

-  Schema drift – Reddit data structure may change

    - Mitigation: Use Glue Crawlers for automatic schema discovery

### 8. Expected Outcomes
- Fully automated Reddit data ingestion
- AWS cost optimized to stay below USD 30/month
- Easily scalable by adding new subreddits via Airflow configuration
- Interactive dashboards providing real-time insights into social media trends.