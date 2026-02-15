# Day 10 Challenge

## Files Created
devops.txt
notes.txt
script.sh

## Permission Changes
chmod 444 devops.txt - made devops file as read-only for all user,group,others.

Tried editing file with read-only permission:
ubuntu@ip-172-31-26-178:~/practice$ echo "try to write this in notes" >> devops.txt
-bash: devops.txt: Permission denied

chmod 644 script.sh - changed permission for script.sh, made non executable for all.
Tried executing a file without execute permission:
ubuntu@ip-172-31-26-178:~/practice$ ./script.sh
-bash: ./script.sh: Permission denied

chmod 755 script.sh - changed permission for script.sh, made executable for user.
ubuntu@ip-172-31-26-178:~/practice$ ./script.sh
hello devops

## Commands Used
touch  devops.txt
echo "create a file with some content" >> notes.txt
vim script.sh
cd practice/
cat notes.txt
ls -l
chmod 755 script.sh
./script.sh
chmod 444 devops.txt
chmod 640 notes.txt
mkdir porjects
chmod 755 porjects/
mv porjects projects
ls -l
chmod 755 projects/
ls -l
echo "try to write this in notes" >> notes.txt
echo "try to write this in notes" >> devops.txt
chmod 644 script.sh
./script.sh

## What I Learned
- Created and read files using Linux commands.
- Explored the permission structure (read, write, execute).
- Modified permissions and observed how access changes.
- Made a script executable and tested how execution depends on permissions.
