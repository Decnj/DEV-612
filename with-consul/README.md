## Task App with Consul

This setup extends the basic Task App stack by adding **Consul Connect** for service discovery and health monitoring.  
Consul provides a UI to view registered services (`frontend`, `backend`, `mongodb`) and their health status.

---

### 📂 Project Structure

```bash
with-consul/
├── backend/          # Node.js backend service
├── frontend/         # Nginx frontend service
├── consul-config/    # Consul service definitions (JSON/HCL files)
│   ├── backend.json
│   ├── frontend.json
│   └── mongodb.json
├── docker-compose.yml
└── README.md
```


---

### ▶️ Running the stack

From the `with-consul` directory:

```bash
docker-compose up
```

This will start:

- Frontend (Nginx) on port 8080

- Backend (Node.js) on port 3000

- MongoDB on port 27017

- Consul on port 8500 (UI) and 8600/udp (DNS)

- Envoy sidecar proxies attached to each service for Consul Connect (mTLS, routing, observability)

---

🌐 Endpoints

- Frontend UI → http://localhost:8080

- Backend API → http://localhost:3000/tasks

- MongoDB → localhost:27017

- Consul UI → http://localhost:8500

---

🛠 Consul Service Registration

Services are registered via JSON files in consul-config/:


frontend.json

```json
{
  "service": {
    "name": "frontend",
    "port": 80,
    "check": {
      "tcp": "frontend:80",
      "interval": "10s"
    },
    "connect": {
      "sidecar_service": {
        "check": {
          "tcp": "frontend:21001",
          "interval": "10s"
        }
      }
    }
  }
}
```



backend.json

```json
{
  "service": {
    "name": "backend",
    "port": 3000,
    "check": {
      "tcp": "backend:3000",
      "interval": "10s"
    },
    "connect": {
      "sidecar_service": {
        "check": {
          "tcp": "backend:21000",
          "interval": "10s"
        }
      }
    }
  }
}
```



mongodb.json

```json
{
  "service": {
    "name": "mongodb",
    "port": 27017,
    "check": {
      "tcp": "mongodb:27017",
      "interval": "10s"
    },
    "connect": {
      "sidecar_service": {
        "check": {
          "tcp": "mongodb:21002",
          "interval": "10s"
        }
      }
    }
  }
}
```

---

✅ Health Checks

- Frontend → Consul checks http://frontend:80 (returns HTML page).

- Backend → Consul checks http://backend:3000/health (returns 200 OK).

- MongoDB → Consul checks TCP connectivity on mongodb:27017.

---

📊 Viewing Services

Open http://localhost:8500 to see:

- ✔ frontend (healthy)

- ✔ backend (healthy once /health endpoint is added)

- ✔ mongodb (healthy if TCP connection succeeds)

---

🔎 Notes

* Consul is running in dev mode (agent -dev -client=0.0.0.0).

* For production, you’d run Consul in server/agent mode with proper clustering.

* Envoy sidecar proxies are now integrated for Consul Connect service mesh features:

  - Mutual TLS (mTLS) encryption

  - Authentication between services

  - Secure traffic routing and observability

---

📈 Performance Testing (DEV‑614)
We used Apache JMeter to simulate user traffic and measure resource utilization.

```bash
# Download and extract JMeter
wget https://downloads.apache.org//jmeter/binaries/apache-jmeter-5.6.3.tgz
tar -xvzf apache-jmeter-5.6.3.tgz
cd apache-jmeter-5.6.3/bin

# Run JMeter
./jmeter.sh
```

Test Plan Setup:

- Thread Group → 50 users, ramp‑up 10s, loop count 100

-HTTP Sampler → requests to frontend and backend endpoints

- Listener → View Results in Table with Summary Report for performance metrics

---

Resource Monitoring:

CPU and memory utilization of app containers and sidecar proxies were monitored using:

```bash
$ docker stats
```

---

Observations:

- Sidecar proxies added ~20–30 MiB RAM each and 1–2% CPU.

- Consul agent added ~50–100 MiB RAM with low CPU.

- App containers behaved almost the same, with slight CPU increases due to traffic encryption and routing.

---
