# Lab 3 - APIs Explorer: Qwik Start

## Lab Summary

This lab focused on understanding Application Programming Interfaces (APIs), API architecture, authentication mechanisms, and practical interaction with Google Cloud APIs. The lab demonstrated how to enable APIs, authenticate using OAuth 2.0, create Cloud Storage buckets through the Cloud Storage JSON/REST API, and upload files programmatically using HTTP requests.

## Lab Objective

The objective of this lab was to learn the fundamentals of APIs, RESTful communication, API authentication methods, and how to interact directly with Google Cloud services using the Cloud Storage JSON/REST API.

## Tools & Services Used

- Google Cloud Platform (GCP)
- Cloud Storage JSON/REST API
- API Library
- OAuth 2.0 Playground
- Cloud Shell
- curl
- gcloud CLI
- JSON
- HTTP Protocol
- REST APIs

## Key Steps Performed

### 1. Configured the Project Region

- Opened Google Cloud Console.
- Activated Cloud Shell.
- Configured the project region.

```bash
gcloud config set compute/region REGION
```

### 2. Explored the API Library

- Opened **APIs & Services > Library**.
- Searched for the **Fitness API**.
- Enabled the Fitness API.
- Reviewed API documentation, quotas, and usage information.

### 3. Created a JSON Configuration File

- Created a JSON file named `values.json`.

```bash
nano values.json
```

- Added bucket configuration details.

```json
{
  "name": "PROJECT_ID-bucket",
  "location": "US",
  "storageClass": "MULTI_REGIONAL"
}
```

- Saved and exited the Nano editor.

### 4. Generated OAuth 2.0 Credentials

- Opened the OAuth 2.0 Playground.
- Selected **Cloud Storage API V1**.
- Authorized access using:

```text
https://www.googleapis.com/auth/devstorage.full_control
```

- Generated an OAuth access token.
- Copied the access token for API authentication.

### 5. Configured Environment Variables

- Stored the OAuth token in an environment variable.

```bash
export OAUTH2_TOKEN=<YOUR_TOKEN>
```

- Stored the current project ID.

```bash
export PROJECT_ID=$(gcloud config get-value project)
```

### 6. Created a Cloud Storage Bucket Using the JSON/REST API

- Sent an authenticated HTTP POST request using curl.

```bash
curl -X POST --data-binary @values.json \
-H "Authorization: Bearer $OAUTH2_TOKEN" \
-H "Content-Type: application/json" \
"https://www.googleapis.com/storage/v1/b?project=$PROJECT_ID"
```

- Successfully created a Cloud Storage bucket using the REST API.
- Verified bucket creation in Cloud Storage.

### 7. Uploaded an Image to Cloud Storage

- Uploaded the sample image `demo-image.png` to Cloud Shell.
- Retrieved the image path.

```bash
realpath demo-image.png
```

- Stored the image path in an environment variable.

```bash
export OBJECT=<DEMO_IMAGE_PATH>
```

- Stored the bucket name in an environment variable.

```bash
export BUCKET_NAME=PROJECT_ID-bucket
```

### 8. Uploaded the File Using the Cloud Storage JSON/REST API

- Sent an authenticated HTTP POST request to upload the image.

```bash
curl -X POST --data-binary @$OBJECT \
-H "Authorization: Bearer $OAUTH2_TOKEN" \
-H "Content-Type: image/png" \
"https://www.googleapis.com/upload/storage/v1/b/$BUCKET_NAME/o?uploadType=media&name=demo-image"
```

- Successfully uploaded the image object to the Cloud Storage bucket.

### 9. Verified Uploaded Object

- Opened **Cloud Storage > Buckets**.
- Selected the created bucket.
- Confirmed that the image object `demo-image` was successfully uploaded.
- Viewed the object details page.

## Evidence/Screenshots

Screenshots/Evidence of work are provided in the screenshot folder.

## Results

- Successfully explored and enabled Google APIs using the API Library.
- Learned the architecture and functionality of RESTful APIs.
- Generated OAuth 2.0 credentials for secure API authentication.
- Created a Cloud Storage bucket using the JSON/REST API.
- Uploaded files to Cloud Storage using HTTP requests.
- Practiced using curl to interact with Google Cloud services.
- Gained practical experience with API authentication, endpoints, JSON payloads, and REST operations.

## Reflection

This lab provided valuable hands-on experience with APIs and their role in modern cloud applications. I learned how RESTful APIs use HTTP methods, endpoints, and JSON data to communicate between clients and servers. The lab also improved my understanding of OAuth authentication and demonstrated how cloud resources can be managed programmatically through API calls rather than using graphical interfaces. This experience strengthened my knowledge of API integration and cloud automation techniques.