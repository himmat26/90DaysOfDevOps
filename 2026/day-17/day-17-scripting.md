## Challenge Tasks

### Task 1: For Loop
1. Create `for_loop.sh` that:
   - Loops through a list of 5 fruits and prints each one
    -   for i in Mango banana guava kiwi
    -   do
    -     echo "Name of fruit is $i "
    -   done
2. Create `count.sh` that:
   - Prints numbers 1 to 10 using a for loop
    - for i in {1..10}
    - do
    -     echo "$i"
    - done

--------------------------------------------------------------------------

### Task 2: While Loop
1. Create `countdown.sh` that:
   - Takes a number from the user
   - Counts down to 0 using a while loop
   - Prints "Done!" at the end
   read -p "enter the number: " num
   -  while [ $num -gt 0 ]
   -  do
   -     echo "$num"
   -     ((num=num-1))
   -  done
   -  echo "Done!"

--------------------------------------------------------------------------

### Task 3: Command-Line Arguments
1. Create `greet.sh` that:
   - Accepts a name as `$1`
   - Prints `Hello, <name>!`
   - If no argument is passed, prints "Usage: ./greet.sh <name>"
2. Create `args_demo.sh` that:
   - Prints total number of arguments (`$#`)
   - Prints all arguments (`$@`)
   - Prints the script name (`$0`)
-------------
# Output:
* Scenario 1: 
    - ubuntu@ip-172-31-26-178:~/scripts$ ./greet1.sh Hawk Devops Challenge
    - Total number of arguments:  3
    - Arguments passed:  Hawk Devops Challenge
    - Script name is:  ./greet1.sh
    - Hello, Hawk!
* Scenario 2:
    - ubuntu@ip-172-31-26-178:~/scripts$ ./greet1.sh
    - Total number of arguments:  0
    - Arguments passed:
    - Script name is:  ./greet1.sh
    - Usage: ./greet.sh <name>

--------------------------------------------------------------------------

### Task 4: Install Packages via Script
1. Create `install_packages.sh` that:
   - Defines a list of packages: `nginx`, `curl`, `wget`
        - List=("nginx" "curl" "wget"  "docker.io"  "git")
   - Loops through the list
        - for i in ${List[@]}
   - Checks if each package is installed (use `dpkg -s` or `rpm -q`)
        - if dpkg -s $i  | grep "installed";then
                echo "Service $i is already installed"
   - Installs it if missing, skips if already present
        - else
                echo "Installing $i "
                apt-get update >/dev/null
                apt-get install $i >/dev/null
   - Prints status for each package
        - echo "Service $i is already installed"
        - echo "Installing $i "
> Run as root: `sudo -i` or `sudo su`


--------------------------------------------------------------------------

### Task 5: Error Handling
1. Create `safe_script.sh` that:
   - Uses `set -e` at the top (exit on error)
   - Tries to create a directory `/tmp/devops-test`
   - Tries to navigate into it
   - Creates a file inside
   - Uses `||` operator to print an error if any step fails

Example:
```bash
mkdir /tmp/devops-test || echo "Directory already exists"
```

2. Modify your `install_packages.sh` to check if the script is being run as root — exit with a message if not.
- set -e

- if [[ $EUID -ne 0 ]]; then
-     echo " Please run this script as root "
-     exit 1
- fi

---------------------
