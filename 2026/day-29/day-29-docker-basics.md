## Challenge Tasks

### Task 1: What is Docker?
Research and write short notes on:
- What is a container and why do we need them?
    - A container is a lightweight, standalone, and executable package of software that includes everything neede to run and application: code, runtime, system tools, libraries and settings.
    - Containers eliminate the "it works on my machine" problem by ensuring that the application runs the same way regardless of where it's deployed, making them ideal for development, testing, and production environments.
    -  lightweight and efficient, allowing for faster startup times and better resource utilization compared to traditional virtual machines.
    -  isolation: Containers provide a level of isolation between applications, ensuring that they do not interfere with each other, which enhances security and stability.
- Containers vs Virtual Machines — what's the real difference?
    Containers
        • Lightweight: Shares the host OS kernel, so containers are much smaller and faster to start.
        • Isolation: Processes are isolated but run on the same OS.
        • Performance: Near-native performance due to minimal overhead.
        • Portability: Containers can run anywhere Docker is installed.
        • Use Case: Ideal for microservices, CI/CD pipelines, and scalable cloud-native apps.
    
    Virtual Machines
        • Heavyweight: Includes a full OS with its own kernel, which consumes more resources.
        • Isolation: Stronger isolation since each VM runs its own OS.
        • Performance: Slower startup and more resource-intensive.
        • Portability: VMs are less portable due to OS dependencies.
        • Use Case: Best for running multiple OS environments or legacy applications.

- What is the Docker architecture? (daemon, client, images, containers, registry)
    - Docker uses a client-server architecture where the client communicates with docker daemon, which manages the containers,images and other resources.

        1. Docker Client → primary user interface to communicate with docker. sends CLI/API requests.
        2. Docker Daemon (dockerd) → persistent background process that manages Docker objects such as images,containers,networks and volumes. orchestrates images, networks, volumes, and delegates container lifecycle.
        3. containerd → manages container lifecycle (create/start/stop/pause/delete), image pull/push, snapshots.
        4. runc → invoked by containerd to actually spawn containers using Linux namespaces, cgroups, and OCI configs.
        5. Docker Registry → storage and distribution of docker images source/target for pull/push.
        6. Docker Objects → Images (templates) and Containers (running instances).


---

### Task 2: Install Docker
1. Install Docker on your machine (or use a cloud instance)
2. Verify the installation
    ubuntu@ip-172-31-26-178:~$ docker --version
    Docker version 28.2.2, build 28.2.2-0ubuntu1~24.04.1

3. Run the `hello-world` container
    ubuntu@ip-172-31-26-178:~$ docker run hello-world

    Hello from Docker!
    This message shows that your installation appears to be working correctly.

4. Read the output carefully — it explains what just happened
    To generate this message, Docker took the following steps:
    1. The Docker client contacted the Docker daemon.
    2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
    4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.


---

### Task 3: Run Real Containers
1. Run an **Nginx** container and access it in your browser
    docker run -d -p 8080:80 nginx
2. Run an **Ubuntu** container in interactive mode — explore it like a mini Linux machine
    docker run -it ubuntu
    Unable to find image 'ubuntu:latest' locally
    latest: Pulling from library/ubuntu
    01d7766a2e4a: Pull complete
    Digest: sha256:d1e2e92c075e5ca139d51a140fff46f84315c0fdce203eab2807c7e495eff4f9
    Status: Downloaded newer image for ubuntu:latest
    root@a51dce4b1db3:/# ps aux
    USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
    root           1  0.0  0.4   4596  3892 pts/0    Ss   11:53   0:00 /bin/bash
    root          10  0.0  0.4   4596  4024 pts/0    S    11:53   0:00 bash
    root          13  0.0  0.4   7896  4120 pts/0    R+   11:54   0:00 ps aux

3. List all running containers
    ubuntu@ip-172-31-26-178:~$ docker ps
    CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS         PORTS     NAMES
    14f373320286   ubuntu    "/bin/bash"   3 seconds ago   Up 2 seconds             strange_spence

4. List all containers (including stopped ones)
    ubuntu@ip-172-31-26-178:~$ docker ps -a
    CONTAINER ID   IMAGE                           COMMAND                  CREATED          STATUS                        PORTS     NAMES
    14f373320286   ubuntu                          "/bin/bash"              22 seconds ago   Up 22 seconds                           strange_spence
    a51dce4b1db3   ubuntu                          "/bin/bash"              3 minutes ago    Exited (127) 56 seconds ago             pensive_banzai
    f41ac32c1862   nginx                           "/docker-entrypoint.…"   11 minutes ago   Exited (0) 4 minutes ago                unruffled_bhabha
    ff2f14e60998   nginx                           "/docker-entrypoint.…"   14 minutes ago   Exited (0) 12 minutes ago               hungry_wozniak
    dfb1e1ba4acf   hello-world                     "/hello"                 17 minutes ago   Exited (0) 17 minutes ago               relaxed_davinci
    
5. Stop and remove a container
    ubuntu@ip-172-31-26-178:~$ docker stop 14f373320286
    14f373320286
    ubuntu@ip-172-31-26-178:~$ docker rm 14f373320286
    14f373320286

---

### Task 4: Explore
1. Run a container in **detached mode** — what's different?
    using -d to execute conatiner in detached mode, it runs the container in background and terminal free after running the command.
2. Give a container a custom **name**:  - --name
3. Map a **port** from the container to your host: -p hostport:containerport
o/p: port 80 of nginx container is mapped to port 8080 of host machine, name my-nginx is given to container, -d flag is used to run container in detached mode.    

-    docker run -d -p 8080:80 --name my-nginx nginx

4. Check **logs** of a running container
    ubuntu@ip-172-31-26-178:~$ docker logs eaaffa2a52d5
    /docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
    /docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
    /docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
    10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
    10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf

5. Run a command **inside** a running container
    ubuntu@ip-172-31-26-178:~$ docker exec -it my-nginx bash
    root@eaaffa2a52d5:/# echo "i am inside the container"
    i am inside the container
    root@eaaffa2a52d5:/#
---

