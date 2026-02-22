## Challenge Tasks

### Task 1: Your First Script
1. Create a file `hello.sh`
   - vim hello.sh
2. Add the shebang line `#!/bin/bash` at the top
    - #!/bin/bash
3. Print `Hello, DevOps!` using `echo`
    - echo "Hello, Devops!"
4. Make it executable and run it
    - chmod +x hello.sh
    - ./hello.sh

**Document:** What happens if you remove the shebang line? 
    - There is no change in the output with/without shebang, this script is executing fine.

------------------------------------------------------------------------------------------------------

### Task 2: Variables
1. Create `variables.sh` with:
   - A variable for your `NAME`
      -  a="Himmat"
   - A variable for your `ROLE` (e.g., "DevOps Engineer")
      -  b="DevOps Engineer"
   - Print: `Hello, I am <NAME> and I am a <ROLE>`
      -  b="DevOps Engineer"
2. Try using single quotes vs double quotes — what's the difference?
    - Double quotes: I am Himmat a DevOps Engineer
    - Single quotes: I am $a a $b
    - ' - treat everything literally variable not expanded.
    - " - Variable expansion is allowed.
------------------------------------------------------------------------------------------------------

### Task 3: User Input with read
1. Create `greet.sh` that:
   - Asks the user for their name using `read`
     -    What is your name: Himmat
   - Asks for their favourite tool
      -   Which is your favourite Devops tool: Git
   - Prints: `Hello <name>, your favourite tool is <tool>`
      -   Hello Himmat, your favourite tool is Git

------------------------------------------------------------------------------------------------------

### Task 4: If-Else Conditions
1. Create `check_number.sh` that:
   - Takes a number using `read`
   - Prints whether it is **positive**, **negative**, or **zero**
      -  Enter the  number: -3
      -  number is negative
      -  Enter the  number: 0
      -  number is zero
      - Enter the  number: 3
      -  Number is positive

2. Create `file_check.sh` that:
   - Asks for a filename
      -  Please enter the file name: 
   - Checks if the file **exists** using `-f`
      - if [[ -f $filename ]]; then
   - Prints appropriate message
      -  Please enter the file name: greet.sh
      -  File exists
      -  Please enter the file name: hael.sh
      -  File does not exist

------------------------------------------------------------------------------------------------------

### Task 5: Combine It All
Create `server_check.sh` that:
1. Stores a service name in a variable (e.g., `nginx`, `sshd`)
    - read -p " Enter the Name of a service: " service
2. Asks the user: "Do you want to check the status? (y/n)"
    - read -p " Do you want to check the status? (y/n) " option
3. If `y` — runs `systemctl status <service>` and prints whether it's **active** or **not**
    - if [[ $option == "y" ]];then
        if systemctl status $service | grep  "Active: active";then
                echo "Service $service is active and running"
        elif  systemctl status $service | grep  "Active: inactive";then
                echo "Service $service is inactive"
        fi
4. If `n` — prints "Skipped."
    elif [[ $option == "n" ]];then
        echo "Skipped."
    else
        echo "please select a valid option"
    fi
    # Output1:
     Enter the Name of a service: nginx
     Do you want to check the status? (y/n) y
     Service nginx is inactive
    # Output2:
     Enter the Name of a service: ssh
     Do you want to check the status? (y/n) n
     Skipped.

------------------------------------------------------------------------------------------------------
