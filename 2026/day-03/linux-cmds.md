##Process Management:

ps - process snapshot, report a snapshot of the current processes, no real time updates.
top - live process montior baisc, real time updates. to ue - quick sytem checks, CPU/memory usage, identify heavy process
htop - interactive process viewer / human-friendly top. interactive version of top.
atop - Advanced System & Process Monitor. Used in performance troubleshooting, I/O bottleneck analysis, Post incident analysis.
kill - It does not immediately destroy it. It sends a signal to process to stop.
kill -9 - force kill



##File system:

pwd - to check present working directory 
mkdir- to create a new directory
cd - change directory 
touch - to create an empty file
ls - to list all the files and directories
cp demo.txt 
mv - move file from path A to B, also used for renameing the files(if moved in same location).
rm - to delete a file permanently, -r to delete a directly and its files recursively.
rmdir - to delete/remove empty directories.
head - display first 10 lines in a file
head -n 2 - to display n number of lines of a file from top.
tail - to display last 10 lines in a file.
tail -n 4 - to display n number of lines of a file from bottom.
less filename - Read a file with forward and backward navigation. Often used with pipe, It is particularly useful for large files as it does not load the entire file into memory, but rather accesses it page by page. Pg up & pg dn to scroll.

##File permissions:

chmod 444 - to change the permissions for a file for user,group,others(rwx)
chown user file - to change the owner of the file
chgrp group file - to change the group of the file



##Networking:

ping - Tests network connectivity and packet loss to a host.
ifconfig - Displays network interface configuration
ip addr - new version of ifconfig
curl -  Sends HTTP request to test APIs or web services
nslookup - Checks DNS resolution of a domain.
traceroute - Shows the network path packets take to reach a host.
telnet - Check Network Connectivity to Specific Port

