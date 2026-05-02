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
