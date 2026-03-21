## Challenge Tasks

### Task 1: Your First Dockerfile
1. Create a folder called `my-first-image`
2. Inside it, create a `Dockerfile` that:
   - Uses `ubuntu` as the base image
   - Installs `curl`
   - Sets a default command to print `"Hello from my custom image!"`
    FROM ubuntu:latest
    RUN apt-get update && apt-get install -y curl
    CMD ["echo", "Hello from my custom image!"]

3. Build the image and tag it `my-ubuntu:v1`
    docker build -t  my-ubuntu:v1 .
    Successfully built 02a55179ee7f
    Successfully tagged my-ubuntu:v1

4. Run a container from your image
    ubuntu@ip-172-31-26-178:~/docker-practice/my-first-image$ docker run my-ubuntu:v1
    Hello from my custom image!


**Verify:** The message prints on `docker run`

---

### Task 2: Dockerfile Instructions
Create a new Dockerfile that uses **all** of these instructions:
- `FROM` — base image
- `RUN` — execute commands during build
- `COPY` — copy files from host to image
- `WORKDIR` — set working directory
- `EXPOSE` — document the port
- `CMD` — default command

O/P - Dockerfile
FROM python:3.9-slim

WORKDIR /app

COPY . /app

RUN pip install --upgrade pip && pip install -r requirements.txt

EXPOSE 5000
CMD ["python", "run.py"]

Build and run it. Understand what each line does.
Image built successfully:
Successfully built af4d579397cb
Successfully tagged flask-app:latest

Container running successfully:
docker run -d -p 5000:80 flask-app:latest
cf368d26edae   flask-app:latest   "python run.py"   3 seconds ago   Up 3 seconds   0.0.0.0:5000->80/tcp,

Ouput on the browser:
Hello Dosto, welcome to DevOps Zero To Hero (Josh)

---

### Task 3: CMD vs ENTRYPOINT
1. Create an image with `CMD ["echo", "hello"]` — run it, then run it with a custom command. What happens?
    create a Dockerfile with:
    CMD ["echo", "Hello from my custom image!"]
    docker run hello:latest
    Hello from my custom image!

    execute with custom command:
    docker run hello:latest echo HelloCMD
    HelloCMD

2. Create an image with `ENTRYPOINT ["echo"]` — run it, then run it with additional arguments. What happens?
    ENTRYPOINT ["echo"]
    without additional arguments: output is blank
    docker run hello:latest

    provided hello as argument:
    docker run hello:latest Hello
    Hello

3. Write in your notes: When would you use CMD vs ENTRYPOINT?
    CMD is used to provide default arguments for the container, which can be overriden when running the container. ENTRYPOINT is used to set a fixed command that will always run, and any additional arguments provided will be passed to it. Use CMD when you want to allow users to override the default command, and use ENTRYPOINT when you want to ensure a specific command is always executed.

---

### Task 4: Build a Simple Web App Image
1. Create a small static HTML file (`index.html`) with any content
2. Write a Dockerfile that:
   - Uses `nginx:alpine` as base
   - Copies your `index.html` to the Nginx web directory
    Dockerfile: 
    FROM nginx:alpine
    WORKDIR /app
    COPY index.html /usr/share/nginx/html/
    EXPOSE 80

3. Build and tag it `my-website:v1`
    docker build -t web-app:latest .
    docker run -d -p 8080:80 web-app:latest

4. Run it with port mapping and access it in your browser
    http://13.62.53.83:8080/

---

### Task 5: .dockerignore
1. Create a `.dockerignore` file in one of your project folders
    touch .dockerignore

2. Add entries for: `node_modules`, `.git`, `*.md`, `.env`
    ubuntu@ip-172-31-26-178:~/docker-practice/Simple-Web-App$ cat .dockerignore
    node_modules
    .git
    *.md
    .env

3. Build the image — verify that ignored files are not included

    ubuntu@ip-172-31-26-178:~/docker-practice/Simple-Web-App$ ls -a
    .  ..  .dockerignore  .env  .git  Dockerfile  demo.md  index.html

    /app # ls -l
    total 0

---

### Task 6: Build Optimization
1. Build an image, then change one line and rebuild — notice how Docker uses **cache**
2. Reorder your Dockerfile so that frequently changing lines come **last**    
    Sending build context to Docker daemon   7.68kB
    Step 1/4 : FROM nginx:alpine
    ---> d0c780774910
    Step 2/4 : WORKDIR /app
    ---> Using cache
    ---> 4eb2960e0605
    Step 3/4 : COPY index.html /usr/share/nginx/html/
    ---> Using cache
    ---> 74b1dcc14a4e
    Step 4/4 : EXPOSE 80
    ---> Using cache
     ---> ea3eacc66f58
    Successfully built ea3eacc66f58
    Successfully tagged web-app:latest


3. Write in your notes: Why does layer order matter for build speed?
    
    - Docker builds image in layers
    - Each layer is cache
    - If one layer changes, all next layers rebuild
    - Placing frequently changing instructions at the end prevents unnecessary rebuilds and speeds up  image build.

---
