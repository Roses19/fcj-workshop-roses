---
title : "Các bước chuẩn bị"
date : 2026-01-01 
weight : 2
chapter : false
pre : " <b> 5.2. </b> "
---

#### IAM permissions
Gắn IAM permission policy sau vào tài khoản aws user của bạn để triển khai và dọn dẹp tài nguyên trong workshop này.
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
#### Tạo Reddit API application để Apache Airflow crawl dữ liệu 
Tạo ứng dụng loại Script App được tạo trên Reddit Developer Portal để được cấp Client ID và Client Secret. Các thông tin này được dùng để xác thực khi Airflow gọi Reddit API (tài khoản tạo cần được xác thực an toàn > 10 ngày)

![create reddit API](/images/5-Workshop/5.2-Prerequisite/api-reddit.png)
Sau đó sẽ được khai báo tại config.conf:
```
[api-keys]
reddit_secret_key = [your secret key]
reddit_client_key = [your client key]
```
#### Khởi tạo S3 Data Lake Structure

Trong lab này, chúng ta sẽ dùng N.Virginia region (us-east-1).

Trong dự án, Data Lake được xây dựng trên Amazon S3 để làm tầng lưu trữ trung tâm cho toàn bộ dữ liệu Reddit, phục vụ cho cả xử lý batch, phân tích lịch sử và truy vấn BI.

Data Lake được thiết kế theo từng tầng giúp phân tách rõ ràng từng giai đoạn của vòng đời dữ liệu:

![create bucket](/images/5-Workshop/5.2-Prerequisite/create-bucket.png)

#### Thiết lập Amazon Redshift Serverless

Amazon Redshift Serverless làm kho dữ liệu trung tâm cho hệ thống phân tích Reddit.

Việc sử dụng Redshift Serverless giúp loại bỏ nhu cầu quản lý cluster vật lý, đồng thời đảm bảo hệ thống có khả năng tự động mở rộng tài nguyên theo khối lượng truy vấn.

Quá trình thiết lập gồm các bước chính:

 + Tạo Namespace để quản lý metadata và database và Workgroup để cung cấp tài nguyên compute để xử lý truy vấn

 + Namespace: ```reddit-namespace```

 + Workgroup: ```reddit-workgroup``` 
![create wg ns redshift serverless](/images/5-Workshop/5.2-Prerequisite/redshift-serverless.png)

Sau khi hoàn tất, Redshift Serverless sẵn sàng đóng vai trò là Data Warehouse trung tâm, kết nối trực tiếp với S3 Data Lake và Glue Data Catalog để phục vụ các bước xử lý và phân tích tiếp theo.
Khởi tạo Database phân tích
