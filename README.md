# Orchestrated Scalable Streaming Pipeline

Production-grade real-time data pipeline: **Airflow-orchestrated** Kafka ingestion → **Spark Structured Streaming** processing → **Cassandra** persistence, fully containerized with Docker Compose.

---

## 🎯 What This Solves

Building real-time data infrastructure requires orchestration, fault tolerance, and scalability at every layer. This project demonstrates how to wire together industry-standard components into a working end-to-end streaming system — the same architecture used in production at data-intensive companies.

---

## 🏗 Architecture

```
┌─────────────────────────────────────────────────┐
│                  Airflow (Orchestration)          │
│  DAG: random_people_names (daily @ 01:00 UTC)    │
│  → Fetches data from randomuser.me API           │
│  → Publishes JSON to Kafka topic                 │
└───────────────────┬─────────────────────────────┘
                    │
                    ▼
┌─────────────────────────────────────────────────┐
│           Kafka Cluster (3 brokers)              │
│  Topic: random_names                             │
│  Schema Registry + Kafka UI                      │
└───────────────────┬─────────────────────────────┘
                    │
                    ▼
┌─────────────────────────────────────────────────┐
│        Spark Structured Streaming                │
│  Reads from Kafka topic in real time             │
│  Parses and transforms JSON → structured schema  │
└───────────────────┬─────────────────────────────┘
                    │
                    ▼
┌─────────────────────────────────────────────────┐
│              Cassandra (Storage)                 │
│  Table: spark_streaming.random_names             │
│  Append-only, low-latency writes                 │
└─────────────────────────────────────────────────┘
```

---

## ⚙️ Components

| Component | Role |
|---|---|
| **Airflow** | Orchestrates the pipeline; schedules data ingestion DAG |
| **Kafka (3-broker cluster)** | Distributed message queue; buffers data between producer and consumer |
| **Schema Registry** | Enforces message schema consistency across producers/consumers |
| **Spark Structured Streaming** | Real-time stream processing with exactly-once semantics |
| **Cassandra** | Highly available, write-optimized NoSQL storage |
| **Docker Compose** | Single-command environment setup for all services |

---

## 🚀 Getting Started

### Prerequisites
- Docker & Docker Compose installed

### Start All Services
```bash
git clone https://github.com/Ajay-Deshpande/Orchestrated-Scalable-Streaming-Pipeline
cd Orchestrated-Scalable-Streaming-Pipeline
docker-compose up -d
```

### Access UIs

| Service | URL | Credentials |
|---|---|---|
| Airflow | http://localhost:8080 | admin / admin |
| Kafka UI | http://localhost:8888 | — |
| Spark UI | http://localhost:8085 | — |
| Cassandra | localhost:9042 | cassandra / cassandra |

---

## 📁 Structure

```
├── docker-compose.yml                  # All services definition
├── airflow/
│   ├── Dockerfile
│   ├── requirements.txt
│   └── dags/
│       └── produce_topic_dag.py        # Airflow DAG
└── spark/
    └── spark_streaming.py              # Spark Structured Streaming job
```

---

## 🛠 Stack

**Orchestration:** Apache Airflow  
**Messaging:** Apache Kafka, Confluent Schema Registry  
**Processing:** Apache Spark (Structured Streaming)  
**Storage:** Apache Cassandra  
**Infrastructure:** Docker, Docker Compose  
**Language:** Python

---

## 📌 Related Projects

- [RealTime-Spark-Streaming](https://github.com/Ajay-Deshpande/RealTime-Spark-Streaming) — Spark streaming fundamentals
