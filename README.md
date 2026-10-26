AWS VPC Peering Project

📌 Project Overview

This project demonstrates how to securely connect two private Virtual Private Clouds (VPCs) in AWS using a VPC Peering Connection. The setup ensures that application servers in different VPCs can communicate using their private IPv4 addresses without traversing the public internet.

🏗 Architecture

The network infrastructure consists of the following components:

VPC A (10.10.0.0/16)

Public Subnet (10.10.1.0/24): Contains a Bastion Host with an attached Internet Gateway (IGW) for external administrative access.

Private Subnet (10.10.2.0/24): Contains an application server (App-A) with no public internet access.

VPC B (10.20.0.0/16)

Private Subnet (10.20.1.0/24): Contains an application server (App-B) with no internet gateway and no public internet access.

VPC Peering Connection: A direct network route linking VPC A and VPC B.

🛠 Prerequisites

An active AWS Account.

Basic knowledge of AWS VPCs, Subnets, and CIDR blocks.

An SSH client (e.g., OpenSSH) and a generated EC2 Key Pair (.pem file).

🚀 Setup & Configuration Steps

1. Network Setup

Created VPC A and VPC B with non-overlapping CIDR blocks.

Created one Public Subnet and one Private Subnet in VPC A, and one Private Subnet in VPC B.

Attached an Internet Gateway to VPC A and configured the Public Route Table to allow internet egress (0.0.0.0/0).

2. Security Groups

Configured explicit, strict security groups:

Bastion SG: Allows inbound SSH (Port 22) only from the administrator's local public IP.

App-A & App-B SGs: Allow inbound SSH (Port 22) only from the Bastion host's private IP (10.10.1.10/32). Allow custom ICMP (Ping) only between the specific private IPs of App-A and App-B.

3. Compute (EC2)

Launched Amazon Linux 2023 t3.micro instances.

Enabled public IP assignment for the Bastion Host only.

Disabled public IP assignment for both App-A and App-B.

4. VPC Peering & Routing

Requested a VPC Peering connection from VPC A to VPC B and accepted it.

Updated the Private Route Table in VPC A to route 10.20.0.0/16 traffic to the peering connection ID.

Updated the Private Route Table in VPC B to route 10.10.0.0/16 traffic back to the peering connection ID (Return Route).

Updated the Public Route Table in VPC A to route 10.20.0.0/16 to the peering connection ID (Required for Bastion SSH proxy jump).

🧪 Validation & Testing

Ping Tests: Verified 0% packet loss when pinging App-B's private IP from App-A, and vice versa.

SSH ProxyJump: Successfully accessed App-B securely from a local machine using the Bastion host as a jump server via ~/.ssh/config.

Controlled Failure: Temporarily removed the ICMP security group rule to prove that the strict security constraints actively block unauthorized traffic over the peering connection.

🧹 Cleanup

To avoid incurring unnecessary AWS charges, all resources were torn down after testing:

Terminated EC2 instances and deleted associated EBS volumes.

Deleted the VPC Peering connection and custom Route Tables.

Deleted Security Groups, Subnets, Internet Gateways, and both VPCs.
