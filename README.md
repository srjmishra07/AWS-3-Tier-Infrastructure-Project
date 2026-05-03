# AWS 3-Tier Web Infrastructure Project

## Phase 1: Networking Foundation
In this phase, I established a secure and isolated network environment using a custom AWS VPC.

### Key Implementation Details:
* **Custom VPC:** Created a VPC with CIDR `10.0.0.0/16` and enabled DNS Hostnames.
* **Subnet Strategy:** - **Public Subnets:** 2 subnets for Load Balancer and Bastion Host (Multi-AZ).
  - **Private Subnets:** 2 subnets for App and Database layers to ensure maximum security.
* **Gateways:** - Attached an **Internet Gateway (IGW)** for public traffic.
  - Deployed a **NAT Gateway (Zonal)** in the public subnet to allow private instances to download updates securely.
* **Routing:** Configured separate Route Tables for Public and Private segments to enforce strict traffic isolation.

---
*Status: Phase 1 Completed*
## Phase 2: Compute & Security
In this phase, I deployed the entry point (Bastion Host) and configured security layers.

### Key Implementation Details:
* **Security Groups:**
  - **SRJ-Bastion-SG:** Restricted access to SSH (Port 22) only from my specific local IP.
  - **SRJ-Web-SG:** Configured to allow HTTP (Port 80) from the internet and SSH only from the Bastion Security Group.
* **Key Management:** Generated a secure RSA key pair (`SRJ-Key.pem`) for encrypted access.
* **Bastion Host:** Launched an Amazon Linux 2023 instance in the Public Subnet to act as a secure jump server.
## Phase 3: Application Layer & NAT Connectivity
In this phase, I deployed the web server in a private subnet and verified secure outbound connectivity.

### Key Implementation Details:
* **SSH Agent Forwarding:** Used agent forwarding to securely access private instances from the Bastion host without storing private keys on the jump server.
* **Private Web Server:** Launched an EC2 instance in `SRJ-Private-Subnet-1` with no public IP.
* **NAT Gateway Validation:** Verified that the private instance can reach the internet for updates via the NAT Gateway.
* **Web Services:** Installed and configured Apache HTTP server on the private instance.
## Phase 4: AWS 3-Tier Architecture Implementation (Web Tier)

## 1. Project Objective
Deployment of a secure and highly available Web Tier. An Application Load Balancer (ALB) is used to route external HTTP traffic to an Apache Web Server hosted within a Private Subnet.

## 2. Infrastructure Components
- **VPC:** SRJ-Main-VPC (10.0.0.0/16)
- **Subnets:** Public Subnets (Bastion/ALB), Private Subnets (Web/App)
- **EC2 Instance:** Private Web Server (10.0.3.21)
- **Load Balancer:** Application Load Balancer (Internet-facing)

## 3. Security Group Configurations

| Security Group | Protocol | Port | Source |
| :--- | :--- | :--- | :--- |
| SRJ-ALB-SG | HTTP | 80 | 0.0.0.0/0 |
| SRJ-Web-SG | HTTP | 80 | SRJ-ALB-SG |
| SRJ-Web-SG | SSH | 22 | SRJ-Bastion-SG |

## 4. Post-Deployment Execution Commands
```bash
# Connect to Private Instance via Bastion Host
ssh -A ec2-user@<Bastion-Public-IP>
ssh ec2-user@10.0.3.21

# Install and Configure Apache Web Server
sudo yum install -y httpd
sudo systemctl start httpd
sudo systemctl enable httpd

# Create Application Index Page
echo "<h1>Welcome to SRJ Cloud Project - Phase 3 Successful!</h1>" | sudo tee /var/www/html/index.html

# Verify Service Status
sudo systemctl status httpd
## Phase 5: S3 Storage Integration (Linux & Windows)
In this phase, I integrated AWS S3 for centralized storage and verified cross-platform access using IAM Roles and CLI.

### ### Key Implementation Details:
* **IAM Role for EC2:** Created an IAM Role with `AmazonS3FullAccess` and attached it to the Private Web Server.
* **Linux Integration:** Verified S3 connectivity from the private instance using AWS CLI commands.
* **Windows Integration:** Configured AWS CLI on the local machine using IAM User access keys.
* **Data Verification:** Successfully uploaded and listed files across both platforms.

---
*Status: Phase 5 Completed *
## Phase 5: Shared File System (AWS EFS)
In this phase, I implemented a scalable network file system (EFS) to allow shared data access across multiple platforms and instances.

### ### Key Implementation Details:
* **EFS Creation:** Deployed an Elastic File System within the custom VPC.
* **Security Group Logic:** Configured 'SRJ-EFS-SG' to allow inbound NFS traffic (Port 2049) specifically from the Web Server Security Group.
* **Linux Mounting:** Successfully mounted the EFS on the private instance using `amazon-efs-utils`.
* **Cross-Platform Access:** Enabled 'Client for NFS' on Windows to verify shared storage connectivity.

---
*Status: Phase 6 Completed *
## Phase 7: Relational Database Service (AWS RDS)
In this phase, I deployed a managed MySQL database in a private subnet and established a secure connection from the web server.

### Key Implementation Details:
* **Subnet Grouping:** Created a DB Subnet Group across multiple Availability Zones using only Private Subnets for high availability and security.
* **Security Layering:** Implemented 'SRJ-RDS-SG' with an inbound rule allowing MySQL traffic (Port 3306) exclusively from the Web Server's Security Group.
* **Database Deployment:** Launched a MySQL 8.0 instance on the Free Tier template with Public Access disabled.
* **Connectivity Validation:** Verified the connection from the Private EC2 instance using the MariaDB client via the RDS Endpoint.

---
*Status: Phase 7 Completed *
### Database Schema & Operations:
* **Initialized DB:** Created `srj_project_db` to store application metadata.
* **Schema Design:** Created a `youtube_videos` table with automated ID incrementing and status tracking.
* **Security Validation:** Confirmed that the RDS instance remains unreachable from the public internet, accepting connections only via the Private Subnet EC2 instance.
## Phase 8: High Availability & Secure Private Connectivity
In this phase, I automated the infrastructure to be self-healing and viral-proof, while ensuring secure internet access for private instances.

### Key Implementation Details:
* **Launch Template ('SRJ-Web-Template'):** Defined the standard blueprint for web servers (AMI, t2.micro, SRJ-Key, and SRJ-Web-SG).
* **Automated Bootstrapping:** Included a User Data script to automatically install Apache and MariaDB clients on every new instance launch.
* **Auto Scaling Group (ASG):** Configured 'SRJ-ASG-Web' with a desired capacity of 2. Successfully tested "Self-Healing" by terminating an instance and observing ASG automatically launch a replacement.
* **Secure Outbound Access:** Leveraged a NAT Gateway in the Public Subnet to provide Private Subnet instances with internet access (for updates/patches) without exposing them to the public web.
* **Verification:** Confirmed 100% connectivity by performing a `ping google.com` from a Private instance (10.0.3.21).

---
*Status: Phase 8 Completed *
## Phase 9: CloudWatch Monitoring & SNS Alerts
Implemented a comprehensive monitoring system to track infrastructure health and performance.

### Key Implementation Details:
* **CloudWatch Dashboard:** Created 'SRJ-Project-Monitor' to visualize CPU utilization of the Auto Scaling Group and RDS instance metrics.
* **Aggregated Metrics:** Leveraged 'By Auto Scaling Group' metrics for a holistic view of cluster performance.
* **Proactive Alerting:** Configured an SNS (Simple Notification Service) topic with email subscriptions for real-time notifications.
* **CloudWatch Alarms:** Set a high CPU utilization threshold (70%) that triggers an automated email alert if the infrastructure is under heavy load.
## Phase 10: Scalable Shared Storage (EFS)
Implemented a centralized file system to ensure data consistency across multiple auto-scaling instances.

### Technical Implementation:
* **Storage Engine:** Utilized Amazon EFS (Elastic File System) for regional, highly available shared storage.
* **Security Layer:** Configured `SRJ-EFS-SG` with an Inbound Rule for NFS (Port 2049), restricted only to the `SRJ-Web-SG` (Security Group Peering).
* **Mount Point Creation:** Created a persistent directory structure at `/var/www/html/shared-data` across the fleet.
* **Persistent Mounting:** Configured the Linux `/etc/fstab` file using the EFS Mount Helper and TLS encryption for secure, automated mounting upon system boot.
* **Flags Used:** `_netdev` (ensures network is up before mounting) and `tls` (encryption in transit).

### Verification:
Validated the active mount using `df -h`, confirming the remote file system is successfully mapped to the local web directory.
