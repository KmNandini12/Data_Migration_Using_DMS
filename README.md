# AWS Database Migration: EC2 MySQL to Managed Amazon RDS
## Leveraging AWS DMS for Zero-Downtime Heterogeneous Migration
This repository details the end-to-end implementation of a database migration strategy moving from a self-managed MySQL instance on Amazon EC2 to a high-availability Amazon RDS environment. By utilizing AWS Database Migration Service (DMS), the project achieves seamless data transition using a Full Load + Change Data Capture (CDC) approach, ensuring zero data loss and minimal application downtime.

## System Architecture
The architecture is designed to bridge the gap between legacy on-premises-style deployments and modern cloud-managed database services.Source: MySQL 8.0.45 (Community Server) hosted on Ubuntu 24.04 LTS (Amazon EC2).Target: Managed Amazon RDS (MySQL Engine).Orchestration: AWS DMS Replication Instance (t3.medium).Security: Multi-layered Security Groups (SGs) restricting traffic to the DMS Replication Instance and internal VPC CIDRs.

##  Objectives & Key ResultsSource Hardening
Configured a Linux-based MySQL source for replication compatibility.Managed Target Deployment: Initialized a resilient RDS instance with public access for testing and private connectivity for production-mimicking security.Pipeline Engineering: Built a robust migration pipeline using DMS Endpoints and Replication Instances.Real-time Sync: Executed a Full Load + CDC task to achieve live, ongoing synchronization.

## 🛠️ Implementation Deep-Dive
### 1. Infrastructure Connectivity
Created a "least-privilege" networking environment to allow components to communicate:


DMS-to-Source: Security Group rules allowed MySQL (3306) traffic from the DMS Replication Instance to the EC2 Source.


DMS-to-Target: Security Group rules allowed MySQL (3306) from the DMS instance to the RDS Target.

### 2. Preparing the Source for CDC
A database migration is only as good as its logs. To enable Change Data Capture (CDC), the source MySQL server required specific binary logging configurations:


Configuration: Modified /etc/mysql/mysql.conf.d/mysqld.cnf to enable log_bin and set binlog_format = ROW.


User Privileges: Created a dedicated dms_user with REPLICATION CLIENT and REPLICATION SLAVE permissions to allow DMS to read the binary logs.

### 3. Migration Task Execution
Configured the DMS task with the following critical parameters:


Migration Type: "Migrate existing data and replicate ongoing changes" (Full Load + CDC).


LOB Mode: Full LOB mode to ensure large data objects were handled correctly.


Table Mapping: Targeted the my_source_db schema with a wildcard % to include all tables.

## 🛠️ Troubleshooting: Solving the "Backup but No CDC" Failure
### The Challenge
Initially, the DMS task would successfully complete the Full Load (the "backup" of existing data) but would fail to start or execute the CDC (real-time changes). The task would either stop or show a "Running with errors" status because it couldn't see ongoing transactions.

### The Root Cause
The failure occurred because the Binary Logs on the EC2 MySQL source were either disabled or incorrectly formatted. Without ROW-level logging, DMS cannot translate binary log events into SQL statements for the target database.

### The Resolution
I overcame this technical hurdle through a four-pronged approach:


Binary Log Enablement: Re-verified and corrected the mysqld.cnf settings, ensuring server-id was unique and binlog_format was strictly set to ROW.


Service Synchronization: Performed a hard restart of the MySQL service (sudo systemctl restart mysql) to flush the configuration changes.


Endpoint Refinement: Switched the source endpoint from a public DNS to the Private IP (172.31.18.181) to reduce latency and networking "handshake" jitter between DMS and EC2.


Task Type Re-Alignment: Re-created the DMS task from scratch to ensure the "Ongoing Changes" flag was properly registered with the newly enabled binary logs.

##  Data Verification

Initial Load: Verified that the students table and seed data appeared in RDS immediately after the "Starting" phase.

## Conclusion
This project demonstrates the transition from a traditional, high-maintenance database model to a modern, managed cloud environment. By resolving the CDC configuration issues, I successfully eliminated the need for a maintenance window, proving that data consistency can be maintained in real-time during a live cutover.


Live Sync Check: Performed a INSERT INTO students (name) VALUES ('Migration_Success_Test'); on the EC2 instance.


Result: Verified that the record appeared in the RDS Target within seconds, confirming a successful CDC handshake.

