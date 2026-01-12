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
        "iam:CreateServiceLinkedRole",
        "iam:ChangePassword",
        "iam:GetAccountPasswordPolicy"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Deny",
      "Action": "redshift:CreateClusterUser",
      "Resource": "arn:aws:redshift:*:*:dbuser:*/redshift_data_api_user"
    }
  ]
}


```

#### Provision resources using CloudFormation

In this lab, we will use **N.Virginia region (us-east-1)**.

To prepare the workshop environment, deploy this **CloudFormation Template** (click link): [PrivateLinkWorkshop ](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/quickcreate?templateURL=https://s3.us-east-1.amazonaws.com/reinvent-endpoints-builders-session/Nested.yaml&stackName=PLCloudSetup). Accept all of the defaults when deploying the template. 

![create stack](/images/5-Workshop/5.2-Prerequisite/create-stack1.png)

+ Tick 2 acknowledgement boxes
+ Choose **Create stack**

![create stack](/images/5-Workshop/5.2-Prerequisite/create-stack2.png)

The **ClouddFormation** deployment requires about 15 minutes to complete.

![complete](/images/5-Workshop/5.2-Prerequisite/complete.png)

+ **2 VPCs** have been created

![vpcs](/images/5-Workshop/5.2-Prerequisite/vpcs.png)

+ **3 EC2s** have been created

![EC2](/images/5-Workshop/5.2-Prerequisite/ec2.png)