# Day 09 Challenge

## Users & Groups Created
- Users: tokyo, berlin, professor, nairobi
- Groups: dev, admin, project-team

## Group Assignments
dev: tokyo,berlin
admin: berlin,professor
project-team: nairobi,tokyo

## Directories Created
drwxrwxr-x 2 root dev          4096 Feb 14 17:14 dev-project
drwxrwxr-x 2 root project-team 4096 Feb 14 17:18 team-workspace

## Commands Used
cat /etc/passwd
sudo useradd tokyo
cat /etc/passwd
sudo useradd berlin
sudo useraddprofessor
sudo useradd professor
groupadd dev
sudo groupadd dev
sudo groupadd admin
cat /etc/group
sudo usermod -aG tokyo dev
sudo usermod -aG dev  tokyo
cat /etc/group
sudo usermod -aG dev  berlin
sudo usermod -aG admin  berlin
sudo usermod -aG admin  professor
cat /etc/group
sudo mkdir dev-project
sudo chgrp dev dev-project/
sudo chmod 775 dev-project/
cd dev-project/
touch tokyo
sudo useradd nairobi
sudo groupadd project-team
sudo usermod -aG project-team nairobi
sudo usermod -aG project-team tokyo
cat /etc/passwd
cat /etc/group
sudo mkdir team-workspace
sudo chgrp project-team team-workspace/
sudo chmod 775 team-workspace/
cd team-workspace/
touch test
cat /etc/group
 
## What I Learned
- Created multiple users with home directories and passwords
- Created and managed groups
- Assigned users to single and multiple groups
- Configured shared directories with proper group ownership
- Applied permissions on directories.
