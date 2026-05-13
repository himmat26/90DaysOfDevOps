## Challenge Tasks

### Task 1: The Problem
1. Run a Postgres or MySQL container
    docker run --name my-mysql -e MYSQL_DATABASE=mydb -e MYSQL_ROOT_PASSWORD=root -p 3306:3306 mysql:latest

2. Create some data inside it (a table, a few rows — anything)
    Create table:
     mysql -u root -p -e "USE mydb; CREATE TABLE users (id INT AUTO_INCREMENT PRIMARY KEY, name VARCHAR(100), created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP);"
    
    Insert data:
    mysql -u root -p mydb -e "INSERT INTO users (name) VALUES ('Alice');"

    view data:
    bash-5.1# mysql -u root -p mydb -e "SELECT * FROM users;"
    Enter password:
    +----+-------+---------------------+
    | id | name  | created_at          |
    +----+-------+---------------------+
    |  1 | Alice | 2026-03-25 06:12:18 |
    +----+-------+---------------------+

3. Stop and remove the container
    ubuntu@ip-172-31-26-178:~/docker-practice$ docker stop 4a9fc9dbead9 && docker rm 4a9fc9dbead9
    4a9fc9dbead9
    4a9fc9dbead9

4. Run a new one — is your data still there?
    Execute the same command to run a new MySQL container:
    docker run --name my-mysql -e MYSQL_DATABASE=mydb -e MYSQL_ROOT_PASSWORD=root -p 3306:3306 mysql:latest
    Then check if the data is still there:
    bash-5.1# mysql -u root -p mydb -e "SELECT * FROM users;"
    Enter password:
    ERROR 1146 (42S02) at line 1: Table 'mydb.users' doesn't exist

Write what happened and why.
    When we ran the mysql container first time and created the table and inserted data, it was stored inside the container. When we stopped and removed the container, all the data stored inside it was lost. So when we ran a new container, it started with a fresh state and the previous data was not available. This is because by default, Docker containers are ephemeral and do not persist data unless we use volumes or bind mounts to store data outside of the container's filesystem.

---

### Task 2: Named Volumes
1. Create a named volume
    docker volume create   my-sql-data

    /docker-practice$ docker volume ls
    DRIVER    VOLUME NAME
    local     0e7d9c52a5f8b1a37df7d53ca86b9811c91e4a3ff1a06acdb9b8e4c84f66ddc0
    local     21d50045865dc9a623a2a52e9ae82202736d7efb6cdd3cdfa8d3a732526f6b38
    local     67c74bd71250aded44793a65e3664426bf8e1088902cd55e1eb1876409358576
    local     c83b73aab05619bd35d45359f65dcc2fac1aa6e93a18173cd3c7e85cd35a7560
    local     my-sql-data

2. Run the same database container, but this time **attach the volume** to it
    docker run -d --name my-mysql -e MYSQL_DATABASE=mydb -e MYSQL_ROOT_PASSWORD=root -p 3306:3306 -v my-sql-data:/var/lib/mysql  mysql:latest

3. Add some data, stop and remove the container
    initially:
    bash-5.1#  mysql -u root -p mydb -e "SELECT * FROM users;"
    Enter password:
    ERROR 1146 (42S02) at line 1: Table 'mydb.users' doesn't exist

    create table:
     -u root -p -e "USE mydb; CREATE TABLE users (id INT AUTO_INCREMENT PRIMARY KEY, name VARCHAR(100), created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP);"
    Insert data:
    mysql -u root -p mydb -e "INSERT INTO users (name) VALUES ('Alice');"

    bash-5.1# mysql -u root -p mydb -e "SELECT * FROM users;"
    Enter password:
    +----+-------+---------------------+
    | id | name  | created_at          |
    +----+-------+---------------------+
    |  1 | Alice | 2026-03-26 06:12:13 |
    +----+-------+---------------------+

    docker stop and remove:
    docker stop 58e0d20a3d47 && docker rm 58e0d20a3d47

4. Run a brand new container with the **same volume**
    docker run -d --name my-mysql -e MYSQL_DATABASE=mydb -e MYSQL_ROOT_PASSWORD=root -p 3306:3306 -v my-sql-data:/var/lib/mysql  mysql:latest

    ubuntu@ip-172-31-26-178:~/docker-practice$ docker exec -it d01b7ac352f1 bash
        bash-5.1# mysql -u root -p mydb -e "SELECT * FROM users;"
        Enter password:
        +----+-------+---------------------+
        | id | name  | created_at          |
        +----+-------+---------------------+
        |  1 | Alice | 2026-03-26 06:12:13 |
        +----+-------+---------------------+

5. Is the data still there?
     Yes, the data is still there because we used a named volume to store the database files. When we stopped and removed the container, the data persisted in the named volume. When we ran a new container and attached the same volume, it was able to access the existing data.

**Verify:** `docker volume ls`, `docker volume inspect`

---

### Task 3: Bind Mounts
1. Create a folder on your host machine with an `index.html` file 
    /var/www/html/index.html
2. Run an Nginx container and **bind mount** your folder to the Nginx web directory
    docker run -d -p 8080:80 -v /var/www/html:/usr/share/nginx/html  nginx
3. Access the page in your browser
    The webpage is live on the browser
4. Edit the `index.html` on your host — refresh the browser
    The changes are reflected immediately in the browser because we used a bind mount, which links the host directory directly to the container's filesystem. Any changes made to the files in the host directory are instantly visible inside the container.

Write in your notes: What is the difference between a named volume and a bind mount?
Bind mount: Uses a folder from your computer directly; whatever you change on your system shows in the container (and vice versa)
Named volumes: Docker creates and manages the storage itself; you don’t deal with actual folder paths, safer and cleaner for long-term use
---

### Task 4: Docker Networking Basics
1. List all Docker networks on your machine
     docker network ls
    NETWORK ID     NAME      DRIVER    SCOPE
    ca6a2068ae29   bridge    bridge    local
    fae5a019683f   host      host      local
    38df6bd845b2   none      null      local

2. Inspect the default `bridge` network
    docker network inspect ca6a2068ae29
    
3. Run two containers on the default bridge — can they ping each other by **name**?
4. Run two containers on the default bridge — can they ping each other by **IP**?

---

### Task 5: Custom Networks
1. Create a custom bridge network called `my-app-net`
2. Run two containers on `my-app-net`
3. Can they ping each other by **name** now?
4. Write in your notes: Why does custom networking allow name-based communication but the default bridge doesn't?

---

### Task 6: Put It Together
1. Create a custom network
2. Run a **database container** (MySQL/Postgres) on that network with a volume for data
3. Run an **app container** (use any image) on the same network
4. Verify the app container can reach the database by container name

---

## Hints
- Volumes: `docker volume create`, `-v volume_name:/path` 
    [path inside container where volume is mounted can be anything, but for databases it’s usually something like `/var/lib/mysql` or `/var/lib/postgresql/data`]
- Bind mount: `-v /host/path:/container/path`
- Networking: `docker network create`, `--network`
- Ping: `docker exec container1 ping container2`