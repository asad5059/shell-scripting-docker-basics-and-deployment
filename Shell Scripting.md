

---

# 🐚 Introduction to Bash Scripting

## 📌 1. What is Shell & Bash?

### 🖥️ GUI vs CLI

* **GUI (Graphical User Interface)**: Interaction through visual elements like buttons and menus.
* **CLI (Command Line Interface)**: Interaction through text-based commands.

  * GUI is **tedious for repetitive tasks** (e.g., creating 100 folders manually).
  * Shell scripting automates these tasks efficiently.

👉 The CLI is **faster and more powerful** for repetitive and automation tasks compared to GUI.

---

### 🐚 The Shell

* The **Shell** is a program that:

  * Interprets user commands
  * Executes them via the system
  * Forms the foundation of **Shell & Bash scripting**

---

### 💻 Bash (Bourne Again Shell)

* Bash is the **most widely used shell** on Linux.
* Beyond running commands, it is a **full-fledged scripting language** for:

  * Automation
  * Task scheduling
  * System operations

---

### 🖲️ Terminal

* The **Terminal** is the interface (window/application) where you:

  * Type commands
  * Run scripts
  * Interact with the shell

---

## ⚙️ 2. Setting Up the Bash Environment

| Platform    | Setup                                          |
| ----------- | ---------------------------------------------- |
| **Linux**   | Bash is available by default                   |
| **macOS**   | Bash is installed, may need switching from Zsh |
| **Windows** | Install **Windows Subsystem for Linux (WSL)**  |

---

## 🧠 3. Real-World Use Case: Log Analysis

### 📖 Scenario

A DevOps engineer needs to analyze logs (`application.log`, `system.log`) daily to monitor system health.

### ❌ Problems with Manual Work

1. **Tedious** – Filtering logs takes **30–45 minutes**
2. **Inconsistent** – Manual tracking leads to **human errors**
3. **Repetitive** – Running the same commands daily is inefficient

### ✅ The Solution: Bash Script Automation

* Aggregate commands
* Automate log filtering
* Ensure consistent outputs

💡 **Benefits:**

* Saves time
* Reduces errors
* Fully automates repetitive tasks

---

## 📁 4. Bash Command Cheat Sheet

Here’s a **structured command reference** by category (all essential for engineers):

### 4.1 File & Directory Operations

| Command | Example          | Explanation                                        |
| ------- | ---------------- | -------------------------------------------------- |
| `ls`    | `ls -l`          | List files with details (permissions, owner, size) |
| `cd`    | `cd logs/`       | Change directory                                   |
| `pwd`   | `pwd`            | Print current directory                            |
| `cp`    | `cp a.txt b.txt` | Copy file                                          |
| `mv`    | `mv a.txt b.txt` | Move/rename file                                   |
| `rm`    | `rm a.txt`       | Delete file                                        |
| `mkdir` | `mkdir logs/tmp` | Create directory                                   |

---

### 4.2 File Viewing & Reading

| Command   | Example               | Explanation                     |
| --------- | --------------------- | ------------------------------- |
| `cat`     | `cat file.txt`        | View entire file content        |
| `less`    | `less file.txt`       | Scrollable view for large files |
| `head`    | `head -n 10 file.txt` | View first 10 lines             |
| `tail`    | `tail -n 10 file.txt` | View last 10 lines              |
| `tail -f` | `tail -f app.log`     | Live log monitoring             |

---

### 4.3 Search & Text Processing

| Command | Example                      | Explanation                        |
| ------- | ---------------------------- | ---------------------------------- |
| `grep`  | `grep "ERROR" app.log`       | Find lines containing a pattern    |
| `awk`   | `awk '{print $1}' file.txt`  | Extract column/field               |
| `sed`   | `sed 's/old/new/g' file.txt` | Replace text in file               |
| `cut`   | `cut -d',' -f1 data.csv`     | Extract columns from CSV           |
| `sort`  | `sort file.txt`              | Sort lines                         |
| `uniq`  | `uniq file.txt`              | Remove duplicate consecutive lines |
| `wc`    | `wc -l file.txt`             | Count lines, words, characters     |

---

### 4.4 Pipes & Redirection

| Command     | Example                      | Explanation                           |
| ----------- | ---------------------------- | ------------------------------------- |
| `\|` (pipe) | `cat file.txt \| grep ERROR` | Send output of one command to another |
| `>`         | `echo "Hi" > file.txt`       | Write to file (overwrite)             |
| `>>`        | `echo "Hi" >> file.txt`      | Append to file                        |
| `2>`        | `cmd 2> err.log`             | Redirect error output                 |

---

### 4.5 Permissions & Ownership

| Command    | Example                    | Explanation                    |
| ---------- | -------------------------- | ------------------------------ |
| `chmod`    | `chmod 755 script.sh`      | Change permissions (rwxr-xr-x) |
| `chmod +w` | `chmod u+w file.txt`       | Add write permission for user  |
| `chown`    | `chown user:user file.txt` | Change owner/group             |

---

### 4.6 Control Flow

| Command | Example                                     | Explanation                    |
| ------- | ------------------------------------------- | ------------------------------ |
| `if`    | `if [ -f a.txt ]; then echo ok; fi`         | Conditional execution          |
| `for`   | `for f in *.txt; do echo $f; done`          | Loop over files                |
| `while` | `while read l; do echo $l; done < file.txt` | Loop through file line by line |

---

### 4.7 Variables & Input

| Command  | Example       | Explanation     |
| -------- | ------------- | --------------- |
| Variable | `name="Asad"` | Store value     |
| Access   | `echo $name`  | Retrieve value  |
| `read`   | `read name`   | Take user input |

---

### 4.8 Test Conditions

| Command | Example           | Explanation        |
| ------- | ----------------- | ------------------ |
| `-f`    | `[ -f file.txt ]` | File exists?       |
| `-d`    | `[ -d dir ]`      | Directory exists?  |
| `-z`    | `[ -z "$var" ]`   | Variable is empty? |
| `=`     | `[ "$a" = "$b" ]` | String comparison  |
| `-eq`   | `[ $a -eq $b ]`   | Numeric comparison |

---

### 4.9 Process Management

| Command | Example     | Explanation               |
| ------- | ----------- | ------------------------- |
| `ps`    | `ps aux`    | List processes            |
| `kill`  | `kill 1234` | Stop process by PID       |
| `top`   | `top`       | Real-time process monitor |

---

### 4.10 Networking

| Command | Example                | Explanation        |
| ------- | ---------------------- | ------------------ |
| `ping`  | `ping google.com`      | Check connectivity |
| `curl`  | `curl https://api.com` | Fetch URL or API   |

---

### 4.11 Archive & Compression

| Command    | Example                  | Explanation     |
| ---------- | ------------------------ | --------------- |
| `tar -cvf` | `tar -cvf a.tar folder/` | Create archive  |
| `tar -xvf` | `tar -xvf a.tar`         | Extract archive |

---

### 4.12 Running Scripts

| Command       | Example              | Explanation            |
| ------------- | -------------------- | ---------------------- |
| `chmod +x`    | `chmod +x script.sh` | Make script executable |
| `./script.sh` | `./script.sh`        | Run script             |

---

### 🔥 Mini Real-World Script

```bash
#!/bin/bash
LOG="application.log"

if [ -f "$LOG" ]; then
  grep "ERROR" "$LOG" > error_logs.txt
  echo "Errors saved to error_logs.txt"
else
  echo "Log file not found"
fi
```

✅ Checks file existence, extracts errors, saves them.

---
