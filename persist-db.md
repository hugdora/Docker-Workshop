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
### Step 3: Run container with volume
```
docker run -dp 127.0.0.1:3000:3000 --mount type=volume,src=todo-db,target=//etc/todos getting-started
```

## 🧪 Verify Data Persistence
- Open the app → http://localhost:3000
- Add some todo items
- Stop and remove the container:
```
docker rm -f <container-id>
```
Start a new container (same command as before)

👉 Data should still be there 🎉

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
👉 The Mountpoint is where data lives on my machine.

⚠️ I may need admin/root access to view it.

### 📌 Summary

In this section, I:

- Learned how container storage works
- Saw why data is lost by default
- Created a Docker volume
- Persisted application data
  
### 📚 Related Documentation
* Docker CLI Reference
* Docker Volumes
  
➡️ Next Steps, I’ll learn how to develop more efficiently using bind mounts.
