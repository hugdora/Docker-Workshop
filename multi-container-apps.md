
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

In the following steps, you'll create the network first and then attach the MySQL container at startup.

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
## Connect to MySQL

Now that you know MySQL is up and running, you can use it. But, how do you use it? If you run
another container on the same network, how do you find the container? Remember that each container has its own IP address.

To answer the questions above and better understand container networking, you're going to make use of the [nicolaka/netshoot](https://github.com/nicolaka/netshoot) container,
which ships with a lot of tools that are useful for troubleshooting or debugging networking issues.

1. Start a new container using the nicolaka/netshoot image. Make sure to connect it to the same network.

   ```console
   $ docker run -it --network todo-app nicolaka/netshoot
   ```

2. Inside the container, you're going to use the `dig` command, which is a useful DNS tool. You're going to look up
   the IP address for the hostname `mysql`.

   ```console
   $ dig mysql
   ```

   You should get output like the following.

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

   In the "ANSWER SECTION", you will see an `A` record for `mysql` that resolves to `172.23.0.2`
   (your IP address will most likely have a different value). While `mysql` isn't normally a valid hostname,
   Docker was able to resolve it to the IP address of the container that had that network alias. Remember, you used the
   `--network-alias` earlier.

   What this means is that your app only simply needs to connect to a host named `mysql` and it'll talk to the
   database.

## Run your app with MySQL

The todo app supports the setting of a few environment variables to specify MySQL connection settings. They are:

- `MYSQL_HOST` - the hostname for the running MySQL server
- `MYSQL_USER` - the username to use for the connection
- `MYSQL_PASSWORD` - the password to use for the connection
- `MYSQL_DB` - the database to use once connected

> [!NOTE]
>
> While using env vars to set connection settings is generally accepted for development, it's highly discouraged
> when running applications in production. Diogo Monica, a former lead of security at Docker,
> [wrote a fantastic blog post](https://blog.diogomonica.com/2017/03/27/why-you-shouldnt-use-env-variables-for-secret-data/)
> explaining why.
>
> A more secure mechanism is to use the secret support provided by your container orchestration framework. In most cases,
> these secrets are mounted as files in the running container. You'll see many apps (including the MySQL image and the todo app)
> also support env vars with a `_FILE` suffix to point to a file containing the variable.
>
> As an example, setting the `MYSQL_PASSWORD_FILE` var will cause the app to use the contents of the referenced file
> as the connection password. Docker doesn't do anything to support these env vars. Your app will need to know to look for
> the variable and get the file contents.

You can now start your dev-ready container.

1. Specify each of the previous environment variables, as well as connect the container to your app network. Make sure that you are in the `getting-started-app` directory when you run this command.

   **Mac / Linux**



   ```console
   $ docker run -dp 127.0.0.1:3000:3000 \
     -w /app -v ".:/app" \
     --network todo-app \
     -e MYSQL_HOST=mysql \
     -e MYSQL_USER=root \
     -e MYSQL_PASSWORD=secret \
     -e MYSQL_DB=todos \
     node:24-alpine \
     sh -c "npm install && npm run dev"
   ```
   
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

   **Command Prompt**


   In Windows, run this command in Command Prompt.

   ```console
   $ docker run -dp 127.0.0.1:3000:3000 ^
     -w /app -v "%cd%:/app" ^
     --network todo-app ^
     -e MYSQL_HOST=mysql ^
     -e MYSQL_USER=root ^
     -e MYSQL_PASSWORD=secret ^
     -e MYSQL_DB=todos ^
     node:24-alpine ^
     sh -c "npm install && npm run dev"
   ```

   **Git Bash**



   ```console
   $ docker run -dp 127.0.0.1:3000:3000 \
     -w //app -v "/.:/app" \
     --network todo-app \
     -e MYSQL_HOST=mysql \
     -e MYSQL_USER=root \
     -e MYSQL_PASSWORD=secret \
     -e MYSQL_DB=todos \
     node:24-alpine \
     sh -c "npm install && npm run dev"
   ```
   
   

2. If you look at the logs for the container (`docker logs -f <container-id>`), you should see a message similar to the following, which indicates it's
   using the mysql database.

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

3. Open the app in your browser and add a few items to your todo list.

4. Connect to the mysql database and prove that the items are being written to the database. Remember, the password
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

   Your table will look different because it has your items. But, you should see them stored there.

## Summary

At this point, you have an application that now stores its data in an external database running in a separate
container. You learned a little bit about container networking and service discovery using DNS.

Related information:
 - [docker CLI reference](/reference/cli/docker/)
 - [Networking overview](/engine/network/)

## Next steps

There's a good chance you are starting to feel a little overwhelmed with everything you need to do to start up
this application. You have to create a network, start containers, specify all of the environment variables, expose
ports, and more. That's a lot to remember and it's certainly making things harder to pass along to someone else.

In the next section, you'll learn about Docker Compose. With Docker Compose, you can share your application stacks in a
much easier way and let others spin them up with a single, simple command.

[Use Docker Compose](/get-started/workshop/07_multi_container/08_using_compose/)


