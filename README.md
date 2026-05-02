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
