# Lab 4 - App Engine: Qwik Start - Python

## Lab Summary

This lab focused on deploying a Python web application using Google App Engine. The application was first tested locally using Flask and then deployed to Google Cloud using App Engine. The lab also included modifying the application and verifying the deployed changes through a web browser.

## Lab Objective

The objective of this lab was to learn how to deploy and manage a simple Python application using Google App Engine and understand how serverless application hosting works in Google Cloud Platform.

## Tools & Services Used

- Google Cloud Platform (GCP)
- Google App Engine
- Cloud Shell
- Python 3
- Flask
- GitHub
- gcloud CLI
- Linux Commands

## Key Steps Performed

### 1. Enabled App Engine Admin API
- Opened the Google Cloud Console.
- Navigated to APIs & Services.
- Enabled the App Engine Admin API.

### 2. Downloaded the Sample Application
- Cloned the Python sample repository from GitHub.
- Navigated to the App Engine hello world directory.
- Created and activated a Python virtual environment.

### 3. Tested the Application Locally
- Started the Flask development server.
- Opened Web Preview on port 5000.
- Verified the application displayed “Hello World!”.

### 4. Modified the Application
- Opened the `main.py` file using nano editor.
- Changed the message from “Hello World!” to “Hello, Cruel World!”.
- Restarted the Flask server.
- Verified the updated output in the browser.

### 5. Deployed the Application
- Used the `gcloud app deploy` command.
- Selected the deployment region.
- Confirmed deployment successfully completed.

### 6. Viewed the Deployed Application
- Opened the deployed App Engine application using:

```bash
gcloud app browse
```

- Verified the application displayed “Hello, Cruel World!” online.

## Evidence/Screenshots

Screenshots/Evidence of work are provided in the screenshot folder.

## Results

- Successfully enabled and configured Google App Engine.
- Downloaded and tested a Python Flask application.
- Modified the application source code successfully.
- Deployed the application to Google App Engine.
- Accessed the deployed application through a public URL.
- Learned basic App Engine deployment and management operations.

## Reflection

This lab provided practical experience with deploying applications using Google App Engine. I learned how serverless platforms simplify application hosting by removing the need to manage servers and infrastructure manually. Testing the application locally before deployment also helped me understand the development workflow in cloud environments.