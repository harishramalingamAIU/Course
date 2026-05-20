# Lab 3: Creating Virtual Machines in Compute Engine

## Lab Summary

This lab focused on creating and managing virtual machine instances using Google Compute Engine. Virtual machines were created using both the Google Cloud Console and the `gcloud` command line interface. The lab also included deploying an NGINX web server and connecting to the virtual machine using SSH.

## Lab Objective

The objective of this lab was to learn how to create and manage Compute Engine virtual machine instances using both the graphical interface and command line tools, as well as deploy and access a web server running on a VM.

## Tools & Services Used

- Google Cloud Platform (GCP)
- Compute Engine
- Cloud Shell
- gcloud CLI
- NGINX Web Server
- SSH
- Debian Linux

## Key Steps Performed

### 1. Configured Region and Zone
- Set the default region and zone using `gcloud` commands.
- Created environment variables for region and zone.

### 2. Created a VM Using Cloud Console
- Opened Compute Engine VM Instances page.
- Created a VM named `gcelab`.
- Selected machine type `e2-medium`.
- Configured Debian GNU/Linux 12 boot disk.
- Enabled HTTP traffic.
- Connected to the VM using SSH.

### 3. Installed NGINX Web Server
- Updated the operating system packages.
- Installed NGINX on the VM.
- Verified the NGINX process was running.
- Accessed the default NGINX web page through the VM external IP.

### 4. Created a VM Using gcloud CLI
- Used Cloud Shell to create a second VM named `gcelab2`.
- Verified successful VM creation.
- Connected to the VM using SSH through command-line commands.

## Evidence/Screenshots

Screenshots/Evidence of work are provided in the screenshot folder.

## Results

- Successfully created VM instances using both Cloud Console and `gcloud`.
- Connected to virtual machines using SSH.
- Installed and configured the NGINX web server.
- Accessed the hosted web page through the VM external IP.
- Learned basic Compute Engine VM management operations.

## Reflection

This lab provided practical experience with Google Compute Engine virtual machines. I learned how to create VMs using both graphical and command line methods and understood the importance of machine configuration, regions, zones, and firewall settings. Deploying NGINX also helped me understand how cloud virtual machines can host web applications and services.

