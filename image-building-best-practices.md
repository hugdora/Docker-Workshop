# Use Docker Compose


[Docker Compose](/compose/) is a tool uses to define and share multi-container applications.
With Compose, create a YAML file to define the services and with a single command, you can spin everything up or tear it all down.

The biggest advantage of Compose is that your application configuration is stored in a file, version-controlled alongside your code.
This makes it easy for others to clone your repository and start the app without manually running multiple commands.

## Create the Compose file

In the `getting-started-app` directory, create a file named `compose.yaml`.

```text
├── getting-started-app/
│ ├── Dockerfile
│ ├── compose.yaml
│ ├── node_modules/
│ ├── package.json
│ ├── package-lock.json
│ ├── spec/
│ └── src/
```
<img width="3090" height="917" alt="image" src="https://github.com/user-attachments/assets/16bc1883-9cef-45e0-b0e6-5f236bd803d1" />


## Define the app service

In [part 6](/get-started/workshop/07_multi_container/), I started the app using a long docker run command. 

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
Now, I will translate that into a Compose service

> Open compose.yaml in a text or code editor

### Step 1: Define the service
   The name will automatically become a network alias, which will be useful when defining your MySQL service.

   ```yaml
   services:
     app:
       image: node:24-alpine
   ```

### Step 2: Add the startup command

   ```yaml
   services:
     app:
       image: node:24-alpine
       command: sh -c "npm install && npm run dev"
   ```

### Step 3: Configure port mapping

   ```yaml
   services:
     app:
       image: node:24-alpine
       command: sh -c "npm install && npm run dev"
       ports:
         - 127.0.0.1:3000:3000
   ```

### Step 4: Set working directory and bind mount

   
   ```yaml
   services:
     app:
       image: node:24-alpine
       command: sh -c "npm install && npm run dev"
       ports:
         - 127.0.0.1:3000:3000
       working_dir: /app
       volumes:
         - ./:/app
   ```

### Step 5: Add environment variables

   ```yaml
   services:
     app:
       image: node:24-alpine
       command: sh -c "npm install && npm run dev"
       ports:
         - 127.0.0.1:3000:3000
       working_dir: /app
       volumes:
         - ./:/app
       environment:
         MYSQL_HOST: mysql
         MYSQL_USER: root
         MYSQL_PASSWORD: secret
         MYSQL_DB: todos
   ```

## Define the MySQL service

In the previous section, I started the app using a long docker run command.

```console
$ docker run -d \
  --network todo-app --network-alias mysql \
  -v todo-mysql-data:/var/lib/mysql \
  -e MYSQL_ROOT_PASSWORD=secret \
  -e MYSQL_DATABASE=todos \
  mysql:8.0
```
Now, you will translate that into a Compose service.

### Step 1: Add the service

   ```yaml

   services:
     app:
       # The app service definition
     mysql:
       image: mysql:8.0
   ```

### Step 2: Configure volume

   ```yaml
   services:
     app:
       # The app service definition
     mysql:
       image: mysql:8.0
       volumes:
         - todo-mysql-data:/var/lib/mysql

   volumes:
     todo-mysql-data:
   ```
### Step 3: Add environment variables

   ```yaml
   services:
     app:
       # The app service definition
     mysql:
       image: mysql:8.0
       volumes:
         - todo-mysql-data:/var/lib/mysql
       environment:
         MYSQL_ROOT_PASSWORD: secret
         MYSQL_DATABASE: todos

   volumes:
     todo-mysql-data:
   ```

### Final complete `compose.yaml` should look like this:


```yaml
services:
  app:
    image: node:24-alpine
    command: sh -c "npm install && npm run dev"
    ports:
      - 127.0.0.1:3000:3000
    working_dir: /app
    volumes:
      - ./:/app
    environment:
      MYSQL_HOST: mysql
      MYSQL_USER: root
      MYSQL_PASSWORD: secret
      MYSQL_DB: todos

  mysql:
    image: mysql:8.0
    volumes:
      - todo-mysql-data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: todos

volumes:
  todo-mysql-data:
```
> Save it
<img width="2430" height="1460" alt="image" src="https://github.com/user-attachments/assets/765275b4-c346-405c-84eb-cd159bee1e9e" />

## Run the application stack

### 1. Clean up existing containers

```
docker ps
```
<img width="2235" height="170" alt="image" src="https://github.com/user-attachments/assets/2239496b-8ca7-4ad5-b8c7-375a54ad1f6b" />

```
docker rm -f <container-id>
```

### 2. Start the stack
    Add the  `-d` flag to run everything in the background.

   ```console
   $ docker compose up -d
   ```
  > Expected output:

   ```plaintext
   Creating network "app_default" with the default driver
   Creating volume "app_todo-mysql-data" with default driver
   Creating app_app_1   ... done
   Creating app_mysql_1 ... done
   ```
<img width="2385" height="452" alt="image" src="https://github.com/user-attachments/assets/b837e698-a8e0-4bfc-9058-c9ac9e345b11" />

### 3. View logs

```
docker compose logs -f
```
> Expected output
  
    ```plaintext
    mysql_1  | 2019-10-03T03:07:16.083639Z 0 [Note] mysqld: ready for connections.
    mysql_1  | Version: '8.0.31'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server (GPL)
    app_1    | Connected to mysql db at host mysql
    app_1    | Listening on port 3000
    ```

<img width="2515" height="1085" alt="image" src="https://github.com/user-attachments/assets/b04aaa19-a00a-4208-a3d3-739d26fed312" />
<img width="2525" height="1122" alt="image" src="https://github.com/user-attachments/assets/cae48289-a7e2-4f4a-b115-c4d8acc322a0" />

To view logs for a specific service:
```
docker compose logs -f app
```
<img width="2522" height="1120" alt="image" src="https://github.com/user-attachments/assets/edc29bf8-8bfd-4f95-94dd-198de7b69c3e" />
<img width="1837" height="367" alt="image" src="https://github.com/user-attachments/assets/f96c8811-9865-45c1-af8d-6e9fe1e8f3d8" />

### 4. Open the app

👉 [http://localhost:3000](http://localhost:3000)

<img width="2437" height="440" alt="image" src="https://github.com/user-attachments/assets/310a9e88-a2ba-404a-86c3-5c671b473ea2" />


## See the app stack in Docker Desktop Dashboard

In Docker Desktop, your containers are grouped under a project name (e.g., `getting-started-app`), which is derived from the folder containing `compose.yaml`.

Expanding the project shows your services (`app`, `mysql`). Container names follow the format:

<service-name>-<replica-number> This makes it easy to identify each container.

<img width="3770" height="1940" alt="image" src="https://github.com/user-attachments/assets/89a7b77d-8012-4586-a7a7-8c21931616e6" />
<img width="3810" height="1795" alt="image" src="https://github.com/user-attachments/assets/7196165b-3212-4e70-aee1-19d5af26f961" />



## Tear it all down

 Run `docker compose down` or hit the trash can on the Docker Desktop Dashboard
for the entire app. The containers will stop and the network will be removed.

> [!WARNING]
>
> By default, named volumes in your compose file are not removed when you run `docker compose down`. If you want to
>remove the volumes, you need to add the `--volumes` flag.
>
> The Docker Desktop Dashboard does not remove volumes when you delete the app stack.

<img width="2317" height="300" alt="image" src="https://github.com/user-attachments/assets/b6fc5474-5167-453b-b570-910e0dc3c494" />


## Summary

Docker Compose simplifies multi-container applications by:

* Defining services in a single file
* Automating networking and volumes
* Making setups reproducible and shareable

## Related information:
 - [Compose overview](/compose/)
 - [Compose file reference](/reference/compose-file/)
 - [Compose CLI reference](/reference/cli/docker/compose/)


















## ➡️ Next Steps

👉 [What next](./what-next.md)

