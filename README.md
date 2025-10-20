# PostgreSQL Log Analysis Pipeline

## 1. Project Description

This project provides a host-based pipeline for collecting and analyzing **PostgreSQL logs** using **Logstash**.  
It focuses on parsing **connection** and **audit** events, enriching them with system metadata, and storing them in a **PostgreSQL / TimescaleDB** database for structured analysis.  

The system runs directly on **Rocky Linux 9** and is designed for production environments where logs are processed in real-time without containerization.

### Key Features
- Reads PostgreSQL log files directly from `/var/log/postgresql/`
- Extracts structured data using **Grok** filters  
- Adds environment metadata (`cluster_name`, `server_name`, `server_ip`) via **Ruby filter**  
- Stores parsed data in PostgreSQL using **JDBC output**  
- Tracks file read state with **sincedb** for continuous updates  
- Includes optional **Bash scripts** for pipeline automation and restarts  

---

## 2. Installation

### Step 1 – Install PostgreSQL

```bash
sudo dnf install postgresql17-server postgresql17-contrib -y
sudo /usr/pgsql-17/bin/postgresql-17-setup initdb
sudo systemctl enable --now postgresql-17
