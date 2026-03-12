# Data_Migration_Using_DMS
# AWS DMS Database Migration Lab

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![AWS](https://img.shields.io/badge/AWS-DMS-orange)](https://aws.amazon.com/dms/)

A hands-on lab demonstrating database migration using AWS Database Migration Service (DMS). This project showcases a complete migration workflow from a source database to a target database with minimal downtime.

## 📋 Overview

This lab provides a practical implementation of database migration using AWS DMS. It includes step-by-step instructions, Terraform/IaC scripts, and configuration files to perform a seamless database migration between different database engines or same-engine migrations.

## 🎯 Learning Objectives

- Set up source and target database instances
- Configure AWS DMS replication instances
- Create and manage DMS endpoints
- Implement full load + CDC (Change Data Capture) migration
- Monitor and validate migration tasks
- Handle common migration challenges and errors

## 🏗️ Architecture


## 🚀 Prerequisites

- AWS Account with appropriate permissions
- AWS CLI installed and configured
- Terraform (v1.0+) or AWS CloudFormation experience
- Basic understanding of:
  - Relational databases
  - AWS networking (VPC, subnets, security groups)
  - SQL queries

## 📦 Supported Database Engines

**Source Databases:**
- MySQL (5.6, 5.7, 8.0)
- PostgreSQL (9.4+)
- Oracle (11g+)
- SQL Server (2012+)

**Target Databases:**
- Amazon RDS (MySQL, PostgreSQL, Oracle, SQL Server)
- Amazon Aurora
- Amazon Redshift
- Amazon S3

## 🛠️ Installation & Setup

### 1. Clone the Repository
```bash
git clone https://github.com/yourusername/aws-dms-migration-lab.git
cd aws-dms-migration-lab
```
aws configure
# Enter your AWS Access Key ID, Secret Access Key, and default region
cd terraform
terraform init
terraform plan
terraform apply -auto-approve
aws cloudformation create-stack \
  --stack-name dms-migration-lab \
  --template-body file://cloudformation/template.yaml \
  --parameters ParameterKey=KeyName,ParameterValue=your-key-pair

  # Run the setup script to create sample data
mysql -h source-db-endpoint -u admin -p < scripts/source-db-setup.sql

# Create replication instance, endpoints, and tasks
python scripts/create-dms-resources.py

aws-dms-migration-lab/
├── terraform/
│   ├── main.tf                 # Main Terraform configuration
│   ├── variables.tf             # Variable definitions
│   ├── outputs.tf               # Output definitions
│   └── modules/
│       ├── vpc/                 # VPC configuration
│       ├── rds/                  # RDS instances
│       └── dms/                  # DMS resources
├── cloudformation/
│   ├── template.yaml            # CloudFormation template
│   └── parameters.json          # Stack parameters
├── scripts/
│   ├── source-db-setup.sql      # Source database initialization
│   ├── verify-migration.sql      # Migration verification queries
│   ├── create-dms-resources.py   # DMS setup automation
│   └── monitoring/               # Monitoring scripts
├── dms-config/
│   ├── endpoints.json            # Endpoint configurations
│   ├── replication-task.json     # Task configurations
│   └── mapping-rules/            # Table mapping rules
├── docs/
│   ├── architecture.md           # Architecture deep dive
│   ├── troubleshooting.md        # Common issues and solutions
│   └── best-practices.md         # DMS best practices
└── test-data/
    ├── sample-schema.sql         # Sample database schema
    └── sample-data.csv           # Test data files
