# 🐳 Docker Lab: "Build Your Own Service"

This lab is designed to help you get familiar with **Docker** and **Docker Compose** by creating very simple services.  
Each student will add their own service into a shared `docker-compose.yml`.  

At the end of the lab, we will have **10 services running together**, each one showing the student’s name and exposing a health endpoint.

---

## 🎯 Objectives
- Learn the basics of creating Docker images and services.
- Practice adding services into a `docker-compose.yml` file.
- Work together as a team, following a **trunk-based development** approach.
- Test and verify that all services are running correctly.

---

## 📂 Project Structure
```
docker-lab/
├─ docker-compose.yml       # All services defined here
├─ README.md
├─ test.sh                  # Script to test all services
└─ services/
   └─ student01/            # Each student creates their own folder
      ├─ Dockerfile
      └─ app/
```

---

## 🔌 Ports
Each student has a dedicated port. Inside the container, all services use port **8080**, but on the host, you will use:
- Student01 → `8101`
- Student02 → `8102`
- ...
- Student10 → `8110`

---

## 📝 Service Requirements
- Must expose port **8080** inside the container.  
- Must respond on:  
  - `/` → with your name.  
  - `/health` → with text `ok`.  

---

## 🚀 Service Options

You can choose **one** of the following templates:

### 1) BusyBox (Static HTML)
**Dockerfile**
```Dockerfile
FROM busybox:stable
WORKDIR /srv
RUN printf "Hello, I am Student01 👋\n" > index.html  && printf "ok\n" > health
EXPOSE 8080
CMD ["sh", "-c", "httpd -f -p 8080 -h /srv"]
```

---

### 2) Python Flask
**app/app.py**
```python
from flask import Flask
app = Flask(__name__)

@app.get("/")
def index():
    return "Hello, I am Student01 👋"

@app.get("/health")
def health():
    return "ok"
```

**Dockerfile**
```Dockerfile
FROM python:3.11-slim
WORKDIR /app
RUN pip install flask
COPY app/ /app/
EXPOSE 8080
CMD ["python", "-m", "flask", "--app", "app:app", "run", "--host=0.0.0.0", "--port=8080"]
```

---

### 3) Node.js Express
**app/server.js**
```js
const express = require("express");
const app = express();
app.get("/", (_, res) => res.send("Hello, I am Student01 👋"));
app.get("/health", (_, res) => res.send("ok"));
app.listen(8080, () => console.log("listening on 8080"));
```

**Dockerfile**
```Dockerfile
FROM node:20-alpine
WORKDIR /app
COPY app/ /app/
RUN npm init -y && npm install express
EXPOSE 8080
CMD ["node", "server.js"]
```

---

## 🤝 Collaboration: Trunk-Based Development
We will follow a **trunk-based workflow**:
1. Everyone uses **main branch (`main` or `trunk`)** as the single source of truth.
2. Create a short-lived branch for your service:
   ```bash
   git checkout -b student01-service
   ```
3. Add your service folder under `services/` and edit `docker-compose.yml`.
4. Commit and push:
   ```bash
   git add .
   git commit -m "Add student01 service"
   git push origin student01-service
   ```
5. Open a Pull Request (PR) to merge into `main`.
6. Another student must review and approve your PR before merging.
7. After merge, everyone pulls the latest trunk:
   ```bash
   git checkout main
   git pull origin main
   ```

👉 This way, all students practice **integration** and avoid merge conflicts.

---

## ✅ Testing All Services
Run all containers:
```bash
docker compose up --build -d
```

Run the test script:
```bash
./test.sh
```

Expected output:
```
✅ port 8101: health ok
✅ port 8102: health ok
...
```

Open your service in the browser:
- `http://localhost:810X/`
- `http://localhost:810X/health`

---

## 📋 Acceptance Criteria
- Service builds successfully.
- `/` → responds with your name.
- `/health` → responds with `ok`.
- Correct port mapping (810X).
- Short README inside your folder with your name and how to run the service.

---

## ⏱ Suggested Timeline
- 10 min → setup & explanation.  
- 40 min → students create their services.  
- 20 min → integrate code (PRs, reviews).  
- 15 min → run tests together and demo.  

---

## 🏆 End Result
By the end of the lab, you will have a **multi-service Docker Compose project** where each service belongs to a student, running together in one environment.
