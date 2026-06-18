# Lab 06 - Cloud IAM: Qwik Start

## Lab Summary

This lab focused on Google Cloud Identity and Access Management (IAM). The lab demonstrated how to manage access permissions for users, assign and revoke IAM roles, and control access to Google Cloud resources. By using two separate user accounts, the lab illustrated how IAM roles affect resource visibility and permissions within a Google Cloud project. :contentReference[oaicite:0]{index=0}

## Lab Objective

The objective of this lab was to learn how to:

- Assign IAM roles to users.
- Remove IAM roles and permissions.
- Understand how different IAM roles affect access to Google Cloud resources.
- Grant resource-specific permissions to users. :contentReference[oaicite:1]{index=1}

## Tools & Services Used

- Google Cloud Platform (GCP)
- Identity and Access Management (IAM)
- Cloud Storage
- Cloud Shell
- IAM Policies
- User Roles and Permissions
- Google Cloud Console

## Key Steps Performed

### 1. Explored IAM Roles

- Signed in using two separate user accounts:
  - Username 1 (Project Owner)
  - Username 2 (Project Viewer)
- Opened the IAM console.
- Reviewed the predefined project-level roles:

```text
Browser
Viewer
Editor
Owner
```

- Examined the permissions associated with each role.
- Verified that Username 1 had Owner privileges and Username 2 had Viewer privileges. :contentReference[oaicite:2]{index=2}

### 2. Created a Cloud Storage Bucket

- Opened Cloud Storage from the Google Cloud Console.
- Created a new bucket with a globally unique name.
- Selected:

```text
Location Type: Multi-region
```

- Successfully created the bucket. :contentReference[oaicite:3]{index=3}

### 3. Uploaded a Sample File

- Opened the newly created bucket.
- Uploaded a sample text or HTML file.
- Renamed the file to:

```text
sample.txt
```

- Verified that the file was successfully uploaded to the bucket. :contentReference[oaicite:4]{index=4}

### 4. Verified Viewer Access

- Switched to Username 2.
- Navigated to:

```text
Cloud Storage > Buckets
```

- Confirmed that the Viewer role allowed read-only access to the bucket and its contents.
- Verified that project resources could be viewed but not modified. :contentReference[oaicite:5]{index=5}

### 5. Removed Project Access

- Switched back to Username 1.
- Opened:

```text
IAM & Admin > IAM
```

- Edited Username 2 permissions.
- Removed the Viewer role.
- Saved the updated IAM policy. :contentReference[oaicite:6]{index=6}

### 6. Verified Access Removal

- Switched to Username 2.
- Attempted to access Cloud Storage resources.
- Received a permission error.
- Confirmed that the user no longer had project-level access. :contentReference[oaicite:7]{index=7}

### 7. Granted Resource-Specific Permissions

- Returned to Username 1.
- Added Username 2 as a principal.
- Assigned the following role:

```text
Cloud Storage > Storage Object Viewer
```

- Saved the updated IAM policy. :contentReference[oaicite:8]{index=8}

### 8. Verified Cloud Storage Access

- Switched to Username 2.
- Opened Cloud Shell.
- Listed bucket contents using:

```bash
gcloud storage ls gs://YOUR_BUCKET_NAME
```

- Verified that Username 2 could view objects stored in the bucket despite lacking project-level Viewer access.

Example output:

```text
gs://YOUR_BUCKET_NAME/sample.txt
```

- Confirmed successful resource-level permission assignment. :contentReference[oaicite:9]{index=9}

## Evidence/Screenshots

Screenshots/Evidence of work are provided in the screenshot folder.

## Results

- Successfully explored Google Cloud IAM roles.
- Created a Cloud Storage bucket and uploaded a sample file.
- Verified Viewer-level project access.
- Removed project-level permissions from a user.
- Confirmed loss of access after role removal.
- Granted Cloud Storage-specific permissions.
- Verified resource-level access through Cloud Shell.
- Gained practical experience managing IAM policies and permissions.

## Reflection

This lab provided practical experience with Google Cloud Identity and Access Management (IAM). I learned how IAM roles determine what actions users can perform within a project and how permissions can be granted at both the project and resource levels. The exercise demonstrated the principle of least privilege by showing how users can be given access only to the resources they need. This lab strengthened my understanding of cloud security, access control, and permission management in Google Cloud.