# 🐚 Shell Scripting, File Systems & Storage: From Zero to Hero

---

## 📚 Table of Contents

1. [The Shell — What It Is and Why It Matters](#1-the-shell--what-it-is-and-why-it-matters)
2. [Navigating the File System](#2-navigating-the-file-system)
3. [Working with Files and Directories](#3-working-with-files-and-directories)
4. [Permissions — The Guardian System](#4-permissions--the-guardian-system)
5. [Redirection, Pipes & Filters](#5-redirection-pipes--filters)
6. [Variables, Environment & Shell Configuration](#6-variables-environment--shell-configuration)
7. [Control Flow — Conditions and Loops](#7-control-flow--conditions-and-loops)
8. [Functions — Writing Reusable Code](#8-functions--writing-reusable-code)
9. [Text Processing — grep, sed, awk](#9-text-processing--grep-sed-awk)
10. [File System Deep Dive — Inodes, Links & Mounts](#10-file-system-deep-dive--inodes-links--mounts)
11. [Storage — Disks, Partitions & Volumes](#11-storage--disks-partitions--volumes)
12. [Process Management](#12-process-management)
13. [Scheduling — Cron & at](#13-scheduling--cron--at)
14. [Putting It All Together — Real World Scripts](#14-putting-it-all-together--real-world-scripts)
15. [Exercises with Full Answers](#15-exercises-with-full-answers)

---

## 1. The Shell — What It Is and Why It Matters

### What is a Shell?

A **shell** is a program that acts as a bridge between you (the user) and the operating system kernel. When we type a command, the shell interprets it and tells the kernel what to do. The kernel then speaks directly to the hardware.

```
You → Shell → Kernel → Hardware
```

Think of the shell as a very smart interpreter. It reads what we type, figures out what program to run, finds it on disk, executes it, and shows us the result.

### Common Shells

| Shell | Full Name | Notes |
|-------|-----------|-------|
| `sh` | Bourne Shell | The original UNIX shell, very portable |
| `bash` | Bourne Again Shell | The most common default on Linux |
| `zsh` | Z Shell | Default on macOS since Catalina, very powerful |
| `fish` | Friendly Interactive Shell | User-friendly, but less POSIX compliant |
| `dash` | Debian Almquist Shell | Lightweight, often used for system scripts |

For this guide, we'll use **bash** — it's universally available and the most widely used in production environments.

### Checking Your Shell

```bash
# Find out which shell we are currently using
echo $SHELL

# Find out the version
bash --version
```

### Our First Commands

```bash
# Print something to the screen
echo "Hello, World!"

# Find out who we are logged in as
whoami

# Find out where we are in the file system
pwd

# Find out what date and time it is
date
```

> 💡 **Pro Tip:** Every command we type is actually an executable file somewhere on the system. We can find where with `which`:
> ```bash
> which echo
> which bash
> ```

---

## 2. Navigating the File System

### The Linux File System Tree

Unlike Windows which uses drive letters (C:\, D:\), Linux has a single unified tree starting from `/` — called the **root**.

```
/
├── bin/        → Essential binaries (ls, cp, mv...)
├── etc/        → Configuration files
├── home/       → User home directories
│   └── alice/
├── var/        → Variable data (logs, databases...)
├── tmp/        → Temporary files (cleared on reboot)
├── usr/        → User programs and utilities
├── proc/       → Virtual filesystem for process info
├── dev/        → Device files
├── mnt/        → Mount points for external drives
└── opt/        → Optional/third-party software
```

### Absolute vs. Relative Paths

An **absolute path** starts from the root `/` and gives the full location:
```bash
/home/alice/documents/report.txt
```

A **relative path** starts from where we currently are:
```bash
documents/report.txt       # if we are already in /home/alice
../bob/notes.txt           # go up one level, then into bob's folder
```

### Special Path Symbols

| Symbol | Meaning |
|--------|---------|
| `.` | Current directory |
| `..` | Parent directory |
| `~` | Home directory of current user |
| `-` | Previous directory (used with `cd`) |

### Essential Navigation Commands

```bash
# Print Working Directory — where are we?
pwd

# Change Directory
cd /var/log              # go to an absolute path
cd documents             # go to a relative path
cd ..                    # go up one level
cd ~                     # go to home directory
cd -                     # go back to previous directory

# List files and directories
ls                       # simple list
ls -l                    # long format (permissions, size, date)
ls -a                    # show hidden files (starting with .)
ls -lh                   # long format with human-readable sizes
ls -lt                   # sort by modification time (newest first)
ls -lR                   # list recursively through all subdirectories

# Find out type of a file
file report.txt
file /bin/bash
```

### Understanding `ls -l` Output

```bash
$ ls -l
-rw-r--r-- 1 alice staff  4096 Mar 15 09:30 report.txt
drwxr-xr-x 3 alice staff   128 Mar 14 14:00 documents/
```

Let's decode this line by line:

```
-rw-r--r--   1   alice   staff   4096   Mar 15 09:30   report.txt
│            │   │       │       │      │              │
│            │   │       │       │      │              └─ Filename
│            │   │       │       │      └─ Modification time
│            │   │       │       └─ File size in bytes
│            │   │       └─ Group owner
│            │   └─ User owner
│            └─ Number of hard links
└─ File type + permissions (10 characters)
```

---

## 3. Working with Files and Directories

### Creating Files and Directories

```bash
# Create an empty file (or update timestamp if it exists)
touch myfile.txt
touch file1.txt file2.txt file3.txt   # create multiple at once

# Create a directory
mkdir mydir
mkdir -p projects/webapp/src          # create nested directories at once

# Create a file with content using echo
echo "Hello World" > greeting.txt

# Create a multi-line file using cat with a heredoc
cat > config.txt << EOF
server=localhost
port=8080
debug=false
EOF
```

### Copying, Moving, Renaming

```bash
# Copy a file
cp source.txt destination.txt
cp -r source_dir/ destination_dir/      # copy directory recursively
cp -p file.txt backup.txt               # preserve permissions and timestamps

# Move or rename a file
mv oldname.txt newname.txt              # rename a file
mv file.txt /home/alice/documents/      # move a file to another location
mv -i file.txt existing.txt            # prompt before overwriting

# Remove files and directories
rm file.txt                             # delete a file
rm -i file.txt                          # ask for confirmation first
rm -r mydir/                            # remove directory and contents
rm -rf mydir/                           # force remove (no prompts) — use carefully!
```

> ⚠️ **Warning:** `rm -rf` is permanent. Unlike the GUI trash, there is no recovery. We always double-check our path before running it. Many a production disaster has started with this command.

### Viewing File Contents

```bash
# Print entire file to screen
cat file.txt

# Print with line numbers
cat -n file.txt

# View one screen at a time (press q to quit, space for next page)
less file.txt
more file.txt

# View only the first/last N lines
head -n 20 file.txt          # first 20 lines
tail -n 20 file.txt          # last 20 lines
tail -f /var/log/syslog      # follow a file in real time (great for logs!)

# Count lines, words, and characters
wc file.txt
wc -l file.txt               # count lines only
wc -w file.txt               # count words only
```

### Finding Files

```bash
# Find by name
find /home -name "*.txt"
find . -name "config.json"                   # search in current directory

# Find by type
find /var -type f -name "*.log"              # files only
find /home -type d -name "projects"          # directories only

# Find by size
find / -size +100M                           # files larger than 100MB
find /tmp -size -1k                          # files smaller than 1KB

# Find by modification time
find /var/log -mtime -7                      # modified in last 7 days
find /tmp -mtime +30                         # not modified in 30+ days

# Find and execute a command on results
find /tmp -name "*.tmp" -exec rm {} \;       # find and delete temp files
find . -name "*.txt" -exec wc -l {} \;      # count lines in all .txt files
```

---

## 4. Permissions — The Guardian System

### Understanding the Permission Model

Linux uses a three-level permission system:

- **Owner (u)** — the user who owns the file
- **Group (g)** — a group of users
- **Others (o)** — everyone else

Each level has three permission bits:

| Symbol | Meaning for Files | Meaning for Directories |
|--------|-------------------|------------------------|
| `r` (4) | Read the file | List directory contents |
| `w` (2) | Write/modify the file | Create/delete files inside |
| `x` (1) | Execute the file | Enter the directory (`cd`) |
| `-` (0) | No permission | No permission |

### Reading Permission Strings

```
-rwxr-xr--
│└──┴──┴──
│ u  g  o
└─ file type (- = file, d = directory, l = symlink)
```

So `-rwxr-xr--` means:
- Owner: read, write, execute
- Group: read, execute
- Others: read only

### Changing Permissions with `chmod`

We can use **symbolic** or **octal (numeric)** notation:

**Symbolic:**
```bash
chmod u+x script.sh             # add execute for owner
chmod g-w file.txt              # remove write from group
chmod o+r file.txt              # add read for others
chmod a+x script.sh             # add execute for all (a = all)
chmod u=rwx,g=rx,o= file.txt    # set exact permissions
```

**Octal (our preferred for scripts):**
```bash
# Each permission: r=4, w=2, x=1
# Add the values for each level

chmod 755 script.sh    # rwxr-xr-x  (owner=7, group=5, others=5)
chmod 644 file.txt     # rw-r--r--  (owner=6, group=4, others=4)
chmod 600 secret.txt   # rw-------  (owner=6, group=0, others=0)
chmod 777 shared/      # rwxrwxrwx  (everyone can do everything — rarely recommended)
```

> 💡 **Memorize these common octal values:**
> - `755` → Standard for executables and directories
> - `644` → Standard for regular files
> - `600` → Private files (SSH keys, config with passwords)
> - `700` → Private scripts

### Changing Ownership

```bash
# Change file owner
chown alice file.txt

# Change owner and group
chown alice:developers file.txt

# Change group only
chgrp developers file.txt
```

### Special Permissions

```bash
# SetUID — run as file owner, not as current user
chmod u+s /usr/bin/passwd   # this is why passwd can change /etc/shadow

# SetGID — run as group of the file
chmod g+s shared_dir/       # files created inside inherit parent's group

# Sticky Bit — only owner can delete their files
chmod +t /tmp               # this is why users can't delete each other's temp files

# Combined with octal (4=SUID, 2=SGID, 1=Sticky)
chmod 4755 program          # SUID set
chmod 1777 /tmp             # Sticky bit on /tmp
```

---

## 5. Redirection, Pipes & Filters

This is where the shell's true power begins to show. We can chain commands together like LEGO bricks.

### Standard Streams

Every process has three standard streams:

| Stream | Name | Default | File Descriptor |
|--------|------|---------|-----------------|
| stdin | Standard Input | Keyboard | 0 |
| stdout | Standard Output | Screen | 1 |
| stderr | Standard Error | Screen | 2 |

### Output Redirection

```bash
# Redirect stdout to a file (overwrite)
echo "Hello" > output.txt
ls -l > filelist.txt

# Redirect stdout to a file (append)
echo "Line 2" >> output.txt
date >> logfile.txt

# Redirect stderr to a file
find / -name "secret" 2> errors.txt

# Redirect both stdout and stderr to same file
command > all_output.txt 2>&1
command &> all_output.txt       # shorthand (bash only)

# Discard output entirely (send to the void)
command > /dev/null 2>&1
```

### Input Redirection

```bash
# Use file content as input
sort < unsorted.txt
wc -l < myfile.txt

# Here Document — inline multiline input
cat << EOF
Line 1
Line 2
Line 3
EOF

# Here String — single line input
grep "pattern" <<< "this is the string to search"
```

### Pipes — The True Power Move

A pipe `|` sends the **stdout** of one command as **stdin** to the next command. We can chain as many as we need.

```bash
# Count how many lines are in a file matching a pattern
grep "ERROR" application.log | wc -l

# List files, sort them, and show only unique entries
ls -1 | sort | uniq

# Find the 5 largest files in current directory
ls -lS | head -6

# Find all running processes by a specific user
ps aux | grep alice

# Show disk usage sorted by size
du -sh * | sort -rh | head -10

# See real-time updates from a log file with filtering
tail -f /var/log/syslog | grep "WARN\|ERROR"
```
---

## 6. Variables, Environment & Shell Configuration

### Defining and Using Variables

```bash
# Define a variable (no spaces around =)
name="Alice"
count=42
pi=3.14

# Access a variable with $
echo "Hello, $name"
echo "The count is ${count}"    # braces recommended to avoid ambiguity

# Curly braces are important for string concatenation
prefix="pre"
echo "${prefix}fix"             # prints: prefix
echo "$prefixfix"               # ERROR: looks for variable "prefixfix"

# Command substitution — store command output in a variable
today=$(date +%Y-%m-%d)
current_user=$(whoami)
file_count=$(ls | wc -l)

echo "Today is $today"
echo "There are $file_count files here"
```

### Variable Types and Scope

```bash
# Read-only variable
readonly MAX_SIZE=100
MAX_SIZE=200     # this will error

# Arrays
fruits=("apple" "banana" "cherry")
echo ${fruits[0]}           # apple (0-indexed)
echo ${fruits[@]}           # all elements
echo ${#fruits[@]}          # length of array

# Add to array
fruits+=("date")

# Associative arrays (bash 4+)
declare -A colors
colors["red"]="#FF0000"
colors["green"]="#00FF00"
echo ${colors["red"]}

# Exporting variables to child processes
export DATABASE_URL="postgres://localhost/mydb"
# Now any script this script calls can also access DATABASE_URL

# Local variables inside functions (more on this later)
my_function() {
    local temp_var="I am local"
}
```

### Special Variables

```bash
$0          # name of the script itself
$1, $2...   # positional parameters (arguments passed to script)
$@          # all arguments as separate words
$*          # all arguments as one string
$#          # number of arguments
$?          # exit code of last command (0=success, non-zero=error)
$$          # PID of current shell
$!          # PID of last background process
$RANDOM     # random number between 0 and 32767
$LINENO     # current line number in script
```

```bash
# Practical example
echo "Script name: $0"
echo "First argument: $1"
echo "All arguments: $@"
echo "Number of arguments: $#"

ls /nonexistent 2>/dev/null
echo "Exit code was: $?"       # will print non-zero
```

### Environment Variables

```bash
# View all environment variables
env
printenv

# View a specific one
echo $HOME
echo $PATH
echo $USER

# Important ones to know:
# $HOME    → current user's home directory
# $PATH    → colon-separated list of directories to search for programs
# $USER    → current username
# $SHELL   → current shell
# $PWD     → current working directory
# $EDITOR  → default text editor
# $LANG    → current locale setting
```

### Shell Configuration Files

These files are loaded automatically when a shell starts:

| File | When Loaded |
|------|------------|
| `/etc/profile` | System-wide, login shells |
| `~/.bash_profile` | User-specific, login shells |
| `~/.bashrc` | User-specific, interactive non-login shells |
| `~/.bash_logout` | When a login shell exits |
| `~/.profile` | POSIX-compatible alternative |

```bash
# Reload configuration without logging out
source ~/.bashrc
# or the shorthand:
. ~/.bashrc

# Add to PATH permanently (add to ~/.bashrc)
export PATH="$HOME/.local/bin:$PATH"

# Create an alias
alias ll='ls -alF'
alias grep='grep --color=auto'
alias ..='cd ..'

# Useful prompt customization (PS1)
# This gives us: user@host:/current/dir$
export PS1='\u@\h:\w\$ '
```

---

## 7. Control Flow — Conditions and Loops

### The `if` Statement

```bash
# Basic syntax
if [ condition ]; then
    # commands
elif [ other_condition ]; then
    # commands
else
    # commands
fi
```

### Test Conditions

```bash
# File tests
[ -f file.txt ]       # true if file exists and is a regular file
[ -d mydir ]          # true if directory exists
[ -e path ]           # true if path exists (file or dir)
[ -r file ]           # true if readable
[ -w file ]           # true if writable
[ -x file ]           # true if executable
[ -s file ]           # true if file is non-empty
[ -L file ]           # true if symbolic link

# String tests
[ -z "$str" ]         # true if string is empty
[ -n "$str" ]         # true if string is NOT empty
[ "$a" = "$b" ]       # true if strings are equal
[ "$a" != "$b" ]      # true if strings are NOT equal

# Numeric tests
[ $x -eq $y ]         # equal
[ $x -ne $y ]         # not equal
[ $x -lt $y ]         # less than
[ $x -le $y ]         # less than or equal
[ $x -gt $y ]         # greater than
[ $x -ge $y ]         # greater than or equal

# Logical operators
[ $a -gt 0 ] && [ $a -lt 100 ]   # AND
[ $a -lt 0 ] || [ $a -gt 100 ]   # OR
[ ! -f file.txt ]                  # NOT
```

> 💡 **`[[ ]]` vs `[ ]`:** We prefer `[[ ]]` in bash scripts — it's more powerful, handles empty variables more safely, and supports `&&` and `||` directly:
> ```bash
> if [[ -f file.txt && -r file.txt ]]; then
>     echo "File exists and is readable"
> fi
> ```

### Practical `if` Examples

```bash
#!/bin/bash

# Check if a file exists before reading it
if [[ -f "$1" ]]; then
    echo "Reading file: $1"
    cat "$1"
else
    echo "Error: File '$1' not found"
    exit 1
fi

# Check if running as root
if [[ $EUID -ne 0 ]]; then
    echo "This script must be run as root"
    exit 1
fi

# Check if a command succeeded
if cp source.txt backup.txt; then
    echo "Backup created successfully"
else
    echo "Backup failed!"
fi
```

### The `case` Statement — Cleaner Multi-branch Logic

```bash
#!/bin/bash

day=$(date +%A)    # Monday, Tuesday, etc.

case "$day" in
    Monday)
        echo "Start of the work week, let's go!"
        ;;
    Friday)
        echo "Almost the weekend!"
        ;;
    Saturday|Sunday)
        echo "It's the weekend!"
        ;;
    *)
        echo "Just another weekday."
        ;;
esac
```

### Loops

#### `for` Loop

```bash
# Loop over a list
for color in red green blue; do
    echo "Color: $color"
done

# Loop over files
for file in *.txt; do
    echo "Processing: $file"
    wc -l "$file"
done

# C-style for loop
for ((i = 1; i <= 10; i++)); do
    echo "Number: $i"
done

# Loop over a range
for i in {1..10}; do
    echo "Count: $i"
done

# Loop with step
for i in {0..20..5}; do    # 0, 5, 10, 15, 20
    echo "$i"
done

# Loop over command output
for user in $(cut -d: -f1 /etc/passwd); do
    echo "User: $user"
done
```

#### `while` Loop

```bash
# Basic while loop
count=1
while [[ $count -le 5 ]]; do
    echo "Count: $count"
    ((count++))
done

# Read a file line by line (the RIGHT way)
while IFS= read -r line; do
    echo "Line: $line"
done < myfile.txt

# Infinite loop with break
while true; do
    read -p "Enter 'quit' to exit: " input
    if [[ "$input" == "quit" ]]; then
        break
    fi
    echo "You said: $input"
done
```

#### `until` Loop

```bash
# Opposite of while — loop UNTIL condition becomes true
count=1
until [[ $count -gt 5 ]]; do
    echo "Count: $count"
    ((count++))
done
```

#### Loop Control

```bash
for i in {1..10}; do
    if [[ $i -eq 3 ]]; then
        continue    # skip iteration 3, jump to next
    fi
    if [[ $i -eq 7 ]]; then
        break       # stop the loop entirely at 7
    fi
    echo "$i"
done
# Output: 1 2 4 5 6
```

---

## 8. Functions — Writing Reusable Code

Functions are one of the most powerful features of shell scripting. They allow us to write modular, maintainable, and reusable code.

### Defining and Calling Functions

```bash
# Definition — must come BEFORE the call
greet() {
    echo "Hello, $1!"
}

# Call the function
greet "World"
greet "Alice"
```

### Arguments and Return Values

```bash
#!/bin/bash

# Functions receive arguments via $1, $2, etc.
add_numbers() {
    local result=$(( $1 + $2 ))
    echo $result     # "return" value via stdout
}

# Capture function output
sum=$(add_numbers 5 3)
echo "Sum: $sum"

# Use return for exit codes (0-255 only)
is_file_empty() {
    if [[ ! -s "$1" ]]; then
        return 0     # true — file is empty
    else
        return 1     # false — file has content
    fi
}

if is_file_empty myfile.txt; then
    echo "The file is empty"
fi
```

### Local Variables

```bash
#!/bin/bash

# Variables inside functions should be LOCAL to avoid polluting global scope
calculate() {
    local x=$1
    local y=$2
    local result=$(( x * y ))
    echo "$result"
}

# This x won't affect any x outside the function
x="I am global"
calculate 5 6
echo "x is still: $x"    # prints: I am global
```

### A Real-World Function Library

```bash
#!/bin/bash
# utils.sh — a library of helper functions

# Logging functions
log_info()    { echo "[INFO]    $(date '+%Y-%m-%d %H:%M:%S') - $*"; }
log_warning() { echo "[WARNING] $(date '+%Y-%m-%d %H:%M:%S') - $*" >&2; }
log_error()   { echo "[ERROR]   $(date '+%Y-%m-%d %H:%M:%S') - $*" >&2; }

# Check if a command exists
command_exists() {
    command -v "$1" &>/dev/null
}

# Require root
require_root() {
    if [[ $EUID -ne 0 ]]; then
        log_error "This script requires root privileges."
        exit 1
    fi
}

# Prompt user for yes/no
confirm() {
    local prompt="${1:-Are you sure?}"
    read -rp "$prompt [y/N]: " response
    [[ "$response" =~ ^[Yy]$ ]]
}

# Usage:
# source utils.sh
# log_info "Starting process..."
# if confirm "Delete all files?"; then rm -rf *; fi
```

---

## 9. Text Processing — grep, sed, awk

These three tools form the holy trinity of text processing. Together, they can transform any text data.

### `grep` — Search & Filter

```bash
# Basic search
grep "error" logfile.txt

# Case-insensitive search
grep -i "ERROR" logfile.txt

# Show line numbers
grep -n "error" logfile.txt

# Invert match (lines that DON'T match)
grep -v "debug" logfile.txt

# Count matches
grep -c "error" logfile.txt

# Search recursively through directories
grep -r "TODO" src/

# Show surrounding context
grep -A 3 "ERROR" logfile.txt    # 3 lines After
grep -B 3 "ERROR" logfile.txt    # 3 lines Before
grep -C 3 "ERROR" logfile.txt    # 3 lines Context (both)

# Extended regex (use | for OR, + for one-or-more)
grep -E "ERROR|FATAL|CRITICAL" logfile.txt
grep -E "[0-9]{3}-[0-9]{4}" contacts.txt    # phone numbers

# Only show the matching part, not the whole line
grep -o "ERROR.*" logfile.txt

# Search for whole word only
grep -w "log" file.txt    # won't match "logger" or "catalog"
```

### `sed` — Stream Editor (Find, Replace, Transform)

```bash
# Substitute (replace) — basic syntax
sed 's/old/new/' file.txt              # first match per line
sed 's/old/new/g' file.txt             # global (all matches per line)
sed 's/old/new/i' file.txt             # case-insensitive
sed 's/old/new/2' file.txt             # only 2nd match on each line

# Edit file in place
sed -i 's/localhost/production.server.com/g' config.txt
sed -i.bak 's/old/new/g' file.txt      # edit in place, keep .bak backup

# Delete lines
sed '/pattern/d' file.txt              # delete lines matching pattern
sed '5d' file.txt                      # delete line 5
sed '5,10d' file.txt                   # delete lines 5 through 10

# Print specific lines
sed -n '5p' file.txt                   # print only line 5
sed -n '5,10p' file.txt               # print lines 5 through 10
sed -n '/start/,/end/p' file.txt       # print between patterns

# Insert and append
sed '5i\New line inserted before line 5' file.txt
sed '5a\New line appended after line 5' file.txt

# Remove blank lines
sed '/^$/d' file.txt

# Remove leading/trailing whitespace
sed 's/^[[:space:]]*//' file.txt    # leading
sed 's/[[:space:]]*$//' file.txt    # trailing
```

### `awk` — Data Extraction & Reporting

`awk` treats each line as a record and splits it into fields. By default, fields are separated by whitespace and numbered `$1`, `$2`, etc. (`$0` is the whole line).

```bash
# Print specific fields (like columns)
awk '{print $1}' file.txt              # first field of each line
awk '{print $1, $3}' file.txt          # first and third fields
awk '{print NR, $0}' file.txt          # print line number, then whole line

# Use custom field separator
awk -F: '{print $1}' /etc/passwd       # : separated — prints usernames
awk -F',' '{print $2}' data.csv        # comma-separated — prints column 2

# Built-in variables
# NR = current record (line) number
# NF = number of fields in current record
# FS = field separator (default: whitespace)
# OFS = output field separator

# Filter rows by pattern
awk '/ERROR/ {print}' logfile.txt
awk '$3 > 100 {print $1, $3}' data.txt   # where field 3 is greater than 100

# Compute totals
awk '{sum += $3} END {print "Total:", sum}' sales.csv

# Print first and last fields
awk '{print $1, $NF}' file.txt

# Multi-line awk with BEGIN and END blocks
awk '
BEGIN {
    print "--- Report Start ---"
    total = 0
}
{
    total += $2
    print $1, $2
}
END {
    print "--- Total:", total, "---"
}
' expenses.txt
```

### Combining All Three — A Real Pipeline

```bash
# From an Apache access log, find the top 10 IPs causing 404 errors
grep " 404 " access.log \
    | awk '{print $1}' \
    | sort \
    | uniq -c \
    | sort -rn \
    | head -10

# Output looks like:
#    245 192.168.1.105
#    178 10.0.0.44
#    ...
```

---

## 10. File System Deep Dive — Inodes, Links & Mounts

### What is an Inode?

Every file on a Linux file system has an **inode** — a data structure that stores metadata about the file. The inode contains:

- File permissions
- Owner and group
- File size
- Timestamps (created, modified, accessed)
- Pointers to the actual data blocks on disk

What the inode does **NOT** contain: the filename. The filename lives in the directory, which maps names → inode numbers.

```bash
# View inode numbers
ls -i file.txt

# View inode details
stat file.txt

# Check inode usage on a filesystem
df -i
```

### Hard Links vs. Symbolic (Soft) Links

**Hard Links** — multiple directory entries pointing to the same inode:

```bash
# Create a hard link
ln original.txt hardlink.txt

# Both point to the same inode — same data, same permissions
ls -li original.txt hardlink.txt    # notice same inode number!

# If we delete the original, the hard link still works
rm original.txt
cat hardlink.txt    # still works!

# Limitations of hard links:
# - Cannot cross filesystem boundaries
# - Cannot link to directories (generally)
```

**Symbolic (Soft) Links** — a pointer that stores a path string:

```bash
# Create a symbolic link
ln -s /path/to/original.txt symlink.txt
ln -s /var/www/html webroot       # commonly used for directories

# List symbolic links
ls -la   # symlinks shown with -> pointing to target

# Check where a symlink points
readlink symlink.txt
readlink -f symlink.txt    # resolve to absolute path

# If the original is deleted, the symlink becomes "dangling" (broken)
```

| Feature | Hard Link | Symbolic Link |
|---------|-----------|---------------|
| Cross filesystem | ❌ | ✅ |
| Link to directory | ❌ | ✅ |
| Shows as link in `ls -l` | ❌ | ✅ |
| Survives deletion of target | ✅ | ❌ |

### Mounting File Systems

"Mounting" means attaching a storage device to a directory in the file system tree. Without mounting, we can't access the device's contents.

```bash
# List all currently mounted filesystems
mount
mount | column -t           # formatted nicely

# View filesystem disk usage
df -h                       # human readable sizes
df -hT                      # also show filesystem type

# Mount a device manually
sudo mount /dev/sdb1 /mnt/usb

# Mount with specific filesystem type
sudo mount -t ext4 /dev/sdb1 /mnt/data
sudo mount -t vfat /dev/sdc1 /mnt/usb   # FAT32 USB drive

# Unmount a device
sudo umount /mnt/usb
sudo umount /dev/sdb1     # either path works

# The /etc/fstab file — auto-mount on boot
cat /etc/fstab

# Test fstab without rebooting
sudo mount -a
```

### The `/proc` and `/sys` Virtual Filesystems

```bash
# /proc — information about running processes and system
cat /proc/cpuinfo           # CPU details
cat /proc/meminfo           # Memory details
cat /proc/uptime            # How long system has been running
ls /proc/1234/              # information about process 1234
cat /proc/mounts            # currently mounted filesystems

# /sys — kernel and hardware information
ls /sys/class/net/          # network interfaces
cat /sys/class/net/eth0/operstate  # is eth0 up or down?
```

---

## 11. Storage — Disks, Partitions & Volumes

### Understanding Block Devices

```bash
# List block devices
lsblk
lsblk -f    # also shows filesystem type and mount point

# List with full info using fdisk
sudo fdisk -l

# Typical output of lsblk:
# NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
# sda      8:0    0   20G  0 disk
# ├─sda1   8:1    0   19G  0 part /
# └─sda2   8:2    0    1G  0 part [SWAP]
# sdb      8:16   1   16G  0 disk
# └─sdb1   8:17   1   16G  0 part /mnt/usb
```

### Partitioning with `fdisk`

```bash
# Start fdisk for a disk (USE WITH CARE — this can destroy data)
sudo fdisk /dev/sdb

# Inside fdisk, press:
# p   → print current partition table
# n   → create new partition
# d   → delete a partition
# t   → change partition type
# w   → write changes and exit
# q   → quit WITHOUT saving
```

### Creating File Systems

```bash
# Format a partition with ext4 (most common Linux filesystem)
sudo mkfs.ext4 /dev/sdb1
sudo mkfs.ext4 -L "MyData" /dev/sdb1    # with a label

# Format with xfs (great for large files)
sudo mkfs.xfs /dev/sdb1

# Format with FAT32 (for cross-platform USB drives)
sudo mkfs.vfat -F32 /dev/sdb1

# Check and repair a filesystem (must be unmounted!)
sudo fsck /dev/sdb1
sudo fsck -y /dev/sdb1     # automatically answer yes to fixes
```

### Disk Usage Analysis

```bash
# See how much space is used/free
df -h                       # by filesystem
df -h /home                 # for a specific directory

# See how much space files and directories take
du -sh /var/log             # total size of /var/log
du -sh *                    # size of everything in current dir
du -sh * | sort -rh          # sorted by size (largest first)
du -h --max-depth=1 /var    # one level deep

# Find the biggest files on the system
find / -type f -printf '%s %p\n' 2>/dev/null | sort -rn | head -20
```

### Swap Space

Swap is disk space used as overflow when RAM is full.

```bash
# Check current swap
swapon --show
free -h

# Create a swap file
sudo dd if=/dev/zero of=/swapfile bs=1G count=2    # create 2GB file
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# Make it permanent (add to /etc/fstab)
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

### LVM — Logical Volume Manager

LVM adds a layer of abstraction over physical disks, making it easy to resize volumes without downtime.

```bash
# The LVM hierarchy:
# Physical Volumes (PV) → Volume Groups (VG) → Logical Volumes (LV)

# Create Physical Volume
sudo pvcreate /dev/sdb /dev/sdc
pvdisplay

# Create Volume Group
sudo vgcreate my_vg /dev/sdb /dev/sdc
vgdisplay

# Create Logical Volume
sudo lvcreate -L 10G -n data_lv my_vg       # 10GB
sudo lvcreate -l 100%FREE -n data_lv my_vg  # use all free space

# Format and mount
sudo mkfs.ext4 /dev/my_vg/data_lv
sudo mount /dev/my_vg/data_lv /mnt/data

# Extend a Logical Volume (while mounted with ext4!)
sudo lvextend -L +5G /dev/my_vg/data_lv
sudo resize2fs /dev/my_vg/data_lv
```

---

## 12. Process Management

### Viewing Processes

```bash
# Snapshot of all processes
ps aux
ps aux | grep nginx            # find nginx processes

# Interactive process viewer (press q to quit)
top
htop    # better version (may need to install)

# Process tree
pstree
pstree -p    # with PIDs

# Find PID of a process by name
pgrep nginx
pidof nginx
```

### Controlling Processes

```bash
# Run a command in the background
long_running_command &

# See background jobs
jobs

# Bring a background job to foreground
fg %1     # job number 1

# Send a foreground job to background
# (First press Ctrl+Z to pause it)
bg %1

# Keep a process running after logout
nohup long_running_command &
nohup long_running_command > output.log 2>&1 &
```

### Signals

```bash
# Send signals with kill
kill PID              # sends SIGTERM (graceful shutdown request)
kill -9 PID           # sends SIGKILL (force kill — cannot be caught)
kill -HUP PID         # sends SIGHUP (reload config)

# Kill by name
pkill nginx
killall nginx

# Common signals:
# SIGTERM (15) — please stop gracefully
# SIGKILL  (9) — stop immediately (cannot be ignored)
# SIGHUP   (1) — hangup / reload
# SIGINT   (2) — interrupt (same as Ctrl+C)
# SIGSTOP (19) — pause (same as Ctrl+Z)
# SIGCONT (18) — continue (resume)

# Kill all processes matching a pattern
pkill -f "python worker"
```

### Process Priority — `nice` and `renice`

Priority ranges from -20 (highest priority) to 19 (lowest priority). Default is 0.

```bash
# Start a process with lower priority
nice -n 10 backup_script.sh

# Start with higher priority (requires root)
sudo nice -n -5 critical_process

# Change priority of a running process
renice -n 10 -p 1234       # lower priority of PID 1234
renice -n -5 -p 1234       # raise priority (requires root)
```

---

## 13. Scheduling — Cron & at

### `cron` — Recurring Scheduled Tasks

Cron runs tasks on a schedule defined in a **crontab** file.

```bash
# Edit current user's crontab
crontab -e

# List current user's crontab
crontab -l

# Remove current user's crontab
crontab -r

# Edit another user's crontab (as root)
sudo crontab -u alice -e
```

**Crontab Format:**

```
┌───────────── minute (0 - 59)
│ ┌───────────── hour (0 - 23)
│ │ ┌───────────── day of month (1 - 31)
│ │ │ ┌───────────── month (1 - 12)
│ │ │ │ ┌───────────── day of week (0 - 6, Sunday=0)
│ │ │ │ │
* * * * *  command_to_run
```

```bash
# Examples:

# Run every minute
* * * * * /path/to/script.sh

# Run at 2:30 AM every day
30 2 * * * /usr/bin/backup.sh

# Run at midnight on the 1st of every month
0 0 1 * * /usr/bin/monthly_report.sh

# Run every 15 minutes
*/15 * * * * /usr/bin/check_health.sh

# Run at 9 AM on weekdays only (Mon-Fri)
0 9 * * 1-5 /usr/bin/send_report.sh

# Run at 6 AM and 6 PM
0 6,18 * * * /usr/bin/sync.sh

# Log output from a cron job
0 2 * * * /usr/bin/backup.sh >> /var/log/backup.log 2>&1
```

### `at` — One-time Scheduled Tasks

```bash
# Schedule a command to run once
at 2:30 PM
> /usr/bin/backup.sh
> <Ctrl+D>

at now + 1 hour
> echo "Done" | mail user@example.com
> <Ctrl+D>

# Simpler with heredoc
at midnight << EOF
/usr/bin/cleanup.sh
EOF

# List pending jobs
atq

# Remove a pending job
atrm 3    # remove job number 3
```

---

## 14. Putting It All Together — Real World Scripts

### Script 1: Automated Backup Script

```bash
#!/bin/bash
#==============================================================================
# backup.sh — Automated backup script with rotation
# Usage: ./backup.sh <source_dir> <backup_dir>
#==============================================================================

set -euo pipefail    # Exit on error, undefined vars, pipe failures

# --- Configuration ---
SOURCE_DIR="${1:?ERROR: Source directory required}"
BACKUP_DIR="${2:?ERROR: Backup directory required}"
MAX_BACKUPS=7        # Keep last 7 backups
TIMESTAMP=$(date +%Y-%m-%d_%H-%M-%S)
BACKUP_FILE="${BACKUP_DIR}/backup_${TIMESTAMP}.tar.gz"
LOG_FILE="${BACKUP_DIR}/backup.log"

# --- Functions ---
log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $*" | tee -a "$LOG_FILE"
}

check_prerequisites() {
    if [[ ! -d "$SOURCE_DIR" ]]; then
        log "ERROR: Source directory '$SOURCE_DIR' does not exist."
        exit 1
    fi

    if [[ ! -d "$BACKUP_DIR" ]]; then
        log "Creating backup directory: $BACKUP_DIR"
        mkdir -p "$BACKUP_DIR"
    fi
}

create_backup() {
    log "Starting backup of '$SOURCE_DIR' → '$BACKUP_FILE'"
    tar -czf "$BACKUP_FILE" -C "$(dirname "$SOURCE_DIR")" "$(basename "$SOURCE_DIR")"
    local size
    size=$(du -sh "$BACKUP_FILE" | cut -f1)
    log "Backup created successfully. Size: $size"
}

rotate_backups() {
    log "Checking for old backups to rotate..."
    local count
    count=$(ls -1 "${BACKUP_DIR}"/backup_*.tar.gz 2>/dev/null | wc -l)

    if [[ $count -gt $MAX_BACKUPS ]]; then
        local to_delete=$(( count - MAX_BACKUPS ))
        log "Deleting $to_delete old backup(s)..."
        ls -1t "${BACKUP_DIR}"/backup_*.tar.gz | tail -n "$to_delete" | xargs rm -f
        log "Rotation complete."
    else
        log "No rotation needed. Currently have $count backup(s)."
    fi
}

# --- Main ---
log "========== Backup Job Started =========="
check_prerequisites
create_backup
rotate_backups
log "========== Backup Job Finished ========="
```

### Script 2: System Health Monitor

```bash
#!/bin/bash
#==============================================================================
# health_check.sh — System health monitor with alerting
#==============================================================================

set -euo pipefail

# --- Thresholds ---
CPU_THRESHOLD=80       # Alert if CPU usage > 80%
MEM_THRESHOLD=85       # Alert if Memory usage > 85%
DISK_THRESHOLD=90      # Alert if disk usage > 90%
ALERT_EMAIL="admin@example.com"

# --- Colors for terminal output ---
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
NC='\033[0m'    # No Color

log_ok()   { echo -e "${GREEN}[OK]${NC}    $*"; }
log_warn() { echo -e "${YELLOW}[WARN]${NC}  $*"; }
log_crit() { echo -e "${RED}[CRIT]${NC}  $*"; }

ALERTS=()

# --- Check CPU ---
check_cpu() {
    local cpu_idle
    cpu_idle=$(top -bn1 | grep "Cpu(s)" | awk '{print $8}' | cut -d'.' -f1)
    local cpu_used=$(( 100 - cpu_idle ))

    if [[ $cpu_used -ge $CPU_THRESHOLD ]]; then
        log_crit "CPU usage is ${cpu_used}% (threshold: ${CPU_THRESHOLD}%)"
        ALERTS+=("CPU: ${cpu_used}%")
    else
        log_ok "CPU usage: ${cpu_used}%"
    fi
}

# --- Check Memory ---
check_memory() {
    local mem_info
    mem_info=$(free | grep Mem)
    local total used
    total=$(echo "$mem_info" | awk '{print $2}')
    used=$(echo "$mem_info" | awk '{print $3}')
    local used_pct=$(( used * 100 / total ))

    if [[ $used_pct -ge $MEM_THRESHOLD ]]; then
        log_crit "Memory usage is ${used_pct}% (threshold: ${MEM_THRESHOLD}%)"
        ALERTS+=("Memory: ${used_pct}%")
    else
        log_ok "Memory usage: ${used_pct}%"
    fi
}

# --- Check Disk ---
check_disk() {
    while IFS= read -r line; do
        local usage mount
        usage=$(echo "$line" | awk '{print $5}' | tr -d '%')
        mount=$(echo "$line" | awk '{print $6}')

        if [[ $usage -ge $DISK_THRESHOLD ]]; then
            log_crit "Disk '${mount}' is ${usage}% full (threshold: ${DISK_THRESHOLD}%)"
            ALERTS+=("Disk ${mount}: ${usage}%")
        else
            log_ok "Disk '${mount}': ${usage}% used"
        fi
    done < <(df -h | grep -E '^/dev/' | grep -v tmpfs)
}

# --- Send Alert ---
send_alert() {
    if [[ ${#ALERTS[@]} -gt 0 ]]; then
        local message="System Health Alert on $(hostname) at $(date):\n"
        for alert in "${ALERTS[@]}"; do
            message+="  - $alert\n"
        done
        echo -e "$message"
        # Uncomment to actually send email:
        # echo -e "$message" | mail -s "ALERT: $(hostname) Health Issues" "$ALERT_EMAIL"
    fi
}

# --- Main ---
echo "========================================="
echo " System Health Report — $(date)"
echo "========================================="
check_cpu
check_memory
check_disk
echo "-----------------------------------------"

if [[ ${#ALERTS[@]} -gt 0 ]]; then
    echo -e "\n${RED}${#ALERTS[@]} alert(s) found!${NC}"
    send_alert
else
    echo -e "\n${GREEN}All systems healthy!${NC}"
fi
```

### Script 3: Log File Analyzer

```bash
#!/bin/bash
#==============================================================================
# log_analyzer.sh — Analyze application log files and generate a report
# Usage: ./log_analyzer.sh <logfile>
#==============================================================================

LOGFILE="${1:?Usage: $0 <logfile>}"

if [[ ! -f "$LOGFILE" ]]; then
    echo "Error: '$LOGFILE' not found."
    exit 1
fi

echo "=============================================="
echo " Log Analysis Report"
echo " File: $LOGFILE"
echo " Generated: $(date)"
echo "=============================================="

total_lines=$(wc -l < "$LOGFILE")
echo -e "\nTotal log lines: $total_lines"

echo -e "\n--- Log Level Breakdown ---"
for level in DEBUG INFO WARN ERROR FATAL; do
    count=$(grep -c " $level " "$LOGFILE" 2>/dev/null || echo 0)
    pct=0
    [[ $total_lines -gt 0 ]] && pct=$(( count * 100 / total_lines ))
    printf "  %-8s %6d  (%3d%%)\n" "$level" "$count" "$pct"
done

echo -e "\n--- Top 10 Error Messages ---"
grep " ERROR " "$LOGFILE" \
    | awk '{$1=$2=$3=""; print $0}' \
    | sort | uniq -c | sort -rn | head -10

echo -e "\n--- Requests per Hour ---"
awk '{print $1}' "$LOGFILE" \
    | grep -oE '[0-9]{4}-[0-9]{2}-[0-9]{2} [0-9]{2}' \
    | sort | uniq -c

echo -e "\n=============================================="
echo " End of Report"
echo "=============================================="
```

---

## 15. Exercises with Full Answers

We have now built up a solid foundation. Let's solidify everything with exercises — from beginner to advanced. We encourage you to try them yourself before looking at the answers.

---

### 🟢 Beginner Exercises

---

#### Exercise 1 — File Explorer
Create a directory called `workshop`, go inside it, create three files (`notes.txt`, `data.csv`, `config.json`), list them with permissions shown, and then view only the names of files ending in `.txt`.

<details>
<summary>✅ Answer</summary>

```bash
mkdir workshop
cd workshop
touch notes.txt data.csv config.json
ls -l
ls *.txt
```

</details>

---

#### Exercise 2 — Permission Manager
Create a file called `secret.txt`. Set its permissions so that only the owner can read and write it (no access for group or others). Verify with `ls -l`.

<details>
<summary>✅ Answer</summary>

```bash
touch secret.txt
chmod 600 secret.txt
ls -l secret.txt
# Output: -rw------- 1 user group 0 Mar 15 10:00 secret.txt
```

</details>

---

#### Exercise 3 — Redirect & Append
Run `date` and save the output to `timestamps.txt`. Then run it again and **append** to the same file. Finally, check the file contains both timestamps.

<details>
<summary>✅ Answer</summary>

```bash
date > timestamps.txt
date >> timestamps.txt
cat timestamps.txt
# Should show two lines with timestamps
```

</details>

---

#### Exercise 4 — Pipe Practice
Use a single pipeline (no temp files) to: list all files in `/etc`, filter only lines containing "conf", sort them alphabetically, and count how many there are.

<details>
<summary>✅ Answer</summary>

```bash
ls /etc | grep "conf" | sort | wc -l
```

</details>

---

### 🟡 Intermediate Exercises

---

#### Exercise 5 — Variables & Arguments
Write a script called `greet.sh` that accepts a name as an argument and prints `"Hello, <name>! Today is <date>."`. If no argument is given, it should print an error and exit with code 1.

<details>
<summary>✅ Answer</summary>

```bash
#!/bin/bash
# greet.sh

if [[ $# -eq 0 ]]; then
    echo "Error: Please provide a name."
    echo "Usage: $0 <name>"
    exit 1
fi

name="$1"
today=$(date +%A,\ %B\ %d\ %Y)
echo "Hello, $name! Today is $today."
```

```bash
chmod +x greet.sh
./greet.sh Alice
# Hello, Alice! Today is Friday, March 15 2024.

./greet.sh
# Error: Please provide a name.
# Usage: ./greet.sh <name>
```

</details>

---

#### Exercise 6 — Loop & File Processing
Write a script that reads a file `numbers.txt` (one number per line) and prints the sum and average of all the numbers.

<details>
<summary>✅ Answer</summary>

```bash
#!/bin/bash
# stats.sh

FILE="numbers.txt"

if [[ ! -f "$FILE" ]]; then
    echo "Error: $FILE not found"
    exit 1
fi

sum=0
count=0

while IFS= read -r num; do
    # Skip empty lines and non-numbers
    [[ -z "$num" ]] && continue
    [[ ! "$num" =~ ^-?[0-9]+([.][0-9]+)?$ ]] && continue

    sum=$(echo "$sum + $num" | bc)
    ((count++))
done < "$FILE"

if [[ $count -eq 0 ]]; then
    echo "No valid numbers found."
    exit 1
fi

average=$(echo "scale=2; $sum / $count" | bc)
echo "Count:   $count"
echo "Sum:     $sum"
echo "Average: $average"
```

</details>

---

#### Exercise 7 — Find & Clean
Write a script that finds all files in `/tmp` that are older than 7 days and deletes them, logging each deletion to `/var/log/cleanup.log`. It should print a summary at the end.

<details>
<summary>✅ Answer</summary>

```bash
#!/bin/bash
# cleanup_tmp.sh

LOG_FILE="/var/log/cleanup.log"
SEARCH_DIR="/tmp"
MAX_AGE_DAYS=7
deleted=0

echo "[$(date)] Starting cleanup of files older than $MAX_AGE_DAYS days in $SEARCH_DIR" >> "$LOG_FILE"

while IFS= read -r -d '' file; do
    echo "[$(date)] Deleting: $file" >> "$LOG_FILE"
    rm -f "$file"
    ((deleted++))
done < <(find "$SEARCH_DIR" -maxdepth 1 -type f -mtime +"$MAX_AGE_DAYS" -print0)

echo "[$(date)] Cleanup complete. Deleted $deleted file(s)." >> "$LOG_FILE"
echo "Done. Deleted $deleted file(s). See $LOG_FILE for details."
```

</details>

---

#### Exercise 8 — Text Processing
From the file `/etc/passwd`, extract and display only the username and home directory of all users whose home directory starts with `/home`, formatted like: `username → /home/directory`.

<details>
<summary>✅ Answer</summary>

```bash
awk -F: '$6 ~ /^\/home/ {print $1 " → " $6}' /etc/passwd
```

Or with grep + awk:

```bash
grep "^.*:.*:.*:.*:.*:/home/" /etc/passwd | awk -F: '{print $1 " → " $6}'
```

</details>

---

### 🔴 Advanced Exercises

---

#### Exercise 9 — Robust Script with Functions
Write a complete deployment script `deploy.sh` that:
1. Accepts an environment (`staging` or `production`) as argument
2. Validates the argument
3. Creates a timestamped backup of `/var/www/html` before deploying
4. Syncs files from a `./dist` folder to `/var/www/html`
5. Logs all actions
6. Has proper error handling

<details>
<summary>✅ Answer</summary>

```bash
#!/bin/bash
#==============================================================================
# deploy.sh — Web application deployment script
# Usage: ./deploy.sh [staging|production]
#==============================================================================

set -euo pipefail

# --- Config ---
VALID_ENVS=("staging" "production")
DEPLOY_DIR="/var/www/html"
SOURCE_DIR="./dist"
BACKUP_ROOT="/var/backups/webapp"
LOG_FILE="/var/log/deploy.log"
TIMESTAMP=$(date +%Y%m%d_%H%M%S)

# --- Logging ---
log() {
    local level="$1"; shift
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] [$level] $*" | tee -a "$LOG_FILE"
}
log_info()  { log "INFO " "$@"; }
log_error() { log "ERROR" "$@" >&2; }

# --- Validation ---
validate_env() {
    local env="$1"
    for valid in "${VALID_ENVS[@]}"; do
        [[ "$env" == "$valid" ]] && return 0
    done
    log_error "Invalid environment: '$env'. Must be one of: ${VALID_ENVS[*]}"
    exit 1
}

# --- Backup ---
create_backup() {
    local backup_path="${BACKUP_ROOT}/${TIMESTAMP}"
    log_info "Creating backup at '$backup_path'..."
    mkdir -p "$backup_path"

    if [[ -d "$DEPLOY_DIR" ]]; then
        cp -rp "$DEPLOY_DIR/." "$backup_path/"
        log_info "Backup complete. $(du -sh "$backup_path" | cut -f1) backed up."
    else
        log_info "Deploy directory doesn't exist yet, skipping backup."
    fi
}

# --- Deploy ---
deploy_files() {
    if [[ ! -d "$SOURCE_DIR" ]]; then
        log_error "Source directory '$SOURCE_DIR' not found."
        exit 1
    fi

    log_info "Deploying from '$SOURCE_DIR' to '$DEPLOY_DIR'..."
    mkdir -p "$DEPLOY_DIR"
    rsync -av --delete "$SOURCE_DIR/" "$DEPLOY_DIR/" >> "$LOG_FILE" 2>&1
    log_info "Deployment complete."
}

# --- Cleanup old backups (keep last 5) ---
cleanup_backups() {
    local count
    count=$(ls -1d "${BACKUP_ROOT}"/2*/ 2>/dev/null | wc -l)
    if [[ $count -gt 5 ]]; then
        local to_remove=$(( count - 5 ))
        log_info "Removing $to_remove old backup(s)..."
        ls -1dt "${BACKUP_ROOT}"/2*/ | tail -n "$to_remove" | xargs rm -rf
    fi
}

# --- Main ---
ENV="${1:-}"
[[ -z "$ENV" ]] && { log_error "Usage: $0 [staging|production]"; exit 1; }

validate_env "$ENV"

log_info "===== Deployment to $ENV started ====="
log_info "User: $(whoami) | Host: $(hostname)"

create_backup
deploy_files
cleanup_backups

log_info "===== Deployment to $ENV completed ====="
```

</details>

---

#### Exercise 10 — Disk Monitor with Alerting
Write a script that monitors all mounted disk partitions, checks if any exceed 80% usage, and if so, emails a summary report. Schedule it to run every hour using cron.

<details>
<summary>✅ Answer</summary>

```bash
#!/bin/bash
# disk_monitor.sh

THRESHOLD=80
HOSTNAME=$(hostname)
ALERT_EMAIL="admin@example.com"
SUBJECT="⚠️ Disk Alert: $HOSTNAME"

check_disks() {
    local alerts=()

    while IFS= read -r line; do
        local mount usage
        usage=$(echo "$line" | awk '{print $5}' | tr -d '%')
        mount=$(echo "$line" | awk '{print $6}')
        local device
        device=$(echo "$line" | awk '{print $1}')

        if [[ "$usage" =~ ^[0-9]+$ ]] && [[ $usage -ge $THRESHOLD ]]; then
            local free_kb
            free_kb=$(echo "$line" | awk '{print $4}')
            alerts+=("  ❌ $mount ($device): ${usage}% used (${free_kb}KB free)")
        fi
    done < <(df -k | grep -E '^/dev/')

    echo "${alerts[@]}"
}

main() {
    local problem_mounts
    mapfile -t problem_mounts < <(check_disks)

    if [[ ${#problem_mounts[@]} -gt 0 ]]; then
        local body="Disk usage alert on $HOSTNAME at $(date):\n\n"
        for m in "${problem_mounts[@]}"; do
            body+="$m\n"
        done
        body+="\nFull disk report:\n$(df -h)\n"

        echo -e "$body"
        # Uncomment to send actual email:
        # echo -e "$body" | mail -s "$SUBJECT" "$ALERT_EMAIL"
    else
        echo "All disks are within healthy limits."
    fi
}

main
```

**Crontab entry to run every hour:**

```bash
# Add this line via: crontab -e
0 * * * * /usr/local/bin/disk_monitor.sh >> /var/log/disk_monitor.log 2>&1
```

</details>

---

## 🏁 Closing Thoughts

We've traveled a long way together — from understanding what a shell is, all the way through file systems, storage management, process control, scheduling, text processing, and building production-grade scripts.

Here's what we want you to take away:

**The shell rewards curiosity.** Every command has a `man` page. Every behavior can be explained. Whenever something surprises us, we look it up. That instinct — to understand *why* — is what separates great engineers from good ones.

**Write scripts like someone else will read them.** They will. That person is often future us, at 2 AM, during an outage. Comment generously. Name variables clearly. Use functions.

**Practice on real systems.** Set up a cheap VPS or a local VM. Break things. Fix them. The skills in this guide only stick when they're tested against reality.

**Keep the manual nearby:**
```bash
man bash           # The comprehensive bash manual
man find
man awk
info coreutils     # GNU core utilities documentation
```

Thank you for spending this time with us. The command line is one of the most powerful tools ever created, and now it belongs to you.

Happy scripting. 🚀


> **"Programs must be written for people to read, and only incidentally for machines to execute."**
> — Harold Abelson, *Structure and Interpretation of Computer Programs*
