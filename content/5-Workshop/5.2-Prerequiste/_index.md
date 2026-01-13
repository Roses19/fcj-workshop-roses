---
title : "Prerequiste"
date : 2026-01-01 
weight : 2 
chapter : false
pre : " <b> 5.2. </b> "
---

#### IAM permissions
Add the following IAM permission policy to your user account to deploy and cleanup this workshop.
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "events:*",
        "schemas:*",
        "scheduler:*",
        "pipes:*",
        "redshift:*",
        "redshift-serverless:*",
        "redshift-data:*",
        "sqlworkbench:*",
        "athena:*",
        "glue:*",
        "s3:*",
        "s3-object-lambda:*",
        "cloudwatch:*",
        "logs:*",
        "observabilityadmin:*",
        "ec2:*",
        "kms:DescribeKey",
        "kms:ListAliases",
        "secretsmanager:*",
        "iam:CreateRole",
        "iam:CreatePolicy",
        "iam:AttachRolePolicy",
        "iam:PutRolePolicy",
        "iam:PassRole",
        "iam:ListRoles",
        "iam:GetRole",
        "iam:ListRolePolicies",
        "iam:GetRolePolicy",
        "iam:ChangePassword",
        "iam:GetAccountPasswordPolicy"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Deny",
      "Action": "redshift:CreateClusterUser",
      "Resource": "arn:aws:redshift:*:*:dbuser:*/redshift_data_api_user"
    },
    {
      "Effect": "Allow",
      "Action": "iam:CreateServiceLinkedRole",
      "Resource": [
        "arn:aws:iam::*:role/aws-service-role/redshift.amazonaws.com/AWSServiceRoleForRedshift",
        "arn:aws:iam::*:role/aws-service-role/redshift-data.amazonaws.com/AWSServiceRoleForRedshift"
      ],
      "Condition": {
        "StringLike": {
          "iam:AWSServiceName": [
            "redshift.amazonaws.com",
            "redshift-data.amazonaws.com"
          ]
        }
    }
}

  ]
}
```
#### Creating a Reddit API Application for Apache Airflow
A Script-type application is created on the Reddit Developer Portal in order to obtain a Client ID and Client Secret. These credentials are used to authenticate Apache Airflow when calling the Reddit API.
(The Reddit account used to create the application must be verified and older than 10 days for security reasons.)

![create reddit API](/images/5-Workshop/5.2-Prerequisite/api-reddit.png)

#### Initializing the S3 Data Lake Structure

In this lab, the N. Virginia region (us-east-1) is used.

In this project, the Data Lake is built on Amazon S3, serving as the central storage layer for all Reddit data. It supports batch processing, historical analysis, and BI queries.

The Data Lake is designed with multiple layers to clearly separate each stage of the data lifecycle: 

![create bucket](/images/5-Workshop/5.2-Prerequisite/create-bucket.png)
#### Defining Variables in config.conf 
- Purpose: to provide a centralized configuration and ensure secure credential management.
```
[database]
database_host = localhost
database_name = airflow_reddit
database_port = 5432
database_username = postgres
database_password = postgres

[file_paths]
input_path = /opt/airflow/data/input
output_path = /opt/airflow/data/output

[api-keys]
reddit_secret_key = [your secret key]
reddit_client_key = [your client key]
                    
[aws]
aws_access_key_id = [your-aws-key]
aws_secret_access_key_id = [your-secret-key]
aws_region = us-east-1
aws_bucket_name = amzn-s3-reddit-airflow-project

[etl_settings]
batch_size = 100
error_handling = abort
log_level = info
```
- Mapping Variables in **constants.py**
These values are then loaded into the application via constants.py:
```
import configparser
import os

parse = configparser.ConfigParser()
parse.read(os.path.join(os.path.dirname(__file__), '../config/config.conf'))

SECRET = parse.get('api-keys', 'reddit_secret_key')
CLIENT_ID = parse.get('api-keys', 'reddit_client_key')

DATABASE_HOST = parse.get('database', 'database_host')
DATABASE_NAME = parse.get('database', 'database_name')
DATABASE_PORT = parse.get('database', 'database_port')
DATABASE_USER = parse.get('database', 'database_username')
DATABASE_PASSWORD = parse.get('database', 'database_password')

#aws
AWS_ACCESS_KEY_ID = parse.get('aws','aws_access_key_id')
AWS_SECRET_KEY = parse.get('aws', 'aws_secret_access_key_id')
AWS_REGION = parse.get('aws', 'aws_region')
AWS_BUCKET_NAME = parse.get('aws','aws_bucket_name')

INPUT_PATH = parse.get('file_paths', 'input_path')
OUTPUT_PATH = parse.get('file_paths', 'output_path')

POST_FIELDS = (
    'id',
    'title',
    'score',
    'num_comments',
    'author',
    'created_utc',
    'url',
    'over_18',
    'edited',
    'spoiler',
    'stickied'
)
```

#### Setting up Amazon Redshift Serverless
Amazon Redshift Serverless is used as the central data warehouse for Reddit analytics.

Using Redshift Serverless eliminates the need to manage physical clusters while allowing the system to automatically scale compute resources based on query demand.

The setup process includes:

+ Creating a Workgroup to provide compute resources for query execution

+ Namespace: ```reddit-namespace```

+ Workgroup: ```reddit-workgroup```
 
![create wg ns redshift serverless](/images/5-Workshop/5.2-Prerequisite/redshift-serverless.png)

After the setup is completed, Redshift Serverless is ready to serve as the central Data Warehouse, directly integrated with the S3 Data Lake and the Glue Data Catalog to support subsequent processing and analytics steps.
