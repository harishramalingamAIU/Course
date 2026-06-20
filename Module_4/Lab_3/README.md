# Lab 03 - Dataflow: Qwik Start - Python

## Lab Summary

This lab focused on setting up a Python development environment for Dataflow using the Apache Beam SDK and running an example word-count pipeline both locally and remotely. The lab demonstrated how to provision a Cloud Storage bucket for pipeline output, install Apache Beam inside an isolated Python container, execute a pipeline locally with the DirectRunner, and then run the same pipeline remotely on Dataflow using the DataflowRunner.

## Lab Objective

The objective of this lab was to learn how to:

- Create a Cloud Storage bucket to store results of a Dataflow pipeline.
- Install the Apache Beam SDK for Python.
- Run a Dataflow pipeline remotely.

## Tools & Services Used

- Google Cloud Platform (GCP)
- Dataflow (Apache Beam SDK for Python)
- Cloud Storage
- Docker
- Cloud Shell / gcloud CLI

## Key Steps Performed

## 1. Set the Region

Configured the default Compute region for the lab session:

```bash
gcloud config set compute/region "REGION"
```

## 2. Re-enabled the Dataflow API

Reset the Dataflow API connection by disabling and re-enabling it:

```bash
gcloud services disable dataflow.googleapis.com
gcloud services enable dataflow.googleapis.com
```

Verified the API was active by searching "Dataflow API" in the Cloud Console search bar and confirming the page showed the option to disable it (indicating it was already enabled).

## 3. Created a Cloud Storage Bucket

Created a multi-region Cloud Storage bucket to store the results of the Dataflow pipeline:

```text
Name:           ____-bucket
Location type:  Multi-region
Location:       us
```

Confirmed the "Public access will be prevented" prompt when it appeared.

## 4. Installed the Apache Beam SDK for Python

Launched a Python Docker container to ensure a supported Python version was used:

```bash
docker run -it -e DEVSHELL_PROJECT_ID=$DEVSHELL_PROJECT_ID python:3.12 /bin/bash
```

Inside the running container, installed the Apache Beam SDK with the GCP extras:

```bash
pip install 'apache-beam[gcp]'==2.67.0
```

Dependency-related warnings were returned during installation but were safe to ignore for the purposes of this lab.

## 5. Ran the Word Count Example Locally

Executed the built-in `wordcount` example pipeline locally using Beam's DirectRunner:

```bash
python -m apache_beam.examples.wordcount --output OUTPUT_FILE
```

Observed an informational message confirming the pipeline ran using the default local runner:

```text
INFO:root:Missing pipeline option (runner). Executing pipeline using the default runner: DirectRunner.
INFO:oauth2client.client:Attempting refresh to obtain initial access_token
```

Listed local files to locate the generated output file(s):

```bash
ls
```

Inspected the contents of the output file to view word counts:

```bash
cat <file name>
```

The output displayed each word found in the input text along with its frequency count.

## 6. Ran the Pipeline Remotely on Dataflow

Set an environment variable pointing to the previously created Cloud Storage bucket:

```bash
BUCKET=gs://<bucket name provided earlier>
```

Re-ran the `wordcount` example, this time targeting the DataflowRunner so the pipeline executed remotely on managed Dataflow workers:

```bash
python -m apache_beam.examples.wordcount --project $DEVSHELL_PROJECT_ID \
  --runner DataflowRunner \
  --staging_location $BUCKET/staging \
  --temp_location $BUCKET/temp \
  --output $BUCKET/results/output \
  --region "filled in at lab start" \
  --worker_machine_type=e2-standard-2
```

Re-ran the command as needed until the job started successfully, confirmed by the message:

```text
JOB_MESSAGE_DETAILED: Workers have started successfully.
```

## 7. Verified the Dataflow Job Succeeded

Navigated to:

```text
Navigation menu > Dataflow
```

Observed the `wordcount` job initially showing a status of **Running**, then opened the job to monitor its execution graph and logs until all pipeline stages completed and the status changed to **Succeeded**.

## 8. Verified the Output in Cloud Storage

Navigated to:

```text
Navigation menu > Cloud Storage
```

Opened the previously created bucket and confirmed the presence of `staging` and `results` directories. Opened the `results` folder and inspected the generated output file(s) to confirm the word counts produced by the remote pipeline run.

## Evidence/Screenshots

Screenshots/Evidence of work are provided in the screenshot folder.

## Results

- Successfully created a multi-region Cloud Storage bucket for Dataflow pipeline output.
- Installed the Apache Beam SDK for Python (`apache-beam[gcp]` 2.67.0) inside an isolated Python 3.12 Docker container.
- Ran the `wordcount` example pipeline locally using the DirectRunner and verified word count output.
- Ran the same `wordcount` example remotely using the DataflowRunner, with staging and temp locations pointed at the Cloud Storage bucket.
- Confirmed the remote Dataflow job reached a **Succeeded** status.
- Verified output files were correctly written to the bucket's `results` directory.

## Reflection

This lab provided practical experience with the full Apache Beam development workflow on Google Cloud, from local testing to remote, distributed execution. I learned how the same pipeline code can run unchanged against different runners simply by changing the `--runner` flag, switching from the local DirectRunner to the fully managed DataflowRunner. Using a Docker container to pin a supported Python version reinforced the importance of dependency and environment management when working with the Beam SDK. Running the pipeline remotely also clarified how staging and temp Cloud Storage locations support Dataflow's distributed worker model, and how job progress and final output can be verified through both the Dataflow monitoring UI and the resulting files in Cloud Storage.
