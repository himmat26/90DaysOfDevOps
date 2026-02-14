Day 08 – Cloud Server Setup: Docker, Nginx & Web Deployment

## commands used
ssh -i "Linux-tu.pem" ubuntu@ec2-51-20-144-138.eu-north-1.compute.amazonaws.com
sudo apt install nginx
sudo systemctl status nginx
ps aux
ps aux | grep 'nginx'
top
htop
ls
cd
ls
cd /var/log/nginx
ls
cat access.log
cat error.log
$ scp -i 'Linux-tu.pem' ubuntu@ec2-13-53-35-255.eu-north-1.compute.amazonaws.com:/var/log/nginx/logs.txt /c/Users/himmats

## Issue encountered::
Permission Denied (publickey)
Session Timed Out
Failure in Name Resolution
SCP transfer failing intermittently

## Root Causes Identified
Permission Issues
Private key did not have correct permissions.
Target file on EC2 did not have read permission for the user.
Session Timeout
Public IP may have changed (if instance restarted without Elastic IP).
Failure in Name Resolution
Verified correct EC2 public DNS/IP from AWS Console.
SCP Command Debugging
SSH Fundamentals Revisited - Authentication via key-pair (public/private).

## Learning:
- To create an EC2 instance on AWS.
- SSH handson 
- process management
- service deployment
- security groups on EC2 server
- Accessing logs for a service
- SCP transfer
