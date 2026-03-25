
# Multi-Container Apps

Up to this point, I've been working with a single-container app. Now, I'll add **MySQL** to my application stack.

A common question arises:

> ❓ Should MySQL run inside the same container or separately?

👉 Best practice: **run services in separate containers**

### Why?

- 🔄 Scale components independently (API vs DB)
- 🔧 Update versions without affecting other services
- ☁️ Use managed databases in production (e.g., AWS RDS)
- ⚙️ Avoid running multiple processes in one container

<img width="561" height="238" alt="image" src="https://github.com/user-attachments/assets/66b6f191-b7f3-4b90-bec9-9858adadfc66" />

---

## Container networking

By default, containers are isolated.

To allow communication between containers:

👉 Place them on the **same Docker network**

---

## Start MySQL

There are two ways to put a container on a network:
 - Assign the network when starting the container.
 - Connect an already running container to a network.

In the following steps, I'll create the network first and then attach the MySQL container at startup.

### 1. Create the network.

   ```console
   $ docker network create todo-app
   ```
<img width="2485" height="205" alt="image" src="https://github.com/user-attachments/assets/5008fe8f-b212-4bc5-b2bb-9d264229458e" />


### 2. Start MySQL container and attach it to the network

PowerShell (Windows):

   ```powershell
   $ docker run -d `
       --network todo-app --network-alias mysql `
       -v todo-mysql-data:/var/lib/mysql `
       -e MYSQL_ROOT_PASSWORD=secret `
       -e MYSQL_DATABASE=todos `
       mysql:8.0
   ```
 <img width="2505" height="880" alt="image" src="https://github.com/user-attachments/assets/b9db0659-f7df-429c-9fb6-565ff331cfde" />

💡 Docker automatically creates the volume todo-mysql-data
   
 ---  
   
### 3. Verify MySQL is running
```
docker ps
```
<img width="2525" height="327" alt="image" src="https://github.com/user-attachments/assets/a4573de1-c873-4f2a-a1c3-ba01dda31ae3" />

Then connect:
```
docker exec -it <mysql-container-id> mysql -u root -p
```
Enter password: `secret`

Check databases: `SHOW DATABASES;`

Expected: `todos` database

   You should see output that looks like this:

   ```plaintext
   +--------------------+
   | Database           |
   +--------------------+
   | information_schema |
   | mysql              |
   | performance_schema |
   | sys                |
   | todos              |
   +--------------------+
   5 rows in set (0.00 sec)
   ```

### 4. Exit the MySQL shell to return to the shell on your machine.

   ```console
   mysql> exit
   ```
<img width="1955" height="137" alt="image" src="https://github.com/user-attachments/assets/515335b9-6947-4562-80af-d2da5b6e0ca4" />

   ---
   
## Test Container Networking

Now, MySQL is up and running, I can use it. I will run another container on the same network
by using of the [nicolaka/netshoot](https://github.com/nicolaka/netshoot) container, which ships with a 
lot of tools that are useful for troubleshooting or debugging networking issues.

1. Start a new container using the nicolaka/netshoot image. Make sure to connect it to the same network.

   ```console
   $ docker run -it --network todo-app nicolaka/netshoot
   ```
 <img width="2485" height="1347" alt="image" src="https://github.com/user-attachments/assets/1007c3ad-3f9d-4c1e-a109-d955cd3098aa" />


2. Inside the container, use the `dig` command, which is a useful DNS tool.
   > look up the IP address for the hostname `mysql`.

   ```console
   $ dig mysql
   ```

   > get output like the following.

   ```text
   ; <<>> DiG 9.18.8 <<>> mysql
   ;; global options: +cmd
   ;; Got answer:
   ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 32162
   ;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0

   ;; QUESTION SECTION:
   ;mysql.				IN	A

   ;; ANSWER SECTION:
   mysql.			600	IN	A	172.23.0.2

   ;; Query time: 0 msec
   ;; SERVER: 127.0.0.11#53(127.0.0.11)
   ;; WHEN: Tue Oct 01 23:47:24 UTC 2019
   ;; MSG SIZE  rcvd: 44
   ```

<img width="1932" height="817" alt="image" src="https://github.com/user-attachments/assets/08652741-96bb-4996-b749-9d2e5ad3eaf5" />

   In the "ANSWER SECTION", see an `A` record for `mysql` that resolves to `172.23.0.2`
   While `mysql` isn't normally a valid hostname,
   Docker was able to resolve it to the IP address of the container that had that network alias.
   Remember, `--network-alias` was uesd earlier.
   
> 💡 This means: Containers can communicate using hostname (mysql) instead of IP

## Run the app with MySQL

### Environment variables used:

- `MYSQL_HOST` - the hostname for the running MySQL server
- `MYSQL_USER` - the username to use for the connection
- `MYSQL_PASSWORD` - the password to use for the connection
- `MYSQL_DB` - the database to use once connected

> ⚠️ Important

Before running:

```docker ps
```
<img width="2530" height="270" alt="image" src="https://github.com/user-attachments/assets/85be4e0a-2236-4507-bbee-e69f1caca90b" />

Remove any container using port 3000:
```
docker rm -f <container-id>
```
<img width="2505" height="352" alt="image" src="https://github.com/user-attachments/assets/3ef0a50c-2575-48a0-bef3-8e1de2268156" />


### Start the app container
 
   **PowerShell**
   In Windows, run this command in PowerShell.

   ```powershell
   $ docker run -dp 127.0.0.1:3000:3000 `
     -w /app -v ".:/app" `
     --network todo-app `
     -e MYSQL_HOST=mysql `
     -e MYSQL_USER=root `
     -e MYSQL_PASSWORD=secret `
     -e MYSQL_DB=todos `
     node:24-alpine `
     sh -c "npm install && npm run dev"
   ```
<img width="2485" height="550" alt="image" src="https://github.com/user-attachments/assets/37bd18b6-5e8e-42ba-b694-ff164ad0c7bb" />

### Verify Connection
```
docker logs -f <container-id>
```
Expected outcome

   ```console
   [nodemon] 3.1.11
   [nodemon] to restart at any time, enter `rs`
   [nodemon] watching path(s): *.*
   [nodemon] watching extensions: js,mjs,cjs,json
   [nodemon] starting `node src/index.js`
   Waiting for mysql:3306.
   Connected!
   Connected to mysql db at host mysql
   Listening on port 3000
   ```
<img width="2480" height="1470" alt="image" src="https://github.com/user-attachments/assets/262b406e-6ea1-491b-ba12-82d64992e53d" />

### Test in Browser
```
http://localhost:3000
```
Add some todo item
<img width="2487" height="442" alt="image" src="https://github.com/user-attachments/assets/5755b61e-48b5-46e8-b9ab-284ea564c441" />
<img width="2425" height="925" alt="image" src="https://github.com/user-attachments/assets/a16b809b-85e5-4fa1-afc9-249836f9a08a" />

### Verify Data in MySQL
 Connect to the mysql database and prove that the items are being written to the database. Remember, the password
   is `secret`.

   ```console
   $ docker exec -it <mysql-container-id> mysql -p todos
   ```

   And in the mysql shell, run the following:

   ```console
   mysql> select * from todo_items;
   +--------------------------------------+--------------------+-----------+
   | id                                   | name               | completed |
   +--------------------------------------+--------------------+-----------+
   | c906ff08-60e6-44e6-8f49-ed56a0853e85 | Do amazing things! |         0 |
   | 2912a79e-8486-4bc3-a4c5-460793a575ab | Be awesome!        |         0 |
   +--------------------------------------+--------------------+-----------+
   ```
<img width="2512" height="1442" alt="image" src="https://github.com/user-attachments/assets/ca2db03d-af62-4065-ae7f-4646036cdade" />
   
## Summary

I now have:

🐳 App running in one container
🗄️ MySQL running in another container
🔗 Communication via Docker network
💾 Data persisted in MySQL

## Related information:

 - [docker CLI reference](/reference/cli/docker/)
 - [Networking overview](/engine/network/)

## ➡️ Next Steps

👉 [Use Docker Compose](./use-docker-compose.md)


