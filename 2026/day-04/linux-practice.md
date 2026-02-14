## commands used
 touch day-04-op.txt
 ps >> day-04-op.txt
 top
 top -5
 top >> day-04-op.txt
 cat day-04-op.txt
 pgrep
 pgrep 1
 pgrep systemd
 pgrep -l sshd
 pgrep -l systemd
 pgrep -l systemd >> day-04-op.txt
 htop
 systemctl status ngnix
 systemctl status nginx
 sudo systemctl restart nginx
 systemctl status nginx
 systemctl list-units
 jornalctl -u nginx
 journalctl -u nginx
 journalctl -u nginx >> day-04-op.txt
 journalctl -u nginx | tail
 scp -i "Linux-tu.pem" ubuntu@ec2-13-48-84-142.eu-north-1.compute.amazonaws.com:day-04-op.txt /c/Users/himmats


 ## Tasks done
 - Checked running processes
 - Inspected a service (nginx)
 - Validated the logs for nginx
 - SSH handson
 - SCP handon - download day 04 output from EC2 to local
