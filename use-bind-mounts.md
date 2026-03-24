
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

## 🧪 Try Bind Mounts 

### Step 1: Run a container with a bind mount
Open a terminal and change directory to the getting-started-app directory.
```bash
docker run -it --mount type=bind,src=.,target=/src ubuntu bash
```
<img width="2520" height="180" alt="image" src="https://github.com/user-attachments/assets/bf3b8c80-d11a-48bb-a018-64473016842f" />


### Step 2: Explore the container
```
pwd
```
<img width="1985" height="167" alt="image" src="https://github.com/user-attachments/assets/aa133e44-1257-44c8-beb4-9387e3d7c8da" />

```
ls
```
<img width="2117" height="140" alt="image" src="https://github.com/user-attachments/assets/394ef974-95e8-441a-beeb-e94003b1e35b" />

```
cd src
```
```
ls
```
<img width="2065" height="180" alt="image" src="https://github.com/user-attachments/assets/a870f4ec-85d1-45f3-a6fd-cc5e1a8b9d70" />

> 👉 I should see my project files inside the container.



### Step 3: Create a file inside the container
```
touch myfile.txt
```
<img width="2070" height="150" alt="image" src="https://github.com/user-attachments/assets/82c9d05d-4cd7-43c1-a75e-5cf636586efd" />


> 👉 Now check my host machine — the file appears instantly 🎉
<img width="3700" height="1345" alt="image" src="https://github.com/user-attachments/assets/8f6ff338-e459-4715-b9eb-02d4792acf26" />

### Step 4: Delete the file from host

> 👉 It disappears inside the container as well.
<img width="2520" height="1155" alt="image" src="https://github.com/user-attachments/assets/0fc446cb-199d-46e9-a3f3-9bee3226e525" />


### Step 5: Exit container
```
Ctrl + D
```
<img width="2020" height="90" alt="image" src="https://github.com/user-attachments/assets/d941c212-0121-49c6-bd46-1dd044ac7bee" />

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
<img width="2507" height="577" alt="image" src="https://github.com/user-attachments/assets/ef31b052-704c-4961-ab8b-5fd917ac5203" />

## 💡 What this command does? Here is the breakdown:

- `-dp 127.0.0.1:3000:3000`  
  → Runs the container in **detached mode** (background) and maps port **3000** from the container to the local machine.

- `-w /app`  
  → Sets the **working directory** inside the container to `/app`.

- `--mount type=bind,src=.,target=/app`  
  → Creates a **bind mount**, linking to current local folder (`.`) to `/app` inside the container.  
  → Any changes make locally are reflected instantly in the container.

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
<img width="2430" height="1470" alt="image" src="https://github.com/user-attachments/assets/0372e885-1e4b-4032-a450-a74672ff847f" />


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
> 👉 Press Ctrl + C to exit when completed watching the logs.

## 🔄 Live Update Example
Open file:
src/static/js/app.js
Update this line:
- {submitting ? 'Adding...' : 'Add Item'}
+ {submitting ? 'Adding...' : 'Add'}
Save file
<img width="2315" height="507" alt="image" src="https://github.com/user-attachments/assets/1a08e53b-741c-4d4d-b060-2ed73fd98d01" />
<img width="2357" height="507" alt="image" src="https://github.com/user-attachments/assets/a99e3425-a156-4a2e-a79c-f1bcaf80af82" />

> 👉 Refresh browser → change appears instantly, Nodemon restarts automatically

<img width="2415" height="442" alt="image" src="https://github.com/user-attachments/assets/fe848b68-9059-44b7-b4e6-1f4c73e51721" />

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







