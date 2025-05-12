Docker
======

- By Derek Webb
- Weight: 1
- Manual QA review was done by Adrian Liew on May 12, 2025 1:57 PM

Concepts
-------

*For this project, students are expected to look at these concepts:*

- [Docker Tutorial](https://www.docker.com/)
- [Docker Cheatsheet](https://www.docker.com/)
- [Proxy vs Reverse Proxy (Real-world Examples)](https://www.docker.com/)
- [What is a Reverse Proxy? (vs. Forward Proxy) | Proxy servers explained](https://www.docker.com/) (Stop at 06:25)

Background Context
-----------------

Docker is a platform that allows you to containerize your applications, meaning that you can package them into portable, self-contained environments which can run anywhere. This means that you can quickly move your application from one environment to another, such as from your local computer to a server, without worrying about dependencies or configuration issues. Docker achieves this by using containers, which are isolated environments that contain everything an application needs to run, such as libraries, dependencies, and configurations. Docker containers are lightweight and can be started and stopped quickly, making them ideal for modern software development and deployment. With Docker, you can also quickly scale your application by running multiple containers of the same application on different hosts (or the same host, as we will do in this project), and manage them using Docker Compose or other orchestration tools.

Ultimately, what you will create in this project is an infrastructure for an application that utilizes a reverse proxy, a load balancer, two application servers, and one front-end server.

**High-level Design**

In this design, there is a single server that acts as the entry point for your application. That server acts as a reverse proxy server, which routes traffic to either the API servers or the front-end static-content server; it also acts as a load balancer to balance traffic between the two API servers. When traffic comes in, the server will determine which service it needs to go to (either the front-end static-content server or the API server) and:

- If the request is to be routed to the front-end static-content server, it will do so and any static content that is returned from the front-end static-content server to the proxy server will then be sent to the client. The client isn’t directly communicating with the front-end static-content server.
- If the request is to be routed to the API server, then it will first go through a load-balancing algorithm to determine which API server to send the request to. We will be using the basic Round Robin load-balancing algorithm for our site. Once the request is routed to an API server and the response is sent back to the proxy server, it will then be sent to the client. The client isn’t directly communicating with either of the API servers.

**What is Round Robin load balancing?** Round Robin load balancing is a method of distributing traffic across multiple servers or nodes in a network. In this approach, the load balancer assigns requests to each server in a rotating manner, meaning that each server takes turns serving the requests. For example, if there are three servers A, B, and C, the first request will be sent to server A, the second to server B, the third to server C, the fourth to A, the fifth to B, and so on, while the cycle repeats. This ensures that each server receives an equal share of the traffic, and can prevent any one server from becoming overwhelmed with requests. Round Robin load balancing is a simple and effective way to distribute traffic, but it may not be suitable for all applications, particularly those with varying workload patterns or resource requirements. For our learning purposes in this project, it is a sufficient algorithm to implement for our load-balancing needs.

Resources
---------

**Read or watch**:

- [Docker Tutorial](https://www.docker.com/)
- [Docker Cheatsheet](https://www.docker.com/)
- [Proxy vs Reverse Proxy (Real-world Examples)](https://www.docker.com/)
- [What is a Reverse Proxy? (vs. Forward Proxy) | Proxy servers explained](https://www.docker.com/) (Stop at 06:25)

**Install**:

- Install Docker Desktop on your local computer (not your sandbox)
- [Docker Installation](https://www.docker.com/)
- For more detailed installation instructions, see:
  - [Windows](https://www.docker.com/)
  - [Mac](https://www.docker.com/)
  - [Linux](https://www.docker.com/)
  - [Chromebook](https://www.docker.com/) - Note: this is the Docker engine, but not Docker Desktop. It is unclear how well this will work on a chromebook - you may want to use a Windows, Mac, or Linux machine or a machine on campus, instead.

Learning Objectives
-------------------

At the end of this project, you are expected to be able to explain to anyone, **without the help of Google**:

### General

- What is Docker and why is it used
- What is a Docker container
- What is a Dockerfile
- How to create and build a Docker image
- How to run a Docker container
- What is Docker Compose and how is it used
- What is a reverse proxy
- What is load balancing and the Round Robin algorithm
- How to scale applications using Docker

Requirements
------------

### General

- Allowed editors: `vi`, `vim`, `emacs`
- All your files will be interpreted on Ubuntu (latest version)
- All your files should end with a new line
- A `README.md` file, at the root of the folder of the project is required
- Your code should follow standard Linux/Unix conventions
- Your Dockerfiles should be clean and follow best practices
- Use the provided GitHub repository for all tasks

### GitHub

**There should be one project repository per student. If you clone/fork/whatever a project repository with the same name before the deadline, you risk a 0% score.**

More Info
---------

### What You Need Before Starting

- Install Docker Desktop on your local computer (not your sandbox)
- Familiarize yourself with Docker using the provided resources

Tasks
-----

### 0. Create Your First Docker Image

- Create a Dockerfile that:
  - Is based on the latest `ubuntu`
  - Updates APT using `apt-get update`
  - Upgrades currently installed software through APT using `apt-get upgrade -y`
  - Once built, runs in a container and echoes “Hello, World!” on the terminal.

**Example Output**:

```
Dereks-MacBook-Pro:docker-project derekwebb$ docker build -f ./Dockerfile -t softy-pinko:task0 .
[+] Building 0.7s (7/7) FINISHED
 => [internal] load build definition from Dockerfile                                         0.0s
 => => transferring dockerfile: 37B                                                          0.0s
 => [internal] load .dockerignore                                                            0.0s
 => => transferring context: 2B                                                              0.0s
 => [internal] load metadata for docker.io/library/ubuntu:latest                             0.6s
 => [1/3] FROM docker.io/library/ubuntu:latest@sha256:dfd64a3b4296d8c9b62aa3309984f8620b98d  0.0s
 => CACHED [2/3] RUN apt-get update                                                          0.0s
 => CACHED [3/3] RUN apt-get upgrade -y                                                      0.0s
 => exporting to image                                                                       0.0s
 => => exporting layers                                                                      0.0s
 => => writing image sha256:45d461b2a1471047589659e82af46202206be08b5b725d941a0a659b843a402  0.0s
 => => naming to docker.io/library/softy-pinko:task0                                         0.0s
Dereks-MacBook-Pro:docker-project derekwebb$ docker run -it --rm --name softy-pinko-task0 softy-pinko:task0
Hello, World!
Dereks-MacBook-Pro:docker-project derekwebb$
```

**Repo:**

- GitHub repository: `holbertonschool-softy-pinko-docker`
- Directory: `task0`
- 1/1 pt

### 1. Back-end

- Start by making a copy of your `task0` directory and name it `task1`.
- Modify the Dockerfile to install `python3`, `python3-pip`, and `flask`.
- Create a Python file named `api.py` with a Flask server that returns “Hello, World!” on `/api/hello`.
- Host the Flask app on `0.0.0.0:5252`.
- Use `/app` as the working directory and copy `api.py` to the Docker image.
- Forward port `5252` when running the container.

**Example Output**:

```
Dereks-MacBook-Pro:task1 derekwebb$ docker build -f ./Dockerfile -t softy-pinko:task1 .
[+] Building 0.9s (12/12) FINISHED
 => [internal] load build definition from Dockerfile                                         0.0s
 => => transferring dockerfile: 37B                                                          0.0s
 => [internal] load .dockerignore                                                            0.0s
 => => transferring context: 2B                                                              0.0s
 => [internal] load metadata for docker.io/library/ubuntu:latest                             0.8s
 => [internal] load build context                                                            0.0s
 => => transferring context: 28 W3mFKBHB0s
 => [1/7] FROM docker.io/library/ubuntu:latest@sha256:ac58ff7fe25edc58bdf0067ca99df00014dbd  0.0s
 => CACHED [2/7] RUN apt-get update                                                          0.0s
 => CACHED [3/7] RUN apt-get upgrade -y                                                      0.0s
 => CACHED [4/7] RUN apt-get install -y python3 python3-pip                                  0.0s
 => CACHED [5/7] RUN pip3 install flask                                                      0.0s
 => CACHED [6/7] WORKDIR /app                                                                0.0s
 => CACHED [7/7] COPY ./api.py /app/api.py                                                   0.0s
 => exporting to image                                                                       0.0s
 => => exporting layers                                                                      0.0s
 => => writing image sha256:58f5eb04ef4a3ac604fcc74adc799c09e09b2697675d9ec552d45c3a9e7d572  0.0s
 => => naming to docker.io/library/softy-pinko:task1                                         0.0s
Dereks-MacBook-Pro:task1 derekwebb$ docker run -p 5252:5252 -it --rm --name softy-pinko-task1 softy-pinko:task1
 * Serving Flask app 'api'
 * Debug mode: off
WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
 * Running on all addresses (0.0.0.0)
 * Running on http://127.0.0.1:5252
 * Running on http://172.17.0.2:5252
Press CTRL+C to quit
```

**Repo:**

- GitHub repository: `holbertonschool-softy-pinko-docker`
- Directory: `task1`
- 1/1 pt

### 2. Front-end

- Copy `task1` to `task2`.
- Create a `back-end` directory in `task2` and move `api.py` and `Dockerfile` into it.
- Create a `front-end` directory and clone `https://github.com/atlas-school/softy-pinko-front-end`.
- Create a `Dockerfile` in `task2/front-end` using the latest `nginx` image.
- Copy `softy-pinko-front-end` files to `/var/www/html/softy-pinko-front-end`.
- Create `softy-pinko-front-end.conf` and copy it to `/etc/nginx/conf.d/default.conf`.
- Configure Nginx to serve on port `9000`.

**Example Output**:

```
Dereks-MacBook-Pro:task2 derekwebb$ docker build -f ./front-end/Dockerfile -t softy-pinko-front-end:task2 ./front-end
[+] Building 0.6s (8/8) FINISHED
 => [internal] load build definition from Dockerfile                                                                                 0.0s
 => => transferring dockerfile: 37B                                                                                                  0.0s
 => [internal] load .dockerignore                                                                                                    0.0s
 => => transferring context: 2B                                                                                                      0.0s
 => [internal] load metadata for docker.io/library/nginx:latest                                                                      0.5s
 => [internal] load build context                                                                                                    

0.0s
 => => transferring context: 34.13kB                                                                                                 0.0s
 => [1/3] FROM docker.io/library/nginx:latest@sha256:af296b188c7b7df99ba960ca614439c99cb7cf252ed7bbc23e90cfda59092305                0.0s
 => CACHED [2/3] COPY ./softy-pinko-front-end /var/www/html/softy-pinko-front-end                                                    0.0s
 => CACHED [3/3] COPY ./softy-pinko-front-end.conf  /etc/nginx/conf.d/default.conf                                                   0.0s
 => exporting to image                                                                                                               0.0s
 => => exporting layers                                                                                                              0.0s
 => => writing image sha256:5aeebcbf58006d92aa190103a44fa395f2e51a42cc7452a3561ce42af3b2aa46                                         0.0s
 => => naming to docker.io/library/softy-pinko-front-end:task2                                                                       0.0s
Dereks-MacBook-Pro:task2 derekwebb$ docker run -p 9000:9000 -it --rm --name softy-pinko-front-end-task2 softy-pinko-front-end:task2
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: /etc/nginx/conf.d/default.conf differs from the packaged version
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2023/06/12 17:00:32 [notice] 1#1: using the "epoll" event method
2023/06/12 17:00:32 [notice] 1#1: nginx/1.25.0
2023/06/12 17:00:32 [notice] 1#1: built by gcc 10.2.1 20210110 (Debian 10.2.1-6)
2023/06/12 17:00:32 [notice] 1#1: OS: Linux 5.15.49-linuxkit
2023/06/12 17:00:32 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2023/06/12 17:00:32 [notice] 1#1: start worker processes
2023/06/12 17:00:32 [notice] 1#1: start worker process 28
2023/06/12 17:00:32 [notice] 1#1: start worker process 29
2023/06/12 17:00:32 [notice] 1#1: start worker process 30
2023/06/12 17:00:32 [notice] 1#1: start worker process 31
2023/06/12 17:00:32 [notice] 1#1: start worker process 32
2023/06/12 17:00:32 [notice] 1#1: start worker process 33
2023/06/12 17:00:32 [notice] 1#1: start worker process 34
2023/06/12 17:00:32 [notice] 1#1: start worker process 35
```

**Repo:**

- GitHub repository: `holbertonschool-softy-pinko-docker`
- Directory: `task2`
- 1/1 pt

### 3. Connecting the Front-end and Back-end

- Copy `task2` to `task3`.
- Add `<h1 id="dynamic-content"></h1>` to `index.html` before the existing `<h1>`.
- Add a `<script>` to `index.html` to fetch data from `http://localhost:5252/api/hello`.
- Update `back-end/Dockerfile` to install `flask-cors`.
- Update `api.py` to enable CORS.

**Example Output**:

```
First terminal (back-end):
Dereks-MacBook-Pro:task3 derekwebb$ docker build -f ./back-end/Dockerfile -t softy-pinko-back-end:task3 ./back-end
[+] Building 1.2s (13/13) FINISHED
 => [internal] load build definition from Dockerfile                                         0.0s
 => => transferring dockerfile: 37B                                                          0.0s
 => [internal] load .dockerignore                                                            0.0s
 => => transferring context: 2B                                                              0.0s
 => [internal] load metadata for docker.io/library/ubuntu:latest                             1.1s
 => [1/8] FROM docker.io/library/ubuntu:latest@sha256:ac58ff7fe25edc58bdf0067ca99df00014dbd032e2246d30a722fa348fd799a5  0.0s
 => [internal] load build context                                                            0.0s
 => => transferring context: 28B                                                             0.0s
 => CACHED [2/8] RUN apt-get update                                                          0.0s
 => CACHED [3/8] RUN apt-get upgrade -y                                                      0.0s
 => CACHED [4/8] RUN apt-get install -y python3 python3-pip                                  0.0s
 => CACHED [5/8] RUN pip3 install flask                                                      0.0s
 => CACHED [6/8] RUN pip3 install flask-cors                                                 0.0s
 => CACHED [7/8] WORKDIR /app                                                                0.0s
 => CACHED [8/8] COPY ./api.py /app/api.py                                                   0.0s
 => exporting to image                                                                       0.0s
 => => exporting layers                                                                      0.0s
 => => writing image sha256:72111d9280c3fb3d875aa956a9d5eb407adc55256e667ec3e30dcadd128fd073  0.0s
 => => naming to docker.io/library/softy-pinko-back-end:task3                                0.0s
Dereks-MacBook-Pro:task3 derekwebb$ docker run -p 5252:5252 -it --rm --name softy-pinko-back-end-task3 softy-pinko-back-end:task3
 * Serving Flask app 'api'
 * Debug mode: off
WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
 * Running on all addresses (0.0.0.0)
 * Running on http://127.0.0.1:5252
 * Running on http://172.17.0.2:5252
Press CTRL+C to quit

Second terminal (front-end):
Dereks-MacBook-Pro:task3 derekwebb$ docker build -f ./front-end/Dockerfile -t softy-pinko-front-end:task3 ./front-end
[+] Building 1.2s (8/8) FINISHED
 => [internal] load build definition from Dockerfile                                         0.0s
 => => transferring dockerfile: 37B                                                          0.0s
 => [internal] load .dockerignore                                                            0.0s
 => => transferring context: 2B                                                              0.0s
 => [internal] load metadata for docker.io/library/nginx:latest                              1.0s
 => CACHED [1/3] FROM docker.io/library/nginx:latest@sha256:af296b188c7b7df99ba960ca614439c99cb7cf252ed7bbc23e90cfda59092305  0.0s
 => [internal] load build context                                                            0.0s
 => => transferring context: 34.59kB                                                         0.0s
 => [2/3] COPY ./softy-pinko-front-end /var/www/html/softy-pinko-front-end                   0.0s
 => [3/3] COPY ./softy-pinko-front-end.conf  /etc/nginx/conf.d/default.conf                  0.0s
 => exporting to image                                                                       0.0s
 => => exporting layers                                                                      0.0s
 => => writing image sha256:f981f38fb967b1a4d477ce370ed52e1fcd97e1dc4863baf1faf47e908bec4057  0.0s
 => => naming to docker.io/library/softy-pinko-front-end:task3                               0.0s
Dereks-MacBook-Pro:task3 derekwebb$ docker run -p 9000:9000 -it --rm --name softy-pinko-front-end-task3 softy-pinko-front-end:task3
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: /etc/nginx/conf.d/default.conf differs from the packaged version
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2023/06/12 19:10:39 [notice] 1#1: using the "epoll" event method
2023/06/12 19:10:39 [notice] 1#1: nginx/1.25.0
2023/06/12 19:10:39 [notice] 1#1: built by gcc 10.2.1 20210110 (Debian 10.2.1-6)
2023/06/12 19:10:39 [notice] 1#1: OS: Linux 5.15.49-linuxkit
2023/06/12 19:10:39 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2023/06/12 19:10:39 [notice] 1#1: start worker processes
2023/06/12 19:10:39 [notice] 1#1: start worker process 28
2023/06/12 19:10:39 [notice] 1#1: start worker process 29
2023/06/12 19:10:39 [notice] 1#1: start worker process 30
2023/06/12 19:10:39 [notice] 1#1: start worker process 31
2023/06/12 19:10:39 [notice] 1#1: start worker process 32
2023/06/12 19:10:39 [notice] 1#1: start worker process 33
2023/06/12 19:10:39 [notice] 1#1: start worker process 34
2023/06/12 19:10:39 [notice] 1#1: start worker process 35
```

**Repo:**

- GitHub repository: `holbertonschool-softy-pinko-docker`
- Directory: `task3`
- 2/2 pts

### 4. Making it Simpler with Docker Compose

- Copy `task3` to `task4`.
- Create a `docker-compose.yml` file in `task4`.
- Define services for front-end and back-end, including `build`, `context`, `dockerfile`, `image`, `ports`, and `depends_on`.
- Use `docker-compose build` and `docker-compose up` to run.

**Example Output**:

```
Dereks-MacBook-Pro:task4 derekwebb$ docker-compose build
[+] Building 1.3s (13/13) FINISHED
 => [internal] load build definition from Dockerfile                                                                       0.0s
 => => transferring dockerfile: 32B                                                                                        0.0s
 => [internal] load .dockerignore                                                                                          0.0s
 => => transferring context: 2B                                                                                            0.0s
 => [internal] load metadata for docker.io/library/ubuntu:latest                                                           1.2s
 => [1/8] FROM docker.io/library/ubuntu:latest@sha256:ac58ff7fe25edc58bdf0067ca99df00014dbd032e2246d30a722fa348fd799a5     0.0s
 => [internal] load build context                                                                                          0.0s
 => => transferring context: 28B                                                                                           0.0s
 => CACHED [2/8] RUN apt-get update                                                                                        0.0s
 => CACHED [3/8] RUN apt-get upgrade -y                                                                                    0.0s
 => CACHED [4/8] RUN apt-get install -y python3 python3-pip                                                                0.0s
 => CACHED [5/8] RUN pip3 install flask                                                                                    0.0s
 => CACHED [6/8] RUN pip3 install flask-cors                                                                               0.0s
 => CACHED [7/8] WORKDIR /app                                                                                              0.0s
 => CACHED [8/8] COPY ./api.py /app/api.py                                                                                 0.0s
 => exporting to image                                                                                                     0.0s
 => => exporting layers                                                                                                    0.0s
 => => writing image sha256:26379ebe4741c491978aa03c623ebbd768c964fefba64055e3013460fe7c5a1d                               0.0s
 => => naming to docker.io/library/softy-pinko-back-end:task4                                                              0.0s
[+] Building 1.0s (8/8) FINISHED
 => [internal] load build definition from Dockerfile                                                                       0.0s
 => => transferring dockerfile: 32B                                                                                        0.0s
 => [internal] load .dockerignore                                                                                          0.0s
 => => transferring context: 2B                                                                                            0.0s
 => [internal] load metadata for docker.io/library/nginx:latest                                                            0.8s
 => [internal] load build context                                                                                          0.1s
 => => transferring context: 3.46kB                                                                                        0.0s
 => [1/3] FROM docker.io/library/nginx:latest@sha256:af296b188c7b7df99ba960ca614439c99cb7cf252ed7bbc23e90cfda59092305      0.0s
 => CACHED [2/3] COPY ./softy-pinko-front-end /var/www/html/softy-pinko-front-end                                          0.0s
 => CACHED [3/3] COPY ./softy-pinko-front-end.conf  /etc/nginx/conf.d/default.conf                                         0.0s
 => exporting to image                                                                                                     0.1s
 => => exporting layers                                                                                                    0.0s
 => => writing image sha256:479f9d992a6eae6a56dff4b0cf7f9754446fa195ef273afba5705f460fee8da4                               0.0s
 => => naming to docker.io/library/softy-pinko-front-end:task4                                                             0.0s
Dereks-MacBook-Pro:task4 derekwebb$ docker-compose up
[+] Running 0/2
 ⠿ front-end Warning                                                                                                                                                                                                                               2.1s
 ⠿ back-end Warning                                                                                                                                                                                                                                2.1s
[+] Building 1.6s (20/20) FINISHED
 => [softy-pinko-front-end:task4 internal] load build definition from Dockerfile                                                                                                                                                                   0.0s
 => => transferring dockerfile: 188B                                                                                                                                                                                                               0.0s
 => [softy-pinko-back-end:task4 internal] load build definition from Dockerfile                                                                                                                                                                    0.0s
 => => transferring dockerfile: 262B                                                                                                                                                                                                               0.0s
 => [softy-pinko-front-end:task4 internal] load .dockerignore                                                                                                                                                                                      0.0s
 => => transferring context: 2B                                                                                                                                                                                                                    0.0s
 => [softy-pinko-back-end:task4 internal] load .dockerignore                                                                                                                                                                                       0.0s
 => => transferring context: 2B                                                                                                                                                                                                                    0.0s
 => [softy-pinko-front-end:task4 internal] load metadata for docker.io/library/nginx:latest                                                                                                                                                        1.4s
 => [softy-pinko-back-end:task4 internal] load metadata for docker.io/library/ubuntu:latest                                                                                                                                                        1.4s
 => [softy-pinko-back-end:task4 internal] load build context                                                                                                                                                                                       0.0s
 => => transferring context: 250B                                                                                                                                                                                                                  0.0s
 => [softy-pinko-back-end:task4 1/8] FROM docker.io/library/ubuntu:latest@sha256:ac58ff7fe25edc58bdf0067ca99df00014dbd032e2246d30a722fa348fd799a5                                                                                                  0.0s
 => [softy-pinko-front-end:task4 internal] load build context                                                                                                                                                                                      0.1s
 => => transferring context: 2.68MB                                                                                                                                                                                                                0.0s
 => [softy-pinko-front-end:task4 1/3] FROM docker.io/library/nginx:latest@sha256:af296b188c7b7df99ba960ca614439c99cb7cf252ed7bbc23e90cfda59092305                                                                                                  0.0s
 => CACHED [softy-pinko-back-end:task4 2/8] RUN apt-get update                                                                                                                                                                                     0.0s
 => CACHED [softy-pinko-back-end:task4 3/8] RUN apt-get upgrade -y                                                                                                                                                                                 0.0s
 => CACHED [softy-pinko-back-end:task4 4/8] RUN apt-get install -y python3 python3-pip                                                                                                                                                             0.0s
 => CACHED [softy-pinko-back-end:task4 5/8] RUN pip3 install flask                                                                                                                                                                                 0.0s
 => CACHED [softy-pinko-back-end:task4 6/8] RUN pip3 install flask-cors                                                                                                                                                                            0.0s
 => CACHED [softy-pinko-back-end:task4 7/8] WORKDIR /app                                                                                                                                                                                           0.0s
 => CACHED [softy-pinko-back-end:task4 8/8] COPY ./api.py /app/api.py                                                                                                                                                                              0.0s
 => [softy-pinko-front-end:task4] exporting to image                                                                                                                                                                                               0.0s
 => => exporting layers                                                                                                                                                                                                                            0.0s
 => => writing image sha256:26379ebe4741c491978aa03c623ebbd768c964fefba64055e3013460fe7c5a1d                                                                                                                                                       0.0s
 => => naming to docker.io/library/softy-pinko-back-end:task4                                                                                                                                                                                      0.0s
 => => writing image sha256:479f9d992a6eae6a56dff4b0cf7f9754446fa195ef273afba5705f460fee8da4                                                                                                                                                       0.0s
 => => naming to docker.io/library/softy-pinko-front-end:task4                                                                                                                                                                                     0.0s
 => CACHED [softy-pinko-front-end:task4 2/3] COPY ./softy-pinko-front-end /var/www/html/softy-pinko-front-end                                                                                                                                      0.0s
 => CACHED [softy-pinko-front-end:task4 3/3] COPY ./softy-pinko-front-end.conf  /etc/nginx/conf.d/default.conf                                                                                                                                     0.0s
[+] Running 3/3
 ⠿ Network task4_default        Created                                                                                                                                                                                                            0.0s
 ⠿ Container task4-back-end-1   Created                                                                                                                                                                                                            0.1s
 ⠿ Container task4-front-end-1  Created                                                                                                                                                                                                            0.1s
Attaching to task4-back-end-1, task4-front-end-1
task4-back-end-1   |  * Serving Flask app 'api'
task4-back-end-1   |  * Debug mode: off
task4-back-end-1   | WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
task4-back-end-1   |  * Running on all addresses (0.0.0.0)
task4-back-end-1   |  * Running on http://127.0.0.1:5252
task4-back-end-1   |  * Running on http://172.18.0.2:5252
task4-back-end-1   | Press CTRL+C to quit
task4-front-end-1  | /docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
task4-front-end-1  | /docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
task4-front-end-1  | /docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
task4-front-end-1  | 10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
task4-front-end-1  | 10-listen-on-ipv6-by-default.sh: info: /etc/nginx/conf.d/default.conf differs from the packaged version
task4-front-end-1  | /docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
task4-front-end-1  | /docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
task4-front-end-1  | /docker-entrypoint.sh: Configuration complete; ready for start up
task4-front-end-1  | 2023/06/12 19:27:43 [notice] 1#1: using the "epoll" event method
task4-front-end-1  | 2023/06/12 19:27:43 [notice] 1#1: nginx/1.25.0
task4-front-end-1  | 2023/06/12 19:27:43 [notice] 1#1: built by gcc 10.2.1 20210110 (Debian 10.2.1-6)
task4-front-end-1  | 2023/06/12 19:27:43 [notice] 1#1: OS: Linux 5.15.49-linuxkit
task4-front-end-1  | 2023/06/12 19:27:43 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
task4-front-end-1  | 2023/06/12 19:27:43 [notice] 1#1: start worker processes
task4-front-end-1  | 2023/06/12 19:27:43 [notice] 1#1: start worker process 28
task4-front-end-1  | 2023/06/12 19:27:43 [notice] 1#1: start worker process 29
task4-front-end-1  | 2023/06/12 19:27:43 [notice] 1#1: start worker process 30
task4-front-end-1  | 2023/06/12 19:27:43 [notice] 1#1: start worker process 31
task4-front-end-1  | 2023/06/12 19:27:43 [notice] 1#1: start worker process 32
task4-front-end-1  | 2023/06/12 19:27:43 [notice] 1#1: start worker process 33
task4-front-end-1  | 2023/06/12 19:27:43 [notice] 1#1: start worker process 34
task4-front-end-1  | 2023/06/12 19:27:43 [notice] 1#1: start worker process 35
```

**Repo:**

- GitHub repository: `holbertonschool-softy-pinko-docker`
- Directory: `task4`
- 1/1 pt

### 5. Proxy Server

- Copy `task4` to `task5`.
- Create a `proxy` directory with a `Dockerfile` using `nginx` and a `proxy.conf` file.
- Copy `proxy.conf` to `/etc/nginx/conf.d/default.conf`.
- Configure `proxy.conf` to listen on port `80`, route `/` to the front-end (`:9000`), and `/api` to the back-end (`:5252`).
- Update `index.html` JavaScript to use `/api/hello`.
- Update `docker-compose.yml` to add a `proxy` service, mapping port `80`, and remove host port mappings for front-end and back-end.

**Example Output**:

```
Dereks-MacBook-Pro:task5 derekwebb$ docker-compose build
[+] Building 1.5s (13/13) FINISHED
 => [internal] load build definition from Dockerfile                                                                       0.0s
 => => transferring dockerfile: 262B                                                                                       0.0s
 => [internal] load .dockerignore                                                                                          0.0s
 => => transferring context: 2B                                                                                            0.0s
 => [internal] load metadata for docker.io/library/ubuntu:latest                                                           1.4s
 => [1/8] FROM docker.io/library/ubuntu:latest@sha256:ac58ff7fe25edc58bdf0067ca99df00014dbd032e2246d30a722fa348fd799a5     0.0s
 => [internal] load build context                                                                                          0.0s
 => => transferring context: 259B                                                                                          0.0s
 => CACHED [2/8] RUN apt-get update                                                                                        0.0s
 => CACHED [3/8] RUN apt-get upgrade -y                                                                                    0.0s
 => CACHED [4/8] RUN apt-get install -y python3 python3-pip                                                                0.0s
 => CACHED [5/8] RUN pip3 install flask                                                                                    0.0s
 => CACHED [6/8] RUN pip3 install flask-cors                                                                               0.0s
 => CACHED [7/8] WORKDIR /app                                                                                              0.0s
 => CACHED [8/8] COPY ./api.py /app/api.py                                                                                 0.0s
 => exporting to image                                                                                                     0.0s
 => => exporting layers                                                                                                    0.0s
 => => writing image sha256:839f2b832b76117f25a85adc73133026d69ab9008eb6c01459922b47cd61a2b1                               0.0s
 => => naming to docker.io/library/softy-pinko-back-end:task5                                                              0.0s
[+] Building 1.1s (8/8) FINISHED
 => [internal] load build definition from Dockerfile                                                                       0.0s
 => => transferring dockerfile: 188B                                                                                       0.0s
 => [internal] load .dockerignore                                                                                          0.0s
 => => transferring context: 2B                                                                                            0.0s
 => [internal] load metadata for docker.io/library/nginx:latest                                                            0.9s
 => [1/3] FROM docker.io/library/nginx:latest@sha256:593dac25b7733ffb7afe1a72649a43e574778bf025ad60514ef40f6b5d606247      0.0s
 => [internal] load build context                                                                                          0.0s
 => => transferring context: 2.68MB                                                                                        0.0s
 => CACHED [2/3] COPY ./softy-pinko-front-end /var/www/html/softy-pinko-front-end                                          0.0s
 => CACHED [3/3] COPY ./softy-pinko-front-end.conf  /etc/nginx/conf.d/default.conf                                         0.0s
 => exporting to image                                                                                                     0.0s
 => => exporting layers                                                                                                    0.0s
 => => writing image sha256:7c073606697aa4578af1ddad5f2210e611f5aa0762175006d0992b637c1de081                               0.0s
 => => naming to docker.io/library/softy-pinko-front-end:task5                                                             0.0s
[+] Building 0.4s (7/7) FINISHED
 => [internal] load build definition from Dockerfile                                                                       0.0s
 => => transferring dockerfile: 31B                                                                                        0.0s
 => [internal] load .dockerignore                                                                                          0.0s
 => => transferring context: 2B                                                                                            0.0s
 => [internal] load metadata for docker.io/library/nginx:latest                                                            0.3s
 => [internal] load build context                                                                                          0.0s
 => => transferring context: 32B                                                                                           0.0s
 => [1/2] FROM docker.io/library/nginx:latest@sha256:593dac25b7733ffb7afe1a72649a43e574778bf025ad60514ef40f6b5d606247      0.0s
 => CACHED [2/2] COPY ./proxy.conf /etc/nginx/conf.d/default.conf                                                          0.0s
 => exporting to image                                                                                                     0.0s
 => => exporting layers                                                                                                    0.0s
 => => writing image sha256:bc5dd1e5f2e15577273ef909c555c32b7dfd892c5c6f07f1d50a8519a4c99f32                               0.0s
 => => naming to docker.io/library/softy-pinko-proxy:task5                                                                 0.0s
Dereks-MacBook-Pro:task5 derekwebb$ docker-compose up
[+] Running 3/0
 ⠿ Container task5-back-end-1   Created                                                                                    0.0s
 ⠿ Container task5-front-end-1  Created                                                                                    0.0s
 ⠿ Container task5-proxy-1      Created                                                                                    0.0s
Attaching to task5-back-end-1, task5-front-end-1, task5-proxy-1
task5-back-end-1   |  * Serving Flask app 'api'
task5-back-end-1   |  * Debug mode: off
task5-back-end-1   | WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
task5-back-end-1   |  * Running on all addresses (0.0.0.0)
task5-back-end-1   |  * Running on http://127.0.0.1:5252
task5-back-end-1   |  * Running on http://172.19.0.2:5252
task5-back-end-1   | Press CTRL+C to quit
task5-front-end-1  | /docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
task5-front-end-1  | /docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
task5-front-end-1  | /docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
task5-front-end-1  | 10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
task5-front-end-1  | 10-listen-on-ipv6-by-default.sh: info: /etc/nginx/conf.d/default.conf differs from the packaged version
task5-front-end-1  | /docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
task5-front-end-1  | /docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
task5-front-end-1  | /docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
task5-front-end-1  | /docker-entrypoint.sh: Configuration complete; ready for start up
task5-front-end-1  | 2023/06/15 19:28:38 [notice] 1#1: using the "epoll" event method
task5-front-end-1  | 2023/06/15 19:28:38 [notice] 1#1: nginx/1.25.1
task5-front-end-1  | 2023/06/15 19:28:38 [notice] 1#1: built by gcc 12.2.0 (Debian 12.2.0-14)
task5-front-end-1  | 2023/06/15 19:28:38 [notice] 1#1: OS: Linux 5.15.49-linuxkit
task5-front-end-1  | 2023/06/15 19:28:38 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
task5-front-end-1  | 2023/06/15 19:28:38 [notice] 1#1: start worker processes
task5-front-end-1  | 2023/06/15 19:28:38 [notice] 1#1: start worker process 28
task5-front-end-1  | 2023/06/15 19:28:38 [notice] 1#1: start worker process 29
task5-front-end-1  | 2023/06/15 19:28:38 [notice] 1#1: start worker process 30
task5-front-end-1  | 2023/06/15 19:28:38 [notice] 1#1: start worker process 31
task5-front-end-1  | 2023/06/15 19:28:38 [notice] 1#1: start worker process 32
task5-front-end-1  | 2023/06/15 19:28:38 [notice] 1#1: start worker process 33
task5-front-end-1  | 2023/06/15 19:28:38 [notice] 1#1: start worker process 34
task5-front-end-1  | 2023/06/15 19:28:38 [notice] 1#1: start worker process 35
task5-proxy-1      | /docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
task5-proxy-1      | /docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
task5-proxy-1      | /docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
task5-proxy-1      | 10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
task5-proxy-1      | 10-listen-on-ipv6-by-default.sh: info: /etc/nginx/conf.d/default.conf differs from the packaged version
task5-proxy-1      | /docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
task5-proxy-1      | /docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
task5-proxy-1      | /docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
task5-proxy-1      | /docker-entrypoint.sh: Configuration complete; ready for start up
task5-proxy-1      | 2023/06/15 19:28:39 [notice] 1#1: using the "epoll" event method
task5-proxy-1      | 2023/06/15 19:28:39 [notice] 1#1: nginx/1.25.1
task5-proxy-1      | 2023/06/15 19:28:39 [notice] 1#1: built by gcc 12.2.0 (Debian 12.2.0-14)
task5-proxy-1      | 2023/06/15 19:28:39 [notice] 1#1: OS: Linux 5.15.49-linuxkit
task5-proxy-1      | 2023/06/15 19:28:39 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
task5-proxy-1      | 2023/06/15 19:28:39 [notice] 1#1: start worker processes
task5-proxy-1      | 2023/06/15 19:28:39 [notice] 1#1: start worker process 28
task5-proxy-1      | 2023/06/15 19:28:39 [notice] 1#1: start worker process 29
task5-proxy-1      | 2023/06/15 19:28:39 [notice] 1#1: start worker process 30
task5-proxy-1      | 2023/06/15 19:28:39 [notice] 1#1: start worker process 31
task5-proxy-1      | 2023/06/15 19:28:39 [notice] 1#1: start worker process 32
task5-proxy-1      | 2023/06/15 19:28:39 [notice] 1#1: start worker process 33
task5-proxy-1      | 2023/06/15 19:28:39 [notice] 1#1: start worker process 34
task5-proxy-1      | 2023/06/15 19:28:39 [notice] 1#1: start worker process 35
task5-back-end-1   | 172.19.0.4 - - [15/Jun/2023 19:28:48] "GET /api/hello HTTP/1.0" 200 -
task5-front-end-1  | 2023/06/15 19:28:49 [error] 30#30: *27 open() "/var/www/html/softy-pinko-front-end/favicon.ico" failed (2: No such file or directory), client: 172.19.0.4, server: softy-pinko-front-end, request: "GET /favicon.ico HTTP/1.0", host: "front-end:9000", referrer: "http://localhost/"
```

**Repo:**

- GitHub repository: `holbertonschool-softy-pinko-docker`
- Directory: `task5`
- 3/3 pts

### 6. Scale Horizontally

- Copy `task5` to `task6`.
- Modify `docker-compose.yml` to run two back-end API servers.
- Create `2-api-servers.txt` with the `docker-compose` command to spin up two API servers.
- Nginx will load-balance using Round Robin.

**Example Output**:

```
[+] Running 4/0
 ⠿ Container task6-back-end-2   Created                                                                                    0.0s
 ⠿ Container task6-back-end-1   Created                                                                                    0.0s
 ⠿ Container task6-front-end-1  Created                                                                                    0.0s
 ⠿ Container task6-proxy-1      Created                                                                                    0.0s
Attaching to task6-back-end-1, task6-back-end-2, task6-front-end-1, task6-proxy-1
task6-back-end-2   |  * Serving Flask app 'api'
task6-back-end-2   |  * Debug mode: off
task6-back-end-2   | WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
task6-back-end-2   |  * Running on all addresses (0.0.0.0)
task6-back-end-2   |  * Running on http://127.0.0.1:5252
task6-back-end-2   |  * Running on http://172.20.0.2:5252
task6-back-end-2   | Press CTRL+C to quit
task6-back-end-1   |  * Serving Flask app 'api'
task6-back-end-1   |  * Debug mode: off
task6-back-end-1   | WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
task6-back-end-1   |  * Running on all addresses (0.0.0.0)
task6-back-end-1   |  * Running on http://127.0.0.1:5252
task6-back-end-1   |  * Running on http://172.20.0.3:5252
task6-back-end-1   | Press CTRL+C to quit
task6-front-end-1  | /docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
task6-front-end-1  | /docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
task6-front-end-1  | /docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
task6-front-end-1  | 10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
task6-front-end-1  | 10-listen-on-ipv6-by-default.sh: info: /etc/nginx/conf.d/default.conf differs from the packaged version
task6-front-end-1  | /docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
task6-front-end-1  | /docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
task6-front-end-1  | /docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
task6-front-end-1  | /docker-entrypoint.sh: Configuration complete; ready for start up
task6-front-end-1  | 2023/06/15 19:52:22 [notice] 1#1: using the "epoll" event method
task6-front-end-1  | 2023/06/15 19:52:22 [notice] 1#1: nginx/1.25.1
task6-front-end-1  | 2023/06/15 19:52:22 [notice] 1#1: built by gcc 12.2.0 (Debian 12.2.0-14)
task6-front-end-1  | 2023/06/15 19:52:22 [notice] 1#1: OS: Linux 5.15.49-linuxkit
task6-front-end-1  | 2023/06/15 19:52:22 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
task6-front-end-1  | 2023/06/15 19:52:22 [notice] 1#1: start worker processes
task6-front-end-1  | 2023/06/15 19:52:22 [notice] 1#1: start worker process 27
task6-front-end-1  | 2023/06/15 19:52:22 [notice] 1#1: start worker process 28
task6-front-end-1  | 2023/06/15 19:52:22 [notice] 1#1: start worker process 29
task6-front-end-1  | 2023/06/15 19:52:22 [notice] 1#1: start worker process 30
task6-front-end-1  | 2023/06/15 19:52:22 [notice] 1#1: start worker process 31
task6-front-end-1  | 2023/06/15 19:52:22 [notice] 1#1: start worker process 32
task6-front-end-1  | 2023/06/15 19:52:22 [notice] 1#1: start worker process 33
task6-front-end-1  | 2023/06/15 19:52:22 [notice] 1#1: start worker process 34
task6-proxy-1      | /docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
task6-proxy-1      | /docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
task6-proxy-1      | /docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
task6-proxy-1      | 10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
task6-proxy-1      | 10-listen-on-ipv6-by-default.sh: info: /etc/nginx/conf.d/default.conf differs from the packaged version
task6-proxy-1      | /docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
task6-proxy-1      | /docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
task6-proxy-1      | /docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
task6-proxy-1      | /docker-entrypoint.sh: Configuration complete; ready for start up
task6-proxy-1      | 2023/06/15 19:52:23 [notice] 1#1: using the "epoll" event method
task6-proxy-1      | 2023/06/15 19:52:23 [notice] 1#1: nginx/1.25.1
task6-proxy-1      | 2023/06/15 19:52:23 [notice] 1#1: built by gcc 12.2.0 (Debian 12.2.0-14)
task6-proxy-1      | 2023/06/15 19:52:23 [notice] 1#1: OS: Linux 5.15.49-linuxkit
task6-proxy-1      | 2023/06/15 19:52:23 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
task6-proxy-1      | 2023/06/15 19:52:23 [notice] 1#1: start worker processes
task6-proxy-1      | 2023/06/15 19:52:23 [notice] 1#1: start worker process 28
task6-proxy-1      | 2023/06/15 19:52:23 [notice] 1#1: start worker process 29
task6-proxy-1      | 2023/06/15 19:52:23 [notice] 1#1: start worker process 30
task6-proxy-1      | 2023/06/15 19:52:23 [notice] 1#1: start worker process 31
task6-proxy-1      | 2023/06/15 19:52:23 [notice] 1#1: start worker process 32
task6-proxy-1      | 2023/06/15 19:52:23 [notice] 1#1: start worker process 33
task6-proxy-1      | 2023/06/15 19:52:23 [notice] 1#1: start worker process 34
task6-proxy-1      | 2023/06/15 19:52:23 [notice] 1#1: start worker process 35
```

**Repo:**

- GitHub repository: `holbertonschool-softy-pinko-docker`
- Directory: `task6`