# AWS VPC Peering – Cross-VPC Private Communication

![AWS](https://img.shields.io/badge/Cloud-AWS-orange)
![Service](https://img.shields.io/badge/Service-VPC%20Peering-blue)
![Region](https://img.shields.io/badge/Region-us--east--1-green)
![Project](https://img.shields.io/badge/Project-EWB%20Internship-purple)

## 1. Project Overview

This project demonstrates how to establish private network communication between two separate Amazon Virtual Private Clouds (VPCs) using AWS VPC Peering.

By default, resources deployed in different VPCs cannot communicate with each other through their private IP addresses. AWS VPC Peering provides a networking connection that allows resources in two VPCs to communicate privately using their private IPv4 addresses.

In this project, two VPCs were created with non-overlapping CIDR blocks. Three EC2 instances were deployed across the VPCs, and the required routing and security configurations were implemented to establish private connectivity.

A Bastion Host was configured in the public subnet of VPC-A to provide SSH access to the private EC2 instances.

**Project Details**

| Attribute | Details |
|---|---|
| Project | AWS VPC Peering |
| Internship | EWB Courses |
| Cloud Provider | Amazon Web Services |
| AWS Region | US East (N. Virginia) – `us-east-1` |
| Networking Service | Amazon VPC |
| Compute Service | Amazon EC2 |
| Connection Type | VPC Peering |
| Number of VPCs | 2 |
| Number of EC2 Instances | 3 |

## 2. Problem Statement

Organizations often deploy applications across multiple VPCs to achieve network isolation and improve infrastructure organization.

However, resources in separate VPCs cannot communicate with one another by default.

The objective of this project is to establish private communication between two independent VPCs without exposing the application instances to the public internet.

This is achieved through VPC Peering, route table configuration, security group rules and private IP connectivity.

## 3. Project Objectives

- Create two independent VPCs with non-overlapping CIDR ranges.
- Configure public and private subnets.
- Attach an Internet Gateway to VPC-A.
- Launch a Bastion Host and two private EC2 instances.
- Establish a VPC Peering connection between both VPCs.
- Configure route tables to enable communication between the VPCs.
- Implement appropriate security group rules.
- Access private EC2 instances through SSH ProxyJump.
- Validate private communication using ICMP ping tests.
- Document the implementation and connectivity results.

## 4. AWS Architecture

### Network Design

The architecture consists of two VPCs connected through a VPC Peering connection.

**VPC-A**
- Public subnet containing the Bastion Host.
- Private subnet containing App-A.
- Internet Gateway attached to the VPC.

**VPC-B**
- Private subnet containing App-B.
- No Internet Gateway required for this project.

### IP Address Allocation

| Resource | CIDR / Private IP |
|---|---|
| VPC-A | `10.10.0.0/16` |
| VPC-B | `10.20.0.0/16` |
| VPC-A Public Subnet | `10.10.1.0/24` |
| VPC-A Private Subnet | `10.10.2.0/24` |
| VPC-B Private Subnet | `10.20.1.0/24` |
| Bastion Host | `10.10.1.10` |
| App-A | `10.10.2.10` |
| App-B | `10.20.1.10` |

The non-overlapping CIDR ranges allow routing between the two VPCs.

## 5. AWS Resources Used

| AWS Resource | Resource Name |
|---|---|
| VPC-A | `ewb-vpc-a` |
| VPC-B | `ewb-vpc-b` |
| Internet Gateway | `ewb-igw-a` |
| Public Route Table | `ewb-rt-a-public` |
| Private Route Table A | `ewb-rt-a-private` |
| Private Route Table B | `ewb-rt-b-private` |
| Bastion Security Group | `ewb-sg-bastion` |
| App-A Security Group | `ewb-sg-app-a` |
| App-B Security Group | `ewb-sg-app-b` |
| VPC Peering | `ewb-a-b-peer` |

**VPC Peering Connection ID**

`pcx-0d16f6699363c4f8d`

**EC2 Instances**

| Instance | Private IP | Purpose |
|---|---|---|
| Bastion | `10.10.1.10` | SSH entry point |
| App-A | `10.10.2.10` | Private application instance in VPC-A |
| App-B | `10.20.1.10` | Private application instance in VPC-B |

App-A and App-B were verified to have no public IPv4 addresses.

## 6. Implementation Steps

### Step 1 – Create VPCs

Created two VPCs with separate IPv4 CIDR blocks:

- VPC-A: `10.10.0.0/16`
- VPC-B: `10.20.0.0/16`

DNS support was enabled for the VPC configuration.

### Step 2 – Create Subnets

Created three subnets:

- Public subnet in VPC-A: `10.10.1.0/24`
- Private subnet in VPC-A: `10.10.2.0/24`
- Private subnet in VPC-B: `10.20.1.0/24`

The public subnet was configured to assign public IPv4 addresses automatically, while the private subnets were configured without automatic public IPv4 assignment.

### Step 3 – Configure Internet Gateway

Created an Internet Gateway named `ewb-igw-a` and attached it to VPC-A.

Added the default route `0.0.0.0/0` to the public route table, targeting the Internet Gateway.

This provides internet connectivity to the public subnet.

### Step 4 – Configure Route Tables

Created three route tables and associated them with the appropriate subnets.

| Route Table | Destination | Target |
|---|---|---|
| `ewb-rt-a-public` | `0.0.0.0/0` | Internet Gateway |
| `ewb-rt-a-public` | `10.20.0.0/16` | VPC Peering |
| `ewb-rt-a-private` | `10.20.0.0/16` | VPC Peering |
| `ewb-rt-b-private` | `10.10.0.0/16` | VPC Peering |

Each route table also retains its automatically created local route.

These routes allow traffic destined for the peer VPC to be forwarded through the VPC Peering connection.

### Step 5 – Configure Security Groups

Created three security groups to control network traffic.

**Bastion Security Group – `ewb-sg-bastion`**

- SSH access on TCP port 22 from the administrator's public IP address.
- SSH access for AWS EC2 Instance Connect through its applicable prefix list.

**App-A Security Group – `ewb-sg-app-a`**

- SSH access from the Bastion Host private IP.
- ICMP Echo Request access from App-B.

**App-B Security Group – `ewb-sg-app-b`**

- SSH access from the Bastion Host private IP.
- ICMP Echo Request access from App-A.

Default outbound rules were retained for the lab.

### Step 6 – Launch EC2 Instances

Launched three EC2 instances using Amazon Linux 2023.

| Instance | Subnet | Private IP |
|---|---|---|
| Bastion | Public subnet – VPC-A | `10.10.1.10` |
| App-A | Private subnet – VPC-A | `10.10.2.10` |
| App-B | Private subnet – VPC-B | `10.20.1.10` |

The Bastion Host was configured with a public IPv4 address.

App-A and App-B were configured as private instances without public IPv4 addresses.

### Step 7 – Create VPC Peering Connection

Created a VPC Peering connection between VPC-A and VPC-B.

Connection name: `ewb-a-b-peer`

Connection ID: `pcx-0d16f6699363c4f8d`

The peering request was accepted, and the connection reached the Active state.

### Step 8 – Configure Peering Routes

Added the required routes to the relevant route tables.

- VPC-A public route table: route to `10.20.0.0/16` through the peering connection.
- VPC-A private route table: route to `10.20.0.0/16` through the peering connection.
- VPC-B private route table: route to `10.10.0.0/16` through the peering connection.

This enabled bidirectional routing between the VPC networks.

### Step 9 – Configure SSH Access Through Bastion

Configured SSH access from Windows PowerShell using an SSH configuration file and the project key pair.

Used SSH ProxyJump to access the private EC2 instances through the Bastion Host.

Example SSH configuration:

```sshconfig
Host ewb-bastion
    HostName <BASTION_PUBLIC_IP>
    User ec2-user
    IdentityFile ~/.ssh/ewb-lab.pem

Host ewb-app-a
    HostName 10.10.2.10
    User ec2-user
    ProxyJump ewb-bastion
    IdentityFile ~/.ssh/ewb-lab.pem

Host ewb-app-b
    HostName 10.20.1.10
    User ec2-user
    ProxyJump ewb-bastion
    IdentityFile ~/.ssh/ewb-lab.pem
```

The private instances were accessed using:

```powershell
ssh ewb-app-a
ssh ewb-app-b
```

This approach allows private instance access without assigning public IP addresses to the application instances.

## 7. Connectivity Testing and Results

Connectivity was tested using the Linux `ping` command from the EC2 instances.

### Test 1 – App-A to App-B

Executed on App-A:

```bash
ping -c 4 10.20.1.10
```

**Observed Result:**

- 4 packets transmitted.
- 4 packets received.
- 0% packet loss.

This confirms successful ICMP communication from VPC-A to VPC-B.

### Test 2 – App-B to App-A

Executed on App-B:

```bash
ping -c 4 10.10.2.10
```

**Observed Result:**

- 4 packets transmitted.
- 4 packets received.
- 0% packet loss.

This confirms successful ICMP communication from VPC-B to VPC-A.

### Test Summary

| Validation | Result |
|---|---|
| VPC Peering status | Active |
| Bastion SSH access | Successful |
| App-A SSH through Bastion | Successful |
| App-B SSH through Bastion | Successful |
| Forward ICMP test | Successful |
| Reverse ICMP test | Successful |
| App-A public IPv4 address | None |
| App-B public IPv4 address | None |

## 8. Troubleshooting

The project involved troubleshooting SSH connectivity and network access.

### SSH Connectivity

Encountered SSH connection issues during initial access.

The troubleshooting process included:

- Checking the Bastion security group inbound rules.
- Verifying the administrator's public IP address.
- Configuring the SSH key file permissions in Windows.
- Correcting the SSH configuration filename.
- Configuring SSH ProxyJump for private instances.

### Private Instance Access

Verified that private EC2 instances could be accessed through the Bastion Host.

### Network Connectivity

Validated the peering configuration through successful ICMP tests in both directions.

When troubleshooting VPC connectivity, important checks include:

- Peering connection status.
- Route table destinations and targets.
- Security group inbound and outbound rules.
- Network ACL rules.
- Instance private IP addresses.

## 9. Key Learning Outcomes

Through this project, I gained practical experience with:

- Amazon VPC creation and configuration.
- IPv4 CIDR planning and subnet design.
- Public and private subnet architecture.
- Internet Gateway configuration.
- AWS route table management.
- VPC Peering connections.
- Security group configuration.
- EC2 instance deployment.
- Bastion Host architecture.
- SSH ProxyJump.
- Private IP communication.
- Network troubleshooting and connectivity testing.

## 10. Evidence and Screenshots

The `screenshots/` directory contains evidence captured from the AWS Management Console and EC2 terminal sessions.

The evidence is organized into the following categories:

| Evidence | Description |
|---|---|
| EC2 instance details | Instance names, private IPs and public IP status |
| VPC Peering | Active peering connection |
| Route tables | Public and private routing configuration |
| Security groups | Bastion, App-A and App-B inbound rules |
| SSH access | Bastion and private instance connections |
| Forward ping | App-A to App-B connectivity |
| Reverse ping | App-B to App-A connectivity |

Only actual captured screenshots should be included in this directory.

## 11. Conclusion

This AWS VPC Peering project provided hands-on experience in establishing private communication between two independent Virtual Private Clouds.

By configuring VPCs, subnets, route tables, security groups and a VPC Peering connection, private connectivity was successfully established between EC2 instances in separate VPCs.

The project also demonstrated Bastion Host access using SSH ProxyJump and validated bidirectional communication using ICMP ping tests.

Overall, this project strengthened my practical understanding of AWS networking, cloud infrastructure configuration, network security and connectivity troubleshooting.

---

**Security Note:** Never upload private SSH keys, AWS credentials, access tokens or other sensitive information to GitHub.
