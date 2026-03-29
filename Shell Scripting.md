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
# ! not operator. -s checks if the file is empty. "$1" is the first parameter of the function
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
