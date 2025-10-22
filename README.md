# 🐳 PostgreSQL + Logstash Audit Pipeline (Docker Setup)

This branch provides a **containerized environment** for PostgreSQL audit and connection log collection using **Logstash**.  
It includes a preconfigured setup with **PostgreSQL 17 + TimescaleDB** and **Logstash** connected via JDBC.

---

## 🚀 Quick Start

### Clone and switch to this branch
```bash
git clone https://github.com/bsenel1/postgres-logstash.git
cd postgres-logstash
git checkout docker
```
### Start the containers
```bash
docker-compose up -d
```
