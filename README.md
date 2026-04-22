# 📡 Observability For DevOps

> A production-ready observability stack for DevOps Engineers — monitor infrastructure, containers, and applications using **Prometheus**, **Grafana**, **cAdvisor**, and **Node Exporter**, all wired together with Docker Compose.

<p align="left">
  <img src="https://img.shields.io/badge/Prometheus-E6522C?style=for-the-badge&logo=prometheus&logoColor=white" />
  <img src="https://img.shields.io/badge/Grafana-F46800?style=for-the-badge&logo=grafana&logoColor=white" />
  <img src="https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white" />
  <img src="https://img.shields.io/badge/cAdvisor-4285F4?style=for-the-badge&logo=google&logoColor=white" />
  <img src="https://img.shields.io/badge/Node_Exporter-37D100?style=for-the-badge&logo=linux&logoColor=white" />
</p>

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Architecture](#-architecture)
- [Stack Components](#-stack-components)
- [Prerequisites](#-prerequisites)
- [Getting Started](#-getting-started)
- [Service Endpoints](#-service-endpoints)
- [Grafana Dashboards](#-grafana-dashboards)
- [Prometheus Configuration](#-prometheus-configuration)
- [Project Structure](#-project-structure)
- [Common Operations](#-common-operations)
- [Troubleshooting](#-troubleshooting)
- [Contributing](#-contributing)

---

## 🔍 Overview

This repository provides a **complete, plug-and-play observability stack** for DevOps engineers. It is designed to give you full visibility into your infrastructure and containerized applications with minimal setup.

**What you get out of the box:**

- 📊 Real-time metrics collection from hosts and containers
- 📈 Pre-configured Grafana dashboards for instant visualization
- 🐳 Container resource monitoring via cAdvisor
- 🖥️ Host-level metrics via Node Exporter
- 🔗 Isolated Docker networking for secure service communication

---

## 🏗️ Architecture

```
┌──────────────────────────────────────────────────────────────┐
│                        Docker Host                           │
│                                                              │
│   ┌──────────────┐        ┌──────────────────────────────┐   │
│   │  Your App /  │        │     monitoring (network)     │   │
│   │  Services    │        │                              │   │
│   └──────┬───────┘        │  ┌────────────────────────┐  │   │
│          │ metrics        │  │      Prometheus         │  │   │
│          ▼                │  │   (scrapes metrics)     │  │   │
│   ┌──────────────┐        │  └──────────┬─────────────┘  │   │
│   │ Node Exporter│◀───────│─────────────┤                │   │
│   │ (host stats) │        │             │                │   │
│   └──────────────┘        │  ┌──────────▼─────────────┐  │   │
│                           │  │        Grafana          │  │   │
│   ┌──────────────┐        │  │  (visualize & alert)   │  │   │
│   │   cAdvisor   │◀───────│─────────────┘              │  │   │
│   │(container    │        │                              │   │
│   │  metrics)    │        └──────────────────────────────┘   │
│   └──────────────┘                                           │
└──────────────────────────────────────────────────────────────┘
```

**Data Flow:**
1. **Node Exporter** exposes host-level metrics (CPU, RAM, disk, network)
2. **cAdvisor** exposes per-container resource metrics
3. **Prometheus** scrapes both exporters on a defined interval and stores time-series data
4. **Grafana** queries Prometheus and renders interactive dashboards

---

## 🧩 Stack Components

| Tool | Version | Purpose | Default Port |
|---|---|---|---|
| **Prometheus** | Latest | Metrics collection & storage | `9090` |
| **Grafana** | Latest | Visualization & dashboards | `3000` |
| **cAdvisor** | Latest | Container resource monitoring | `8080` |
| **Node Exporter** | Latest | Host system metrics | `9100` |

---

## ✅ Prerequisites

Make sure the following are installed on your Linux server before proceeding:

- **Docker** `20.10+` — [Install Guide](https://docs.docker.com/engine/install/)
- **Docker Compose** `v2.0+` — [Install Guide](https://docs.docker.com/compose/install/)
- **Minimum 2 GB RAM** (4 GB recommended)
- **Ports available:** `3000`, `8080`, `9090`, `9100`

Verify your setup:

```bash
docker --version
docker compose version
```

---

## 🚀 Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/Praveen48589/Observability-For-Devops.git
cd Observability-For-Devops
```

### 2. Start the Observability Stack

```bash
docker compose up -d
```

This will pull all required images and start all services in detached mode.

### 3. Verify All Containers Are Running

```bash
docker compose ps
```

Expected output:

```
NAME              STATUS          PORTS
prometheus        Up              0.0.0.0:9090->9090/tcp
grafana           Up              0.0.0.0:3000->3000/tcp
cadvisor          Up              0.0.0.0:8080->8080/tcp
node-exporter     Up              0.0.0.0:9100->9100/tcp
```

### 4. Access the Services

| Service | URL | Default Credentials |
|---|---|---|
| Grafana | http://localhost:3000 | `admin` / `admin` |
| Prometheus | http://localhost:9090 | — |
| cAdvisor | http://localhost:8080 | — |
| Node Exporter | http://localhost:9100/metrics | — |

> **Note:** Replace `localhost` with your server's IP address if accessing remotely.

---

## 📊 Grafana Dashboards

After logging into Grafana, the stack comes pre-configured with Prometheus as a data source.

### Import Community Dashboards

Navigate to **Dashboards → Import** and use these recommended dashboard IDs:

| Dashboard | ID | Description |
|---|---|---|
| Node Exporter Full | `1860` | Complete host metrics |
| Docker & Container Monitoring | `893` | Per-container stats |
| cAdvisor Dashboard | `14282` | Container resource usage |
| Prometheus Stats | `2` | Prometheus internals |

**Steps to import:**
1. Go to `http://localhost:3000`
2. Login with `admin / admin` (change on first login)
3. Click **Dashboards** → **Import**
4. Enter the dashboard ID and click **Load**
5. Select **Prometheus** as the data source and click **Import**

---

## ⚙️ Prometheus Configuration

Prometheus is configured to scrape the following targets defined in `prometheus.yml`:

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'node-exporter'
    static_configs:
      - targets: ['node-exporter:9100']

  - job_name: 'cadvisor'
    static_configs:
      - targets: ['cadvisor:8080']
```

To verify all targets are healthy, open:
```
http://localhost:9090/targets
```

All targets should show **State: UP**.

---

## 📁 Project Structure

```
Observability-For-Devops/
│
├── docker-compose.yml        # Main stack definition
├── prometheus.yml            # Prometheus scrape configuration
├── grafana/
│   └── provisioning/         # Auto-provisioned datasources & dashboards
└── README.md
```

---

## 🛠️ Common Operations

| Action | Command |
|---|---|
| Start the stack | `docker compose up -d` |
| Stop the stack | `docker compose down` |
| Restart a service | `docker compose restart <service>` |
| View live logs | `docker compose logs -f` |
| View logs for one service | `docker compose logs -f prometheus` |
| Check resource usage | `docker stats` |
| Pull latest images | `docker compose pull` |
| Rebuild and restart | `docker compose up -d --force-recreate` |

---

## 🔧 Troubleshooting

### Prometheus targets show DOWN

Check that containers are on the same Docker network and service names match `prometheus.yml`:

```bash
docker network inspect monitoring
docker compose ps
```

---

### Grafana shows "No Data"

Verify Prometheus is reachable from Grafana:

1. Go to **Configuration → Data Sources → Prometheus**
2. Set URL to `http://prometheus:9090`
3. Click **Save & Test** — should show ✅ **Data source is working**

---

### Port already in use

Find and stop the conflicting process:

```bash
sudo lsof -i :<port>
sudo kill -9 <PID>
```

---

### Container keeps restarting

Check the container logs for errors:

```bash
docker compose logs <service-name>
```

---

## 🤝 Contributing

Contributions are welcome! If you'd like to improve this project:

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/your-feature`
3. Commit your changes: `git commit -m 'Add your feature'`
4. Push to the branch: `git push origin feature/your-feature`
5. Open a Pull Request

Please open an issue first for major changes so we can discuss the approach.

---

## 📄 License

This project is open source and available under the [MIT License](LICENSE).

---

<p align="center">
  Built with ❤️ for DevOps Engineers · <a href="https://github.com/Praveen48589/Observability-For-Devops">github.com/Praveen48589/Observability-For-Devops</a>
</p>
