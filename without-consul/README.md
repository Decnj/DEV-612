## DEV-612 (Without Consul)

This folder contains the **basic setup** of the Task App using Docker Compose.  
It runs three services: **Frontend (Nginx)**, **Backend (Node.js + Express)**, and **Database (MongoDB)**.

---


### 📂 Project Structure

without-consul/
```bash
├── backend/
│   ├── Dockerfile
│   ├── index.js
│   ├── package.json
├── frontend/
│   ├── Dockerfile
│   ├── index.html
├── docker-compose.yml
└── .gitignore
```

---

### 🛠 Services
- **Frontend (Nginx)**  
  Serves the static `index.html` page. The page allows adding and viewing tasks.

- **Backend (Node.js + Express)**  
  Provides REST API endpoints (`/tasks`) to create and fetch tasks. Connects to MongoDB for persistence.

- **MongoDB**  
  Stores task data in a database named `taskdb`.

---

### ▶️ Running the App
1. Navigate into the `without-consul` folder:
   ```bash
   $ cd without-consul
   ```

2. Start the services:
```bash
$ docker-compose up
```

3. Access the app:
Frontend: http://localhost:8080 (from browser)
Backend API: http://localhost:3000/tasks
MongoDB: localhost:27017

⚙️ Notes
The backend connects to MongoDB using the service alias mongo defined in docker-compose.yml.
Tasks are stored in the mongo_data Docker volume, so data persists across container restarts.
You can stop the stack with:

```bash
docker-compose down
```

