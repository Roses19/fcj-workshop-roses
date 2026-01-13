---
title : "Quá trình thu thập dữ liệu"
date : 2026-01-01 
weight : 2
chapter : false
pre : " <b> 5.3.2 </b> "
---

#### Kết nối Reddit API

Hệ thống sử dụng Reddit API thông qua ứng dụng Developer loại script để thu thập bài viết và bình luận từ các subreddit như dataengineering.

Airflow sử dụng thư viện PRAW để: Xác thực OAuth, truy xuất dữ liệu và tuân thủ giới hạn requests

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

+ DAG chạy thành công
![Check DAG](/images/5-Workshop/5.3-Ingestion/DAG.png)

#### Xử lý dữ liệu Reddit

- Các trường dữ liệu bao gồm: id, title, score, num_comments, author,
 created_utc, url, over_18, edited , spoiler, stickied
- Hàm **extract_posts** được sử dụng để truy vấn Reddit API thông qua thư viện PRAW và lấy danh sách các bài viết từ một subreddit theo bộ lọc thời gian.
```
def extract_posts (reddit_instance: Reddit, subreddit:str, time_filter:str, limit = None):
    subreddit = reddit_instance.subreddit(subreddit)
    posts = subreddit.top(time_filter = time_filter, limit=limit)

    post_lists = []

    for post in posts: 
        post_dict = vars(post)
        
        post = {key: post_dict[key] for key in POST_FIELDS}
        post_lists.append(post)

    return post_lists
```
+ Mỗi bài viết được chuyển thành dạng dictionary và chỉ giữ lại các trường cần thiết nhằm giảm kích thước dữ liệu và tối ưu cho quá trình xử lý.

- Hàm **transform_data** thực hiện chuẩn hóa dữ liệu trước khi lưu vào Data Lake. 
```
def transform_data (post_df: pd.DataFrame):
    post_df['created_utc'] = pd.to_datetime(post_df.get('created_utc', None),unit='s',errors='coerce')
    post_df['over_18'] = post_df.get('over_18', False)
    post_df['over_18'] = post_df['over_18'].fillna(False).astype(bool)
    post_df['author'] = post_df['author'].astype(str)
    edited_mode = post_df['edited'].mode()
    post_df['edited'] = np.where(post_df['edited'].isin([True, False]),
                                 post_df['edited'],edited_mode).astype(bool)
    post_df['num_comments'] = post_df['num_comments'].astype(int)
    post_df['score'] = post_df['score'].astype(int)
    post_df['title'] = post_df['title'].astype(str)
    return post_df
```
#### Tóm tắt

Chúc mừng bạn đã hoàn thành thu thập dữ liệu. Trong phần này, dữ liệu đã được thu thập và chuẩn hóa trước khi đưa vào Data Lake (dữ liệu thô sang dạng bảng có cấu trúc, sẵn sàng cho việc lưu trữ trên Amazon S3 và phân tích bằng Athena và Redshift trong các bước tiếp theo của pipeline).
![Task extraction](/images/5-Workshop/5.3-Ingestion/extract.png)