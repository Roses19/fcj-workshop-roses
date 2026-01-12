---
title : "Quá trình thu thập dữ liệu"
date : 2026-01-01 
weight : 2
chapter : false
pre : " <b> 5.3.2 </b> "
---

#### Kết nối Reddit API

Hệ thống sử dụng Reddit API thông qua ứng dụng Developer loại script để thu thập bài viết và bình luận từ các subreddit như dataengineering.

- Airflow sử dụng thư viện PRAW để: Xác thực OAuth, truy xuất dữ liệu và tuân thủ giới hạn requests

```
def connect_reddit(client_id, client_secret, user_agent) -> Reddit:
    try:
        reddit = praw.Reddit(client_id = client_id,
                             client_secret = client_secret,
                             user_agent = user_agent)
        print("Connect to Reddit")
        return reddit
    except Exception as e:
        print(e)
        sys.exit(1)
```
#### DAG Reddit ETL trong Airflow
- Airflow được sử dụng để điều phối toàn bộ quá trình thu thập và lưu trữ dữ liệu Reddit.

- Cần khai báo DAG và 1 taskchạy theo lịch:
```
dag = DAG(  
    dag_id='etl_reddit_pipeline',
    default_args=default_args,
    schedule_interval='@daily',
    catchup=False,
    tags=['reddit', 'etl', 'pipeline']
)
```
+ Chạy docker kiểm tra DAG chạy thành công không ( cần chạy dịch vụ airflow-init trước để khởi tạo database và tài khoản airflow)
![Check docker](/images/5-Workshop/5.3-Ingestion/docker.png)

#### Tóm tắt

Chúc mừng bạn đã hoàn thành truy cập S3 từ VPC. Trong phần này, bạn đã tạo gateway endpoint cho Amazon S3 và sử dụng AWS CLI để tải file lên. Quá trình tải lên hoạt động vì gateway endpoint cho phép giao tiếp với S3 mà không cần Internet gateway gắn vào "VPC Cloud". Điều này thể hiện chức năng của gateway endpoint như một đường dẫn an toàn đến S3 mà không cần đi qua pub    lic Internet.