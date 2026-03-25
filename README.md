
# 📦 Docker Workshop

A hands-on workshop to learn Docker fundamentals, containerization, multi-container applications, and best practices.

---

## 🚀 Overview

This project walks through building and running a **containerized Todo application** using:

* Docker 🐳
* Dockerfile
* Volumes & Bind Mounts
* Multi-container architecture (Node.js + MySQL)
* Docker Compose

---

## 🧠 What You Will Learn

* How to containerize a Node.js application
* How Docker images and containers work
* Managing persistent data with volumes
* Using bind mounts for development
* Running multi-container apps with networking
* Simplifying workflows with Docker Compose
* Optimising images with best practices

These topics reflect common Docker learning paths used in workshops and training projects ([GitHub][1])

---

## 📁 Project Structure

```text
getting-started-app/
├── Dockerfile
├── compose.yaml
├── package.json
├── package-lock.json
├── src/
└── spec/
```

---

## ⚙️ Prerequisites

Make sure you have installed:

* Docker Desktop
* Git
* A code editor (VS Code recommended)

---

## ▶️ Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/hugdora/Docker-Workshop.git
cd Docker-Workshop/getting-started-app
```

---

### 2. Run with Docker Compose (Recommended)

```bash
docker compose up -d
```

---

### 3. Open the application

👉 **Open your app:** [http://localhost:3000](http://localhost:3000)

---

## 🐳 Services

Docker Compose runs two services:

* **app** → Node.js application
* **mysql** → Database

Container naming follows:

```
<project>-<service>-<index>
```

Example:

```
docker-workshop-app-1
docker-workshop-mysql-1
```

---

## 🧪 Development Mode (Live Reload)

Run with bind mounts and nodemon:

```bash
docker run -dp 127.0.0.1:3000:3000 \
  -w /app \
  --mount type=bind,src=.,target=/app \
  node:24-alpine \
  sh -c "npm install && npm run dev"
```

---

## 💾 Data Persistence

* Uses Docker volumes
* MySQL data stored in:

```
todo-mysql-data
```

---

## 🛠️ Useful Commands

```bash
docker ps                    # list running containers
docker logs -f <id>         # view logs
docker rm -f <id>           # remove container
docker compose down         # stop all services
```

---

## 🧱 Best Practices Applied

* Layer caching in Dockerfile
* Multi-stage builds (concept)
* Separation of concerns (app vs DB)
* Use of Docker Compose for orchestration

---

## 📚 Documentation Sections

* Containerizing the app
* Updating the application
* Sharing images
* Persisting data
* Bind mounts
* Multi-container apps
* Docker Compose
* Image best practices

---

## ⚠️ Common Issues

### Port already in use

```bash
docker rm -f <container-id>
```

---

### Changes not reflected

* Ensure you're using bind mounts
* Restart container or refresh browser (`Ctrl + F5`)

---

## 🧹 Clean Up

```bash
docker compose down --volumes
```

---

## 📚 Related Resources

* [Docker Documentation](https://docs.docker.com/)
* [Docker Compose Docs](https://docs.docker.com/compose/)
* [Dockerfile Reference](https://docs.docker.com/engine/reference/builder/)

---

## 🙌 Contributing

Feel free to fork this repository and improve the documentation or examples.

---

## 📄 License

This project is for educational purposes.

---


[1]: https://github.com/PacktWorkshops/The-Docker-Workshop?utm_source=chatgpt.com "PacktWorkshops/The-Docker-Workshop"
