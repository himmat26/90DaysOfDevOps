# Day 11 Challenge

## Files & Directories Created
devops-file.txt
team-notes.txt
config.yaml
app-logs
heist-project/vault
heist-project/plans
heist-project/vault/gold.txt
heist-project/plans/startegy.conf
bank-heist/access-code.txt
bank-heist/blueprint.pdf
bank-heist/escape-plan.txt

## Ownership Changes
devops-file.txt : owner - ubuntu -> tokyo
team-notes.txt : group - ubuntu -> heistteam
config.yaml : ubuntu:ubuntu -> professor:heist-team
app-logs : ubuntu:ubuntu  -> berlin:heist-team
heist-project : ubuntu:ubuntu -> professor planners 
bank-heist : ubuntu:ubuntu ->
access-code.txt : ubuntu:ubuntu -> tokyo:vault-team
blueprint.pdf : ubuntu:ubuntu -> berlin:tecj-team 
escape-plan.txt : ubuntu:ubuntu -> nairobi vault-team

## Commands Used
touch devops-file.txt
sudo chown tokyo devops-file.txt
touch team-notes.txt
sudo groupadd heist-team
sudo chgrp heist-team team-notes.txt
touch config.yaml
sudo chown professor:heist-team config.yaml
mkdir app-logs
sudo chown berlin:heist-team app-logs/
mkdir -p  heist-project/vault
mkdir -p  heist-project/plans
touch heist-project/vault/gold.txt
touch heist-project/plans/startegy.conf
sudo groupadd planners
sudo chown -R professor:planners heist-project/
cd heist-project/
cat /etc/group
cat /etc/passwd
sudo group add vault-team
sudo groupadd vault-team techt-team
sudo groupadd vault-team
sudo groupadd tecj-team
cd practice/
mkdir bank-heist
touch bank-heist/access-code.txt
touch bank-heist/blueprint.pdf
touch bank-heist/escape-plan.txt
ls  -l
cd  bank-heist/.
access-code.txt
ls -l

## What I Learned
- Changed both owner and group together in one command.
- Applied recursive ownership changes to entire directories.
- Practiced structured ownership inside nested directories



