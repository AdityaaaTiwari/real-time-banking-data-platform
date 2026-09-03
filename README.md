## 🏦 Real-Time Banking Data Platform

> ⚡ An end-to-end Data Engineering platform for real-time banking data ingestion, CDC, orchestration, transformation, and analytics.

---

## 📌 Project Overview

This project simulates a modern banking data ecosystem where customer, account, and transaction data is generated in PostgreSQL and processed through a real-time data pipeline.

The platform uses **Debezium + Apache Kafka** for Change Data Capture, **MinIO** for raw data storage, **Apache Airflow** for orchestration, **Snowflake** as the analytical warehouse, and **dbt** for transformations and historical data management.

### 🎯 What this project demonstrates

- 🔄 Real-time Change Data Capture
- 📡 Event-driven data streaming
- 🗄️ OLTP → OLAP data movement
- ☁️ Cloud data warehousing
- 🧱 Bronze → Silver → Gold architecture
- 📊 Analytics-ready data models
- 🕒 Historical data tracking with SCD Type 2
- ⚙️ Automated workflow orchestration
- 🧪 Data quality testing
- 🚀 CI/CD automation

---

## 🏗️ Architecture

The following architecture shows the complete data flow from the operational banking database to the analytics layer.

architecture.png

### 🔄 Pipeline Flow

🐘 PostgreSQL → 🔄 Debezium → 📡 Kafka → 🪣 MinIO → ⚙️ Airflow → ❄️ Snowflake → 🧱 dbt → 📊 Analytics
````

---

## 🔄 End-to-End Pipeline

```text
🐘 PostgreSQL
      │
      │ Database Changes
      ▼
🔄 Debezium
      │
      │ CDC Events
      ▼
📡 Apache Kafka
      │
      │ Raw Events
      ▼
🪣 MinIO
      │
      │ Orchestration
      ▼
⚙️ Apache Airflow
      │
      │ Data Ingestion
      ▼
❄️ Snowflake
      │
      ├── 🥉 Bronze
      │
      ├── 🥈 Silver
      │
      └── 🥇 Gold
             │
             ▼
        🧱 dbt
             │
             ▼
        📊 Analytics
```

---

## ⚙️ Tech Stack

| Technology            | Role                          |
| --------------------- | ----------------------------- |
| 🐍 **Python**         | Data generation & scripting   |
| 🐘 **PostgreSQL**     | OLTP source database          |
| 🔄 **Debezium**       | Change Data Capture           |
| 📡 **Apache Kafka**   | Event streaming               |
| 🪣 **MinIO**          | Raw object storage            |
| ⚙️ **Apache Airflow** | Workflow orchestration        |
| ❄️ **Snowflake**      | Cloud data warehouse          |
| 🧱 **dbt**            | SQL transformations & testing |
| 🐳 **Docker**         | Containerized infrastructure  |
| 🔧 **Git**            | Version control               |
| 🚀 **GitHub Actions** | CI/CD                         |

---

## 🗃️ Data Model

The simulated banking system contains three major entities.

### 👤 Customers

```text
customer_id
first_name
last_name
email
phone
date_of_birth
created_at
updated_at
```

### 🏦 Accounts

```text
account_id
customer_id
account_type
balance
currency
status
created_at
updated_at
```

### 💳 Transactions

```text
transaction_id
account_id
transaction_type
amount
transaction_status
transaction_timestamp
```

### 🔗 Relationships

```text
👤 Customer
     │
     │ 1 : N
     ▼
🏦 Account
     │
     │ 1 : N
     ▼
💳 Transaction
```

---

## 🔥 Change Data Capture

Debezium captures changes from PostgreSQL using the database's Write-Ahead Log (WAL).

Instead of performing full database extraction repeatedly, the pipeline processes changes such as:

```text
INSERT
UPDATE
DELETE
```

Flow:

```text
PostgreSQL
    │
    ▼
WAL
    │
    ▼
Debezium
    │
    ▼
Kafka Topics
```

This allows downstream systems to consume incremental changes.

---

## 📡 Apache Kafka

Kafka acts as the event streaming layer between the source database and downstream consumers.

```text
PostgreSQL
     ↓
 Debezium
     ↓
  Kafka
     ↓
  MinIO
```

This decouples the source system from downstream processing.

---

## 🪣 MinIO

MinIO provides S3-compatible object storage for raw CDC events.

Kafka events are consumed and persisted before downstream processing.

```text
Kafka
  ↓
MinIO
  ↓
Raw CDC Data
```

The raw layer also allows data to be reprocessed when required.

---

## ⚙️ Apache Airflow

Airflow manages the pipeline workflows and dependencies.

### Main workflows

* 📥 MinIO → Snowflake ingestion
* 🔄 Data processing
* 🕒 Snapshot execution
* 🔁 Scheduled pipeline execution

### DAGs

```text
minio_to_snowflake_banking
SCD2_snapshots
```

Airflow provides:

* Scheduling
* Task dependencies
* Retries
* Monitoring
* Task-level logging

---

## ❄️ Snowflake Data Warehouse

Snowflake is used as the analytical data warehouse.

The warehouse follows a layered architecture:

```text
🥉 Bronze
    ↓
🥈 Silver
    ↓
🥇 Gold
```

### 🥉 Bronze Layer

Raw ingested data with minimal transformation.

### 🥈 Silver Layer

Cleaned and standardized datasets.

### 🥇 Gold Layer

Business-ready datasets designed for analytics.

---

## 🧱 dbt Transformations

dbt handles SQL transformations inside Snowflake.

```text
Raw Data
    ↓
Staging Models
    ↓
Dimensions + Facts
    ↓
Analytics Models
```

dbt is used for:

* 🧹 Data transformations
* 🧪 Data quality tests
* 🔗 Model dependencies
* 📚 Documentation
* ⚡ Incremental models
* 🕒 Historical snapshots

---

## 🕒 SCD Type 2

Slowly Changing Dimension Type 2 is used to preserve historical versions of changing records.

Example:

```text
account_id | status | valid_from | valid_to
-----------|--------|------------|---------
101        | ACTIVE | 2026-01-01 | 2026-03-10
101        | CLOSED | 2026-03-10 | NULL
```

Instead of overwriting the old record, a new version is created.

This allows historical analysis such as:

* 📅 What was the account status on a particular date?
* 🔍 When did a record change?
* ↩️ What was the previous value?
* 🕒 Which version was active at a given time?

---

## 🐳 Local Infrastructure

The project uses Docker to run the development infrastructure.

Main services:

```text
🐘 PostgreSQL
📡 Kafka
🔌 Kafka Connect
🔄 Debezium
🪣 MinIO
⚙️ Airflow
```

Snowflake is used as the cloud data warehouse target.

---

## 📂 Repository Structure

```text
real-time-banking-data-platform/

├── .github/
│   └── workflows/
│       ├── ci.yml
│       └── cd.yml
│
├── banking_dbt/
│   ├── models/
│   │   ├── staging/
│   │   ├── marts/
│   │   └── sources.yml
│   │
│   ├── snapshots/
│   └── dbt_project.yml
│
├── consumer/
│   └── kafka_to_minio.py
│
├── data-generator/
│   └── faker_generator.py
│
├── docker/
│   └── dags/
│       ├── minio_to_snowflake_dag.py
│       └── scd_snapshots.py
│
├── kafka-debezium/
│   └── generate_and_post_connector.py
│
├── postgres/
│   └── schema.sql
│
├── .gitignore
├── docker-compose.yml
├── dockerfile-airflow.dockerfile
├── requirements.txt
└── README.md
```

---

## 🚀 Getting Started

### 1️⃣ Clone

```bash
git clone https://github.com/AdityaaaTiwari/real-time-banking-data-platform.git

cd real-time-banking-data-platform
```

### 2️⃣ Start Infrastructure

```bash
docker compose up -d
```

### 3️⃣ Check Services

```bash
docker compose ps
```

### 4️⃣ Open Airflow

```text
http://localhost:8080
```

Use the Airflow UI to monitor and trigger the pipeline DAGs.

---

## 🧪 CI/CD

GitHub Actions is used to automate project validation.

```text
💻 Code Commit
      ↓
🔍 Build / Lint
      ↓
🧱 dbt Compile
      ↓
🧪 dbt Tests
      ↓
🚀 Deployment
```

This helps maintain consistent code quality and reduces manual deployment steps.

---

## 💡 Key Data Engineering Concepts

* 🔄 Change Data Capture
* 📡 Event Streaming
* 🗄️ OLTP vs OLAP
* 🧱 Data Lake / Object Storage
* ❄️ Cloud Data Warehouse
* 🥉 Bronze / Silver / Gold Architecture
* 📐 Dimensional Modeling
* 📊 Fact & Dimension Tables
* 🕒 SCD Type 2
* ⚡ Incremental Processing
* ⚙️ Workflow Orchestration
* 🧪 Data Quality
* 🐳 Containerization
* 🚀 CI/CD
* 📈 Pipeline Monitoring
* 🔁 Retry & Failure Handling

---

## 🛠️ Engineering Challenges

### 🔹 Incremental Processing

CDC reduces unnecessary full-table extraction by capturing only database changes.

### 🔹 OLTP vs OLAP

PostgreSQL handles operational workloads while Snowflake is used for analytical workloads.

### 🔹 Historical Tracking

SCD Type 2 preserves previous versions of changing records.

### 🔹 Pipeline Orchestration

Airflow manages dependencies, scheduling, monitoring, and retries.

### 🔹 Reproducibility

Docker makes the development environment easier to reproduce.

---

## 🚀 Future Improvements

* ⚡ Add PySpark for large-scale processing
* 📋 Add Kafka Schema Registry
* 🧪 Add advanced data quality validation
* 📊 Add real-time monitoring dashboards
* 🔔 Add Airflow failure alerts
* 🏗️ Add Terraform infrastructure
* ☁️ Deploy the platform on AWS
* 🛡️ Add fraud detection analytics
* 📈 Add banking business dashboards
* ❄️ Optimize Snowflake warehouse usage

---

## 🎯 Interview Summary

### One-Minute Explanation

> I built an end-to-end banking data platform where PostgreSQL acts as the operational source. Debezium captures database changes using CDC and publishes them to Kafka. The events are persisted in MinIO, Airflow orchestrates the ingestion into Snowflake, and dbt transforms the warehouse data into analytics-ready models while maintaining historical changes using SCD Type 2.

### Architecture in One Line

```text
🐘 PostgreSQL
      ↓
🔄 Debezium
      ↓
📡 Kafka
      ↓
🪣 MinIO
      ↓
⚙️ Airflow
      ↓
❄️ Snowflake
      ↓
🧱 dbt
      ↓
📊 Analytics
```

---

## 🏆 Project Highlights

* 🏦 End-to-end banking data platform
* 🔄 PostgreSQL CDC with Debezium
* 📡 Kafka-based event streaming
* 🪣 Raw data storage with MinIO
* ⚙️ Airflow orchestration
* ❄️ Snowflake data warehouse
* 🧱 Bronze / Silver / Gold architecture
* 🧪 dbt transformations and testing
* 🕒 SCD Type 2 historical tracking
* 🐳 Docker-based infrastructure
* 🚀 GitHub Actions CI/CD

---

# 👨‍💻 Author

**Aditya Tiwari**

B.Tech — Computer Science Engineering

**GitHub:** [AdityaaaTiwari](https://github.com/AdityaaaTiwari)

**LinkedIn:** [Aditya Tiwari](https://www.linkedin.com/in/aditya-tiwari-a99739342/)

---

> **Project Note:** This repository is a customized implementation of a modern banking data engineering architecture for hands-on learning, experimentation, and Data Engineering interview preparation.

```

