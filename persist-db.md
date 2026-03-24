# 💾 Persist the Database

I have noticed that my todo list resets every time I restart the container.  
Why does this happen?

In this section, I will see:

- How container filesystems work  
- Why data is lost when containers are removed  
- How to persist data using Docker volumes  

---

## 🧱 The Container Filesystem

Each container has its own filesystem built from image layers.

- Containers share the same base image layers  
- Each container gets its own **writable layer**  
- Changes made in one container are **not shared** with others  

---

## 🔬 See This in Practice

### Step 1: Create a file in a container

```bash
docker run --rm alpine touch greeting.txt
```
<img width="2392" height="230" alt="image" src="https://github.com/user-attachments/assets/bb2587a9-6fc1-4be7-b7e6-a5d33c1ea557" />

>> 💡Tips: Any commands you specify after the image name (in this case, alpine) are executed inside the container. 
In this case, the command touch greeting.txt puts a file named greeting.txt on the container's filesystem.

### Step 2: Check for the file in a new container
```
docker run --rm alpine stat greeting.txt
```
❌ Output:
stat: can't stat 'greeting.txt': No such file or directory

<img width="2362" height="107" alt="image" src="https://github.com/user-attachments/assets/fd6514fc-8d15-424c-a889-5a83d9b8d1cc" />

👉 The file does not exist because each container has its own isolated filesystem.

## 📦 Container Volumes

By default: Data inside containers is temporary It is lost when the container is removed

## ✅ Solution: Use Volumes

Volumes allow me to:

- Store data outside the container
- Persist data across container restarts
- Share data between containers
  
## 🗂️ Persist the Todo Data

My app stores data in:

/etc/todos/todo.db

This is a SQLite database file.

👉 If we store this file in a volume, the data will persist.

## 🚀 Create a Volume and Run the Container:

### Step 1: Create a volume
```
docker volume create todo-db
```
<img width="2130" height="90" alt="image" src="https://github.com/user-attachments/assets/deb63e87-04c2-4ac8-8c8a-16e974c4183f" />

### Step 2: Remove old container
```
docker rm -f <container-id>
```
<img width="2575" height="447" alt="image" src="https://github.com/user-attachments/assets/dc4e43d9-b56a-4a67-8daf-68b2a81d0f57" />

### Step 3: Run container with volume
```
docker run -dp 127.0.0.1:3000:3000 --mount type=volume,src=todo-db,target=//etc/todos getting-started
```
<img width="2935" height="182" alt="image" src="https://github.com/user-attachments/assets/60b33296-4ef7-441f-8280-cf9714d098bc" />

## 🧪 Verify Data Persistence
- Open the app → http://localhost:3000
<img width="2210" height="470" alt="image" src="https://github.com/user-attachments/assets/def996a6-195e-479f-a9c9-d1edbc622613" />

- Add some todo items
<img width="3070" height="767" alt="image" src="https://github.com/user-attachments/assets/20d8060c-6bf5-4cf9-a92c-ee3cbb3a6fbb" />

- Stop and remove the container:
```
docker rm -f <container-id>
```
<img width="2605" height="370" alt="image" src="https://github.com/user-attachments/assets/1bb4d9ec-051c-4264-b263-e9c4bb25ec95" />

Start a new container (same command as before)
```
docker run -dp 127.0.0.1:3000:3000 --mount type=volume,src=todo-db,target=//etc/todos getting-started
```
<img width="2925" height="195" alt="image" src="https://github.com/user-attachments/assets/2f39d301-fdcc-4b1a-9400-6986be36602f" />

### 👉 Data should still be there 🎉

<img width="2465" height="762" alt="image" src="https://github.com/user-attachments/assets/24d9f7b4-61e0-44c9-baed-ff76d2dc8044" />


## 🔍 Inspect the Volume

To see where Docker stores data:
```
docker volume inspect todo-db
```
Example output:
```
[
  {
    "CreatedAt": "2019-09-26T02:18:36Z",
    "Driver": "local",
    "Labels": {},
    "Mountpoint": "/var/lib/docker/volumes/todo-db/_data",
    "Name": "todo-db",
    "Options": {},
    "Scope": "local"
  }
]
```
<img width="2210" height="577" alt="image" src="https://github.com/user-attachments/assets/433a9027-e2ce-4449-88b8-64b48fa867a7" />

> 👉 The Mountpoint is where data lives on my machine.

>> ⚠️ I may need admin/root access to view it.

## 📌 Summary

In this section, I:

- Learned how container storage works
- Saw why data is lost by default
- Created a Docker volume
- Persisted application data
  
## 📚 Related Documentation
- [Docker CLI Reference](https://docs.docker.com/engine/reference/commandline/docker/)
- [Docker Volumes](https://docs.docker.com/storage/volumes/)
  
## ➡️ Next Steps

👉 [Use bind mounts](./use-bind-mounts.md)
