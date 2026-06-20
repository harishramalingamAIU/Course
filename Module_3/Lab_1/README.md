# Lab 01 - Virtual Private Cloud (VPC) Networks: Qwik Start

## Lab Summary

This lab focused on creating and managing Virtual Private Cloud (VPC) networks in Google Cloud. The lab demonstrated how to create custom mode VPC networks, configure firewall rules, create Compute Engine VM instances, test network connectivity between different VPC networks, and configure a VM instance with multiple network interfaces.

## Lab Objective

The objective of this lab was to learn how to:

- Create custom VPC networks.
- Configure firewall rules for network traffic.
- Create VM instances inside specific VPC subnets.
- Understand connectivity between VM instances across different VPC networks.
- Configure multiple network interfaces on a single VM instance.

## Tools & Services Used

- Google Cloud Platform (GCP)
- Virtual Private Cloud (VPC)
- Compute Engine
- Cloud Shell
- gcloud CLI
- Firewall Rules
- VM Instances
- Network Interfaces
- Linux Networking Commands

## Key Steps Performed

## 1. Created Custom VPC Networks

### Created managementnet Network

- Opened:

```text
VPC Network > VPC Networks
```

- Created a custom mode VPC network named:

```text
managementnet
```

- Created a subnet:

```text
Name: managementsubnet-1
IP Range: 10.130.0.0/20
```

- Verified that the network was successfully created.

---

### Created privatenet Network

- Created another custom VPC network using Cloud Shell.

```bash
gcloud compute networks create privatenet \
--subnet-mode=custom
```

- Created subnets:

```bash
gcloud compute networks subnets create privatesubnet-1 \
--network=privatenet \
--region=REGION_1 \
--range=172.16.0.0/24
```

```bash
gcloud compute networks subnets create privatesubnet-2 \
--network=privatenet \
--region=REGION_2 \
--range=172.20.0.0/20
```

- Verified available networks:

```bash
gcloud compute networks list
```

- Verified available subnets:

```bash
gcloud compute networks subnets list --sort-by=NETWORK
```

## 2. Created Firewall Rules

### Managementnet Firewall Rule

- Created a firewall rule to allow:

```text
SSH (22)
RDP (3389)
ICMP
```

- Configuration:

```text
Network: managementnet
Source Range: 0.0.0.0/0
Target: All instances
```

---

### Privatenet Firewall Rule

- Created firewall rules using Cloud Shell.

```bash
gcloud compute firewall-rules create \
privatenet-allow-icmp-ssh-rdp \
--direction=INGRESS \
--priority=1000 \
--network=privatenet \
--action=ALLOW \
--rules=icmp,tcp:22,tcp:3389 \
--source-ranges=0.0.0.0/0
```

- Verified firewall rules:

```bash
gcloud compute firewall-rules list --sort-by=NETWORK
```

## 3. Created VM Instances

### Created managementnet VM

Created a VM instance:

```text
Name:
managementnet-vm-1
```

Configuration:

```text
Machine Type: e2-micro
Network: managementnet
Subnet: managementsubnet-1
```

---

### Created privatenet VM

Created VM using Cloud Shell:

```bash
gcloud compute instances create privatenet-vm-1 \
--zone=ZONE \
--machine-type=e2-micro \
--subnet=privatesubnet-1
```

- Verified VM instances:

```bash
gcloud compute instances list --sort-by=ZONE
```

## 4. Tested Network Connectivity

### Tested External IP Connectivity

- Connected to `mynet-vm-1` using SSH.
- Tested ping to external IP addresses of:

```text
mynet-vm-2
managementnet-vm-1
privatenet-vm-1
```

Example:

```bash
ping -c 3 EXTERNAL_IP
```

- Verified that all instances were reachable through public IP addresses because firewall rules allowed ICMP traffic.

---

### Tested Internal IP Connectivity

Tested communication using internal IP addresses.

Successful:

```text
mynet-vm-1 → mynet-vm-2
```

Reason:

- Both VMs are inside the same VPC network.

Failed:

```text
mynet-vm-1 → managementnet-vm-1
mynet-vm-1 → privatenet-vm-1
```

Reason:

- Different VPC networks are isolated by default.
- Internal communication requires additional configuration such as:

```text
VPC Peering
VPN
Cloud Interconnect
```

## 5. Created VM With Multiple Network Interfaces

Created a VM instance:

```text
Name:
vm-appliance
```

Machine configuration:

```text
Machine Type:
e2-standard-4
```

Added three network interfaces:

### NIC 0

```text
Network:
privatenet

Subnet:
privatesubnet-1
```

### NIC 1

```text
Network:
managementnet

Subnet:
managementsubnet-1
```

### NIC 2

```text
Network:
mynetwork
```

- Verified network interfaces inside the VM.

Command:

```bash
sudo ifconfig
```

- Confirmed three interfaces:

```text
eth0
eth1
eth2
```

Each interface had a different internal IP address.

## 6. Tested Multiple Interface Connectivity

From `vm-appliance`, tested connectivity to:

```text
privatenet-vm-1
managementnet-vm-1
mynet-vm-1
```

All successful because the VM had interfaces connected to those networks.

Example:

```bash
ping -c 3 INTERNAL_IP
```

---

Tested:

```text
vm-appliance → mynet-vm-2
```

Result:

Failed.

Reason:

- The subnet route was not available.
- Traffic followed the default route through the primary interface.

Checked routing table:

```bash
ip route
```

Example:

```text
default via 172.16.0.1 dev eth0
```

The default route used `eth0`, causing traffic to leave through the privatenet interface.

## Evidence/Screenshots

Screenshots/Evidence of work are provided in the screenshot folder.

## Results

- Successfully created custom VPC networks.
- Configured firewall rules for network traffic.
- Created VM instances in different VPC networks.
- Tested external and internal connectivity.
- Verified VPC network isolation.
- Created a VM instance with multiple network interfaces.
- Explored routing behavior inside a multi-NIC VM.
- Gained practical experience with Google Cloud networking concepts.

## Reflection

This lab provided practical experience with Google Cloud VPC networking and network security. I learned how VPC networks provide isolated environments, how firewall rules control traffic flow, and how VM instances communicate within and between networks. The lab also improved my understanding of network interfaces, routing tables, and how multiple network connections can be configured on a single VM instance. This knowledge is important for designing secure and scalable cloud network architectures.