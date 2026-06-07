# Lab 7 - Cloud Storage: Qwik Start - CLI/SDK

## Lab Summary

This lab focused on managing Google Cloud Storage resources using the Google Cloud CLI. The lab demonstrated how to create a Cloud Storage bucket, upload and download objects, organize files into folders, manage object permissions, and delete stored objects.

## Lab Objective

The objective of this lab was to learn how to use Google Cloud Storage and the Google Cloud CLI (`gcloud storage`) to store, manage, organize, and control access to objects in cloud storage buckets.

## Tools & Services Used

- Google Cloud Platform (GCP)
- Google Cloud Storage
- Cloud Shell
- gcloud CLI
- Linux Commands
- Storage Buckets
- Access Control Lists (ACLs)

## Key Steps Performed

### 1. Configured the Compute Region

- Opened Google Cloud Console.
- Activated Cloud Shell.
- Configured the default compute region.

```bash
gcloud config set compute/region REGION
```

### 2. Created a Cloud Storage Bucket

- Created a new Cloud Storage bucket using a unique bucket name.
- Followed Google Cloud bucket naming requirements.

```bash
gcloud storage buckets create gs://YOUR-BUCKET-NAME
```

- Verified the bucket creation in the Cloud Storage Console.

### 3. Uploaded an Object to the Bucket

- Downloaded a sample image (Ada Lovelace portrait) to Cloud Shell.

```bash
curl https://upload.wikimedia.org/wikipedia/commons/thumb/a/a4/Ada_Lovelace_portrait.jpg/800px-Ada_Lovelace_portrait.jpg --output ada.jpg
```

- Uploaded the image to the Cloud Storage bucket.

```bash
gcloud storage cp ada.jpg gs://YOUR-BUCKET-NAME
```

- Removed the local copy after upload.

```bash
rm ada.jpg
```

### 4. Downloaded an Object from the Bucket

- Downloaded the uploaded image from Cloud Storage back to Cloud Shell.

```bash
gcloud storage cp -r gs://YOUR-BUCKET-NAME/ada.jpg .
```

- Verified that the file was successfully downloaded.

### 5. Created a Folder and Copied an Object

- Created a folder named `image-folder` within the bucket.
- Copied the image into the folder.

```bash
gcloud storage cp gs://YOUR-BUCKET-NAME/ada.jpg gs://YOUR-BUCKET-NAME/image-folder/
```

- Verified that the object appeared inside the folder.

### 6. Listed Bucket Contents

- Displayed all objects and folders stored in the bucket.

```bash
gcloud storage ls gs://YOUR-BUCKET-NAME
```

- Confirmed that both the original image and the folder were present.

### 7. Viewed Object Details

- Retrieved detailed information about the uploaded object.

```bash
gcloud storage ls -l gs://YOUR-BUCKET-NAME/ada.jpg
```

- Verified file size and creation timestamp.

### 8. Made an Object Publicly Accessible

- Granted public read access to the image using an Access Control List (ACL).

```bash
gcloud storage objects update gs://YOUR-BUCKET-NAME/ada.jpg \
--add-acl-grant=entity=allUsers,role=READER
```

- Verified public access by opening the generated object URL in a web browser.

### 9. Removed Public Access

- Revoked public access permissions from the object.

```bash
gcloud storage objects update gs://YOUR-BUCKET-NAME/ada.jpg \
--remove-acl-grant=allUsers
```

- Confirmed that the object was no longer publicly accessible.

### 10. Deleted an Object

- Deleted the image object from the bucket.

```bash
gcloud storage rm gs://YOUR-BUCKET-NAME/ada.jpg
```

- Verified that the object was successfully removed while the copied version in `image-folder` remained.

## Evidence/Screenshots

Screenshots/Evidence of work are provided in the screenshot folder.

## Results

- Successfully created a Cloud Storage bucket.
- Uploaded and downloaded objects using the Google Cloud CLI.
- Organized objects using folders within the bucket.
- Listed bucket contents and viewed object metadata.
- Configured public access permissions using ACLs.
- Removed public access permissions when no longer needed.
- Deleted objects from Cloud Storage successfully.
- Gained practical experience managing cloud storage resources through the command line.

## Reflection

This lab provided practical experience with Google Cloud Storage and its command-line management tools. I learned how to create and manage storage buckets, upload and organize objects, configure access permissions, and perform common storage administration tasks. The lab improved my understanding of cloud-based object storage, access control mechanisms, and the use of Google Cloud CLI for storage management.