## Challenge Tasks

### Task 1: Docker Images
1. Pull the `nginx`, `ubuntu`, and `alpine` images from Docker Hub
    - docker pull nginx
    - docker pull ubuntu
    - docker pull alpine
2. List all images on your machine — note the sizes
    ubuntu@ip-172-31-26-178:~$ docker images
    REPOSITORY               TAG        IMAGE ID       CREATED        SIZE
    nginx                    latest     99133eed2307   3 days ago     161MB
    flask-app                latest     452c9cbba4b0   3 weeks ago    133MB
    ubuntu                   latest     bbdabce66f1b   4 weeks ago    78.1MB
    alpine                   latest     a40c03cbb81c   6 weeks ago    8.44MB
    himmatk2000/python-app   latest     fbaf34eb05b7   2 months ago   133MB
    python                   3.9-slim   085da638e1b8   4 months ago   122MB
    hello-world              latest     1b44b5a3e06a   7 months ago   10.1kB

3. Compare `ubuntu` vs `alpine` — why is one much smaller?
    Alpine is a lightweight image often used as a base image for application. Alpine is designed to include only essential components, it remove unnecessary files and libraries, extra utilities resulting into a much smaller image size.

4. Inspect an image — what information can you see?
    docker image inspect shows metadata about the images: Id - to track the exact image version, RepoTags - to identify the image by name and tag, Created - to know when the image was built, Size - to understand the disk space it occupies, Layers - to see the underlying layers that make up the image, and other details like environment variables, exposed ports, and default command.
    ubuntu@ip-172-31-26-178:~$ docker image inspect nginx

5. Remove an image you no longer need
    ubuntu@ip-172-31-26-178:~$ docker rmi hello-world:latest
    Untagged: hello-world:latest
    Untagged: hello-world@sha256:85404b3c53951c3ff5d40de0972b1bb21fafa2e8daa235355baf44f33db9dbdd
    Deleted: sha256:1b44b5a3e06a9aae883e7bf25e45c100be0bb81a0e01b32de604f3ac44711634


---

### Task 2: Image Layers
1. Run `docker image history nginx` — what do you see?
    In output each line represents a layer of the docker image created during the image build process. It is created by instructions in a Dockerfile.

2. Each line is a **layer**. Note how some layers show sizes and some show 0B
    Each layer executed by a command in the Dockerfile. Some layers show sizes because they add new files or data to the image, while others show 0B because they may only modify existing files or metadata without adding new content.

3. Write in your notes: What are layers and why does Docker use them?
    A docker layer is simply a step in building a docker image. Each layer represents a a change made to the image. It is used to build images faster if nothing is changed in the layer it reuses it from cache. Also, it allows efficient storage as layers can be shared between images.

---

### Task 3: Container Lifecycle
Practice the full lifecycle on one container:
1. **Create** a container (without starting it)
    ubuntu@ip-172-31-26-178:~$ docker create -p 8080:80 nginx
    81a81784da1d126a31ba25ce95c8e3ac429fec4184a8f8d28896ad6eab0034eb

2. **Start** the container
    ubuntu@ip-172-31-26-178:~$ docker start 81a81784da1d
    81a81784da1d

3. **Pause** it and check status
    ubuntu@ip-172-31-26-178:~$ docker pause 81a81784da1
    CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS                       PORTS                                     NAMES
    81a81784da1d   nginx     "/docker-entrypoint.…"   5 minutes ago   Up About a minute (Paused)   0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   heuristic_curran

4. **Unpause** it
    ubuntu@ip-172-31-26-178:~$ docker unpause 81a81784
    Status - Up 2 minutes 

5. **Stop** it
    ubuntu@ip-172-31-26-178:~$ docker stop 81a817

6. **Restart** it
    ubuntu@ip-172-31-26-178:~$ docker restart  81a817
    Status - Up 2 seconds 

7. **Kill** it
    docker kill 81a81784
    Status
    Exited (137) 7 seconds ago 

8. **Remove** it
    ubuntu@ip-172-31-26-178:~$ docker rm 81a817

---

### Task 4: Working with Running Containers
1. Run an Nginx container in detached mode
    ubuntu@ip-172-31-26-178:~$ docker run -d -p 8080:80 nginx
    00963096c081648e8e26a34ea202a5d754d7b5980b08f0903ad3af4eac3855b1

2. View its **logs**
    ubuntu@ip-172-31-26-178:~$ docker logs 00963096c081648e8e26a34ea202a5d754d7b5980b08f0903ad3af4eac3855b1
    /docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
    /docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
    /docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
    10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
    10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
    /docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
    /docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
    /docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
    /docker-entrypoint.sh: Configuration complete; ready for start up
    2026/03/14 12:48:47 [notice] 1#1: using the "epoll" event method
    2026/03/14 12:48:47 [notice] 1#1: nginx/1.29.6
    2026/03/14 12:48:47 [notice] 1#1: built by gcc 14.2.0 (Debian 14.2.0-19)
    2026/03/14 12:48:47 [notice] 1#1: OS: Linux 6.17.0-1007-aws

3. View **real-time logs** (follow mode)
    ubuntu@ip-172-31-26-178:~$ docker logs -f  00963096c081648e

4. **Exec** into the container and look around the filesystem
    docker exec -it 00963096c081648e8e26a34ea202a5d754d7b5980b08f0903ad3af4eac3855b1 bash
    root@00963096c081:/# ls

5. Run a single command inside the container without entering it
    ubuntu@ip-172-31-26-178:~$ docker exec -it 00963096c081648e cat /etc/os-release
    PRETTY_NAME="Debian GNU/Linux 13 (trixie)"
    NAME="Debian GNU/Linux"
    VERSION_ID="13"
    VERSION="13 (trixie)"
    VERSION_CODENAME=trixie
    DEBIAN_VERSION_FULL=13.3
    ID=debian

6. **Inspect** the container — find its IP address, port mappings, and mounts
    ubuntu@ip-172-31-26-178:~$ docker inspect 00963096c081648e
    "IPAddress": "172.17.0.2",
    "Ports": {
                "80/tcp": [
                    {
                        "HostIp": "0.0.0.0",
                        "HostPort": "8080"
                    },
                    {
                        "HostIp": "::",
                        "HostPort": "8080"
                    }   
                ]
    },
    "Volumes": null,
    "Mounts": [],

---

### Task 5: Cleanup
1. Stop all running containers in one command
    ubuntu@ip-172-31-26-178:~$ docker stop $(docker ps -aq)
    00963096c081
    eaaffa2a52d5
    a51dce4b1db3
    f41ac32c1862
    9b1a9235916a
    ff2f14e60998
    3551f18558c2

2. Remove all stopped containers in one command
    ubuntu@ip-172-31-26-178:~$ docker system prune -a

3. Remove unused images
    ubuntu@ip-172-31-26-178:~$ docker system prune -a

4. Check how much disk space Docker is using
    ubuntu@ip-172-31-26-178:~$ docker system df
    TYPE            TOTAL     ACTIVE    SIZE      RECLAIMABLE
    Images          0         0         0B        0B
    Containers      0         0         0B        0B
    Local Volumes   0         0         0B        0B
    Build Cache     0         0         0B        0B

---


