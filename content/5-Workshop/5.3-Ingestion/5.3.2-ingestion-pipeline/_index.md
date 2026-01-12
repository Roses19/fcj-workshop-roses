---
title : "Ingestion Pipeline"
date : 2026-01-01 
weight : 2
chapter : false
pre : " <b> 5.3.2 </b> "
---


#### Reddit API Connection

The system uses the Reddit API through a Developer script-type application to collect posts and comments from subreddits such as dataengineering.

Apache Airflow uses the PRAW library to handle: OAuth authentication, data retrievalretrieval, compliance with Reddit API request limits

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
#### Reddit ETL DAG in Airflow
- Apache Airflow is used to orchestrate the entire Reddit data collection and storage process.

- A DAG and a scheduled task are defined as follows:
    ```
    dag = DAG(  
        dag_id='etl_reddit_pipeline',
        default_args=default_args,
        schedule_interval='@daily',
        catchup=False,
        tags=['reddit', 'etl', 'pipeline']
    )
    ```
+ Docker is used to verify that the DAG is running successfully (the airflow-init service must be executed first to initialize the database and create the Airflow user).
![Check docker](/images/5-Workshop/5.3-Ingestion/docker.png)

+ DAG successfully executed
![Check DAG](/images/5-Workshop/5.3-Ingestion/DAG.png)

#### Reddit Data Processing
- The extracted data includes the following fields:
id, title, score, num_comments, author, created_utc, url, over_18, edited, spoiler, stickied

- The **extract_posts** function queries the Reddit API via PRAW and retrieves posts from a given subreddit based on a time filter.
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
+ Each post is converted into a dictionary and only the required fields are retained to reduce data size and optimize downstream processing.

- The **transform_data** function performs data normalization before storing it in the Data Lake.
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

This section completes the data ingestion phase. Reddit data has been successfully collected and normalized before being loaded into the Data Lake. The raw API data is transformed into structured tabular format, making it ready for storage on Amazon S3 and for querying through Athena and Redshift in the next stages of the pipeline.
![Task extraction](/images/5-Workshop/5.3-Ingestion/extract.png)