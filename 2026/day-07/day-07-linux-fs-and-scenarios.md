day-07-linux-fs-and-scenarios

## Write 1-2 lines explaining what it contains
## Run ls -l <directory> and note 1-2 files/folders you see

lrwxrwxrwx   1 root root     7 Apr 22  2024 bin -> usr/bin  --> contains Essential user command binaries (like ls, cp).
drwxr-xr-x   5 root root  4096 Feb 13 20:39 boot     --> contains Files needed to boot the system (kernel, bootloader)
drwxr-xr-x  16 root root  3300 Feb 15 11:35 dev      --> Device files representing hardware (e.g., disks, USB).
drwxr-xr-x 117 root root 12288 Feb 14 17:17 etc	     --> System configuration files.
drwxr-xr-x  15 root root  4096 Feb 14 04:41 home     --> User home directories.
lrwxrwxrwx   1 root root     7 Apr 22  2024 lib -> usr/lib --> Shared libraries needed by system programs.
drwxr-xr-x   2 root root  4096 Oct 22 09:59 media    --> Mount points for removable media (USB, CD).
drwxr-xr-x   2 root root  4096 Oct 22 09:59 mnt      --> Temporary mount points for filesystems.
drwxr-xr-x   5 root root  4096 Feb 14 17:18 opt	     --> Optional software packages.
dr-xr-xr-x 181 root root     0 Feb 15 11:34 proc     --> Virtual filesystem for process and kernel info
drwx------   4 root root  4096 Nov 11 17:55 root     --> Home directory for the root user.
drwxr-xr-x  32 root root  1080 Feb 15 12:17 run	     --> Runtime data for processes since boot.
lrwxrwxrwx   1 root root     8 Apr 22  2024 sbin -> usr/sbin --> System binaries for admin tasks.
drwxr-xr-x   2 root root  4096 Oct 22 09:59 srv	     --> Data for services (like web or FTP).
dr-xr-xr-x  13 root root     0 Feb 15 11:34 sys	     --> Kernel and system info (similar to /proc).
drwxrwxrwt  12 root root  4096 Feb 15 12:20 tmp	     --> Temporary files (cleared on reboot).
drwxr-xr-x  12 root root  4096 Oct 22 09:59 usr	     --> User programs, libraries, and documentation.
drwxr-xr-x  14 root root  4096 Nov 11 17:10 var      --> Variable data like logs, caches, spool files.

---------------------------------------------------------------------------------------------------------------
## Scenario 1: Service Not Starting

A web application service called 'myapp' failed to start after a server reboot.

Step 1: systemctl status myapp
Why: to check the service status if it is active/inactive or failed.

Step 2: journalctl -u myapp
Why: to see the logs of the service if there are any errors, helps to see config error, file permission issues,etc

Step 3:systemctl is-enabled myapp
Why: to check if the service is enabled at the system reboot

Step 4: systemctl restart myapp
Why: to restart the service after fixing the issue.

---------------------------------------------------------------------------------------------------------------
## Scenario 2: High CPU Usage

Your manager reports that the application server is slow.
You SSH into the server. What commands would you run to identify
which process is using high CPU?

- Use a command that shows live CPU usage
top - live process monitor to see real time updates of process, shows CPU/mem usages.
htop - interactive process viewer with real time updates on usage CPU/mem of processes, can sort the processes based on %CPU.
 
- Look for processes sorted by CPU percentage
ps aux --sort=-%cpu - to sort the process based on CPU usage

---------------------------------------------------------------------------------------------------------------
## Scenario 3: Finding Service Logs

A developer asks: "Where are the logs for the 'docker' service?"
The service is managed by systemd.
What commands would you use?

- Check service status first
systemctl status docker

- View last 50 lines of logs
journalctl -u docker -n 50

- Follow logs in real-time
journalctl -u docker -f
---------------------------------------------------------------------------------------------------------------
## Scenario 4: File Permissions Issue

A script at /home/user/backup.sh is not executing.
When you run it: ./backup.sh

Step 1: Check current permissions
Command: ls -l 
-rw-r--r-x 1 ubuntu ubuntu   20 Feb 15 11:45 script.sh

Step 2: Add execute permission
Command: chmod 755 script.sh
-rwxr-xr-x 1 ubuntu ubuntu   20 Feb 15 11:45 script.sh

Step 3: Verify it worked
Command: ./script.sh
o/p: hello devops


