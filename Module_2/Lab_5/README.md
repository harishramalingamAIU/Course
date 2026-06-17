# Lab 05 - User Authentication with Identity-Aware Proxy (IAP)

## Lab Summary

This lab focused on securing a Cloud Run web application using Identity-Aware Proxy (IAP). The lab demonstrated how to deploy a Python-based web application to Cloud Run, restrict access using IAP, retrieve authenticated user information, and implement cryptographic verification to ensure that identity data provided by IAP cannot be spoofed.

## Lab Objective

The objective of this lab was to learn how to deploy a web application on Cloud Run, secure it using Identity-Aware Proxy (IAP), access authenticated user identity information, and verify that identity data using cryptographic signatures.

## Tools & Services Used

- Google Cloud Platform (GCP)
- Cloud Run
- Identity-Aware Proxy (IAP)
- Cloud Shell
- Python
- Flask
- gcloud CLI
- OAuth Authentication
- JWT (JSON Web Token)
- IAM (Identity and Access Management)

## Key Steps Performed

### 1. Downloaded the Application Code

- Opened Cloud Shell.
- Downloaded the lab source code from Cloud Storage.

```bash
gcloud storage cp gs://<bucket>/user-authentication-with-iap.zip .
```

- Extracted the project files.

```bash
unzip user-authentication-with-iap.zip
```

- Navigated to the project directory.

```bash
cd user-authentication-with-iap
```

### 2. Deployed a Basic Cloud Run Application

- Navigated to the first application folder.

```bash
cd 1-HelloWorld
```

- Deployed the application to Cloud Run.

```bash
gcloud run deploy user-auth-lab \
--source . \
--allow-unauthenticated \
--region="REGION"
```

- Verified successful deployment.
- Accessed the generated Cloud Run service URL.
- Confirmed that the application displayed a simple "Hello World" page.

### 3. Enabled Identity-Aware Proxy (IAP)

- Opened **Security > Identity-Aware Proxy** in the Google Cloud Console.
- Enabled the Identity-Aware Proxy API.
- Enabled IAP protection for the Cloud Run service.
- Verified that access to the application was restricted.

### 4. Granted User Access Through IAP

- Added the student account as an authorized principal.
- Assigned the role:

```text
IAP-Secured Web App User
```

- Saved the IAM policy changes.
- Refreshed the application.
- Confirmed successful access through IAP authentication.

### 5. Accessed User Identity Information

- Navigated to the second application version.

```bash
cd ~/user-authentication-with-iap/2-HelloUser
```

- Deployed the updated application.

```bash
gcloud run deploy user-auth-lab \
--source . \
--region="REGION"
```

- Examined the application code.

```bash
cat main.py
```

- Observed that the application retrieved user identity information from IAP request headers:

```python
user_email = request.headers.get(
    'X-Goog-Authenticated-User-Email'
)

user_id = request.headers.get(
    'X-Goog-Authenticated-User-ID'
)
```

- Verified that the application displayed:
  - Authenticated user email
  - Persistent user ID

### 6. Tested Application Without IAP Protection

- Disabled Identity-Aware Proxy.
- Allowed public access to the Cloud Run service.
- Refreshed the application.

- Observed that no user identity information was available.

- Simulated a spoofed request using curl.

```bash
curl -X GET YOUR_URL \
-H "X-Goog-Authenticated-User-Email: totally fake email"
```

- Verified that fake user identity information could be injected when IAP was disabled.

### 7. Implemented Cryptographic Verification

- Navigated to the third application version.

```bash
cd ~/user-authentication-with-iap/3-HelloVerifiedUser
```

- Retrieved the IAP JWT Audience Client ID.
- Stored it as an environment variable during deployment.

```bash
gcloud run deploy user-auth-lab \
--source . \
--set-env-vars IAP_AUDIENCE="YOUR_CLIENT_ID" \
--region="REGION"
```

- Examined the authentication helper file.

```bash
cat auth.py
```

- Reviewed the JWT verification logic.

```python
assertion = request.headers.get(
    'X-Goog-IAP-JWT-Assertion'
)

info = jwt.decode(
    assertion,
    keys(),
    algorithms=['ES256'],
    audience=audience()
)
```

- Verified that authenticated user information was extracted from a cryptographically signed JWT.

### 8. Re-enabled IAP and Configured Permissions

- Re-enabled Identity-Aware Proxy for the Cloud Run service.

- Granted the IAP service account permission to invoke the Cloud Run service.

```bash
gcloud run services add-iam-policy-binding user-auth-lab \
--member="serviceAccount:service-$(gcloud projects describe PROJECT_ID \
--format='value(projectNumber)')@gcp-sa-iap.iam.gserviceaccount.com" \
--role="roles/run.invoker" \
--region="REGION"
```

- Refreshed the application.
- Confirmed that verified user identity information was displayed successfully.

### 9. Verified Cryptographically Signed User Information

- Confirmed that:
  - User email was displayed correctly.
  - User ID was displayed correctly.
  - Identity information was obtained from a verified JWT.
  - Spoofed identity headers could no longer be trusted without a valid signature.

## Evidence/Screenshots

Screenshots/Evidence of work are provided in the screenshot folder.

## Results

- Successfully deployed a Python web application on Cloud Run.
- Enabled and configured Identity-Aware Proxy (IAP).
- Restricted application access to authorized users.
- Retrieved authenticated user identity information.
- Demonstrated the risk of header spoofing when IAP is disabled.
- Implemented JWT-based cryptographic verification.
- Verified user identities securely using signed assertions.
- Gained practical experience with Cloud Run security, authentication, and access management.

## Reflection

This lab provided hands-on experience with securing cloud applications using Identity-Aware Proxy. I learned how IAP authenticates users before allowing access to applications and how authenticated user information can be passed securely to backend services. The lab also demonstrated the importance of cryptographic verification using JWT assertions to prevent identity spoofing. Overall, the exercise strengthened my understanding of cloud security, authentication mechanisms, access control, and secure application deployment on Google Cloud.