# Linux Fundamentals — Command Line Basics

## 🎯 Objective

To learn and practice essential Linux commands inside a Kali Linux VM, covering navigation, file operations, permissions, process management, and log analysis — with a focus on commands relevant to a SOC analyst's daily work.

## 🧰 Environment

- **OS:** Kali Linux (VirtualBox VM)
- **Shell:** Bash

## 🔍 Commands Practiced

### 1. Navigation & Basic Info

| Command | What It Does |
|---|---|
| `pwd` | Prints the current working directory |
| `ls` | Lists files/folders in the current directory |
| `ls -la` | Lists all files (including hidden ones) with detailed info: permissions, owner, size, date |
| `cd <path>` | Changes directory |
| `cd ..` | Moves one directory up |
| `cd ~` | Goes straight to the home directory |

### 2. File Operations

| Command | What It Does |
|---|---|
| `touch <file>` | Creates a new empty file |
| `mkdir <folder>` | Creates a new directory |
| `cp <src> <dest>` | Copies a file |
| `mv <src> <dest>` | Moves or renames a file |
| `rm <file>` | Deletes a file |
| `rmdir <folder>` | Deletes an empty directory |

### 3. Permissions & Users

| Command | What It Does |
|---|---|
| `ls -l <file>` | Shows permissions in `-rwxr-xr-x` format |
| `chmod <mode> <file>` | Changes file permissions (e.g. `chmod 755`) |
| `whoami` | Shows the currently logged-in user |
| `id` | Shows user ID, group ID, and group memberships |
| `sudo <command>` | Runs a command with root/administrator privileges |

### 4. Process Management

| Command | What It Does |
|---|---|
| `ps aux` | Lists all currently running processes |
| `top` | Shows live process and resource (CPU/memory) usage |
| `kill <PID>` | Forcefully stops a process by its Process ID |

### 5. Search & Log Analysis

| Command | What It Does |
|---|---|
| `find / -name "*.log" 2>/dev/null` | Searches the whole filesystem for `.log` files, suppressing permission errors |
| `grep "<word>" <file>` | Searches for a specific word/pattern inside a file |
| `cat /var/log/auth.log` | Displays authentication log content (logins, sudo usage) |
| `tail -f <file>` | Shows live updates to a file as new lines are added |
| `journalctl` | Views the systemd journal — the modern logging system Kali uses instead of a flat `syslog` file |
| `journalctl -p err -b` | Shows only error-level logs from the current boot |
| `sudo journalctl -f` | Follows the journal live, in real time |
| `last` | Shows a history of successful logins (from `wtmp`) |
| `sudo lastb` | Shows a history of **failed** login attempts (from `btmp`) |

## 🔎 Observation — Kali's Logging Structure

Running `ls -la /var/log/` showed that Kali doesn't keep a traditional `syslog` file by default — it primarily relies on the **systemd journal** (`/var/log/journal/`) for centralized logging. However, several other important log files do exist, including:

- **`auth.log`** — authentication and privilege escalation events
- **`btmp`** — failed login attempts
- **`wtmp`** — successful login history
- **`lastlog`** — last login timestamp per user
- **`boot.log`** — system startup events
- **`dpkg.log`** — software install/update history

This is an important distinction from older Linux distributions that log everything to a flat `/var/log/syslog` file — modern systems often centralize logging through `journalctl` instead.

## 🧠 Key Takeaway (SOC Relevance)

- **`ps aux`** is one of the first commands used to investigate a potentially compromised system — spotting unfamiliar or suspicious process names is a common first step in incident response
- **`last` and `sudo lastb`** are directly useful for spotting **brute-force login attempts** — a high volume of failed logins (`lastb`) followed by a successful one (`last`) from the same source is a classic red flag
- **`auth.log`** (or the journal equivalent) is one of the most important files a SOC analyst checks — it records every login, logout, and privilege escalation (`sudo`) attempt
- **`grep` and `journalctl -p err`** are essential for quickly filtering large log files down to just the relevant events, instead of manually reading through everything
- Understanding **file permissions** (`ls -l`, `chmod`) matters because misconfigured permissions (e.g. world-writable sensitive files) are a common security weakness attackers look for

## 📌 Notes

This is my first Linux fundamentals writeup, following the completion of the networking and Wireshark packet-analysis section of my learning path. Next, I plan to explore more networking-specific Linux commands (`ifconfig`/`ip`, `netstat`/`ss`) before moving on to Windows Event Viewer basics.
