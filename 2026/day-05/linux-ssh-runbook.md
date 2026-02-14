# Linux Troubleshooting Runbook -- SSH Service

Check basic system health and verify SSH service is running properly.
Service: ssh
------------------------------------------------------------------------
## Environment Basics
### Command
uname -  

o/p 
Linux
### Command
uname -a

o/p
Linux ip-172-31-26-178 6.14.0-1018-aws #18~24.04.1-Ubuntu SMP Mon Nov 24 19:46:27 UTC 2025 x86_64 x86_64 x86_64 GNU/Linux

------------------------------------------------------------------------
### Command
cat /etc/os-release

o/p
Ubuntu 24.04.4 LTS

mkdir /tmp/runbook-demo

o/p
created an empty directory
------------------------------------------------------------------------
cp /etc/hosts /tmp/runbook-demo/ 

o/p
copied host file to temp directory

### Command
ls -l /tmp/runbook-demo

o/p
total 4
------------------------------------------------------------------------
## CPU and Memory Check

### Command

free -h

ubuntu@ip-172-31-26-178:~$ free -h
               total        used        free      shared  buff/cache   available
Mem:           914Mi       409Mi       176Mi       2.8Mi       488Mi       504Mi
Swap:             0B          0B          0B
------------------------------------------------------------------------

### Command
htop 

o/p
process viewer, found ssh service pid.

ps 1272 - ps pid

o/p
   PID TTY      STAT   TIME COMMAND
   1272 ?        S      0:00 sshd: ubuntu@pts/0
------------------------------------------------------------------------
## Disk Check
### Command
df -h

o/p
Filesystem       Size  Used Avail Use% Mounted on
/dev/root        6.8G  4.4G  2.4G  65% /
tmpfs            458M     0  458M   0% /dev/shm
tmpfs            183M 1012K  182M   1% /run
tmpfs            5.0M     0  5.0M   0% /run/lock
efivarfs         128K  3.8K  120K   4% /sys/firmware/efi/efivars
/dev/nvme0n1p16  881M  155M  665M  19% /boot
/dev/nvme0n1p15  105M  6.2M   99M   6% /boot/efi
tmpfs             92M   12K   92M   1% /run/user/1000

du -sh  /var/log

o/p
309M    /var/log
------------------------------------------------------------------------
## Log Check
### Command

journalctl -u ssh -n 50

o/p
no errors found
------------------------------------------------------------------------

## If this worsens
1.  Restart SSH service
2.  Check SSH logs
3.  Verify security group and port 22 access
