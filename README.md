## Introduction

This repository delivers a host-based pipeline for collecting and analyzing **PostgreSQL logs** with **Logstash**.  
It parses **connection** and **audit** events, enriches them with host metadata (`cluster_name`, `server_name`, `server_ip`), and stores structured records in **PostgreSQL / TimescaleDB** for querying and monitoring.  
The setup targets **Rocky Linux 9** and assumes direct installation on the host.

---

## Step 1 — Install PostgreSQL (Rocky Linux)

```bash
# 1) Install PostgreSQL server + contrib packages
sudo dnf install postgresql17-server postgresql17-contrib -y

# 2) Initialize the database cluster (creates data dir, default configs)
sudo /usr/pgsql-17/bin/postgresql-17-setup initdb

# 3) Enable and start the service on boot
sudo systemctl enable --now postgresql-17

# 4) Quick health check
systemctl --no-pager status postgresql-17
psql --version  # verify client installed (optional)
```
## Step 2 — Configure Logging and pgaudit
> Goal: Enable detailed connection and audit logging in PostgreSQL by adjusting key parameters in `postgresql.conf`.  
> This step ensures that both standard activity logs and audit trails are captured for Logstash.
---
### Edit PostgreSQL Configuration

Open the main configuration file:

```bash
# Open postgresql.conf inside the data directory
sudo vi /var/lib/pgsql/17/data/postgresql.conf
```
```bash
# Activate the built-in logging collector
logging_collector = on

# Log all new connections to the database
log_connections = on

# Log when a client disconnects
log_disconnections = on

# Store logs in the internal "log" directory
log_directory = 'log'

# Use timestamped filenames for each log file
log_filename = 'postgresql-%a.log'

# Capture all SQL statements for analysis
log_statement = 'all'

# Include useful metadata in each log line
log_line_prefix = '%m [%p] user=%u,db=%d, client_ip=%h app=%a'

# Rotate log files daily or when they exceed 10MB
log_rotation_age = 1d
log_rotation_size = 10MB

# Do not truncate old logs on rotation (keep history)
log_truncate_on_rotation = off

# Load pgaudit extension at server startup
shared_preload_libraries = 'pgaudit'

# Enable full audit logging (DDL, DML, etc.)
pgaudit.log = 'all'

# Skip catalog object logging (less noise)
pgaudit.log_catalog = off
```

## Step 3 — Install and Configure Logstash

> Goal: Install Logstash on the host system, enable it as a service, and prepare the directory structure for pipeline configuration.

---

### Install Logstash

Add Elastic’s official repository and install Logstash:

```bash
# Import Elastic GPG key
sudo rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch

# Create the YUM repo definition
sudo tee /etc/yum.repos.d/logstash.repo > /dev/null <<'EOF'
[logstash-8.x]
name=Elastic repository for 8.x packages
baseurl=https://artifacts.elastic.co/packages/8.x/yum
gpgcheck=1
enabled=1
autorefresh=1
type=rpm-md
EOF

# Install Logstash package
sudo dnf install logstash -y

# Enable Logstash to start automatically at boot
sudo systemctl enable logstash
