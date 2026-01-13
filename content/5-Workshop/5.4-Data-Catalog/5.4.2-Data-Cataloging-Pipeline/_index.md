---
title : "Data Cataloging Pipeline"
date : 2026-01-01
weight : 2
chapter : false
pre : " <b> 5.4.2 </b> "
---


#### 1. Create a Glue Database
- The Glue Database is used to group Reddit tables and centrally manage metadata

![create database](/images/5-Workshop/5.4-Data-Catalog/createDB.png)
#### 2. Create a Glue Job

- Data stored in S3 (Raw Zone) needs to be standardized, cleaned, and optimized before being queried.

![create Glue ETL](/images/5-Workshop/5.4-Data-Catalog/createETL.png)
- The job is configured with Source from raw layer (s3://amzn-s3-reddit-airflow-project/raw/ **<raw_files>**) and target from transformed layer (s3://amzn-s3-reddit-airflow-project/transformed/): 

- Additional scripts are written to standardize the data:
	+ The data is read from S3 as a DynamicFrame, then converted to a Spark DataFrame to perform transformations.
	```
	from awsglue import DynamicFrame
	from pyspark.sql.functions import concat_ws
	```
	+ Three attributes — edited, spoiler, and stickied — are combined into a single column ESS_updated to simplify the schema and create a composite analytical feature.

    + After processing, the data is converted back into a DynamicFrame.

    + Finally, the standardized data is written to the Transformed Zone (S3), ready for querying and analysis using Athena and Redshift.
```

#convert DynamicFrame to DataFrame
df = AmazonS3_node1766723555757.toDF()

#concatenate three columns into a single columns
df_combined = df.withColumn('ESS_updated', concat_ws('-', df['edited'], df['spoiler'], df['stickied']))
df_combined = df_combined.drop('edited','spoiler','stickied')
#convert back to DynamicFrame
S3bucket_node_combined = DynamicFrame.fromDF(df_combined,glueContext,'S3bucket_node_combined')
# Script generated for node Amazon S3
EvaluateDataQuality().process_rows(frame=S3bucket_node_combined, ruleset=DEFAULT_DATA_QUALITY_RULESET, publishing_options={"dataQualityEvaluationContext": "EvaluateDataQuality_node1766723551899", "enableDataQualityResultsPublishing": True}, additional_options={"dataQualityResultsPublishing.strategy": "BEST_EFFORT", "observations.scope": "ALL"})
AmazonS3_node1766723556784 = glueContext.write_dynamic_frame.from_options(frame=S3bucket_node_combined, connection_type="s3", format="csv", connection_options={"path": "s3://amzn-s3-reddit-airflow-project/transformed/", "partitionKeys": []}, transformation_ctx="AmazonS3_node1766723556784")

job.commit()		
```

- Choose an appropriate IAM Role that includes the following policies  ```AmazonS3ReadOnlyAccess```, ```AWSGlueServiceRole```, and Custom inline policy: 
```
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Effect": "Allow",
			"Action": [
				"s3:PutObject",
				"s3:GetObject",
				"s3:DeleteObject",
				"s3:ListBucket"
			],
			"Resource": [
				"arn:aws:s3:::amzn-s3-reddit-airflow-project",
				"arn:aws:s3:::amzn-s3-reddit-airflow-project/*"
			]
		}
	]
}
```
![choose Role](/images/5-Workshop/5.4-Data-Catalog/IAMRoleGlue.png)
- After the job runs successfully, the cleaned and standardized data is stored in the Transformed Zone:
![run job](/images/5-Workshop/5.4-Data-Catalog/runjob.png)
#### 2. Create a Glue Crawler
- The crawler is configured as follows:
	+ Name: ```reddit_crawler```
    + Data source: Amazon S3
    + Path: ```s3://amzn-s3-reddit-airflow-project/transformed/``` (the standardized data layer)
	+ IAM role:```AWSGlueServiceRole-Reddit-glue-role``` 
    + Database name: reddit_db
![Create Crawler](/images/5-Workshop/5.4-Data-Catalog/createCrawler.png)
- Result: The crawler scans all standardized filesfilesfilesfiles, automatically detects the schema, and stores table metadata in the Glue Data Catalog

- When the crawler runs, Glue reads the transformed data and generates tables, which are then registered in the Glue Catalog.
![table in db](/images/5-Workshop/5.4-Data-Catalog/table.png)
