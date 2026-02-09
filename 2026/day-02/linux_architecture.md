What is Linux?
- Linus is an open source operating system built by Linus Torvalds in 1991. It has a massive user base due to its security capabilties.
- OS is the software that manages the CP, memory and storage. The OS acts as a bridge between Hardware and appliation and makes the connection and do the work.


Architecture of Linux:
Every linux  based operating system has a linux kernel which manages the hardware resource and a set of packages that make up the rest of OS.

Linux Architecture:

Linux architecture is a layered structure of Linux OS that defines how its components - the kernel, the shell , system libraries and hardware interact with each other to manage the resoucres and execute user programs efficiently

Kernel - Heart of the Linux OS. core of the Linux O that manags hardware resources and controls communication between hardware and sofware.

Shell - Interface / CLI that allows user to communicate with the OS by entering the commands. Interprets and executes the user commands.
        Acts as a bridge between User actions and kernel processing.

System libraries - Provide essential functional that allows the application to interact with kernel without needing to access it directly.  Contains reusable pre-written code for common system operations. Acts as an interface between application and kernel.

Hardware Layer - CPU, RAM, Storage, Input/Output devices.

Process - a simple program that is currently running in he system whenever a command is executed in Linux, the OS creates a process to run that command.
        - Every process has a uniques number called Process ID (PID).


Proces states:
Running: The process is either running or ready to run. the CPU is allocated to a process. if not enough CPU is present then process is lined p to run.

sleeping state: the process is in sleeping state when it is waiting for a resource to be available.

Interruptible sleep(s): in this the process wait for a Input (event/signal) from a terminal by a user or another process. waiting for something to happen,sleep time, waiting on socket. it is safe to interrupt, 

UniInterruptible sleep(D): the process is inside the kernel paces,holding or depending on resources like Disk read/write, Network filesystem, Hardware operations, interupting couls corrupt data, crash the system, leave kernel locks inconsistent.

Stopped(T): A stopped process is a process that has been paused and is not running, but can resume later. when Ctrl+Z (suspends the process), kill -STOP processid. The process still exists, it has memory, resources and files ope, it can be resumed.

Zombie(Z): the process is terminated/dead but entries/information for the process is still present in the process table. when a process terminates it is the responsibility of the Parent process to clean up the child's process and information from the process table as well.


Systemd - the first process (PID = 1)  started by linux kernel, manages entire system. It initializes the system,manages services, and controls system resources during startup and runtime. example: systemctl status ssh , systemd know the pid, state, controls lifecycle.
systemd is needed to start, manage, and keep the system healthy automatically.