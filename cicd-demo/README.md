# Now understand what happens.

Dockerfile
    │
    │ docker build
    ▼
Docker image
    │
    │ docker run
    ▼
Container
    │
    ▼
Node.js application

## Issue faced:

keerthana@Keerthanas-MacBook-Air cicd-demo % docker build -t cicd-demo:v1 .
[+] Building 4.4s (11/11) FINISHED                                                                                                docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                                                              0.0s
 => => transferring dockerfile: 155B                                                                                                              0.0s
 => [internal] load metadata for docker.io/library/node:22-alpine                                                                                 2.4s
 => [auth] library/node:pull token for registry-1.docker.io                                                                                       0.0s
 => [internal] load .dockerignore                                                                                                                 0.0s
 => => transferring context: 2B                                                                                                                   0.0s
 => [1/5] FROM docker.io/library/node:22-alpine@sha256:c610fcdfb1d5b4740dd70c284ed3cb16bb857e0f7166196e36a5501df7a3aa32                           0.0s
 => => resolve docker.io/library/node:22-alpine@sha256:c610fcdfb1d5b4740dd70c284ed3cb16bb857e0f7166196e36a5501df7a3aa32                           0.0s
 => [internal] load build context                                                                                                                 0.0s
 => => transferring context: 856B                                                                                                                 0.0s
 => CACHED [2/5] WORKDIR /app                                                                                                                     0.0s
 => [3/5] COPY package*.json ./                                                                                                                   0.0s
 => [4/5] RUN npm install                                                                                                                         1.4s
 => [5/5] COPY . .                                                                                                                                0.0s
 => exporting to image                                                                                                                            0.3s
 => => exporting layers                                                                                                                           0.2s
 => => exporting manifest sha256:7831dba128064c459029dcfb0142044fd92fe338b1c190cc13232c90aa05a776                                                 0.0s
 => => exporting config sha256:5562cbede4cb8ae38244aaf33c71721f25f51705d29654e7b1e59a319253f176                                                   0.0s
 => => exporting attestation manifest sha256:d4e33a63eb1d58effa16bc970f05ac31bc9af5268a9a73e9179e4274e1ba1849                                     0.0s
 => => exporting manifest list sha256:559f55cf773a52544ef613eefd1e37747ca906f4687f937106c32ec64dc05d9d                                            0.0s
 => => naming to docker.io/library/cicd-demo:v1                                                                                                   0.0s
 => => unpacking to docker.io/library/cicd-demo:v1                                                                                                0.1s

View build details: docker-desktop://dashboard/build/desktop-linux/desktop-linux/2pyiy9j48iepfwzx96njyhkkw
keerthana@Keerthanas-MacBook-Air cicd-demo % docker images
REPOSITORY                                TAG                                                                           IMAGE ID       CREATED          SIZE
cicd-demo                                 v1                                                                            559f55cf773a   17 seconds ago   232MB
keerthana@Keerthanas-MacBook-Air cicd-demo % docker run -p 3000:3000 cicd-demo:v1

> cicd-demo@1.0.0 start
> node server.js

Server running on port 3000

keerthana@Keerthanas-MacBook-Air cicd-demo % curl http://localhost:3000
curl: (52) Empty reply from server

demo % docker rm -f ad081caae075
ad081caae075
keerthana@Keerthanas-MacBook-Air cicd-demo % docker run --name cicd-test -p 127.0.0.1:3000:3000 cicd-demo:v1

> cicd-demo@1.0.0 start
> node server.js

Server running on port 3000
          
keerthana@Keerthanas-MacBook-Air cicd-demo % docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS                      NAMES
0887ebb00c58   cicd-demo:v1   "docker-entrypoint.s…"   9 seconds ago   Up 8 seconds   127.0.0.1:3000->3000/tcp   cicd-test
keerthana@Keerthanas-MacBook-Air cicd-demo % curl -v http://127.0.0.1:3000
*   Trying 127.0.0.1:3000...
* Connected to 127.0.0.1 (127.0.0.1) port 3000
> GET / HTTP/1.1
> Host: 127.0.0.1:3000
> User-Agent: curl/8.7.1
> Accept: */*
> 
* Request completely sent off
< HTTP/1.1 200 OK
< Content-Type: text/plain
< Date: Fri, 28 Aug 2026 11:15:10 GMT
< Connection: keep-alive
< Keep-Alive: timeout=5
< Transfer-Encoding: chunked
< 
Hello from CI/CD v1
* Connection #0 to host 127.0.0.1 left intact
keerthana@Keerthanas-MacBook-Air cicd-demo % 

### So the complete flow is:

Mac
│
│ localhost:3000
▼
Docker published port
│
│ 127.0.0.1:3000 → container:3000
▼
Container
│
│ port 3000
▼
Node.js server
│
▼
HTTP 200
Hello from CI/CD v1


What was wrong before?
----------------------
* Your earlier container showed:

3000/tcp

That means the container exposes port 3000 internally, but Docker had not published it to your Mac.

* Now it shows:

127.0.0.1:3000->3000/tcp

That means the host-to-container mapping exists.

