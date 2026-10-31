# AWS VPC Peering – Cross-VPC Private Communication

## Project Overview
This project demonstrates private communication between two Amazon Virtual Private Clouds (VPCs) using AWS VPC Peering.

**Cloud Platform:** Amazon Web Services (AWS)  
**Region:** US East (N. Virginia) – `us-east-1`  
**Project:** EWB AWS Internship

## Architecture

| Component | Configuration |
|---|---|
| VPC-A | `10.10.0.0/16` |
| VPC-B | `10.20.0.0/16` |
| VPC-A Public Subnet | `10.10.1.0/24` |
| VPC-A Private Subnet | `10.10.2.0/24` |
| VPC-B Private Subnet | `10.20.1.0/24` |
| Bastion | `10.10.1.10` |
| App-A | `10.10.2.10` |
| App-B | `10.20.1.10` |

## Implementation

- Created two VPCs with non-overlapping CIDR blocks.
- Configured public and private subnets.
- Attached an Internet Gateway to VPC-A.
- Configured three route tables.
- Created security groups for Bastion, App-A and App-B.
- Launched three EC2 instances.
- Established an active VPC Peering connection.
- Configured routes for communication between the VPCs.
- Implemented SSH access through a Bastion host using ProxyJump.

## Connectivity Testing

| Test | Result |
|---|---|
| SSH to Bastion | Successful |
| SSH to App-A through Bastion | Successful |
| SSH to App-B through Bastion | Successful |
| App-A to App-B ping | 4 received, 0% loss |
| App-B to App-A ping | 4 received, 0% loss |
| Private instances public IPv4 check | No public IPv4 addresses |

## Key Learnings

- AWS VPC and subnet configuration
- IPv4 CIDR planning
- Route tables and Internet Gateway
- VPC Peering and private routing
- Security Groups
- EC2 and SSH ProxyJump
- Network connectivity troubleshooting

## Evidence

Screenshots of the AWS configuration and connectivity tests will be available in the `screenshots/` directory.

## Conclusion

This project provided hands-on experience with AWS networking, VPC Peering, route configuration, security groups, bastion-host access and private communication between EC2 instances.
