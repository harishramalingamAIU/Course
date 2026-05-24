# Lab 5 - Cloud Run Functions: Qwik Start - Command Line

## Lab Summary

This lab focused on creating, deploying, testing, and monitoring a Cloud Run Function using the Google Cloud command line interface. The function was written in Node.js and triggered using a Pub/Sub messaging event. The lab also demonstrated how serverless event-driven applications work in Google Cloud Platform.

## Lab Objective

The objective of this lab was to learn how to create and deploy a serverless Cloud Run Function, trigger it using Google Cloud Pub/Sub, and monitor execution logs using Google Cloud logging tools.

## Tools & Services Used

- Google Cloud Platform (GCP)
- Cloud Run Functions (Gen2)
- Google Cloud Pub/Sub
- Cloud Shell
- Node.js
- JavaScript
- gcloud CLI
- Linux Commands

## Key Steps Performed

### 1. Configured Cloud Environment
- Opened Google Cloud Console.
- Activated Cloud Shell.
- Configured the default deployment region using gcloud CLI.

### 2. Created the Function Project
- Created a new project folder named `gcf_hello_world`.
- Created the `index.js` file for the function code.
- Created the `package.json` file for dependencies.

### 3. Developed the Cloud Function
- Used the Google Functions Framework.
- Configured a CloudEvent function named `helloPubSub`.
- Decoded incoming Pub/Sub messages.
- Logged greeting messages to Cloud Logging.

### 4. Installed Dependencies
- Installed Node.js dependencies using:

```bash
npm install
```

### 5. Deployed the Function
- Deployed the Cloud Run Function using:

```bash
gcloud functions deploy
```

- Configured:
  - Runtime environment
  - Trigger topic
  - Entry point
  - Service account
  - Region settings

### 6. Tested the Function
- Published a test message to the Pub/Sub topic using:

```bash
gcloud pubsub topics publish cf-demo --message="Cloud Function Gen2"
```

- Triggered the Cloud Function successfully.

### 7. Viewed Function Logs
- Checked execution logs using:

```bash
gcloud functions logs read nodejs-pubsub-function
```

- Verified that the function output displayed:

```text
Hello, Cloud Function Gen2!
```

## Evidence/Screenshots

Screenshots/Evidence of work are provided in the screenshot folder.

## Results

- Successfully created a Cloud Run Function using Node.js.
- Configured Pub/Sub event triggers.
- Deployed the function using Cloud Shell commands.
- Triggered the function successfully using Pub/Sub messaging.
- Viewed and verified execution logs.
- Learned serverless and event-driven cloud computing concepts.

## Reflection

This lab provided practical experience with Google Cloud Run Functions and event-driven architecture. I learned how serverless functions execute only when triggered by events, reducing infrastructure management complexity. The lab also improved my understanding of Pub/Sub messaging, Cloud Logging, and deploying Node.js applications using Google Cloud command-line tools.