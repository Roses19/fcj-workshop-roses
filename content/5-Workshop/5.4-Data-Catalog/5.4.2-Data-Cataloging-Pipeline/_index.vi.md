---
title : "Pipeline quản lý danh mục dữ liệu"
date : 2026-01-01
weight : 2
chapter : false
pre : " <b> 5.4.2 </b> "
---

#### 1. Tạo Glue Database
- Glue Database dùng để gom nhóm các bảng Redditvà quản lý metadata tập trung

![create database](/images/5-Workshop/5.4-Data-Catalog/createDB.png)
#### 2. Tạo Glue Job

- Dữ liệu đã nằm trong S3 (tầng Raw) cần được chuẩn hóa, làm sạch và tối ưu dữ liệu trước khi đưa vào truy vấn.

![create Glue ETL](/images/5-Workshop/5.4-Data-Catalog/createETL.png)
- Job được tạo với Source từ tầng raw (s3://amzn-s3-reddit-airflow-project/raw/ **<file cần làm sạch>**) và đích là tầng S3 tầng transformed (s3://amzn-s3-reddit-airflow-project/transformed/): 

- Viết thêm scripts để chuẩn hóa dữ liệu: 
	+ Dữ liệu được đọc từ S3 dưới dạng DynamicFrame, sau đó chuyển sang Spark DataFrame để thực hiện các phép biến đổi.
	```
	from awsglue import DynamicFrame
	from pyspark.sql.functions import concat_ws
	```
	+ Ba thuộc tính edited, spoiler và stickied được kết hợp thành một cột duy nhất (ESS_updated) nhằm đơn giản hóa schema và tạo ra một đặc trưng tổng hợp cho phân tích. Sau khi xử lý, dữ liệu được chuyển lại thành DynamicFrame.
	+  Cuối cùng, dữ liệu đã được chuẩn hóa được ghi vào S3 (Transformed Zone), sẵn sàng cho việc truy vấn và phân tích bằng Athena và Redshift.
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

- Chọn IAM Role phù hợp gồm các policy ```AmazonS3ReadOnlyAccess```, ```AWSGlueServiceRole```, và customer inline: 
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
- Sau khi chạy Job thành công, dữ liệu chuẩn hóa sẽ được chuyển sang tầng transform:
![run job](/images/5-Workshop/5.4-Data-Catalog/runjob.png)
#### 2. Tạo Glue Crawler
- Crawler được cấu hình:
	+ Name: ```reddit_crawler```
    + Data source: Amazon S3
    + Path: ```s3://amzn-s3-reddit-airflow-project/transformed/``` (lấy từ tầng đã được chuẩn hóa)
	+ IAM role: Tạo mới role với tên ```AWSGlueServiceRole-Reddit-glue-role``` 
    + Tên Database lưu trữ: reddit_db
![Create Crawler](/images/5-Workshop/5.4-Data-Catalog/createCrawler.png)
- Kết quả: Crawler sẽ duyệt toàn bộ file dữ liệu đã được chuẩn hóa, phát hiện schema tự động và lưu trữ vào cơ sở dữ liệu
- Chạy crawler: Glue đọc JSON và sinh bảng, sau đó cập nhật Glue Catalog
![table in db](/images/5-Workshop/5.4-Data-Catalog/table.png)
