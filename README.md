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
