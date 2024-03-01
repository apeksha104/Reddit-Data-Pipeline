# Reddit-Data-Pipeline

## 🚀 Overview

This project establishes an end-to-end data engineering solution to orchestrate the extraction, transformation, and loading (ETL) of Reddit data into a Redshift data warehouse. Leveraging Apache Airflow, Celery, PostgreSQL, Amazon S3, AWS Glue, Amazon Athena, and Amazon Redshift, the pipeline seamlessly processes data from Reddit's API through various stages for analytical purposes.

## 🏗️ Architecture

![image](https://github.com/apeksha104/Reddit-Data-Pipeline/assets/52379747/74bc4502-07bb-4ad6-8554-c394d96f3bc6)


1. **Reddit API**: Serves as the primary data source.
2. **Apache Airflow & Celery**: Orchestrates the ETL process and efficiently manages task distribution.
3. **PostgreSQL**: Temporary storage for intermediate data and metadata management.
4. **Amazon S3**: Functions as the repository for raw data.
5. **AWS Glue**: Performs data cataloging and ETL jobs.
6. **Amazon Athena**: Facilitates SQL-based data transformation.
7. **Amazon Redshift**: Serves as the data warehousing solution for analytics and querying.

##️ 🛠️ Prerequisites

Ensure the following prerequisites are met before setting up the system:

- AWS Account with appropriate permissions for S3, Glue, Athena, and Redshift.
- Reddit API credentials.
- Docker Installation
- Python 3.9 or higher

## 🔧 System Setup

1. **Clone the repository:**
   ```bash
   git clone https://github.com/airscholar/RedditDataEngineering.git
   ```

2. **Create a virtual environment:**
   ```bash
   python3 -m venv venv
   ```

3. **Activate the virtual environment:**
   ```bash
   source venv/bin/activate
   ```

4. **Install the dependencies:**
   ```bash
   pip install -r requirements.txt
   ```

5. **Rename the configuration file and credentials:**
   ```bash
   mv config/config.conf.example config/config.conf
   ```

6. **Start the containers:**
   ```bash
   docker-compose up -d
   ```

7. **Launch the Airflow web UI:**
   ```bash
   open http://localhost:8080
   ```
### 🚀 Usage

**Reddit ETL:**
1. Install the required dependencies by running:
   ```bash
   pip install -r requirements.txt
   ```

2. Execute the `etl_reddit_pipeline` DAG in Apache Airflow using the provided DAG definition.

3. The `reddit_extraction` task within the DAG extracts Reddit posts from the specified subreddit (`dataengineering` by default) for the specified time period and limit.

4. The extracted data is then transformed using the `transform_data` function, applying necessary data type conversions.

5. The transformed data is loaded into a CSV file, named based on the execution timestamp, and stored in the specified output path.

**AWS ETL:**
1. Ensure you have valid AWS credentials with appropriate permissions.

2. Install the required dependencies by running:
   ```bash
   pip install -r requirements.txt
   ```

3. Execute the `etl_reddit_pipeline` DAG in Apache Airflow to run the `reddit_extraction` task.

4. The `upload_s3` task within the DAG uploads the CSV file containing Reddit data to an Amazon S3 bucket.

5. Ensure that the `AWS_ACCESS_KEY_ID` and `AWS_ACCESS_KEY` constants in the `utils.constants` module are appropriately set with your AWS credentials.

6. Run the `upload_s3_pipeline` script, which will connect to the specified S3 bucket and upload the CSV file to the 'raw' folder.

7. The Reddit data is now stored in the Amazon S3 bucket, ready for further processing and analytics.


### 📊 Data Source

**Reddit ETL:**
- The data source for this ETL process is Reddit, accessed through the Reddit API.
- The `connect_reddit` function establishes a connection to Reddit using the provided client ID, client secret, and user agent.
- The `extract_posts` function fetches Reddit posts from a specified subreddit using the established Reddit connection.

**AWS ETL:**
- The data source for the AWS ETL process is the output of the Reddit ETL stored in a CSV file.
- The `upload_s3_pipeline` function uploads the extracted Reddit data to an Amazon S3 bucket for further processing.

### 🔍 Data Processing

**Reddit ETL:**
- The `transform_data` function performs data transformation on the extracted Reddit posts.
  - Converts the 'created_utc' field to a datetime format.
  - Converts the 'over_18' field to a boolean.
  - Converts the 'edited' field to a boolean.
  - Converts 'num_comments' and 'score' to integer types.
  - Converts 'title' to a string.

**AWS ETL:**
- No specific data processing is performed in the AWS ETL pipeline; it focuses on uploading data to S3 for storage.

### 💾 Data Storage

**Reddit ETL:**
- The transformed Reddit data is stored in a CSV file using the `load_data_to_csv` function.
- The output CSV file is named based on the provided `file_name` parameter and is saved in the specified output path.

**AWS ETL:**
- The Reddit data is stored in an Amazon S3 bucket.
- The `connect_to_s3` function establishes a connection to the S3 bucket using the provided AWS access key ID and secret.
- The `create_bucket_if_not_exist` function checks if the specified bucket exists and creates it if not.
- The `upload_to_s3` function uploads the Reddit data CSV file to the 'raw' folder within the specified S3 bucket.

This data pipeline ensures a structured flow from Reddit data extraction to its transformation and storage in both a local CSV file and an Amazon S3 bucket for further processing and analytics.

## 🔄 Workflow Orchestration

The ETL workflow is orchestrated using Apache Airflow with the following tasks:

1. **Reddit Extraction Task (`reddit_extraction`):**
   - **Description:** Initiates the extraction of Reddit data using a custom Python function (`reddit_pipeline`).
   - **Parameters:**
     - `file_name`: The name of the output file for the extracted data (e.g., `reddit_20231022`).
     - `subreddit`: The specific subreddit from which data is extracted (e.g., `dataengineering`).
     - `time_filter`: The time filter for Reddit data extraction (e.g., `day`).
     - `limit`: The limit on the number of posts to extract (e.g., `100`).
   - **Functionality:** Utilizes the `reddit_pipeline` function to fetch data from the specified subreddit based on the given parameters. The extracted data is stored in a file with a timestamped postfix.

2. **S3 Upload Task (`s3_upload`):**
   - **Description:** Uploads the extracted Reddit data to an Amazon S3 bucket.
   - **Functionality:** Invokes the `upload_s3_pipeline` Python function, facilitating the transfer of the extracted data to an S3 bucket. The data is now available in the cloud storage, setting the stage for further processing.

The workflow ensures that the extraction from Reddit is followed by the seamless upload of the extracted data to Amazon S3. The schedule interval is set to daily (`@daily`), ensuring the workflow runs periodically.

This orchestrated workflow provides a structured and automated approach to the extraction and initial storage of Reddit data, facilitating subsequent processing stages.

## 📈 Monitoring

Monitoring is facilitated through the Airflow web UI, providing insights into task execution, logs, and overall pipeline health.

## 🤝 Contributing

Contribute to the project by submitting bug reports, feature requests, or code contributions. Follow the guidelines outlined in the [CONTRIBUTING.md](CONTRIBUTING.md) file.

## 📝 License

This project is licensed under the [MIT License](LICENSE).
