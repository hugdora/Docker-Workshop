
# 🔗 Use Bind Mounts

In the previous section, I used a volume to persist database.  
Now, I’ll learn about **bind mounts**, which are commonly used during development.

---

## 📌 What is a Bind Mount?

A bind mount allows us to:

- Share files between current **host machine** and a **container**
- See code changes instantly inside the container
- Develop without rebuilding the image every time

---

## ⚖️ Volume vs Bind Mount

| Feature            | Named Volume        | Bind Mount              |
|------------------|--------------------|-------------------------|
| Storage location | Managed by Docker  | Controlled by you       |
| Data persistence | Yes                | Yes                     |
| Real-time sync   | No                 | Yes                     |

---

## 🧪 Try Bind Mounts (Hands-on)

### Step 1: Run a container with a bind mount

```bash
docker run -it --mount type=bind,src=.,target=/src ubuntu bash
```
### Step 2: Explore the container
```
pwd
```
```
ls
```
```
cd src
```
```
ls
```

> 👉 I should see my project files inside the container.

### Step 3: Create a file inside the container
```
touch myfile.txt
```
> 👉 Now check my host machine — the file appears instantly 🎉

### Step 4: Delete the file from host

> 👉 It disappears inside the container as well.

### Step 5: Exit container
```
Ctrl + D
```
## 🚀 Development with Bind Mounts

Bind mounts are powerful for development because they allow:
* Live code updates
* Automatic app reload
* Faster workflow
  
## ▶️ Run Your App in Development Mode

For PowerShell / Windows:
```
docker run -dp 127.0.0.1:3000:3000 `
  -w /app `
  --mount "type=bind,src=.,target=/app" `
  node:24-alpine `
  sh -c "npm install && npm run dev"
```
## 💡 What this command does

```bash
docker run -dp 127.0.0.1:3000:3000 \
  -w /app \
  --mount type=bind,src=.,target=/app \
  node:24-alpine \
  sh -c "npm install && npm run dev"
```
## 🔍 Breakdown

- `-dp 127.0.0.1:3000:3000`  
  → Runs the container in **detached mode** (background) and maps port **3000** from the container to your local machine.

- `-w /app`  
  → Sets the **working directory** inside the container to `/app`.

- `--mount type=bind,src=.,target=/app`  
  → Creates a **bind mount**, linking your current local folder (`.`) to `/app` inside the container.  
  → Any changes you make locally are reflected instantly in the container.

- `node:24-alpine`  
  → Specifies the **base image** used to run the container (lightweight Node.js environment).

- `sh -c "npm install && npm run dev"`  
  → Runs a shell command that:
  - Installs dependencies (`npm install`)
  - Starts the app in development mode (`npm run dev`)
  - Uses **nodemon** to automatically restart the app on changes

### 📊 Check Logs
```
docker logs -f <container-id>

```

## ✅ Expected Output

```text
nodemon -L src/index.js
[nodemon] 2.0.20
[nodemon] to restart at any time, enter `rs`
[nodemon] watching path(s): *.*
[nodemon] watching extensions: js,mjs,json
[nodemon] starting `node src/index.js`
Using sqlite database at /etc/todos/todo.db
Listening on port 3000
```
> 👉 When you're done watching the logs, press Ctrl + C to exit.

## 🔄 Live Update Example
Open file:
src/static/js/app.js
Update this line:
- {submitting ? 'Adding...' : 'Add Item'}
+ {submitting ? 'Adding...' : 'Add'}
Save file

> 👉 Refresh browser → change appears instantly
> 👉 Nodemon restarts automatically


## 📌 Summary

In this section, I:

* Learned what bind mounts are
* Shared files between host and container
* Enabled live development with nodemon
* Updated the app without rebuilding

## 📚 Related Documentation
- [Docker CLI Reference](https://docs.docker.com/engine/reference/commandline/docker/)
- [Manage Data in Docker](https://docs.docker.com/storage/)

## ➡️ Next Steps

👉 [Multi-Container Apps](./multi-container-apps.md)







