# Lab 07 - Managed Service for Apache Spark: Qwik Start (Command Line)

## Lab Summary

This lab focused on using the gcloud command line to create and manage a Managed Service for Apache Spark cluster. The lab demonstrated how to provision a Spark/Hadoop cluster, grant the necessary IAM permissions to the default Compute Engine service account, submit a sample Spark job to calculate Pi, and dynamically scale the number of worker nodes in the cluster.

## Lab Objective

The objective of this lab was to learn how to:

- Create a Managed Apache Spark cluster using the command line.
- Run a simple Apache Spark job.
- Modify the number of workers in the cluster.

## Tools & Services Used

- Google Cloud Platform (GCP)
- Managed Service for Apache Spark (Dataproc)
- Cloud Shell / gcloud CLI
- IAM Roles and Service Accounts
- Cloud Storage (staging/temp buckets)
- VPC Networking (Private Google Access)

## Key Steps Performed

## 1. Configured the Region

Set the default region for Managed Apache Spark operations:

```bash
gcloud config set dataproc/region Region
```

## 2. Reset the Dataproc API

Disabled and re-enabled the Cloud Dataproc API to ensure a clean API state:

```bash
gcloud services disable dataproc.googleapis.com --force
gcloud services enable dataproc.googleapis.com
```

## 3. Granted IAM Permissions to the Default Service Account

Since no custom service account was specified, the cluster uses the Compute Engine default service account, which does not have storage bucket permissions by default. Retrieved the project ID and project number:

```bash
PROJECT_ID=$(gcloud config get-value project) && \
gcloud config set project $PROJECT_ID

PROJECT_NUMBER=$(gcloud projects describe $PROJECT_ID --format='value(projectNumber)')
```

Granted the **Storage Admin** and **Dataproc Worker** roles to the default Compute Engine service account:

```bash
gcloud projects add-iam-policy-binding $PROJECT_ID \
  --member=serviceAccount:$PROJECT_NUMBER-compute@developer.gserviceaccount.com \
  --role=roles/storage.admin

gcloud projects add-iam-policy-binding $PROJECT_ID \
  --member=serviceAccount:$PROJECT_NUMBER-compute@developer.gserviceaccount.com \
  --role=roles/dataproc.worker
```

These roles allow the cluster to read/write to the shared staging and temp buckets used by Managed Apache Spark in the region.

## 4. Enabled Private Google Access

Enabled Private Google Access on the default subnetwork so cluster VMs without external IPs can still reach Google APIs:

```bash
gcloud compute networks subnets update default --region=REGION --enable-private-ip-google-access
```

## 5. Created the Cluster

Created a Managed Apache Spark cluster named `example-cluster` using `e2-standard-4` machines for both master and worker nodes:

```bash
gcloud dataproc clusters create example-cluster \
  --worker-boot-disk-size 500 \
  --worker-machine-type=e2-standard-4 \
  --master-machine-type=e2-standard-4
```

Confirmed the zone selection prompt with `Y` and waited for cluster creation to complete:

```text
Waiting for cluster creation operation...done.
Created [... example-cluster]
```

## 6. Submitted a Spark Job

Submitted a sample Spark job that estimates the value of Pi using the `SparkPi` example class:

```bash
gcloud dataproc jobs submit spark --cluster example-cluster \
  --class org.apache.spark.examples.SparkPi \
  --jars file:///usr/lib/spark/examples/jars/spark-examples.jar -- 1000
```

The command specified:

- The target cluster (`example-cluster`) to run the job on.
- The main class containing the Pi-calculating application logic.
- The location of the JAR file containing the job's code.
- A job parameter (`1000` tasks) passed after the double-dash (`--`) separator.

Observed the job output as it ran to completion:

```text
Waiting for job output...
...
Pi is roughly 3.14118528
...
state: FINISHED
```

## 7. Updated the Cluster (Scaling Workers)

Scaled the cluster up to four worker nodes:

```bash
gcloud dataproc clusters update example-cluster --num-workers 4
```

```text
Waiting on operation [projects/.../regions/global/operations/...].
Waiting for cluster update operation...done.
```

Scaled the cluster back down to two worker nodes using the same command pattern:

```bash
gcloud dataproc clusters update example-cluster --num-workers 2
```

This confirmed that cluster size can be adjusted on demand from the command line without recreating the cluster.

## Evidence/Screenshots

Screenshots/Evidence of work are provided in the screenshot folder.

## Results

- Successfully created a Managed Service for Apache Spark cluster (`example-cluster`) via the gcloud command line.
- Granted the Compute Engine default service account the IAM roles required for cluster operation (Storage Admin, Dataproc Worker).
- Enabled Private Google Access on the default subnetwork.
- Submitted and successfully ran a sample Spark job (`SparkPi`) that completed with state `FINISHED`.
- Dynamically scaled the cluster's worker node count up to 4 and back down to 2 using `gcloud dataproc clusters update`.

## Reflection

This lab provided hands-on, command-line-driven experience managing a Spark/Hadoop cluster on Google Cloud. I learned how Managed Service for Apache Spark relies on IAM permissions for its underlying service account to manage staging and temp storage buckets, and how Private Google Access allows cluster nodes to reach Google APIs without requiring external IP addresses. Submitting and monitoring a real Spark job reinforced how job parameters and JAR file locations are passed through the gcloud CLI, while the cluster update commands demonstrated how easily compute capacity can be scaled elastically to match workload demands without rebuilding the cluster from scratch.
