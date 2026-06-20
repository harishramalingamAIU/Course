# Lab 04 - Cloud Monitoring: Qwik Start

## Lab Summary

This lab focused on setting up Google Cloud Monitoring and Cloud Logging for a Compute Engine VM instance running a LAMP-style Apache web server. The lab demonstrated how to install monitoring and logging agents, create uptime checks, configure alerting policies, build custom dashboards and charts, and correlate VM lifecycle events (stop/start) with logs and monitoring data.

## Lab Objective

The objective of this lab was to learn how to:

- Monitor a Compute Engine virtual machine (VM) instance with Cloud Monitoring.
- Install monitoring and logging agents for the VM.
- Create an uptime check to verify resource availability.
- Create an alerting policy with email notifications.
- Build a custom dashboard with charts for key metrics.
- View and interpret VM instance logs in Cloud Logging.

## Tools & Services Used

- Google Cloud Platform (GCP)
- Compute Engine
- Cloud Monitoring
- Cloud Logging
- Cloud Ops Agent (Monitoring + Logging agent)
- Apache2 HTTP Server / PHP
- Cloud Shell / gcloud CLI

## Key Steps Performed

## 1. Set Default Region and Zone

Configured the default Compute Engine region and zone in Cloud Shell:

```bash
gcloud config set compute/zone "ZONE"
export ZONE=$(gcloud config get compute/zone)

gcloud config set compute/region "REGION"
export REGION=$(gcloud config get compute/region)
```

## 2. Created a Compute Engine Instance

Created a VM instance with the following configuration:

```text
Name:        lamp-1-vm
Region:      <REGION>
Zone:        <ZONE>
Series:      E2
Machine:     e2-medium
Boot disk:   Debian
Firewall:    Allow HTTP traffic
```

Waited for the instance to launch and confirmed successful creation.

## 3. Installed Apache2 HTTP Server

Connected via SSH to `lamp-1-vm` and installed Apache2 and PHP:

```bash
sudo apt-get update
sudo apt-get install apache2 php7.0
sudo service apache2 restart
```

Verified the installation by navigating to the instance's External IP and confirming the Apache2 default page loaded successfully.

## 4. Created a Monitoring Metrics Scope

Navigated to:

```text
Navigation menu > Monitoring
```

This automatically created a Monitoring Metrics Scope tied to the project, enabling Monitoring features for the lab environment.

## 5. Installed the Monitoring and Logging Agents

Ran the Cloud Ops Agent install script in the SSH terminal of `lamp-1-vm` to install both the Monitoring and Logging agents in a single step:

```bash
curl -sSO https://dl.google.com/cloudagents/add-google-cloud-ops-agent-repo.sh
sudo bash add-google-cloud-ops-agent-repo.sh --also-install
```

Verified the agent status:

```bash
sudo systemctl status google-cloud-ops-agent"*"
```

Pressed `q` to exit the status view, then updated package lists:

```bash
sudo apt-get update
```

The Cloud Ops Agent collects disk, CPU, network, and process metrics by default, and streams logs from the VM to Cloud Logging.

## 6. Created an Uptime Check

Created an uptime check to continuously verify that the VM's web server was reachable:

```text
Protocol:         HTTP
Resource Type:    URL
Hostname:         External IP of lamp-1-vm
Check Frequency:  1 minute
Title:            Lamp Uptime Check
```

Left Response Validation and Alert & Notification settings at their defaults, ran a connectivity test (confirmed with a green check mark), then created the uptime check.

## 7. Created an Alerting Policy

Configured a new alerting policy based on network traffic:

```text
Metric:                 Network traffic (agent.googleapis.com/interface/traffic)
Resource/Metric path:   VM instance > Interface
Threshold position:     Above threshold
Threshold value:        500
Retest window:          1 minute
```

Created a new email notification channel:

```text
Notification type:  Email
Email address:       <personal email>
Display name:        <custom display name>
```

Linked the new notification channel to the policy, added documentation text to be included in the alert email, and named the policy:

```text
Alert name: Inbound Traffic Alert
```

Reviewed and created the policy.

## 8. Created a Custom Dashboard and Charts

Created a new custom dashboard:

```text
Dashboard name: Cloud Monitoring LAMP Qwik Start Dashboard
```

**Chart 1 - CPU Load**

```text
Widget type:   Line
Widget title:  CPU Load
Metric:        CPU load (1m)
Resource path: VM instance > Cpu
```

**Chart 2 - Received Packets**

```text
Widget type:   Line
Widget title:  Received Packets
Metric:        Received packets
Resource path: VM instance > Instance
```

Refreshed the dashboard tab to confirm both charts displayed live metric data.

## 9. Viewed VM Logs in Cloud Logging

Navigated to:

```text
Navigation menu > Logging > Logs Explorer
```

Filtered logs to the `lamp-1-vm` resource:

```text
Resource: VM Instance > lamp-1-vm
```

Opened the Compute Engine console in a separate browser window alongside the Logs Explorer to observe real-time log activity while modifying the VM.

**Stop/Start test:**

- Stopped the `lamp-1-vm` instance from the Compute Engine console and observed the corresponding stop event appear in the Logs Explorer.
- Started the instance again and watched the startup log messages stream into Cloud Logging as the VM came back online.

This confirmed that Cloud Logging captures VM lifecycle events (stop/start) in near real time.

## 10. Checked Uptime Check Results and Triggered Alerts

Navigated to:

```text
Navigation menu > Monitoring > Uptime checks
```

Observed that immediately after restarting the instance, the uptime check regions showed a failed status; after a few minutes and a page reload, the regions returned to an active/healthy state.

Opened **Lamp Uptime Check** to review per-region results.

**Checked triggered alerts:**

- Navigated to `Alerting` in the left menu and reviewed the list of incidents and events.
- Checked the registered email inbox and confirmed receipt of a Cloud Monitoring Alert email.
- Noted the recommendation to remove the email notification channel from the alerting policy afterward, to avoid continued notifications while lab resources remain active.

## Evidence/Screenshots

Screenshots/Evidence of work are provided in the screenshot folder.

## Results

- Successfully created and configured a Compute Engine VM running Apache2.
- Installed the Cloud Ops Agent to enable Monitoring and Logging data collection.
- Created a functioning uptime check that validated VM availability over HTTP.
- Configured an alerting policy with a custom email notification channel based on network traffic thresholds.
- Built a custom Cloud Monitoring dashboard with CPU load and received packets charts.
- Verified that VM stop/start events were correctly reflected in Cloud Logging.
- Confirmed uptime check status changes and received a triggered alert notification via email.

## Reflection

This lab provided practical, end-to-end experience with Google Cloud's observability stack. I learned how the Cloud Ops Agent bridges a VM's system-level metrics and logs into Cloud Monitoring and Cloud Logging, and how uptime checks and alerting policies work together to provide automated visibility into application health. Building a custom dashboard reinforced how raw metrics can be turned into actionable visualizations, while the stop/start exercise clearly demonstrated the near real-time relationship between VM state changes and the logs/alerts they generate. This strengthened my understanding of proactive infrastructure monitoring and incident notification workflows in a cloud environment.
