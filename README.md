# AWS Database Migration: EC2 MySQL to Managed Amazon RDS
## Project Overview

In modern cloud architectures, transitioning from self-managed database instances to managed services is a critical step for achieving scalability, high availability, and reducing operational overhead. This project demonstrates a complete **heterogeneous data migration** from a standalone MySQL server hosted on **Amazon EC2** to a managed **Amazon RDS for MySQL** instance.

The migration is executed using **AWS Database Migration Service (DMS)** with a **Full Load + Change Data Capture (CDC)** strategy. This approach ensures:

- The initial dataset is migrated efficiently
- Any real-time transactions occurring on the source are continuously synchronized to the target
- Minimal application downtime during live cutover
- Complete data integrity throughout the migration process

This architecture is production-ready and can be adapted for live database migrations in enterprise environments.

---

## Architecture
![Architecture Diagram](https://github.com/KmNandini12/Data_Migration_Using_DMS/blob/99562de0b3d1e3984c3e60b237f5f98fa2e87e59/screenshots/Architecture.png)
The architecture is designed to bridge the gap between legacy on-premises-style deployments and modern cloud-managed database services.Source: MySQL 8.0.45 (Community Server) hosted on Ubuntu 24.04 LTS (Amazon EC2).Target: Managed Amazon RDS (MySQL Engine).Orchestration: AWS DMS Replication Instance (t3.medium).Security: Multi-layered Security Groups (SGs) restricting traffic to the DMS Replication Instance and internal VPC CIDRs.

##  Objectives & Key ResultsSource Hardening
Configured a Linux-based MySQL source for replication compatibility.Managed Target Deployment: Initialized a resilient RDS instance with public access for testing and private connectivity for production-mimicking security.Pipeline Engineering: Built a robust migration pipeline using DMS Endpoints and Replication Instances.Real-time Sync: Executed a Full Load + CDC task to achieve live, ongoing synchronization.

### Network Configuration

| Component | Security Group Rules |
|-----------|---------------------|
| EC2 (Source) | SSH (22) from My IP, MySQL (3306) from DMS SG |
| RDS (Target) | MySQL (3306) from DMS SG and EC2 Private IP |
| DMS Instance | Within same VPC as EC2 and RDS |

---

## Prerequisites

Before replicating this project, ensure you have:

| Requirement | Details |
|-------------|---------|
| **AWS Account** | Active account with VPC access and appropriate service limits |
| **IAM Permissions** | Full access to EC2, RDS, and DMS services |
| **Local Environment** | Terminal with SSH client and MySQL client installed |
| **Basic Knowledge** | Familiarity with MySQL, AWS Console, and networking concepts |
| **Key Pair** | EC2 key pair for SSH access to the source instance |

---

## Technologies Used

| Technology | Purpose |
|------------|---------|
| **Amazon EC2** | Hosting source MySQL database (Ubuntu 22.04 LTS) |
| **Amazon RDS for MySQL** | Managed target database service |
| **AWS DMS** | Migration service for full load + CDC |
| **MySQL 8.0** | Database engine on both source and target |
| **Ubuntu 22.04 LTS** | Operating system for source EC2 instance |
| **AWS Security Groups** | Network access control |

---

## Step-by-Step Implementation

### 1. Security Group Configuration

#### EC2 Security Group
- **Name**: `ec2-mysql-sg`
- **Inbound Rules**:
  - SSH (port 22) – Source: My IP (for administrative access)
  - MySQL (port 3306) – Source: DMS Replication Instance Security Group

#### RDS Security Group
- **Name**: `rds-mysql-sg`
- **Inbound Rules**:
  - MySQL (port 3306) – Source: DMS Replication Instance Security Group
  - MySQL (port 3306) – Source: EC2 Instance Private IP (for verification)

### 2. Source Database Setup (EC2)

#### Launch EC2 Instance
- **AMI**: Ubuntu 22.04 LTS
- **Instance Type**: t2.micro (free tier eligible)
- **Security Group**: `ec2-mysql-sg`
- **Key Pair**: Your existing key pair

#### Install and Configure MySQL

```bash
# Update system packages
sudo apt update && sudo apt upgrade -y

# Install MySQL server
sudo apt install mysql-server -y

# Verify installation
mysql --versionied that the record appeared in the RDS Target within seconds, confirming a successful CDC handshake.


