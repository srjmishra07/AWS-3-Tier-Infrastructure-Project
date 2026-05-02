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
*Status: Phase 1 Completed ✅*
