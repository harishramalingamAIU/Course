# Lab 02 - VPC Networks: Controlling Access

## Lab Summary

This lab focused on securing and managing access within a Google Cloud Virtual Private Cloud (VPC) network. The lab demonstrated how to create multiple nginx web servers, control external HTTP access using tagged firewall rules, and manage permissions using IAM roles and service accounts.

The lab also explored the difference between Network Admin and Security Admin roles by assigning permissions to a service account and testing the allowed operations.

## Lab Objective

The objective of this lab was to learn how to:

- Create web servers inside a VPC network.
- Install and configure nginx web servers.
- Use network tags with firewall rules to control access.
- Create and manage service accounts.
- Assign IAM roles to service accounts.
- Understand the difference between Network Admin and Security Admin permissions.

## Tools & Services Used

- Google Cloud Platform (GCP)
- Virtual Private Cloud (VPC)
- Compute Engine
- Cloud Shell
- gcloud CLI
- Firewall Rules
- IAM Roles
- Service Accounts
- nginx Web Server
- Linux Commands

## Key Steps Performed

## 1. Created the Blue Web Server

- Opened:

```text
Compute Engine > VM Instances
```

- Created a VM instance:

```text
Name:
blue
```

- Configured the VM in the default VPC network.

- Added a network tag:

```text
web-server
```

The network tag was used later to apply firewall rules only to this VM.

## 2. Created the Green Web Server

- Created another VM instance:

```text
Name:
green
```

- Deployed it inside the default VPC network.

- Did not assign any network tag.

The purpose was to compare access behavior between tagged and untagged instances.

## 3. Installed and Configured nginx

### Installed nginx on Blue Server

Connected through SSH and installed nginx:

```bash
sudo apt-get install nginx-light -y
```

Edited the default webpage:

```bash
sudo nano /var/www/html/index.nginx-debian.html
```

Changed:

```html
<h1>Welcome to nginx!</h1>
```

to:

```html
<h1>Welcome to the blue server!</h1>
```

Verified:

```bash
cat /var/www/html/index.nginx-debian.html
```

---

### Installed nginx on Green Server

Installed nginx:

```bash
sudo apt-get install nginx-light -y
```

Modified the webpage:

```html
<h1>Welcome to the green server!</h1>
```

Verified the updated page.

## 4. Created Tagged Firewall Rule

Created a firewall rule:

```text
Name:
allow-http-web-server
```

Configuration:

```text
Network:
default

Target Tags:
web-server

Source IP:
0.0.0.0/0

Protocols:
tcp:80
icmp
```

This rule allowed HTTP traffic only to VM instances with the:

```text
web-server
```

network tag.

## 5. Created Test VM

Created a testing VM using Cloud Shell:

```bash
gcloud compute instances create test-vm \
--machine-type=e2-micro \
--subnet=default \
--zone=ZONE
```

Verified the VM was running.

## 6. Tested HTTP Connectivity

### Internal IP Testing

From `test-vm`, tested access to:

```text
blue internal IP
green internal IP
```

Both servers responded successfully.

Reason:

- All instances are inside the same VPC.
- Default internal firewall rules allow communication.

---

### External IP Testing

Tested:

```bash
curl BLUE_EXTERNAL_IP
```

Output:

```text
Welcome to the blue server!
```

Successful because:

- Blue server had the `web-server` tag.
- Firewall rule allowed HTTP traffic.

Tested:

```bash
curl GREEN_EXTERNAL_IP
```

Result:

- Connection failed.

Reason:

- Green server did not have the required network tag.
- Firewall rule did not apply.

## 7. Explored Default Service Account Permissions

Attempted to list firewall rules:

```bash
gcloud compute firewall-rules list
```

Result:

```text
Insufficient Permission
```

Attempted deleting firewall rules:

```bash
gcloud compute firewall-rules delete allow-http-web-server
```

Result:

```text
Insufficient Permission
```

This showed that the default Compute Engine service account did not have enough permissions.

## 8. Created Network Admin Service Account

Created a service account:

```text
Name:
Network-admin
```

Assigned role:

```text
Compute Network Admin
```

Generated a JSON key file:

```text
credentials.json
```

## 9. Authorized Test VM Using Service Account

Uploaded the credentials file to `test-vm`.

Activated the service account:

```bash
gcloud auth activate-service-account \
--key-file credentials.json
```

Tested firewall listing:

```bash
gcloud compute firewall-rules list
```

Result:

- Firewall rules could be listed successfully.

Tested deleting firewall rule:

```bash
gcloud compute firewall-rules delete allow-http-web-server
```

Result:

```text
Permission denied
```

Reason:

Network Admin allows viewing and managing network resources but does not manage firewall rules.

## 10. Updated Service Account to Security Admin

Changed the role from:

```text
Compute Network Admin
```

to:

```text
Compute Security Admin
```

Tested listing firewall rules:

```bash
gcloud compute firewall-rules list
```

Successful.

Tested deleting firewall rule:

```bash
gcloud compute firewall-rules delete allow-http-web-server
```

Successful.

The firewall rule was removed.

## 11. Verified Firewall Rule Removal

Tested external access to blue server:

```bash
curl BLUE_EXTERNAL_IP
```

Result:

- Request failed.

Reason:

- The HTTP firewall rule was deleted.
- External HTTP access was blocked.

## Evidence/Screenshots

Screenshots/Evidence of work are provided in the screenshot folder.

## Results

- Successfully created two nginx web servers.
- Configured custom web pages for each server.
- Created tagged firewall rules.
- Controlled external HTTP access using network tags.
- Tested internal and external network connectivity.
- Created a service account with IAM permissions.
- Explored Network Admin and Security Admin roles.
- Verified the difference between networking and security permissions.
- Successfully managed firewall rules using Security Admin privileges.

## Reflection

This lab provided practical experience with Google Cloud networking security and access control. I learned how VPC firewall rules can control traffic flow using network tags and how IAM roles determine what actions users and service accounts can perform. The lab demonstrated the principle of least privilege by showing that Network Admin and Security Admin roles provide different levels of access. This improved my understanding of cloud security, network isolation, firewall management, and permission-based resource control.