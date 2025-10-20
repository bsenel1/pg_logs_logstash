# postgres-logstash
## Introduction

This project automates the collection and processing of **PostgreSQL logs** using **Logstash**.  
It parses both **connection** and **audit** logs, enriches them with host metadata, and stores the results in a **PostgreSQL / TimescaleDB** database for analysis and monitoring.

### What It Does

- Collects PostgreSQL log files directly from the host system.  
- Parses connection and audit events using **Grok** patterns.  
- Adds metadata such as `cluster_name`, `server_name`, and `server_ip` via a Ruby filter.  
- Writes structured log data into a PostgreSQL database using the **JDBC output plugin**.  
- Supports continuous file tracking with **sincedb** for real-time updates.  
- Can run either on the host or inside a **Docker container**.  
- Includes an optional **Bash script** for pipeline automation and restarts.  
- Designed and tested on **Rocky Linux 9**.
