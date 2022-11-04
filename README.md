# ETL-Processing-on-Google-Cloud-Using-Dataflow-and-BigQuery on Google Cloud
# Requirments:
 _Python
 
 _BigQuery
 
 _Apache Beam

## Create Cloud Storage Bucket
$ gsutil mb -c regional -l   gs://$PROJECT

Use the gsutil command to copy files into the Cloud Storage bucket you just created:

$ gsutil cp gs://spls/gsp290/data_files/usa_names.csv 
gs://$PROJECT/data_files/
  
$ gsutil cp gs://spls/gsp290/data_files/head_usa_names.csv 
gs://$PROJECT/data_files/

## Create the BigQuery Dataset
$ bq make lake

## Build a Dataflow Pipeline
we will create an append-only Dataflow which will ingest data into the BigQuery table.
![alt text](https://cdn.qwiklabs.com/xu2ZNOCZ0IgdlDNdPrmJN%2FDIpdFiPVPCSstHGOYHeDM%3D?raw=true)

## Data Ingestion
You will now build a Dataflow pipeline with a TextIO source and a BigQueryIO destination to ingest data into BigQuery. More specifically, it will:

-Ingest the files from Cloud Storage.

-Filter out the header row in the files.

-Convert the lines read to dictionary objects.

-Output the rows to BigQuery.

The Dataflow job in this lab requires Python3.7. To ensure you're on the proper version, you will run the process on a Python 3.7 Docker container.

$ docker run -it -e PROJECT=$PROJECT -v $(pwd)/dataflow-python-examples:/dataflow python:3.7 /bin/bash

install the apach-beam :

$ pip install apache-beam[gcp]==2.24.0

cd dataflow/

$ python dataflow_python_examples/data_ingestion.py \
  --project=$PROJECT --region= \
  --runner=DataflowRunner \
  --staging_location=gs://$PROJECT/test \
  --temp_location gs://$PROJECT/test \
  --input gs://$PROJECT/data_files/head_usa_names.csv \
  --save_main_session
  
  ## Data Transformation:
 $python dataflow_python_examples/data_transformation.py \
  --project=$PROJECT \
  --region= \
  --runner=DataflowRunner \
  --staging_location=gs://$PROJECT/test \
  --temp_location gs://$PROJECT/test \
  --input gs://$PROJECT/data_files/head_usa_names.csv \
  --save_main_session
  
 ## Data lake to Mart
Now build a Dataflow pipeline that reads data from 2 BigQuery data sources, and then joins the data sources. Specifically, you:

_Ingest files from 2 BigQuery sources.

_Join the 2 data sources.

_Filter out the header row in the files.

_Convert the lines read to dictionary objects.

_Output the rows to BigQuery.

$ python dataflow_python_examples/data_lake_to_mart.py \
  --worker_disk_type="compute.googleapis.com/projects//zones//diskTypes/pd-ssd" \
  --max_num_workers=4 \
  --project=$PROJECT \
  --runner=DataflowRunner \
  --staging_location=gs://$PROJECT/test \
  --temp_location gs://$PROJECT/test \
  --save_main_session \
  --region=
