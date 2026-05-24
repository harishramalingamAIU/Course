# Lab 6 - Google Kubernetes Engine: Qwik Start

## Lab Summary

This lab focused on deploying and managing a containerized application using Google Kubernetes Engine (GKE). The lab demonstrated how to create a Kubernetes cluster, deploy an application container, expose the application using a Kubernetes Service, and finally delete the cluster after use.

## Lab Objective

The objective of this lab was to learn how to use Google Kubernetes Engine (GKE) to deploy, manage, and scale containerized applications using Kubernetes.

## Tools & Services Used

- Google Cloud Platform (GCP)
- Google Kubernetes Engine (GKE)
- Kubernetes
- Cloud Shell
- kubectl
- gcloud CLI
- Docker Containers
- Linux Commands

## Key Steps Performed

### 1. Configured Compute Region and Zone
- Opened Google Cloud Console.
- Activated Cloud Shell.
- Configured the default compute region and zone using gcloud commands.

```bash
gcloud config set compute/region REGION
gcloud config set compute/zone ZONE
```

### 2. Created a GKE Cluster
- Created a Kubernetes cluster named `lab-cluster`.
- Used e2-medium virtual machine instances for cluster nodes.

```bash
gcloud container clusters create --machine-type=e2-medium --zone=ZONE lab-cluster
```

- Verified that the cluster status was running successfully.

### 3. Configured Cluster Authentication
- Retrieved cluster authentication credentials using:

```bash
gcloud container clusters get-credentials lab-cluster
```

- Connected kubectl to the GKE cluster successfully.

### 4. Deployed an Application
- Created a Kubernetes deployment named `hello-server`.
- Used the sample container image:

```text
gcr.io/google-samples/hello-app:1.0
```

- Deployment command used:

```bash
kubectl create deployment hello-server --image=gcr.io/google-samples/hello-app:1.0
```

### 5. Exposed the Application
- Created a Kubernetes Service with LoadBalancer type.

```bash
kubectl expose deployment hello-server --type=LoadBalancer --port 8080
```

- Retrieved the external IP address using:

```bash
kubectl get service
```

- Accessed the deployed application through the browser using the external IP and port 8080.

### 6. Verified Application Output
- Confirmed that the application displayed:

```text
Hello, world!
```

- Verified version and hostname details from the deployed container.

### 7. Deleted the Cluster
- Removed the Kubernetes cluster after completing the deployment.

```bash
gcloud container clusters delete lab-cluster
```

- Confirmed successful cluster deletion.

## Evidence/Screenshots

Screenshots/Evidence of work are provided in the screenshot folder.

## Results

- Successfully created a Google Kubernetes Engine cluster.
- Configured Kubernetes authentication credentials.
- Deployed a containerized application to the cluster.
- Exposed the application using a Kubernetes LoadBalancer service.
- Accessed the application through a public external IP address.
- Deleted the cluster successfully after completing the lab.
- Learned basic Kubernetes deployment and container orchestration concepts.

## Reflection

This lab provided practical experience with Kubernetes and Google Kubernetes Engine. I learned how container orchestration works and how Kubernetes simplifies application deployment, scaling, and management. The lab also improved my understanding of deployments, services, external load balancing, and cluster management in cloud environments.