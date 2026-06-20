# Lab 03 - Application Load Balancer with Cloud Armor

## Lab Summary

This lab focused on deploying a Google Cloud Global External Application Load Balancer across two regions and protecting it at the network edge using Google Cloud Armor. The lab demonstrated how to architect cross-regional failover with Managed Instance Groups (MIGs), validate traffic distribution under high-concurrency stress tests, and implement edge-level IP denylisting to block malicious or unwanted traffic before it reaches backend resources.

## Lab Objective

The objective of this lab was to learn how to:

- Architect cross-regional failover using Managed Instance Groups (MIGs).
- Implement edge-level security policies via Cloud Armor.
- Validate traffic distribution and overflow mechanisms under high-concurrency stress tests.
- Denylist a malicious client IP address at the load balancer edge.

## Tools & Services Used

- Google Cloud Platform (GCP)
- Compute Engine (VM Instances, Instance Templates, Managed Instance Groups)
- Global External Application Load Balancer
- Google Cloud Armor
- VPC Firewall Rules
- Cloud Logging
- siege (load testing tool)
- Cloud Shell / gcloud CLI

## Key Steps Performed

## 1. Configured HTTP and Health Check Firewall Rules

Created a firewall rule to allow inbound HTTP traffic to tagged web server instances:

```text
Name:              default-allow-http
Network:           default
Target tags:       http-server
Source IPv4 range: 0.0.0.0/0
Protocol/Port:     tcp:80
```

Created a second firewall rule to allow Google's health check probe ranges to reach the backends:

```text
Name:              default-allow-health-check
Network:           default
Target tags:       http-server
Source IPv4 ranges: 130.211.0.0/22, 35.191.0.0/16
Protocol:          tcp
```

These rules ensure the load balancer can both serve traffic to, and verify the health of, instances tagged with `http-server`.

## 2. Configured Instance Templates and Managed Instance Groups

Created two global instance templates (`Region 1-template` and `Region 2-template`), each using an E2 `e2-micro` machine type, tagged with `http-server`, and bound to the appropriate regional subnetwork.

Each template referenced a startup script that automatically installed Apache and customized the welcome page with the instance's client IP, hostname, and server location:

```text
startup-script-url: gs://spls/gsp215/gcpnet/httplb/startup.sh
```

Created two regional Managed Instance Groups, one per region, each configured with autoscaling:

```text
                        Region 1-mig (Region 1)   Region 2-mig (Region 2)
Min instances:          1                          1
Max instances:          2                          2
Autoscaling signal:     CPU utilization             CPU utilization
Target utilization:     80%                          80%
Initialization period:  45s                          45s
```

Verified that instances were created in both regions and that each instance's web page correctly displayed the client IP, hostname, and server location, confirming correct backend identification.

## 3. Configured the Application Load Balancer

Created a Global External Application Load Balancer named `http-lb` with the following configuration:

### Frontend Configuration

- IPv4 frontend - Protocol HTTP, Port 80, Ephemeral IP
- IPv6 frontend - Protocol HTTP, Port 80, Auto-allocated IP

Application Load Balancing terminates client IPv6 requests at the global load balancing layer and proxies them over IPv4 to backends.

### Backend Configuration

```text
Backend service:    http-backend

Backend 1: Region 1-mig
  Port:                     80
  Balancing mode:           Rate
  Maximum RPS:              50
  Capacity:                 100

Backend 2: Region 2-mig
  Port:                     80
  Balancing mode:           Utilization
  Max backend utilization:  80%
  Capacity:                 100

Health check:        http-health-check (TCP, port 80)
Logging:              Enabled, sample rate 1
```

This configuration keeps `Region 1-mig` at or below 50 requests per second per instance, and `Region 2-mig` at or below 80% CPU utilization, allowing the load balancer to spill traffic to Region 2 only once Region 1 is saturated.

## 4. Tested the Application Load Balancer

Accessed the load balancer's IPv4 and IPv6 frontend addresses directly in a browser and confirmed traffic was served by the nearest healthy backend (`Region 1-mig`, given regional proximity).

Created a `siege-vm` in a third region to simulate client load. Installed the siege load-testing tool and ran a stress test against the load balancer's IPv4 address:

```bash
sudo apt-get update && sudo apt-get -y install siege
export LB_IP=[LB_IP_v4]
siege -c 150 -t120s http://$LB_IP
```

While monitoring the load balancer's Monitoring tab, observed that traffic was initially directed only to `Region 1-mig`. As request volume and concurrency increased, the load balancer began distributing overflow traffic to `Region 2-mig` as well, demonstrating that traffic is normally routed to the closest backend but spills over to additional backends under high load.

Sample siege output:

```text
{       "transactions":                        24729,
        "availability":                       100.00,
        "elapsed_time":                       119.07,
        "data_transferred":                     3.77,
        "response_time":                        0.66,
        "transaction_rate":                   207.68,
        "throughput":                           0.03,
        "concurrency":                        137.64,
        "successful_transactions":             24729,
        "failed_transactions":                     0,
        "longest_transaction":                 10.45,
        "shortest_transaction":                 0.03
}
```

## 5. Denylisted the siege-vm with Cloud Armor

Noted the external IP address of `siege-vm` (`[SIEGE_IP]`) and created a new Cloud Armor security policy:

```text
Policy name:          denylist-siege
Default rule action:  Allow

Custom rule:
  Match condition:    [SIEGE_IP]/32
  Action:             Deny
  Response code:      403 (Forbidden)
  Priority:           1000

Target:               Backend service - http-backend
```

Attached the policy to the `http-backend` backend service used by the Application Load Balancer.

## 6. Verified the Security Policy

From the `siege-vm` terminal, attempted to access the load balancer:

```bash
curl http://$LB_IP
```

The request returned a `403 Forbidden` response, confirming the Cloud Armor deny rule was enforced for the `siege-vm`'s IP address.

Re-ran the siege stress test from `siege-vm`; the command produced no successful output since all requests were being blocked at the edge before reaching the backend.

Confirmed that the load balancer remained accessible from a normal browser session, since the default policy rule allowed all other traffic - only the explicitly denylisted `siege-vm` IP was blocked.

Explored Cloud Logging by querying logs scoped to the `http-lb-forwarding-rule` resource. Expanded a log entry's `httpRequest` and `jsonPayload.enforcedSecurityPolicy` fields and confirmed the `configuredAction` was `DENY`, matching the `denylist-siege` policy and the `siege-vm`'s source IP.

## Evidence/Screenshots

Screenshots/Evidence of work are provided in the screenshot folder.

## Results

- Successfully deployed a Global External Application Load Balancer with backends in two regions.
- Configured autoscaling Managed Instance Groups with region-specific instance templates.
- Verified correct HTTP and health-check firewall configuration.
- Validated traffic distribution behavior under normal and high-load conditions using siege.
- Created and applied a Cloud Armor security policy to denylist a specific client IP address.
- Confirmed enforcement of the deny rule via direct testing and Cloud Logging analysis.

## Reflection

This lab provided hands-on experience designing a globally distributed, resilient web application architecture and securing it at the network edge. I learned how Application Load Balancer backend configurations (rate-based versus utilization-based balancing modes) influence when and how traffic overflows between regions, and how autoscaling instance groups respond to load. Configuring Cloud Armor reinforced the value of edge-level filtering: malicious or unwanted traffic can be dropped at Google's Point of Presence before it ever reaches the VPC or consumes backend compute resources. Reviewing the Cloud Armor policy logs also demonstrated how to audit and verify security enforcement decisions in a production-like environment.
