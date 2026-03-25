# Image-building best practices


## Image layering

Docker images are built in layers. Each instruction in a Dockerfile creates a new layer, which helps with caching and reuse.

These layers can be inspected by using:

```    console
     docker image history getting-started
```

> Example output:

```
    plaintext
    IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
    a78a40cbf866        18 seconds ago      /bin/sh -c #(nop)  CMD ["node" "src/index.j…    0B                  
    f1d1808565d6        19 seconds ago      /bin/sh -c npm install --omit=dev               85.4MB              
    a2c054d14948        36 seconds ago      /bin/sh -c #(nop) COPY dir:5dc710ad87c789593…   198kB               
    9577ae713121        37 seconds ago      /bin/sh -c #(nop) WORKDIR /app                  0B                  
    b95baba1cfdb        13 days ago         /bin/sh -c #(nop)  CMD ["node"]                 0B                  
    <missing>           13 days ago         /bin/sh -c #(nop)  ENTRYPOINT ["docker-entry…   0B                  
    <missing>           13 days ago         /bin/sh -c #(nop) COPY file:238737301d473041…   116B                
    <missing>           13 days ago         /bin/sh -c apk add --no-cache --virtual .bui…   5.35MB              
    <missing>           13 days ago         /bin/sh -c addgroup -g 1000 node     && addu…   74.3MB              
    <missing>           13 days ago         /bin/sh -c #(nop)  ENV NODE_VERSION=12.14.1     0B                  
    <missing>           13 days ago         /bin/sh -c #(nop)  CMD ["/bin/sh"]              0B                  
    <missing>           13 days ago         /bin/sh -c #(nop) ADD file:e69d441d729412d24…   5.59MB   
```
<img width="2480" height="785" alt="image" src="https://github.com/user-attachments/assets/b7c136a7-4863-4779-85f2-1e0e3c8e9e01" />

* Each line represents a layer
* New layers appear at the top
* Layer sizes help identify large image components

> To view full commands without truncation:

```console
     docker image history --no-trunc getting-started
```
<img width="2510" height="162" alt="image" src="https://github.com/user-attachments/assets/64ed911d-de18-40a4-8818-fe283aa18350" />
<img width="2382" height="155" alt="image" src="https://github.com/user-attachments/assets/efdec826-45ca-4e4b-a093-1f23b26b9858" />
<img width="2382" height="162" alt="image" src="https://github.com/user-attachments/assets/2c12d6b1-64f1-453d-bbc5-5eec6583051e" />
<img width="2492" height="167" alt="image" src="https://github.com/user-attachments/assets/101973de-c243-478e-9ba7-3ba8f4782ee1" />
<img width="2280" height="160" alt="image" src="https://github.com/user-attachments/assets/4917aea2-9b4d-40b2-9cad-e01a940e5737" />
<img width="2185" height="165" alt="image" src="https://github.com/user-attachments/assets/eb7fac96-209b-4dbc-8a7a-01e524972c36" />
<img width="2530" height="495" alt="image" src="https://github.com/user-attachments/assets/d0224efc-e94f-4255-a76c-45df6716e852" />
...

## Layer caching

Docker caches layers to speed up builds. However, if one layer changes, all subsequent layers must be rebuilt.

### ❌ Inefficient Dockerfile.

```
dockerfile
# syntax=docker/dockerfile:1
FROM node:24-alpine
WORKDIR /app
COPY . .
RUN npm install --omit=dev
CMD ["node", "src/index.js"]
EXPOSE 3000
```

> 👉 Problem: Any code change invalidates the cache and forces npm install to run again

### 1. ✅ Optimised Dockerfile

 `package.json` first, install dependencies, and then copy everything else in.
 
```
dockerfile
   # syntax=docker/dockerfile:1
   FROM node:24-alpine
   WORKDIR /app
   COPY package.json package-lock.json ./
   RUN npm install --omit=dev
   COPY . .
   CMD ["node", "src/index.js"]
```
<img width="1892" height="940" alt="image" src="https://github.com/user-attachments/assets/1873d770-d67e-4f52-9b8b-b932d92b6fb1" />

> 👉 Benefits:

* Dependencies are only reinstalled when package.json changes
* Faster rebuilds
* Smaller incremental updates

### 2. 🔁 Build and observe caching

```console
    $ docker build -t getting-started .
```

> Example output:

```plaintext
    [+] Building 16.1s (10/10) FINISHED
    => [internal] load build definition from Dockerfile
    => => transferring dockerfile: 175B
    => [internal] load .dockerignore
    => => transferring context: 2B
    => [internal] load metadata for docker.io/library/node:24-alpine
    => [internal] load build context
    => => transferring context: 53.37MB
    => [1/5] FROM docker.io/library/node:24-alpine
    => CACHED [2/5] WORKDIR /app
    => [3/5] COPY package.json package-lock.json ./
    => [4/5] RUN npm install --omit=dev
    => [5/5] COPY . .
    => exporting to image
    => => exporting layers
    => => writing image     sha256:d6f819013566c54c50124ed94d5e66c452325327217f4f04399b45f94e37d25
    => => naming to docker.io/library/getting-started
```
<img width="2497" height="1057" alt="image" src="https://github.com/user-attachments/assets/29d82f25-960e-4137-be4b-eafa1a779802" />

### 3. After making changes to the `src/static/index.html` file.

For example, change the `<title>` to "The Awesome Todo App".
<img width="3352" height="657" alt="image" src="https://github.com/user-attachments/assets/5e51c348-2c76-42e9-8924-eb50d61d7afc" />

### 4. Build the Docker image one more time
```
docker build -t getting-started .`
```
> This time, expected output should look a little different.

```plaintext
    [+] Building 1.2s (10/10) FINISHED
    => [internal] load build definition from Dockerfile
    => => transferring dockerfile: 37B
    => [internal] load .dockerignore
    => => transferring context: 2B
    => [internal] load metadata for docker.io/library/node:24-alpine
    => [internal] load build context
    => => transferring context: 450.43kB
    => [1/5] FROM docker.io/library/node:24-alpine
    => CACHED [2/5] WORKDIR /app
    => CACHED [3/5] COPY package.json package-lock.json ./
    => CACHED [4/5] RUN npm install
    => [5/5] COPY . .
    => exporting to image
    => => exporting layers
    => => writing image     sha256:91790c87bcb096a83c2bd4eb512bc8b134c757cda0bdee4038187f98148e2eda
    => => naming to docker.io/library/getting-started
```
> 👉 Only the changed layers are rebuilt — making builds much faster.
<img width="2510" height="1077" alt="image" src="https://github.com/user-attachments/assets/ce7894a0-1238-49dc-bab6-187691ba0432" />


## Multi-stage builds

Multi-stage builds allow us to use multiple environments during build time while keeping the final image small and clean.

🎯 Benefits
* Separate build-time and runtime dependencies
* Reduce image size
* Improve security

### Maven/Tomcat example

When building Java-based applications, we need a JDK to compile the source code to Java bytecode. However,
that JDK isn't needed in production. Also, we might be using tools like Maven or Gradle to help build the app.
Those also are not needed in our final image. Multi-stage builds help.

```
dockerfile
# syntax=docker/dockerfile:1
FROM maven AS build
WORKDIR /app
COPY . .
RUN mvn package

FROM tomcat
COPY --from=build /app/target/file.war /usr/local/tomcat/webapps 
```

> 👉 Only the final artifact is included in the runtime image.

### React example

When building React applications, we need a Node environment to compile the JS code (typically JSX), SASS stylesheets,
and more into static HTML, JS, and CSS. If we are not doing server-side rendering, we don't even need a Node environment
for our production build. We can ship the static resources in a static nginx container.

```dockerfile
# syntax=docker/dockerfile:1
FROM node:24-alpine AS build
WORKDIR /app
COPY package* ./
RUN npm install
COPY public ./public
COPY src ./src
RUN npm run build

FROM nginx:alpine
COPY --from=build /app/build /usr/share/nginx/html
```

In the previous Dockerfile example, it uses the `node:24-alpine` image to perform the build (maximising layer caching) and then copies the output
into an nginx container.


  > [!Tips]
  > This React example is for illustration purposes. The getting-started todo app is a `Node.js` backend application, not a React frontend.

## Summary

This section help to:
* Understand Docker image layers
* Use layer caching to speed up builds
* Optimize Dockerfiles for efficiency
* Use multi-stage builds to reduce image size

## Related information:
 - [Dockerfile reference](/reference/dockerfile/)
 - [Dockerfile best practices](/build/building/best-practices/)


## ➡️ Next Steps

👉 [What next](./what-next.md)
