## Task App with Consul

This setup extends the basic Task App stack by adding **Consul** for service discovery and health monitoring.  
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

Frontend (Nginx) on port 8080

Backend (Node.js) on port 3000

MongoDB on port 27017

Consul on port 8500 (UI) and 8600/udp (DNS)


🌐 Endpoints

Frontend UI → http://localhost:8080

Backend API → http://localhost:3000/tasks

MongoDB → localhost:27017

Consul UI → http://localhost:8500


🛠 Consul Service Registration

Services are registered via JSON files in consul-config/:


frontend.json

```json
{
  "service": {
    "name": "frontend",
    "port": 80,
    "tags": ["nginx"],
    "check": {
      "http": "http://frontend:80",
      "interval": "10s"
    }
  }
}
```


Backend.json

```json
json
{
  "service": {
    "name": "backend",
    "port": 3000,
    "tags": ["nodejs"],
    "check": {
      "http": "http://backend:3000/health",
      "interval": "10s"
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
    "tags": ["database"],
    "check": {
      "tcp": "mongodb:27017",
      "interval": "10s"
    }
  }
}
```

✅ Health Checks
Frontend → Consul checks http://frontend:80 (returns HTML page).

Backend → Consul checks http://backend:3000/health (returns 200 OK).

MongoDB → Consul checks TCP connectivity on mongodb:27017.



📊 Viewing Services

Open http://localhost:8500 to see:

frontend (healthy)

backend (healthy once /health endpoint is added)

mongodb (healthy if TCP connection succeeds)



🔎 Notes

Consul is running in dev mode (agent -dev -client=0.0.0.0).

For production, you’d run Consul in server/agent mode with proper clustering.

Sidecar proxies (Envoy) are not required here — they’re only needed if you want Consul Connect service mesh features.

