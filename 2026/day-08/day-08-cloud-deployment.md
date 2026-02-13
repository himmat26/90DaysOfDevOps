Day 08 – Cloud Server Setup: Docker, Nginx & Web Deployment

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

Learning:
- To create an EC2 instance on AWS.
- SSH handson 
- process management
- service deployment
- security groups on EC2 server
- Accessing logs for a service
