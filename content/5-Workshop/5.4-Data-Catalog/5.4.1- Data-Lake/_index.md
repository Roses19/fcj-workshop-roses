---
title : "Data Lake Storage (Amazon S3)"
date : 2026-01-01
weight : 1
chapter : false
pre : " <b> 5.4.1 </b> "
---

To store data in a durable and scalable way, the S3 bucket is organized into multiple layers.

![Create folder](/images/5-Workshop/5.4-Data-Catalog/folderS3.png)
- The system connects to the S3 bucket using the credentials ```AWS_ACCESS_KEY_ID``` and ```AWS_SECRET_KEY```, which are defined in the constants.py file, ensure exist bucket and then writes data into the bucket:
    ```
    def connect_to_s3():
        try:
            s3= s3fs.S3FileSystem(anon=False,
                                key=AWS_ACCESS_KEY_ID,
                                secret=AWS_SECRET_KEY)
            return s3
        except Exception as e:
            print(e)
    def create_bucket_if_not_exist(s3:s3fs.S3FileSystem,bucket:str):
        try:
            if not s3.exists(bucket):
                s3.mkdir(bucket)
                print("Bucket created")
            else:
                print("Bucket already exists")
        except Exception as e:
            print("Error here")
            print(e)
    def upload_to_s3(s3:s3fs.S3FileSystem,bucket:str,file_path: str,s3_file_name: str):
        try:
            s3.put(file_path,bucket+'/raw/'+ s3_file_name)
            print("File upload to s3")
        except FileNotFoundError:
            print("The file was not found")
    ```
- An Airflow task is created to upload data to S3:
    ```
    upload_s3 = PythonOperator(
        task_id = 's3_upload',
        python_callable = upload_s3_pipeline,
        dag=dag
    )
        
    extract >> upload_s3
    ```
- The python_callable function ```upload_s3_pipeline``` performs the upload process:
    ```
    def upload_s3_pipeline(ti):
        file_path = ti.xcom_pull(task_ids= 'reddit_extraction',key = 'return_value')
        
        s3 =connect_to_s3()
        create_bucket_if_not_exist(s3, AWS_BUCKET_NAME)
        upload_to_s3(s3, AWS_BUCKET_NAME,file_path, file_path.split('/')[-1])
    ```




