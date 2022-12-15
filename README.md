# **YouTube_Trending_Videos_Analysis**
## **Scenario:** 
### To analyze which Youtube genre videos are trending most based on factors such as views, likes, comments, region etc..

## **Data source**
###  From Kaggle, Trending YouTube videos statistics [Download from here](https://www.kaggle.com/datasets/datasnaek/youtube-new)
     - CSV: contains country wise trending videos per day. Each country’s data is in a separate file. (Columns - video title, channel title, publish time, tags,           views, likes and dislikes, description, and comment count.)
     - JSON: contains categories of each video in a country specific file 

## Project Architecture
![architecture](https://github.com/Chaithra8/YouTube_Trending_Videos_Analysis/blob/main/Project_Architecture.png)

## Workflow diagram
![Workflow](https://github.com/Chaithra8/YouTube_Trending_Videos_Analysis/blob/main/Worflow_diagram.png)

## Steps:
###  1. Download YouTube data from Kaggle.
  2. In AWS IAM account, Create S3 bucket to store raw CSV and JSON files.
  3. Upload datset from your mocal machine onto S3 bucket through AWS CLI. For command refer [local_CLI_S3.sh](https://github.com/Chaithra8/YouTube_Trending_Videos_Analysis/blob/main/local_CLI_S3.sh)
  4. Using AWS Glue crawler, build data catalog table for both CSV & JSON files. These glue tables will be stored inside defined database. Data inside Glue tables can      be verified by querying them using AWS Athena.
  5. JSON files are converted into Parquet files inorder to store them in the form of column row format. This transformation is done by using AWS Lambda, which gets        triggered when any JSON related actions are performed inside S3(like create, update etc.,).
     After conevrting into Parquet file, Lambda function directly stores this parquet file onto Cleansed S3 bucket & also creates Glue table.
     For Lambda code refer [Lambda_JSON_to_Parquet.py](https://github.com/Chaithra8/YouTube_Trending_Videos_Analysis/blob/main/Lambda_JSON_to_Parquet.py)
  6. CSV is converted into Parquet using Glue ETL Jobs which is again stored inside cleansed S3 bucket. Here ETL job will not create Glue tables hence it is done by        using Crawler.
     For ETL Job Script refer [ETL_CSV_to_Parquet.py](https://github.com/Chaithra8/YouTube_Trending_Videos_Analysis/blob/main/ETL_CSV_to_Parquet.py)
  7. For the purpose of analysis both these Paquet files are joined using Glue ETL built-in inner join tranformation. This saves joned parquet file in both Analytics        S3 bucket & Glue table in analytics database.
  8. Finally using AWS QuickSight, dataset inside analytics glue table is analysed by building dashboard as shown below.
![Dashboard](https://github.com/Chaithra8/YouTube_Trending_Videos_Analysis/blob/main/YT_Trending_Analysis_Dashboard.png)
  
## Conclusion:
### According to Analysis Dashboard, top 3 trending YouTube Genre based on views, likes, region and trending dates are: Music, People & Blogs and Entertainment.
  
