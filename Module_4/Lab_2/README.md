# Lab 02 - Dataflow: Qwik Start - Templates

## Lab Summary

This lab focused on creating a streaming data pipeline using a Google-provided Dataflow template. The lab demonstrated how to use the Pub/Sub to BigQuery template to read JSON messages from a public Pub/Sub topic in real time and write them into a BigQuery table, using a combination of Cloud Shell commands and BigQuery SQL queries to provision resources and validate the resulting streamed data.

## Lab Objective

The objective of this lab was to learn how to:

- Create a BigQuery dataset and table.
- Create a Cloud Storage bucket.
- Create a streaming pipeline using the Pub/Sub to BigQuery Dataflow template.
- Query streamed data using standard SQL in BigQuery.

## Tools & Services Used

- Google Cloud Platform (GCP)
- Dataflow (Google-provided templates)
- Pub/Sub
- BigQuery
- Cloud Storage
- Cloud Shell / gcloud CLI / bq CLI / gsutil

## Key Steps Performed

## 1. Re-enabled the Dataflow API

Reset the Dataflow API by disabling and re-enabling it for the project, to ensure a clean API connection:

```bash
gcloud services disable dataflow.googleapis.com --project Project ID --force
gcloud services enable dataflow.googleapis.com --project Project ID
```

## 2. Created a BigQuery Dataset

Created a BigQuery dataset named `taxirides` using the `bq` command-line tool:

```bash
bq mk taxirides
```

```text
Dataset '<myprojectid:taxirides>' successfully created
```

## 3. Created a BigQuery Table

Created a `realtime` table within the `taxirides` dataset, partitioned by the `timestamp` field, with a comma-separated schema definition:

```bash
bq mk \
--time_partitioning_field timestamp \
--schema ride_id:string,point_idx:integer,latitude:float,longitude:float,\
timestamp:timestamp,meter_reading:float,meter_increment:float,ride_status:string,\
passenger_count:integer -t taxirides.realtime
```

```text
Table 'myprojectid:taxirides.realtime' successfully created
```

The schema parameter accepted either a path to a local JSON schema file or, as used here, a comma-separated list of `field:data_type` definitions.

## 4. Created a Cloud Storage Bucket

Created a globally unique Cloud Storage bucket using the Project ID as the bucket name:

```bash
export BUCKET_NAME="Bucket Name"
gsutil mb gs://$BUCKET_NAME/
```

This bucket was later used as the staging location for the Dataflow pipeline.

## 5. Deployed the Dataflow Pipeline

Ran the Pub/Sub to BigQuery Dataflow template to deploy a streaming pipeline named `iotflow`:

```bash
gcloud dataflow jobs run iotflow \
    --gcs-location gs://dataflow-templates-"Region"/latest/PubSub_to_BigQuery \
    --region "Region" \
    --worker-machine-type e2-medium \
    --staging-location gs://"Bucket Name"/temp \
    --parameters inputTopic=projects/pubsub-public-data/topics/taxirides-realtime,outputTableSpec="Table Name":taxirides.realtime
```

The pipeline configuration specified:

- The Google-provided template location (`PubSub_to_BigQuery`) in the regional templates bucket.
- The deployment region and worker machine type (`e2-medium`).
- A staging location in the newly created Cloud Storage bucket.
- The source Pub/Sub topic (`taxirides-realtime`, a public dataset) and the destination BigQuery table (`taxirides.realtime`).

Verified the running job by navigating to:

```text
Navigation menu > Analytics > Dataflow > Jobs
```

## 6. Verified Streamed Data in BigQuery

Opened BigQuery from the Navigation menu and confirmed that the `taxirides` dataset and `realtime` table were populated as the pipeline streamed data from Pub/Sub into BigQuery (allowing a few minutes for data to populate).

## 7. Submitted a Query

Queried the streamed taxi ride data using standard SQL in the BigQuery editor:

```sql
SELECT * FROM `"Bucket Name".taxirides.realtime` LIMIT 1000
```

Ran the query and confirmed 1000 rows of taxi ride data were returned in the Query Results panel, validating that messages had flowed successfully from the public Pub/Sub topic, through the Dataflow streaming pipeline, into the BigQuery table.

## Evidence/Screenshots

Screenshots/Evidence of work are provided in the screenshot folder.

## Results

- Successfully re-enabled the Dataflow API for the project.
- Created a BigQuery dataset (`taxirides`) and a time-partitioned table (`realtime`) with a defined schema.
- Created a Cloud Storage bucket for pipeline staging.
- Deployed a streaming Dataflow pipeline using the Google-provided Pub/Sub to BigQuery template.
- Verified that the pipeline successfully streamed real-time taxi ride data from a public Pub/Sub topic into BigQuery.
- Queried the resulting table with standard SQL and confirmed 1000 rows of streamed data.

## Reflection

This lab provided practical experience with Google Cloud's managed streaming data pipeline tooling. I learned how Dataflow's Google-provided templates remove the need to write custom pipeline code for common patterns, since the Pub/Sub to BigQuery template handled schema mapping and write operations out of the box once the destination dataset, table, and staging bucket were correctly provisioned. The exercise also reinforced how Pub/Sub, Dataflow, and BigQuery fit together in a typical streaming analytics architecture: a topic as the real-time ingestion source, Dataflow as the processing and transformation layer, and BigQuery as the queryable analytical sink. Running the final SQL query against live streamed data made the end-to-end flow of the pipeline concrete and verifiable.
