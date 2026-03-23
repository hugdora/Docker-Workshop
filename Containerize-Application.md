# Containerize an Application

For the rest of this guide, I'll be working with a simple todo list manager that runs on Node.js.
This guide doesn't require any prior experience with JavaScript.

---

## Prerequisites

- Install the latest version of [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- Install a [Git client](https://git-scm.com/downloads)
- Use an IDE or text editor (recommended: [Visual Studio Code](https://code.visualstudio.com/))

---

## Get the App

Before running the application, you need to download the source code.

### 1. Clone the repository

```bash
git clone https://github.com/docker/getting-started-app.git
````
<img width="1672" height="717" alt="image" src="https://github.com/user-attachments/assets/9fcbeea0-9c27-4e00-9ed1-a8525c85ec57" />

### 2. View project structure

```text
getting-started-app/
├── .dockerignore
├── package.json
├── package-lock.json
├── README.md
├── spec/
├── src/
```

---

## Build the App Image

A **Dockerfile** is a script that contains instructions to build a Docker image.

### 1. Create a Dockerfile

<img width="2150" height="390" alt="image" src="https://github.com/user-attachments/assets/8953c001-9f92-47ea-8637-96b677c3f9a0" />

Inside the `getting-started-app` directory, create a file named `Dockerfile`:

```dockerfile
# syntax=docker/dockerfile:1

FROM node:24-alpine
WORKDIR /app
COPY . .
RUN npm install --omit=dev
CMD ["node", "src/index.js"]
EXPOSE 3000

<img width="1967" height="625" alt="image" src="https://github.com/user-attachments/assets/de29c46f-3db6-4399-b679-a9cc7bbc0dde" />

```

### What this Dockerfile does:

* Uses a lightweight Node.js base image (`node:24-alpine`)
* Sets `/app` as the working directory
* Copies all project files into the container
* Installs dependencies
* Starts the app using Node.js
* Exposes port `3000`

---

### 2. Build the Docker image

```bash
cd /path/to/getting-started-app
docker build -t getting-started .
```
<img width="2495" height="937" alt="image" src="https://github.com/user-attachments/assets/3efcd56b-34f8-427e-9a5f-f73f17bb3465" />
<img width="2460" height="917" alt="image" src="https://github.com/user-attachments/assets/1acfc0d0-4507-4c12-826b-23b1a0cb7848" />


#### Notes:

* `-t getting-started` → names your image
* `.` → tells Docker to use the current directory
* Docker downloads base image layers automatically if not present

---

## Start the Application Container

Run the app inside a container:

```bash
docker run -d -p 127.0.0.1:3000:3000 getting-started
```
<img width="2170" height="160" alt="image" src="https://github.com/user-attachments/assets/79ab039b-b737-4f27-97b9-a8023755a5d7" />

### Flags explained:

* `-d` → runs container in background (detached mode)
* `-p` → maps ports (`HOST:CONTAINER`)

  * `127.0.0.1:3000` → your local machine
  * `3000` → container port

---

## Open the App

Visit:

👉 [http://localhost:3000](http://localhost:3000)

You should see the todo list application running.
<img width="2975" height="450" alt="image" src="https://github.com/user-attachments/assets/98f1be2e-4bf8-4e17-b59b-57babcf35436" />


---
## Verify Running Containers

### Using CLI:

```bash
docker ps
```

Example output:

```text
CONTAINER ID   IMAGE              COMMAND                  CREATED         STATUS         PORTS                      NAMES
df784548666d   getting-started    "docker-entrypoint..."   2 minutes ago   Up 2 minutes   127.0.0.1:3000->3000/tcp   priceless_mcclintock
```
<img width="2502" height="237" alt="image" src="https://github.com/user-attachments/assets/ff651c8a-7e21-4a6c-91e2-28e583aac953" />

### Using Docker Desktop:

* Open Docker Desktop
* Go to the **Containers** tab
* You’ll see your running container
<img width="3115" height="1580" alt="image" src="https://github.com/user-attachments/assets/9936b518-d133-4d4a-82d2-d998bf6fd161" />

---
## Summary

I have:

* Created a Dockerfile
* Built a Docker image
* Run a container
* Accessed application in the browser

---
## Related Documentation

* [Dockerfile Reference](https://docs.docker.com/engine/reference/builder/)
* [Docker CLI Reference](https://docs.docker.com/engine/reference/commandline/docker/)

---



