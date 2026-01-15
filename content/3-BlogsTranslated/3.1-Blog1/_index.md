---
title: "Build and manage your modern data stack using dbt and AWS Glue through dbt-glue, the new trusted dbt adapter"
date: 2026-01-05
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---



Original blog:: [AWS Big Data Blog – dbt & AWS Glue](https://aws.amazon.com/vi/blogs/big-data/build-and-manage-your-modern-data-stack-using-dbt-and-aws-glue-through-dbt-glue-the-new-trusted-dbt-adapter/)

## Introduction

**dbt** is an open-source, SQL-first tool that allows you to write reusable and extensible data transformations using SQL and Python. dbt focuses on the transform layer in the ELT/ETL workflow on data warehouses and databases through corresponding adapters to enable extraction and loading of data. 

This allows data engineers, data scientists, and analytics engineers to define business logic using SQL SELECT statements and eliminates the need to manually write DML and DDL statements. dbt also enables fast and collaborative analytics code deployment following software development best practices such as modularity, portability, CI/CD, and documentation.

**dbt** is primarily used with data warehouses (such as Amazon Redshift), where customers want to separate transformation logic from storage and compute engines.

To meet the demand for cloud data lakes, AWS introduced the dbt-glue adapter in 2022 — an AWS Glue adapter for open-source dbt that is battle-tested, enabling data engineers to use dbt across both data lakes and data warehouses, paying only for the compute they need.

**Adapter dbt-glue** extends dbt access to data lakes and allows running data transformation workloads on AWS Glue’s serverless Spark with ease. AWS continues to invest in dbt-glue to support additional requirements.

Today, we are pleased to announce that the dbt-glue adapter is now a trusted adapter based on our strategic collaboration with dbt Labs. Trusted adapters are adapters not maintained by **dbt Labs**, but adaptors that that dbt Lab is comfortable recommending to users for use in production.


The key capabilities of the dbt-glue adapter are as follows\:
- Runs SQL as Spark SQL on AWS Glue interactive sessions.
- Manages table definitions on the **Amazon SageMaker Lakehouse Catalog** with storage on Amazon S3.
- Supports open table formats such as Apache Hudi, Delta Lake, and Apache Iceberg
- Supports **AWS Lake Formation permissions** for fine-grained access control

In addition to those capabilities, the dbt-glue adapter is designed to optimize resource utilization with several techniques on top of AWS Glue interactive sessions.

This post demonstrates how the dbt-glue adapter helps your workload, and how you can build a modern data stack using dbt and AWS Glue using the dbt-glue adapter.

## Common use cases
**1. A central analytics team at a large corporation**

A central analytics team needs to track operational performance. They ingest application logs into Parquet tables in the raw layer of an S3-based data lake. They also extract structured data from operational systems.

For example, organizational schema and cost data for different components are stored in the raw layer using Iceberg to preserve the original schema for multiple data access patterns.

The team uses dbt-glue to build gold models optimized for BI — combining technical logs with billing data and organizing metrics by business unit. Iceberg supports warehouse-style modeling for high-performance BI analytics. Combining Iceberg and dbt-glue enables building data models that are ready for consumption.

**2. Building GDPR-compliant data products**

An analytics team at a European company operates a data lake on S3 and needs to build new data products to enrich healthcare data, which requires GDPR compliance such as the right to be forgotten and data deletion. They use Iceberg to meet these requirements and dbt to model data on the data lake because dbt-glue supports Iceberg and simplifies the use of this storage format.

##  How dbt and dbt-glue work
- The following are key dbt features::
    + **Project** – A dbt project enforces a top-level structure on the staging, models, permissions, and adapters. A project can be checked into a GitHub repo for version control.
    + **SQL** – dbt relies on SQL select statements for defining data transformation logic. Instead of raw SQL, dbt offers templatized SQL (using Jinja) that allows code modularity. Instead of having to copy/paste SQL in multiple places, data engineers can define modular transforms and call those from other places within the project. Having a modular pipeline helps data engineers collaborate on the same project.
    + **Models** – dbt models are primarily written as a SELECT statement and saved as a .sql file. Data engineers define dbt models for their data representations.
    + **Materializations** –  Materializations are strategies for persisting dbt models in a warehouse. There are five types of materializations built into dbt: table, view, incremental, ephemeral, and materialized view. 
    + **Data lineage** – dbt tracks data lineage, allowing you to understand the origin of data and how it flows through different transformations. dbt also supports impact analysis, which helps identify the downstream effects of changes.

- The high-level data flow is as follows: 

    1. Data engineers ingest data from data sources to raw tables and define table definitions for the raw tables.

    2. Data engineers write dbt models with templatized SQL.

    3. The dbt adapter converts dbt models to SQL statements compatible in a data warehouse.

    4. The data warehouse runs the SQL statements to create intermediate tables or final tables, views, or materialized views.

The following diagram illustrates the architecture.
![architecture](/images/3-Blogstranslated/image.png)
- dbt-glue works with the following steps:

    1. The dbt-glue adapter converts dbt models to SQL statements compatible in Spark SQL.

    2.AWS Glue interactive sessions run the SQL statements to create intermediate tables or final tables, views, or materialized views.

    3. dbt-glue supports: ```csv```, ```parquet```, ```hudi```, ```delta```, ```iceberg```.

    4. On the dbt-glue adapter, table or incremental are commonly used for materializations at the destination. There are three strategies for incremental materialization. The merge strategy requires ```hudi```, ```delta```, or ```iceberg```. With the other two strategies,  ```append``` and ```insert_overwrite```, you can use any of the formats above.

The following diagram illustrates this architecture.
![diagram illustrates](/images/3-Blogstranslated/image-1.png)

## Example use case

In this post, we use the data from the [**New York City Taxi Records**](https://registry.opendata.aws/nyc-tlc-trip-records-pds/) dataset. This dataset is available in the [Open Data on AWS (RODA)](https://registry.opendata.aws/)which is a repository containing public datasets from AWS resources. The raw Parquet table records in this dataset stores trip records.

- The objective is to create the following three tables, which contain metrics based on the raw table:
    + **silver_avg_metrics** – Basic metrics based on NYC Taxi Open Data for the year 2016
    + **gold_passengers_metrics** – Metrics per passenger based on the silver metrics table
    + **gold_cost_metrics** – Metrics per cost based on the silver metrics table

The final goal is to create two well-designed gold tables that store already aggregated results in Iceberg format for ad hoc queries through **Amazon Athena**.

## Prerequisites

+ Prepare an **IAM Role** with permissions to run **Glue interactive session** and **dbt-glue**
+ Create a Glue database and tables for the Taxi dataset

+ Create an S3 bucket for output data

+ Configure Athena to explore the data

+ Use CloudFormation to deploy all infrastructure quickly

With these prerequisites, we simulate the situation that data engineers have already ingested data from data sources to raw tables, and defined table definitions for the raw tables.

For ease of use, we prepared a CloudFormation template. This template deploys all the required infrastructure. To create these resources, choose Launch Stack in the ```us-east-1``` RegionRegion.
## Detailed steps: 
**1. Install dbt, the dbt CLI** : ```$ pip3 install --no-cache-dir dbt-core ```
**2.  Install the dbt-glue adapter** :```$ pip3 install --no-cache-dir dbt-glue```
**3. Initialize a dbt project with** with ```$ dbt init``` and enter the name ```dbt_glue_demo```. Choose the ```glue```database. A new empty project will be created: 
```
$ cd dbt_glue_demo 
$ tree .
.
├── README.md
├── analyses
├── dbt_project.yml
├── macros
├── models
│   └── example
│       ├── my_first_dbt_model.sql
│       ├── my_second_dbt_model.sql
│       └── schema.yml
├── seeds
├── snapshots
└── tests
```
**4. Create a source**: In ```models/```create the file ```source_tables.yml```:
```
version: 2

sources:
  - name: data_source
    schema: nyctaxi

    tables:
      - name: records
```

This source definition corresponds to the AWS Glue table  ```nyctaxi.records```, which we created in the CloudFormation stack.

**5. Create models**

In this step, we create a dbt model that represents the average values for trip duration, passenger count, trip distance, and total amount of charges. Complete the following steps:
- Create the **silver** folder under **models**, then create the file **silver_avg_metrics.sql** with the following content:
```
WITH source_avg as ( 
    SELECT avg((CAST(dropoff_datetime as LONG) - CAST(pickup_datetime as LONG))/60) as avg_duration 
    , avg(passenger_count) as avg_passenger_count 
    , avg(trip_distance) as avg_trip_distance 
    , avg(total_amount) as avg_total_amount
    , year
    , month 
    , type
    FROM {{ source('data_source', 'records') }} 
    WHERE year = "2016"
    AND dropoff_datetime is not null 
    GROUP BY year, month, type
) 
SELECT *
FROM source_avg
```
- Create the file models/silver/schema.yml with the following contents:
```
version: 2

models:
  - name: silver_avg_metrics
    description: This table has basic metrics based on NYC Taxi Open Data for the year 2016

    columns:
      - name: avg_duration
        description: The average duration of a NYC Taxi trip

      - name: avg_passenger_count
        description: The average number of passenger per NYC Taxi trip

      - name: avg_trip_distance
        description: The average NYC Taxi trip distance

      - name: avg_total_amount
        description: The avarage amount of a NYC Taxi trip

      - name: year
        description: The year of the NYC Taxi trip

      - name: month
        description: The month of the NYC Taxi trip 

      - name: type
        description: The type of the NYC Taxi 
```
- Create a **gold** folder under **models**, and inside it create the file **gold_cost_metrics.sql**: 
```
{{ config(
    materialized='incremental',
    incremental_strategy='merge',
    unique_key=["year", "month", "type"],
    file_format='iceberg',
    iceberg_expire_snapshots='False',
    table_properties={'format-version': '2'}
) }}
SELECT (avg_total_amount/avg_trip_distance) as avg_cost_per_distance
, (avg_total_amount/avg_duration) as avg_cost_per_minute
, year
, month 
, type 
FROM {{ ref('silver_avg_metrics') }}
```
- Create the file **models/gold/gold_passengers_metrics.sql**  with the following contents: 
```
{{ config(
    materialized='incremental',
    incremental_strategy='merge',
    unique_key=["year", "month", "type"],
    file_format='iceberg',
    iceberg_expire_snapshots='False',
    table_properties={'format-version': '2'}
) }}
SELECT (avg_total_amount/avg_passenger_count) as avg_cost_per_passenger
, (avg_duration/avg_passenger_count) as avg_duration_per_passenger
, (avg_trip_distance/avg_passenger_count) as avg_trip_distance_per_passenger
, year
, month 
, type 
FROM {{ ref('silver_avg_metrics') }}
```
- Create the file models/gold/schema.yml with the following contents:
```
version: 2

models:
  - name: gold_cost_metrics
    description: This table has metrics per cost based on NYC Taxi Open Data

    columns:
      - name: avg_cost_per_distance
        description: The average cost per distance of a NYC Taxi trip

      - name: avg_cost_per_minute
        description: The average cost per minute of a NYC Taxi trip

      - name: year
        description: The year of the NYC Taxi trip

      - name: month
        description: The month of the NYC Taxi trip

      - name: type
        description: The type of the NYC Taxi

  - name: gold_passengers_metrics
    description: This table has metrics per passenger based on NYC Taxi Open Data

    columns:
      - name: avg_cost_per_passenger
        description: The average cost per passenger for a NYC Taxi trip

      - name: avg_duration_per_passenger
        description: The average number of passenger per NYC Taxi trip

      - name: avg_trip_distance_per_passenger
        description: The average NYC Taxi trip distance

      - name: year
        description: The year of the NYC Taxi trip

      - name: month
        description: The month of the NYC Taxi trip 

      - name: type
        description: The type of the NYC Taxi
```
- Remove the **models/example/** folder, because it’s just an example created in the  ```dbt init``` command.

**6. Configure the dbt project**

-dbt_project.yml is a key configuration file for dbt projects. It contains the following code:
```
models:
  dbt_glue_demo:
    # Config indicated by + and applies to all files under models/example/
    example:
      +materialized: view
```
- We configure **dbt_project.yml** to replace the preceding code with the following: 
```
models:
  dbt_glue_demo:
    silver:
      +materialized: table

```
This is because that we want to materialize the models under **silver** as Parquet tables.
**7. Configure a dbt profile**

**dbt profile** is a configuration that specifies how to connect to a particular database. The profiles are defined in the **profiles.yml** file within a dbt project.

Complete the following steps to configure a dbt profile:

1. Create the ```profiles``` directory. 
2. Create the file ```profiles/profiles.yml``` with the following contents:
    ```
    dbt_glue_demo:
    target: dev
    outputs:
        dev:
        type: glue
        query-comment: demo-nyctaxi
        role_arn: "{{ env_var('DBT_ROLE_ARN') }}"
        region: us-east-1
        workers: 5
        worker_type: G.1X
        schema: "dbt_glue_demo_nyc_metrics"
        database: "dbt_glue_demo_nyc_metrics"
        session_provisioning_timeout_in_seconds: 120
        location: "{{ env_var('DBT_S3_LOCATION') }}"
      ```
3. Create the ```profiles/iceberg/``` directory..
4. Create the file  ```profiles/iceberg/profiles.yml``` with the following contents:

    ```
    dbt_glue_demo:
    target: dev
    outputs:
        dev:
            type: glue
            query-comment: demo-nyctaxi
            role_arn: "{{ env_var('DBT_ROLE_ARN') }}"
            region: us-east-1
            workers: 5
            worker_type: G.1X
            schema: "dbt_glue_demo_nyc_metrics"
            database: "dbt_glue_demo_nyc_metrics"
            session_provisioning_timeout_in_seconds: 120
            location: "{{ env_var('DBT_S3_LOCATION') }}"
            datalake_formats: "iceberg"
            conf: spark.sql.catalog.glue_catalog=org.apache.iceberg.spark.SparkCatalog --conf spark.sql catalog.glue_catalog.warehouse={{ env_var('DBT_S3_LOCATION') }}warehouse/ --conf spark.sql.catalog.glue_catalog.catalog-impl=org.apache.iceberg.aws.glue.GlueCatalog --conf spark.sql.catalog.glue_catalog.io-impl=org.apache.iceberg.aws.s3.S3FileIO --conf spark.sql.extensions=org.apache.iceberg.spark.extensions.IcebergSparkSessionExtensions
      ```
The last two lines are added for setting Iceberg configurations on AWS Glue interactive sessions.

**8. Run the dbt project**

Run the dbt project

1. To run the project dbt, you should be in the project folder: ``$ cd dbt_glue_demo```
2. The project requires you to set environment variables in order to run on the AWS account:
```
$ export DBT_ROLE_ARN="arn:aws:iam::$(aws sts get-caller-identity --query "Account" --output text):role/GlueInteractiveSessionRole"
$ export DBT_S3_LOCATION="s3://aws-dbt-glue-datalake-$(aws sts get-caller-identity --query "Account" --output text)-us-east-1"
```
3. Make sure the profile is set up correctly from the command line:
```
$ dbt debug --profiles-dir profiles
...
05:34:22 Connection test: [OK connection ok]
05:34:22 All checks passed!
```
If you see any failures, check if you provided the correct IAM role ARN and S3 location in Step 2.
4. Run the models with the following code:
```
$ dbt run -m silver --profiles-dir profiles
$ dbt run -m gold --profiles-dir profiles/iceberg/
```
Now the tables are successfully created in the SageMaker Lakehouse Catalog, and the data is materialized in the Amazon S3 location.

You can verify those tables by opening the AWS Glue console, choosing **Databases** in the navigation pane, and opening ```dbt_glue_demo_nyc_metrics```.
![check db](/images/3-Blogstranslated/image-2.png)

## Query materialized tables through Athena

Let’s query the target table using Athena to verify the materialized tables. Complete the following steps:

1. On the Athena console, switch the workgroup to ```athena-dbt-glue-aws-blog```.

2. If the workgroup ```athena-dbt-glue-aws-blog``` settings dialog box appears, choose Acknowledge.

3. Use the following query to explore the metrics created by the dbt project:
```
SELECT cm.avg_cost_per_minute
    , cm.avg_cost_per_distance
    , pm.avg_cost_per_passenger
    , cm.year
    , cm.month
    , cm.type
FROM "dbt_glue_demo_nyc_metrics"."gold_passengers_metrics" pm
LEFT JOIN "dbt_glue_demo_nyc_metrics"."gold_cost_metrics" cm
    ON cm.type = pm.type
    AND cm.year = pm.year
    AND cm.month = pm.month
WHERE cm.type = 'yellow'
    AND cm.year = '2016'
    AND cm.month = '6'
```
The following screenshot shows the results of this query.
![result-query](/images/3-Blogstranslated/image-3.png)

## The following screenshot shows the results of this query.

Complete the following steps to review your documentation:

1. Generate the following documentation for the project:
```
$ dbt docs generate --profiles-dir profiles/iceberg
11:41:51  Running with dbt=1.7.1
11:41:51  Registered adapter: glue=1.7.1
11:41:51  Unable to do partial parsing because profile has changed
11:41:52  Found 3 models, 1 source, 0 exposures, 0 metrics, 478 macros, 0 groups, 0 semantic models
11:41:52  
11:41:53  Concurrency: 1 threads (target='dev')
11:41:53  
11:41:53  Building catalog
11:43:32  Catalog written to /Users/username/Documents/workspace/dbt_glue_demo/target/catalog.json
```

2. Run the following command to open the documentation on your browser:
```$ dbt docs serve --profiles-dir profiles/iceberg```
![open doc](/images/3-Blogstranslated/image-4.png)

3. In the navigation pane, choose  ```gold_cost_metrics``` under ```dbt_glue_demo/models/gold```.
You can see the detailed view of the model ```gold_cost_metrics```,  as shown in the following screenshot.
![model](/images/3-Blogstranslated/image-5.png)

4. To see the lineage graph, choose the circle icon at the bottom right.

![circle icon](/images/3-Blogstranslated/image-6.png)

## Clean up

To clean up your environment, complete the following steps:

1. Delete the database created by dbt:
```$ aws glue delete-database —name dbt_glue_demo_nyc_metrics```

2. Delete all generated data:
```
$ aws s3 rm s3://aws-dbt-glue-datalake-$(aws sts get-caller-identity —query "Account" —output text)-us-east-1/ —recursive
$ aws s3 rm s3://aws-athena-dbt-glue-query-results-$(aws sts get-caller-identity —query "Account" —output text)-us-east-1/ —recursive
```

3. Delete the CloudFormation stack:
```$ aws cloudformation delete-stack —stack-name dbt-demo```
## Conclusion

This post demonstrated how the dbt-glue adapter helps your workload, and how you can build a modern data stack using dbt and AWS Glue using the dbt-glue adapter. You learned the end-to-end operations and data flow for data engineers to build and manage a data stack using dbt and the dbt-glue adapter.
