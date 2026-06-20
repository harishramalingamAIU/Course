# Lab 07 - Video Intelligence: Qwik Start

## Lab Summary

This lab focused on using the Cloud Video Intelligence API to extract metadata from video content stored in Cloud Storage. The lab demonstrated how to set up authorization using a custom service account, build a JSON request to trigger label detection on a sample video, and poll the resulting long-running operation until annotated results - including detected entities, time segments, and confidence scores - were returned.

## Lab Objective

The objective of this lab was to learn how to:

- Set up authorization for a custom service account.
- Send an annotate video request to the Video Intelligence API.

## Tools & Services Used

- Google Cloud Platform (GCP)
- Cloud Video Intelligence API
- IAM Service Accounts
- Cloud Storage (sample video source)
- Cloud Shell / gcloud CLI / curl

## Key Steps Performed

## 1. Created a Service Account

Created a new service account named `quickstart` to handle authorization for API calls:

```bash
gcloud iam service-accounts create quickstart
```

## 2. Created a Service Account Key File

Generated a JSON key file for the new service account:

```bash
gcloud iam service-accounts keys create key.json --iam-account quickstart@<your-project-123>.iam.gserviceaccount.com
```

## 3. Authenticated as the Service Account

Activated the service account using the generated key file:

```bash
gcloud auth activate-service-account --key-file key.json
```

## 4. Obtained an Authorization Token

Generated an access token to be used for authenticating subsequent API requests:

```bash
gcloud auth print-access-token
```

The resulting bearer token was used in later `curl` requests to the Video Intelligence API.

## 5. Created the Annotate Video Request

Built a JSON request file specifying the source video and the desired annotation feature:

```bash
cat > request.json <<EOF
{
   "inputUri":"gs://spls/gsp154/video/train.mp4",
   "features": [
       "LABEL_DETECTION"
   ]
}
EOF
```

The request used a public sample video (`train.mp4`) hosted in Cloud Storage and requested `LABEL_DETECTION` to identify entities present in the video.

## 6. Submitted the Annotate Video Request

Sent the request to the `videos:annotate` endpoint, authenticating with a freshly generated access token:

```bash
curl -s -H 'Content-Type: application/json' \
    -H 'Authorization: Bearer '$(gcloud auth print-access-token)'' \
    'https://videointelligence.googleapis.com/v1/videos:annotate' \
    -d @request.json
```

Since video annotation is a long-running operation, the API responded immediately with an operation name rather than final results:

```json
{
  "name": "projects/474887704060/locations/asia-east1/operations/16366331060670521152"
}
```

## 7. Polled the Operation for Results

Used the returned operation name (along with its project and location) to poll the `v1.operations` endpoint:

```bash
curl -s -H 'Content-Type: application/json' \
    -H 'Authorization: Bearer '$(gcloud auth print-access-token)'' \
    'https://videointelligence.googleapis.com/v1/projects/PROJECTS/locations/LOCATIONS/operations/OPERATION_NAME'
```

**First poll (in progress):** The response included progress metadata but no `done` field, indicating the annotation job was still running:

```json
{
  "name": "projects/425437283751/locations/asia-east1/operations/17938636079131796601",
  "metadata": {
    "@type": "type.googleapis.com/google.cloud.videointelligence.v1.AnnotationProgressMetadata",
    "progressMetadata": [
      {
        "inputUri": "gs://spls/gsp154/video/train.mp4",
        "startTime": "2016-09-22T21:41:56.766091Z",
        "lastUpdateTime": "2016-09-22T21:42:03.889743Z"
      }
    ]
  }
}
```

**Second poll (after ~1 minute, completed):** Re-running the same command after waiting roughly a minute returned a completed response, with `done: true` and a populated `annotationResults` array:

```json
{
  "done": true,
  "response": {
    "@type": "type.googleapis.com/google.cloud.videointelligence.v1.AnnotateVideoResponse",
    "annotationResults": [
      {
        "inputUri": "/spls/gsp154/video/train.mp4",
        "segmentLabelAnnotations": [
          {
            "entity": {
              "entityId": "/m/01yrx",
              "languageCode": "en-US"
            },
            "segments": [
              {
                "segment": {
                  "startTimeOffset": "0s",
                  "endTimeOffset": "14.833664s"
                },
                "confidence": 0.98509187
              }
            ]
          }
        ]
      }
    ]
  }
}
```

Reviewed the `segmentLabelAnnotations`, noting that each detected entity included its Knowledge Graph entity ID, language code, the video time segment(s) in which it appeared, and a confidence score indicating detection certainty.

## Evidence/Screenshots

Screenshots/Evidence of work are provided in the screenshot folder.

## Results

- Successfully created a custom IAM service account and key for Video Intelligence API authorization.
- Authenticated as the service account and generated a valid access token.
- Built and submitted a label-detection annotate video request against a sample video stored in Cloud Storage.
- Polled the resulting long-running operation, observing both an in-progress and a completed (`done: true`) response.
- Reviewed the final annotation results, including detected entities, their time segments, and confidence scores.

## Reflection

This lab provided practical experience with Google Cloud's asynchronous, long-running API pattern, which differs from simpler synchronous APIs. I learned how video annotation requests return an operation name immediately rather than blocking until results are ready, requiring a follow-up polling step to check the `done` status before reading the final response. Working through service account creation, key generation, and access token retrieval also reinforced a consistent authentication pattern that applies across many Google Cloud APIs. Reviewing the final label annotations - complete with entity IDs, time segments, and confidence scores - demonstrated how the Video Intelligence API turns raw video content into structured, searchable metadata without requiring any custom computer vision model development.
