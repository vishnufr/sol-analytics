# Solana Memecoin Analytics Microservice

A real-time analytics microservice designed for Solana memecoins.  
It exposes REST and WebSocket APIs for live metrics, holder insights, and continuous token monitoring — all backed by a scalable, containerized architecture.

[![FastAPI](https://img.shields.io/badge/FastAPI-005571?style=for-the-badge&logo=fastapi)](https://fastapi.tiangolo.com/)
[![Solana](https://img.shields.io/badge/Solana-9945FF?style=for-the-badge&logo=solana&logoColor=white)](https://solana.com/)
[![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)](https://docker.com/)
[![Redis](https://img.shields.io/badge/Redis-DC382D?style=for-the-badge&logo=redis&logoColor=white)](https://redis.io/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-316192?style=for-the-badge&logo=postgresql&logoColor=white)](https://postgresql.org/)

---

# 🍀 Bounty Compliance

## ✅ Core Requirements

| Requirement        | Implementation                                       | Status       |
|-------------------|------------------------------------------------------|--------------|
| **REST API**      | FastAPI with organized API routes & OpenAPI docs     | ✔️ Complete  |
| **WebSocket**     | Real-time Solana subscriptions via Helius            | ✔️ Complete  |
| **Docker Setup**  | Multi-service `docker-compose` system                 | ✔️ Complete  |
| **UI Dashboard**  | Simple in-browser token analytics interface           | ✔️ Complete  |

---

# 📈 Implemented Metrics

| Metric                | Description                                         | Real-time | Endpoint                                  |
|----------------------|-----------------------------------------------------|-----------|--------------------------------------------|
| **Market Cap**       | Supply × price with live updates                    | ✔️        | `/tokens/{mint}/market-cap`                |
| **Velocity**         | Volume / market cap analysis                        | ✔️        | `/tokens/{mint}/velocity`                  |
| **Concentration**    | Distribution across top 1, 5, 15 holders            | ✔️        | `/tokens/{mint}/concentration`             |
| **Paperhand Ratio**  | Measures short-term selling behavior                 | ✔️        | `/tokens/{mint}/paperhand`                 |

---

# 🚀 Quick Start

### Prerequisites
- Docker + Docker Compose  
- Helius API key  

### Deploy in One Command
```bash
git clone <your-repo-url>
cd sol-analytics
echo "HELIUS_API_KEY=your-key" > .env
docker-compose up -d
```

### Service Access

| Tool / UI           | URL                                   |
|---------------------|----------------------------------------|
| API Docs            | http://localhost:8000/docs             |
| Analytics UI        | http://localhost:8000/ui               |
| Health Check        | http://localhost:8000/health           |
| Grafana             | http://localhost:3000                  |
| Adminer (Postgres)  | http://localhost:8080                  |
| Kafka UI            | http://localhost:8081                  |
| Prometheus          | http://localhost:9090                  |

---

# 📊 Example API Usage

### Complete Analytics Snapshot
```bash
curl "http://localhost:8000/api/v1/tokens/<MINT>/analytics?include_real_time=true"
```

### WebSocket Client Example
```javascript
const ws = new WebSocket("ws://localhost:8000/ws/tokens/<MINT>");
ws.onmessage = msg => console.log(JSON.parse(msg.data));
```

### Batch Analytics
```bash
curl -X POST "http://localhost:8000/api/v1/tokens/batch/analytics" \
  -H "Content-Type: application/json" \
  -d '{"token_mints": ["<M1>", "<M2>"], "metrics": ["market_cap", "velocity"]}'
```

---

# 🏗 Architecture Overview

```mermaid
graph TB
    UI[Web UI] --> API
    Client[REST Clients] --> API
    WSClient[WS Clients] --> WS

    subgraph Backend
        API[FastAPI Service]
        WS[WebSocket Stream Handler]
        Analytics[Analytics Engine]
    end

    subgraph Data
        Redis[(Redis Cache)]
        PG[(PostgreSQL)]
        Kafka[(Kafka Events)]
    end

    API --> Analytics
    WS --> Analytics
    Analytics --> Redis
    Analytics --> PG
    Analytics --> Kafka

    subgraph External
        Helius[Helius RPC]
        Jupiter[Jupiter Price API]
    end

    Analytics --> Helius
    Analytics --> Jupiter
```

---

# 🧮 Metric Definitions

### **Market Cap**
```
market_cap = circulating_supply × live_price
```

### **Velocity**
```
velocity = rolling_24h_volume ÷ market_cap
```

### **Concentration**
```
top_N_ratio = sum(balance_top_N) ÷ total_supply × 100
```

### **Paperhand Ratio**
```
paperhand_ratio = (holders_sold_within_window ÷ active_holders) × 100
```

---

# 📈 Performance Notes

| Category             | Typical Value |
|---------------------|----------------|
| Cached API Latency  | 40–80ms        |
| WebSocket Latency   | <50ms          |
| DB Query Time       | <10ms          |
| Cache Hit Rate      | ~95%           |
| Throughput          | 2k+ req/sec    |

Load tests (K6) are included in `/scripts`.

---

# 🔧 Environment Variables

```bash
HELIUS_API_KEY=your_key_here
DATABASE_URL=postgresql://postgres:password@postgres:5432/market_data
REDIS_URL=redis://redis:6379/0
KAFKA_BOOTSTRAP_SERVERS=kafka:29092
LOG_LEVEL=INFO
ENVIRONMENT=production
```

---

# 🗂 Project Structure

```
app/
├── api/              # Route definitions
├── core/             # Config, DB sessions
├── models/           # Database models
├── schemas/          # Pydantic schemas
├── services/         # Analytics logic
├── tasks/            # Background jobs
└── middleware/       # Request middleware
scripts/              # Load tests, tools
docker/               # Docker configs
```

---

# 🧪 Development Setup

```bash
python -m venv venv
source venv/bin/activate
pip install -r requirements/dev.txt
uvicorn app.main:app --reload
```

---

# 📡 WebSocket Endpoints

| Endpoint               | Purpose                           |
|------------------------|------------------------------------|
| `/ws/tokens/{mint}`    | Real-time token metric stream      |
| `/ws/system`           | Global system stats stream         |

---

# ⚙ REST Endpoints Summary

| Method | Endpoint                                   | Description                          |
|--------|---------------------------------------------|--------------------------------------|
| GET    | `/api/v1/tokens/{mint}/analytics`          | All metrics                           |
| GET    | `/market-cap`                               | Market cap only                       |
| GET    | `/velocity`                                 | Token velocity                        |
| GET    | `/concentration`                           | Holder concentration                  |
| GET    | `/paperhand`                               | Paperhand ratio                       |
| POST   | `/track`                                    | Begin real-time tracking              |
| GET    | `/live`                                     | Latest live metrics                   |
| POST   | `/batch/analytics`                          | Multi-token analytics                 |

---

# 🧭 Monitoring & Observability

- **Grafana dashboards** for latency, throughput, and analytics metrics  
- **Prometheus** scrapes for performance counters  
- `/health` endpoint for quick diagnostics  

Example Prometheus metrics:
```
http_requests_total
http_request_duration_seconds
token_analytics_requests_total
helius_api_calls_total
cache_hits_total
```

---

# 🤝 Contributing

Contributions are welcome.  
If adding new metrics or analytics modules, keep everything async and avoid blocking external calls.

---

# 📌 Notes

This architecture was designed for Solana memecoin analytics but can be adapted for any token tracking, real-time event processing, or blockchain analytics application.
