# Technical Challenges 

This section is for documenting the errors I faced and the solutions to them. 

This course was a great learning experience, and through making mistakes, and debugging errors I was able to really get hands-on experience with the tools almost like an internship. 



WORK IN PROGRESS

## Week 3 


Week 3 Errors: 

	• Error while reading table: external_yellow_tripdata, error message: input file is not in parquet format: 
		○ Solution Passing wrong file format

	• Also passing in the wrong file paths is common when mapping between airflow and GCP

	• Common** "When defining a table with an ExternalDataConfiguration, a schema must be present on either the Table or the ExternalDataConfiguration. If schema is present on both, the schemas must be the same. 
		○ Error most likely occurs when you're pointing to a particular file path, and it consists of files of variable schemas and google cloud doesn't have capability like AWS to merge the files of various schemas into one, and produce an enlarged schema based on that. 

So look into your files to see if there was csv mixed with parqet to analyze it. We assume the files we downloaded have a consistent schema. Then we look in the code and look at the original specification of the operators on GCP documentation GCP ExternalDataConfiguration was where the error occurred so we look that one up. 
		
We notice an autodetect one and so we pass a tag to auto-detect the schema and pass it . Oh look at that, it worked. 
		
	• "exceptions.NotFound: 404 Not Found: Dataset pivotal-surfer-336713:trips_data_all was not found in location europe-west3" (Sejal's dataset)
		○ We parameterized our BIGQUERY DATASET variable which is our external table, and forgot to change it in our DDL Create TBL Query which is used in task which creates a partitioned Query . Simply change and fix
	• google.api_core.exceptions.NotFound: 404 Not found: Table dtc-de-338723:trips_data_all.yellow_tripdata_external_table was not found in location us-west1

		○ Had retry from task by clearing the the last task, had a type "etable" and named improperly external table in bigquery re run the the create bigquery table task operator, from parquet files in storage and then create the partiitoned table in bigquery 
	• google.api_core.exceptions.BadRequest: 400 Error while reading table: trips_data_all.green_tripdata_external_table, error message: Parquet column 'ehail_fee' has type DOUBLE which does not match the target cpp_type INT64.
		○ Looking into the slack, someone did exploratory data analysis in jupyter and read top 10 rows into head and by loading one month at a time, it occurs in two of the files/months in 2019. It seems to be an issue that occurs when converting to parquet. By exploring logs and also omitting the wild card


		Sejal Vaidya  4 days ago
		Also, try by removing the wildcard portion *.parquet". And just specifying "gs://{bucket}/green/ alone. There was another issue reported where they were facing issues with the wildcard 


		Sejal Vaidya  4 days ago
		Hi,
		It’s possible that certain files, while being converted to parquet, are not retaining the original type for some fields (eg. 10.0 Decimal auto-converts to 10 INT).
		These files possibly need to be explicitly type-casted (individually) before pushing to GCS (sorry :disappointed: )
		If the number of files having this issue is quite large, I would also suggest uploading CSV’s directly (the way I did it in the video). And create BQ tables based on them. But please note you cannot have mixed formats/schema on a GCS path to create BQ tables upon (as the schema needs to be consistent).




### Week 4 

* Course overview
* Introduction to GCP
* Docker and docker-compose 
* Running Postgres locally with Docker
* Setting up infrastructure on GCP with Terraform
* Preparing the environment for the course
* Homework

[More details](week_1_basics_n_setup)


### [Week 2: Data ingestion](week_2_data_ingestion)

* Data Lake
* Workflow orchestration
* Setting up Airflow locally
* Ingesting data to GCP with Airflow
* Ingesting data to local Postgres with Airflow
* Moving data from AWS to GCP (Transfer service)
* Homework

[More details](week_2_data_ingestion)



### [Week 3: Data Warehouse](week_3_data_warehouse)

Goal: Structuring data into a Data Warehouse


* Data warehouse (BigQuery) (25 minutes)
    * What is a data warehouse solution
    * What is big query, why is it so fast, Cost of BQ,  (5 min)
    * Partitoning and clustering, Automatic re-clustering (10 min)
    * Pointing to a location in google storage (5 min)
    * Loading data to big query & PG (10 min) -- using Airflow operator?
    * BQ best practices
    * Misc: BQ Geo location, BQ ML 
    * Alternatives (Snowflake/Redshift)



### [Week 4: Analytics engineering](week_4_analytics_engineering/taxi_rides_ny/)

Goal: Transforming Data in DWH to Analytical Views


* Basics (15 mins)
    * What is DBT?
    * ETL vs ELT 
    * Data modeling
    * DBT fit of the tool in the tech stack
* Usage (Combination of coding + theory) (1:30-1:45 mins)
    * Anatomy of a dbt model: written code vs compiled Sources
    * Materialisations: table, view, incremental, ephemeral  
    * Seeds 
    * Sources and ref  
    * Jinja and Macros 
    * Tests  
    * Documentation 
    * Packages 
    * Deployment: local development vs production 
    * DBT cloud: scheduler, sources and data catalog (Airflow)
* Google data studio -> Dashboard
* Extra knowledge:
    * DBT cli (local)



### [Week 5: Batch processing](week_5_batch_processing)

Goal: 

* Distributed processing (Spark) (40 + ? minutes)
    * What is Spark, spark cluster (5 mins)
    * Explaining potential of Spark (10 mins)
    * What is broadcast variables, partitioning, shuffle (10 mins)
    * Pre-joining data (10 mins)
    * use-case
    * What else is out there (Flink) (5 mins)
* Extending Orchestration env (airflow) (30 minutes)
    * Big query on airflow (10 mins)
    * Spark on airflow (10 mins)


### [Week 6: Streaming](week_6_stream_processing)

* Basics
    * What is Kafka
    * Internals of Kafka, broker
    * Partitoning of Kafka topic
    * Replication of Kafka topic
* Consumer-producer
* Schemas (avro)
* Streaming
    * Kafka streams
* Kafka connect
* Alternatives (PubSub/Pulsar)




### [Week 7, 8 & 9: Project](project)

* Putting everything learned to practice

Duration: 2-3 weeks

* Focus
  *  Delta Lake/Lakehouse
    * Databricks
    * Apache iceberg
    * Apache hudi
  * Data mesh
  * KSQLDB
  * Streaming analytics
  * Mlops
  

## Overview

### Architecture diagram
<img src="images/architecture/arch_1.jpg"/>

### Technologies
* *Google Cloud Platform (GCP)*: Cloud-based auto-scaling platform by Google
  * *Google Cloud Storage (GCS)*: Data Lake
  * *BigQuery*: Data Warehouse
* *Terraform*: Infrastructure-as-Code (IaC)
* *Docker*: Containerization
* *SQL*: Data Analysis & Exploration
* *Airflow*: Pipeline Orchestration
* *DBT*: Data Transformation
* *Spark*: Distributed Processing
* *Kafka*: Streaming

