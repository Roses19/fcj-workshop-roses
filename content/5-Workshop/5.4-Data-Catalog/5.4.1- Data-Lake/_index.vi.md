---
title : "Lưu trữ vào Data Lake (Amazon S3)"
date : 2026-01-01
weight : 1
chapter : false
pre : " <b> 5.4.1 </b> "
---

Để lưu trữ dữ liệu một cách bền vững, bạn sẽ cần phải phân tầng lưu trữ tại S3

![Create folder](/images/5-Workshop/5.4-Data-Catalog/folderS3.png)

- Thực hiện kết nối với bucket, với các giá trị ```AWS_ACCESS_KEY_ID```, ```AWS_SECRET_KEY``` được cấu hình riêng tại file constants.py, đảm bảo bucket tồn tại và ghi dữ liệu vào bucket:
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
- Viết task Airflow để upload dữ liệu lên S3:
    ```
    upload_s3 = PythonOperator(
        task_id = 's3_upload',
        python_callable = upload_s3_pipeline,
        dag=dag
    )
        
    extract >> upload_s3
    ```
- Với python_callable là ```upload_s3_pipeline```:
    ```
    def upload_s3_pipeline(ti):
        file_path = ti.xcom_pull(task_ids= 'reddit_extraction',key = 'return_value')
        
        s3 =connect_to_s3()
        create_bucket_if_not_exist(s3, AWS_BUCKET_NAME)
        upload_to_s3(s3, AWS_BUCKET_NAME,file_path, file_path.split('/')[-1])
    ```
