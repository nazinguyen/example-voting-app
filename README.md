# Polyglot Microservices Orchestration & Automated Operations Platform

![CI/CD Status](https://img.shields.io/github/actions/workflow/status/nazinguyen/example-voting-app/deploy-voting.yml?label=Build%20%26%20Deploy&logo=github-actions)
![Docker](https://img.shields.io/badge/Containerization-Docker-blue?logo=docker)
![Monitoring](https://img.shields.io/badge/Monitoring-Prometheus%20%26%20Grafana-orange?logo=grafana)
![Alerting](https://img.shields.io/badge/Alerting-Telegram-blue?logo=telegram)

## Introduction
This project demonstrates a full-cycle **DevOps implementation** for a microservices architecture application (Python, Node.js, .NET, Redis, PostgreSQL). 

It goes beyond simple deployment by integrating a robust **Automated CI/CD Pipeline**, **Deep Observability (Monitoring)**, and an **Incident Response System (ChatOps)** via Telegram.

## Architecture
The system is deployed on **DigitalOcean Cloud** using Docker Compose for orchestration.

* **Frontend:** Voting App (Python), Result App (Node.js)
* **Backend:** Worker (.NET)
* **Data Layer:** Redis (Queue/Cache), PostgreSQL (Persistent DB)
* **Operations Stack:** GitHub Actions, Prometheus, Grafana, cAdvisor, Node Exporter.

---

## Key Features Implementation

### 1. Automated CI/CD Pipeline 
* **Tool:** GitHub Actions.
* **Workflow:**
    * Triggered on `git push` to the `main` branch.
    * Securely connects to the DigitalOcean Droplet via SSH (using GitHub Secrets).
    * **Fail-Fast Strategy:** Implemented `set -e` in deployment scripts to halt execution immediately upon any error, preventing partial/corrupt deployments.
    * **Optimization:** Smart container recreation (only rebuilds changed services).

### 2. Comprehensive Monitoring & Observability 
* **Infrastructure Monitoring:** `Node Exporter` tracks Host CPU, RAM, and Disk I/O.
* **Container Monitoring:** `cAdvisor` provides granular metrics for each specific container (RAM usage of Redis, CPU load of Worker, etc.).
* **Visualization:** Custom Grafana Dashboards visualizing real-time health of the entire stack.

> **[INSERT YOUR GRAFANA DASHBOARD SCREENSHOT HERE]**
> *Figure 1: Real-time Dashboard showing Container Health & Resource Usage.*

### 3. SRE & Automated Alerting (ChatOps) 
* **Goal:** Reduce Mean Time to Detect (MTTD).
* **Implementation:** * Configured **Prometheus Alert Rules** to detect service downtime (e.g., Redis crash).
    * Tuned Alert Manager for **High-Frequency Checks (10s interval)**.
    * Integrated with **Telegram Bot** using custom HTML templates for clean, actionable notifications.

> **[INSERT YOUR TELEGRAM ALERT SCREENSHOT HERE]**
> *Figure 2: Instant Telegram notification when Redis service was manually stopped (Chaos Engineering test).*

---

## Tech Stack

| Category | Technologies |
| :--- | :--- |
| **Cloud Provider** | DigitalOcean (Droplet) |
| **Containerization** | Docker, Docker Compose (v2) |
| **CI/CD** | GitHub Actions, Bash Scripting |
| **Monitoring** | Prometheus, Grafana, cAdvisor, Node Exporter |
| **Alerting** | Grafana Alert Manager, Telegram Webhook |
| **OS** | Ubuntu 22.04 LTS |

---

## Chaos Engineering & Incident Response
To validate the reliability of the system, I performed a simulated failure test:

1.  **Simulation:** Manually stopped the Redis container (`docker stop redis`) to simulate a critical cache failure.
2.  **Detection:** Prometheus detected the target down within **10 seconds**.
3.  **Alerting:** Grafana triggered a "Firing" alert to my Telegram.
4.  **Recovery:** Executed the Incident Playbook to restart the service (`docker start redis`).
5.  **Resolution:** System recovered, and a "Resolved" notification was sent.

---

## How to Run (Local Dev)

```bash
# 1. Clone the repo
git clone [https://github.com/nazinguyen/example-voting-app.git](https://github.com/nazinguyen/example-voting-app.git)

# 2. Start the application
docker compose up -d

# 3. Access the apps
# Vote: http://localhost:8080
# Result: http://localhost:8081
# Grafana: http://localhost:3000
```
