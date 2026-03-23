# 📦 Share the Application

Now that I've built a Docker image, I can share it with others.

To share Docker images, I use a **Docker registry**.  
The default registry is **Docker Hub**, which hosts many of the images I’ve already used.

---

## 🆔 Docker ID

A Docker ID allows me to access Docker Hub, the world’s largest library for container images.

👉 Create one here if I don’t have it:  
https://hub.docker.com/signup

---

## 📁 Create a Repository

Before pushing my image, I need a repository on Docker Hub.

### Steps:

1. Sign in to Docker Hub  
   👉 https://hub.docker.com

2. Click **Create Repository**

3. Enter:
   - **Repository Name**: `getting-started`
   - **Visibility**: Public

4. Click **Create**
   
<img width="3765" height="1395" alt="image" src="https://github.com/user-attachments/assets/7ff17d13-e420-4694-bc52-620d234ba71d" />

the repository is created

<img width="3780" height="1590" alt="image" src="https://github.com/user-attachments/assets/114b9d3e-26fe-4d07-8494-d35be7f93d4c" />

---

## 🚀 Push the Image

### Step 1: Try pushing (I’ll see an error)

```bash
docker push docker/getting-started
```
<img width="2172" height="240" alt="image" src="https://github.com/user-attachments/assets/0580033c-59b2-4143-b2b4-9e7507410d6f" />

❌ Expected error
An image does not exist locally with the tag: docker/getting-started
💡 Why this happens

My local image is named:
```
getting-started
```
But Docker expects:
```
MY-USERNAME/getting-started
```

🔍 Check my images

```
docker image ls
```
<img width="2127" height="330" alt="image" src="https://github.com/user-attachments/assets/c88755ce-b89b-4487-afe2-0ed4eb9970c5" />

🔐 Step 2: Login to Docker Hub
docker login

Enter my Docker Hub username and password.

🏷️ Step 3: Tag my image

Replace MY-USERNAME with my Docker ID:

```
docker tag getting-started MY-USERNAME/getting-started
```
<img width="2182" height="125" alt="image" src="https://github.com/user-attachments/assets/4ec699d4-d306-410b-9672-05d193ae0e43" />

⬆️ Step 4: Push the image
```
docker push MY-USERNAME/getting-started
```
<img width="2105" height="582" alt="image" src="https://github.com/user-attachments/assets/e9ff9b79-5107-4b10-bedc-ab4f1d50b6a5" />

👉 If no tag is specified, Docker uses latest by default.

🌍 Run the Image Anywhere

Now that my image is on Docker Hub, I can run it on any machine.

<img width="3757" height="1740" alt="image" src="https://github.com/user-attachments/assets/c56eb26e-9fc0-46c8-bde1-a60ccdfef493" />


📌 Summary

In this section, I:

* Created a Docker Hub repository
* Tagged your Docker image
* Pushed the image to Docker Hub
* Ran the image from a remote registry

📚 Related Documentation

* Docker CLI Reference
* Docker Hub Overview
* Multi-platform Images

➡️ Next Steps: Persist the DB
