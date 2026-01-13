---
title : "Clean up"
date : 2024-01-01
weight : 6
chapter : false
pre : " <b> 5.6. </b> "
---


#### Resource Cleanup

Because the Data Platform uses multiple compute-intensive services (such as AWS Glue and Amazon Redshift), costs can increase rapidly if these resources are not properly managed, even when there are no active users. Therefore, cleaning up resources after completing experiments is necessary to avoid unnecessary charges.

#### 1.  Glue Crawler

Delete the crawler to prevent Glue from continuing to scan data: Go to AWS Glue → Crawlers → reddit_crawler → Delete
![delete crawler](/images/5-Workshop/5.6-Cleanup/delete-crawler.png)


#### 2. Glue Job

Delete the ETL job to stop Glue from consuming compute resources: Go to Glue → Jobs → reddit_glue_jobjob.py → Action → Delete job(s)
![delete job](/images/5-Workshop/5.6-Cleanup/delete-job.png)

#### 3. Glue Logs
Delete Glue execution logs to avoid storage charges in CloudWatch: Go to  CloudWatch → Log groups → /aws-glue/* → Delete group logs
![delete log](/images/5-Workshop/5.6-Cleanup/delete-log.png)

#### 4. Delete S3 Buckets

+ Open the Amazon S3 console

+ Select the buckets created for the project (raw, transformed, athena, and glue)

+ Click Empty to remove all data, then click Delete and confirm

![delete s3](/images/5-Workshop/5.6-Cleanup/delete-bucket.png)