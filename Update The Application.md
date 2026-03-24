# 🐳 Update the Application

In the previous section, I containerized a simple todo application.  
In this section, I will:

- Update the application source code
- Rebuild the Docker image
- Stop and remove a running container
- Run the updated version of the app

---


## ✏️ Update the Source Code

I will modify the message displayed when there are no todo items.

### Step 1: Edit the file

Open the file:

```bash
src/static/js/app.js

Update line 56:

- <p className="text-center">No items yet! Add one above!</p>
```

<img width="1835" height="250" alt="image" src="https://github.com/user-attachments/assets/dfc7561b-5d17-4ea3-8287-542616cf5078" />

```

+ <p className="text-center">You have no todo items yet! Add one above!</p>

```
<img width="2672" height="265" alt="image" src="https://github.com/user-attachments/assets/4a66d592-acbf-4d23-a7ab-4b2b04052d3d" />


### 🏗️ Rebuild the Docker Image after saving that file


After updating, rebuild the image:

```
docker build -t getting-started .
```

<img width="2462" height="997" alt="image" src="https://github.com/user-attachments/assets/176e33c1-71df-4e17-bceb-3e12fb830e39" />



### ▶️ Run the Updated Container

Start a new container:

```
docker run -dp 127.0.0.1:3000:3000 getting-started
```
<img width="2505" height="252" alt="image" src="https://github.com/user-attachments/assets/6684affe-539e-48b0-b535-d1e76592cc41" />

⚠️ Common Error

I may encounter this error:
docker: Error response from daemon: Bind for 127.0.0.1:3000 failed: port is already allocated.

### 💡 Why this happens?

* My previous container is still running
* It is already using port 3000
>> Only one process can use a port at a time

### 🧹 Remove the Old Container
Option 1: Using CLI

* List running containers:
```
docker ps
```
<img width="2507" height="317" alt="image" src="https://github.com/user-attachments/assets/134f2da3-d69b-4c1a-bd52-744ca269a4a6" />

* Stop the container:
```
docker stop <container-id>
```
<img width="2167" height="142" alt="image" src="https://github.com/user-attachments/assets/e9271401-1bfc-45d4-9a2f-f249a5b3c3ec" />

* Remove the container:

```
docker rm <container-id>
```
<img width="2175" height="140" alt="image" src="https://github.com/user-attachments/assets/a61db655-3623-40b6-be8a-1dc1b9cbc0bf" />
<img width="2465" height="270" alt="image" src="https://github.com/user-attachments/assets/6d0b1b0f-e7ae-4114-8abf-43a00640ef7f" />


> ⚡ Shortcut
```
docker rm -f <container-id>
```
Will stop and remove the container

<img width="2247" height="147" alt="image" src="https://github.com/user-attachments/assets/7766ee49-8192-4979-89df-3a587a37ceb9" />

Option 2: Using Docker Desktop 
```
Open Docker Desktop >> Go to Containers >> Click the 🗑️ icon >> Confirm Delete forever
```
<img width="3127" height="1520" alt="image" src="https://github.com/user-attachments/assets/3e732384-1be6-4e6a-871c-f313bd4e54a6" />


### 🚀 Start the Updated Application
```
docker run -dp 127.0.0.1:3000:3000 getting-started
```
<img width="2162" height="157" alt="image" src="https://github.com/user-attachments/assets/574d4f8d-2711-47e4-ab81-7d7e1acbcb20" />

### 🌐 View The App

👉 http://localhost:3000

I should now see:

You have no todo items yet! Add one above!

<img width="2562" height="425" alt="image" src="https://github.com/user-attachments/assets/8e264d1c-18ce-4bf8-888d-73352a8accd5" />


---
## 📌 Summary

In this section, I:
```
- Updated application code
- Rebuilt Docker image
- Removed old container
- Ran updated app
```

### 📚 Related Documentation

[Docker CLI Reference](https://docs.docker.com/engine/reference/commandline/docker/)

### ➡️ Next Steps
```
👉 Share the Application
```


