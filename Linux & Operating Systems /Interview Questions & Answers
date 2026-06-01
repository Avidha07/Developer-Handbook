# 🐧 Linux & Operating Systems — Interview Questions & Answers

> 20 core Linux/OS concepts explained the way you'd answer them in a real technical interview.

---

## Table of Contents

1. [What happens when you type a command in terminal?](#1-what-happens-when-you-type-a-command-in-terminal)
2. [Difference between Process and Thread](#2-difference-between-process-and-thread)
3. [What is a Zombie Process?](#3-what-is-a-zombie-process)
4. [What is a Daemon Process?](#4-what-is-a-daemon-process)
5. [Difference between Hard Link and Soft Link](#5-difference-between-hard-link-and-soft-link)
6. [What is an Inode in Linux?](#6-what-is-an-inode-in-linux)
7. [Explain chmod 755](#7-explain-chmod-755)
8. [Difference between kill and kill -9](#8-difference-between-kill-and-kill--9)
9. [Difference between grep, find, and locate](#9-difference-between-grep-find-and-locate)
10. [Difference between > and >>](#10-difference-between--and-)
11. [What is Pipe | in Linux?](#11-what-is-pipe--in-linux)
12. [What is Swap Memory?](#12-what-is-swap-memory)
13. [What happens when RAM gets full?](#13-what-happens-when-ram-gets-full)
14. [Difference between TCP and UDP](#14-difference-between-tcp-and-udp)
15. [Difference between HTTP and HTTPS](#15-difference-between-http-and-https)
16. [What is SSH?](#16-what-is-ssh)
17. [What is a Cron Job?](#17-what-is-a-cron-job)
18. [What is Environment Variable and PATH?](#18-what-is-environment-variable-and-path)
19. [Explain fork() and exec()](#19-explain-fork-and-exec)
20. [Production server is down — how will you debug?](#20-production-server-is-down--how-will-you-debug)

---

## 1. What happens when you type a command in terminal?

This is a classic question that tests how deeply you understand the OS. Here's the full journey:

**Step-by-step flow:**

```
You type: ls -la
Press Enter
```

1. **Shell reads the input** — The terminal passes your input to the shell (bash/zsh). The shell tokenizes it: command = `ls`, argument = `-la`.

2. **Shell checks for an alias** — If you've defined `alias ls='ls --color'`, it expands that first.

3. **Shell looks up the command** — It searches directories listed in your `$PATH` environment variable (e.g., `/usr/bin`, `/bin`) to find the `ls` binary.
   ```bash
   which ls      # → /usr/bin/ls
   ```

4. **Shell calls `fork()`** — The shell creates a child process (a copy of itself).

5. **Child calls `exec()`** — The child process replaces itself with the `ls` program by calling `execve()`.

6. **Kernel takes over** — The kernel loads the binary into memory, sets up the stack, heap, and starts execution.

7. **`ls` runs** — It makes system calls (like `opendir()`, `readdir()`) to read the directory and writes output to `stdout`.

8. **Process exits** — `ls` calls `exit()`, the kernel cleans up, sends the exit status back to the parent (shell).

9. **Shell resumes** — The shell displays a new prompt, ready for the next command.

> **One-liner answer:** The shell finds the binary via `$PATH`, forks a child process, the child `exec`s the binary, the kernel runs it, and control returns to the shell when it exits.

---

## 2. Difference between Process and Thread

**A process is an independent program in execution. A thread is a unit of execution within a process.**

Think of it this way: A process is like a full restaurant. A thread is like one of the waiters inside that restaurant. Multiple waiters (threads) share the same kitchen, tables, and supplies (memory), but each waiter handles their own task independently.

| Feature | Process | Thread |
|---------|---------|--------|
| **Memory** | Has its own memory space | Shares memory with other threads in the same process |
| **Creation cost** | Heavy — OS must allocate full resources | Light — shares parent's resources |
| **Communication** | Inter-Process Communication (IPC) — pipes, sockets, shared memory | Direct — shared memory within the process |
| **Crash impact** | One process crash doesn't affect others | One thread crash can kill the entire process |
| **Switching cost** | Expensive (context switch) | Cheaper |
| **Example** | Chrome opens each tab as a new process | A web server handles each request as a new thread |

```bash
# View running processes
ps aux

# View threads of a process
ps -eLf | grep <process_name>

# Or with top
top -H -p <PID>
```

> **Interview insight:** Processes are isolated and safer. Threads are faster but share state, which introduces race conditions and requires synchronization (mutexes, semaphores).

---

## 3. What is a Zombie Process?

A **zombie process** is a process that has **finished execution but still has an entry in the process table** because its parent hasn't yet read its exit status.

**How it happens:**
1. Child process finishes and calls `exit()`
2. The kernel keeps a small record of the child (PID, exit code) waiting for the parent to collect it via `wait()`
3. If the parent never calls `wait()`, the child lingers as a zombie

```bash
# Zombie processes show as 'Z' in the STAT column
ps aux | grep 'Z'
# output: 1234  Z  defunct

# See zombie count
ps aux | awk '{print $8}' | grep -c Z
```

**Key facts:**
- Zombies consume **no CPU or memory** — just a PID slot
- They cannot be killed with `kill -9` because they're already dead
- The fix is to fix the **parent** — make it call `wait()`, or kill the parent so `init`/`systemd` (PID 1) adopts and reaps the zombie

```bash
# Kill the parent to let init reap the zombie
kill -9 <parent_PID>
```

> **One-liner:** A zombie is a dead process waiting for its parent to acknowledge its death. Fix the parent, not the zombie.

---

## 4. What is a Daemon Process?

A **daemon** is a background process that runs continuously, not attached to any terminal, waiting to handle requests or perform system tasks.

The name comes from Greek mythology — a daemon was a background spirit doing helpful work unseen.

**Characteristics:**
- Runs in the background (detached from terminal)
- Usually starts at boot time
- Often ends with `d` in the name: `sshd`, `httpd`, `crond`, `nginx`
- Has PPID = 1 (parent is `init`/`systemd`)

```bash
# Common daemons
sshd        # SSH daemon — handles incoming SSH connections
nginx       # Web server daemon
crond       # Cron scheduler daemon
systemd     # The mother of all daemons — PID 1

# Check if a daemon is running
systemctl status nginx
systemctl status sshd

# Start / stop / restart
systemctl start nginx
systemctl stop nginx
systemctl restart nginx

# Enable daemon to start on boot
systemctl enable nginx
```

**How a process becomes a daemon:**
1. `fork()` from parent, parent exits
2. Call `setsid()` to create a new session (detach from terminal)
3. Close standard file descriptors (stdin, stdout, stderr)
4. Change working directory to `/`

> **Difference from regular background process (`&`):** A daemon is properly detached from the terminal and session. A background job (`./script.sh &`) is still tied to your shell session and dies when you log out.

---

## 5. Difference between Hard Link and Soft Link

Both are ways to create references to files, but they work at different levels.

**Analogy:**
- Hard link = a second name tag on the same person
- Soft link (symlink) = a sticky note pointing to where the person sits

| Feature | Hard Link | Soft Link (Symbolic Link) |
|---------|-----------|--------------------------|
| **Points to** | Same inode (actual data) | Another file's path |
| **Works across filesystems?** | ❌ No | ✅ Yes |
| **Works on directories?** | ❌ No (usually) | ✅ Yes |
| **If original deleted** | File still accessible | Link becomes broken (dangling) |
| **inode** | Same inode as original | Different inode |

```bash
# Create a hard link
ln original.txt hardlink.txt

# Create a soft link (symlink)
ln -s original.txt softlink.txt
ln -s /etc/nginx/nginx.conf ~/nginx.conf   # absolute path

# Check links
ls -li original.txt hardlink.txt softlink.txt
# Hard links show the same inode number
# Soft links show -> original.txt

# Find all hard links to an inode
find / -inum <inode_number>

# Check if a symlink is broken
ls -la softlink.txt   # shows -> path, red if broken
```

> **Practical use:** Symlinks are everywhere in Linux — `/usr/bin/python` → `/usr/bin/python3.11`. Hard links are used in backup tools like `rsnapshot` to save disk space.

---

## 6. What is an Inode in Linux?

An **inode (index node)** is a data structure on the filesystem that stores **metadata about a file** — everything except the file's name and actual content.

**What an inode contains:**
- File type (regular, directory, symlink, etc.)
- File size
- Owner (UID) and group (GID)
- Permissions
- Timestamps (created, modified, accessed)
- Number of hard links
- Pointers to the actual data blocks on disk

**What an inode does NOT contain:**
- The filename (stored in the directory)
- The actual file content

```bash
# View inode number of a file
ls -i filename.txt
# output: 1234567 filename.txt

# View detailed inode info
stat filename.txt

# Check inode usage on filesystem
df -i
# Shows total inodes, used, free per partition

# You can run out of inodes even if disk has space!
df -h   # disk space
df -i   # inode count
```

**Why it matters:**
- A directory is just a table mapping filenames → inode numbers
- When you delete a file, you remove the directory entry; the inode (and data) is freed only when all hard links are removed AND no process has it open

> **Key insight:** Two filenames (hard links) can point to the same inode — that's why deleting one doesn't destroy the data.

---

## 7. Explain chmod 755

`chmod` changes file **permissions**. The number `755` is **octal notation** representing permissions for three groups: **owner**, **group**, **others**.

**Permission values:**
| Value | Permission | Binary |
|-------|-----------|--------|
| 4 | Read (r) | 100 |
| 2 | Write (w) | 010 |
| 1 | Execute (x) | 001 |

**Breaking down 755:**

```
  7        5        5
Owner    Group    Others
r+w+x    r+x      r+x
4+2+1    4+0+1    4+0+1
```

- **Owner (7):** Read + Write + Execute — full control
- **Group (5):** Read + Execute — can run but not modify
- **Others (5):** Read + Execute — same as group

```bash
# Set permissions to 755
chmod 755 script.sh

# Using symbolic notation (equivalent)
chmod u=rwx,go=rx script.sh

# Common permission patterns
chmod 644 file.txt      # rw-r--r-- (typical file)
chmod 755 script.sh     # rwxr-xr-x (executable/directory)
chmod 600 private.key   # rw------- (SSH private key)
chmod 777 file.txt      # rwxrwxrwx (everyone — avoid in production!)

# View permissions
ls -la script.sh
# output: -rwxr-xr-x  1 user group 1234 Jun 1 10:00 script.sh

# Recursively set permissions
chmod -R 755 /var/www/html
```

**The first character in `ls -la` output:**
- `-` = regular file
- `d` = directory
- `l` = symlink

> **Interview tip:** Explain the risk of `chmod 777` — it gives everyone write access, which is a security vulnerability in shared or web server environments.

---

## 8. Difference between kill and kill -9

Both send signals to a process, but the key difference is whether the **process gets a chance to clean up**.

| Command | Signal | Name | Behavior |
|---------|--------|------|----------|
| `kill <PID>` | 15 | SIGTERM | Politely asks the process to terminate; process can catch it, save state, and exit gracefully |
| `kill -9 <PID>` | 9 | SIGKILL | Forces the kernel to immediately terminate the process — cannot be caught, blocked, or ignored |

```bash
# Find a process
ps aux | grep nginx
pgrep nginx

# Graceful shutdown (SIGTERM) — preferred
kill 1234
kill -15 1234
kill -SIGTERM 1234

# Force kill (SIGKILL) — last resort
kill -9 1234
kill -SIGKILL 1234

# Kill by name
pkill nginx         # SIGTERM to all processes named nginx
pkill -9 nginx      # SIGKILL to all

# Kill all processes of a user
pkill -u username

# List all available signals
kill -l
```

**Other useful signals:**
```bash
kill -1  <PID>   # SIGHUP — reload config without restart (nginx, apache)
kill -2  <PID>   # SIGINT — same as Ctrl+C
kill -19 <PID>   # SIGSTOP — pause a process
kill -18 <PID>   # SIGCONT — resume a paused process
```

> **Best practice:** Always try `kill` (SIGTERM) first, give the process a few seconds, then use `kill -9` only if it doesn't respond. SIGKILL can leave temp files, open DB connections, and corrupted state behind.

---

## 9. Difference between grep, find, and locate

These three are all "search" tools but they search for completely different things.

| Tool | Searches for | Searches in |
|------|-------------|-------------|
| `grep` | **Text content** inside files | File contents |
| `find` | **Files/dirs** by name, type, size, date, permissions | Live filesystem |
| `locate` | **Files** by name | Pre-built database (fast but may be stale) |

### `grep` — search inside file content
```bash
# Find lines containing "error" in a file
grep "error" app.log

# Case-insensitive
grep -i "error" app.log

# Recursive — search all files in a directory
grep -r "TODO" ./src

# Show line numbers
grep -n "def main" script.py

# Show only filenames that match
grep -rl "password" /etc

# Invert match (lines that DON'T contain the pattern)
grep -v "DEBUG" app.log

# Using regex
grep -E "error|warning|critical" app.log
```

### `find` — search for files on live filesystem
```bash
# Find by name
find /home -name "*.log"

# Find by type (f=file, d=directory, l=symlink)
find /var -type f -name "*.conf"

# Find files modified in last 7 days
find /var/log -mtime -7

# Find files larger than 100MB
find / -size +100M

# Find and execute a command on results
find /tmp -name "*.tmp" -exec rm {} \;

# Find by permissions
find /etc -perm 777
```

### `locate` — fast name-based search using a database
```bash
# Search for a file by name
locate nginx.conf

# Update the database (run as root)
updatedb

# Case-insensitive
locate -i readme.md
```

> **Summary:** Use `grep` to find text inside files, `find` for precise filesystem queries on live data, and `locate` for a quick filename search when speed matters over freshness.

---

## 10. Difference between > and >>

Both redirect output to a file, but they differ in what happens to existing content.

| Operator | Behavior |
|----------|---------|
| `>` | **Overwrites** — creates the file if it doesn't exist, wipes it if it does |
| `>>` | **Appends** — creates the file if it doesn't exist, adds to the end if it does |

```bash
# Overwrite — dangerous if file has important content
echo "Hello" > output.txt        # output.txt now contains only "Hello"
echo "World" > output.txt        # output.txt now contains only "World" (Hello is gone!)

# Append — safe, adds to existing content
echo "Hello" >> log.txt
echo "World" >> log.txt
cat log.txt
# Hello
# World

# Redirect stderr (errors) to a file
command 2> errors.log

# Redirect both stdout and stderr
command > output.log 2>&1
command &> output.log            # shorthand

# Discard output entirely (send to /dev/null)
command > /dev/null 2>&1

# Append stderr
command 2>> errors.log
```

**Real-world use:**
```bash
# Logging a cron job — always append, never overwrite
0 2 * * * /usr/bin/backup.sh >> /var/log/backup.log 2>&1

# Reset a log file
> app.log         # empties the file without deleting it
```

> **Common mistake:** Using `>` when you meant `>>` in a log script, accidentally wiping your log history.

---

## 11. What is Pipe `|` in Linux?

A **pipe** connects the **standard output (stdout)** of one command directly to the **standard input (stdin)** of another command — without creating a temporary file.

**Analogy:** Think of a water pipe — water (data) flows from one end to the other. You can chain multiple pipes to transform data step by step.

```bash
# Basic pipe: output of ls becomes input to grep
ls -la | grep ".log"

# Chain multiple pipes
cat /etc/passwd | grep "/bin/bash" | cut -d: -f1 | sort

# Count lines in output
ps aux | wc -l

# Find the top 5 largest files
du -sh /var/log/* | sort -rh | head -5

# Real-world examples
# Find all error lines and count unique messages
cat app.log | grep "ERROR" | sort | uniq -c | sort -rn

# Monitor logs in real time and filter
tail -f /var/log/nginx/access.log | grep "500"

# Check which ports are in use
netstat -tuln | grep LISTEN

# Find memory usage of a specific process
ps aux | grep nginx | awk '{print $6}'
```

**Named Pipes (FIFOs):**
```bash
# Create a named pipe (persists on filesystem)
mkfifo mypipe
echo "data" > mypipe &   # write in background
cat mypipe               # read from it
```

> **Key insight:** Pipes implement the Unix philosophy: "Do one thing well, and connect small tools together." `grep`, `awk`, `sed`, `sort`, `uniq`, `cut`, `wc` are all designed to work together through pipes.

---

## 12. What is Swap Memory?

**Swap is disk space that the OS uses as an overflow extension of RAM** when physical memory (RAM) is full.

When the system runs low on RAM, the kernel moves **inactive memory pages** from RAM to the swap space on disk. This frees up RAM for active processes. When that data is needed again, it's swapped back into RAM (potentially swapping something else out).

```bash
# Check swap usage
free -h
swapon --show

# Example output of free -h:
#               total   used    free   shared  buff/cache  available
# Mem:           16Gi   8.2Gi   2.1Gi  512Mi     5.7Gi     7.3Gi
# Swap:           8Gi   1.2Gi   6.8Gi

# Check swap activity in real time
vmstat 1
# si = swap in (pages read from disk to RAM)
# so = swap out (pages written from RAM to disk)
```

**Types of swap:**
```bash
# Swap partition — dedicated disk partition
# Swap file — a regular file used as swap

# Create a swap file (e.g., 2GB)
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# Make permanent — add to /etc/fstab
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

**Swappiness** — how aggressively the kernel uses swap:
```bash
# Check current swappiness (default: 60)
cat /proc/sys/vm/swappiness

# Lower = prefer RAM, only swap when necessary
# For servers/databases, use 10
sudo sysctl vm.swappiness=10
```

> **The downside:** Disk is 100–1000x slower than RAM. Heavy swapping (called **thrashing**) causes severe performance degradation. Swap is a safety net, not a substitute for RAM.

---

## 13. What happens when RAM gets full?

When RAM is exhausted, the OS goes through a series of increasingly aggressive measures:

**Stage 1 — Use the page cache**
The kernel first reclaims memory used for disk cache (recently read files). This is "free" memory that can be reclaimed without any data loss.

**Stage 2 — Swap out inactive pages**
The kernel moves least-recently-used memory pages from RAM to swap space on disk (if swap is available).

**Stage 3 — OOM Killer activates**
If swap is also exhausted, the **Out-Of-Memory (OOM) Killer** kicks in. The kernel scores every process based on memory usage, runtime, and priority, then kills the process with the highest "oom_score" to free up memory.

```bash
# Check OOM killer activity in kernel logs
dmesg | grep -i "oom"
dmesg | grep -i "killed process"

# View OOM scores for processes
cat /proc/<PID>/oom_score

# Protect a process from OOM killer (-17 = never kill)
echo -17 > /proc/<PID>/oom_adj

# Monitor memory in real time
watch -n 1 free -h
top   # press M to sort by memory
htop  # interactive, color-coded
```

**Memory metrics explained:**
```bash
free -h
# Mem: total | used | free | shared | buff/cache | available
# "available" is what really matters — free + reclaimable cache
```

**Prevention strategies:**
- Set memory limits on containers/services (`cgroups`)
- Monitor with alerts before hitting 80% memory usage
- Use `ulimit` to cap memory per process
- Size your swap as a safety net
- Profile and fix memory leaks in applications

> **Interview insight:** The OOM killer is a last resort. In production, you should never let it fire — it means your capacity planning failed. Set up memory alerts and tune `vm.swappiness` and `vm.overcommit_memory` appropriately.

---

## 14. Difference between TCP and UDP

Both are transport layer protocols, but they make different tradeoffs between **reliability and speed**.

| Feature | TCP | UDP |
|---------|-----|-----|
| **Full name** | Transmission Control Protocol | User Datagram Protocol |
| **Connection** | Connection-oriented (3-way handshake) | Connectionless |
| **Reliability** | Guaranteed delivery, ordering, error checking | No guarantee — fire and forget |
| **Speed** | Slower (overhead for reliability) | Faster (minimal overhead) |
| **Data ordering** | Guaranteed in order | May arrive out of order |
| **Use cases** | HTTP/S, SSH, FTP, email, databases | DNS, video streaming, gaming, VoIP |
| **Header size** | 20 bytes | 8 bytes |

**TCP 3-way handshake:**
```
Client → Server:  SYN       (I want to connect)
Server → Client:  SYN-ACK   (OK, I'm ready)
Client → Server:  ACK       (Great, let's go)
         [Connection established]
```

```bash
# Check TCP connections and their states
netstat -tn
ss -tn

# Check UDP
ss -un

# TCP states you'll see:
# LISTEN      — waiting for connections
# ESTABLISHED — active connection
# TIME_WAIT   — connection closing, waiting to ensure remote end got the FIN
# CLOSE_WAIT  — remote end closed, local still open
```

> **When to use which:**
> - Use **TCP** when data integrity is critical (banking, file transfers, web pages)
> - Use **UDP** when speed matters more than perfection (live video, online gaming, DNS lookups where a retry is cheap)

---

## 15. Difference between HTTP and HTTPS

**HTTP** (HyperText Transfer Protocol) is the foundation of web communication. **HTTPS** is HTTP with an added **TLS/SSL encryption layer**.

| Feature | HTTP | HTTPS |
|---------|------|-------|
| **Port** | 80 | 443 |
| **Encryption** | None — plain text | TLS/SSL encrypted |
| **Data security** | Anyone on the network can read it | Encrypted end-to-end |
| **Authentication** | None | Server identity verified by certificate |
| **SEO** | Lower ranking | Google prefers HTTPS |
| **Use case** | Internal tools, local dev | Any public-facing website |

**How HTTPS (TLS) works:**
```
1. Client connects to server on port 443
2. Server sends its SSL certificate (contains public key)
3. Client verifies certificate with a Certificate Authority (CA)
4. Client and server negotiate a session key (asymmetric → symmetric)
5. All further communication is encrypted with the session key
```

```bash
# Check a site's certificate
openssl s_client -connect google.com:443 -showcerts

# View certificate expiry
echo | openssl s_client -connect example.com:443 2>/dev/null \
  | openssl x509 -noout -dates

# Check what TLS version a server supports
nmap --script ssl-enum-ciphers -p 443 example.com

# Test HTTP vs HTTPS response
curl -I http://example.com      # should redirect to HTTPS
curl -I https://example.com     # 200 OK
```

> **Key point in interviews:** HTTPS doesn't just encrypt data — it also authenticates the server so you know you're talking to the real website and not an impersonator (man-in-the-middle attack).

---

## 16. What is SSH?

**SSH (Secure Shell)** is a cryptographic network protocol for **securely connecting to remote machines** over an unsecured network.

It replaced older, insecure protocols like Telnet and rsh. SSH encrypts all traffic, including authentication credentials.

**How SSH works:**
1. TCP connection to port 22
2. Server sends its public host key
3. Client verifies the host key (from `~/.ssh/known_hosts`)
4. Key exchange — client and server agree on a session encryption key
5. User authenticates (password or key-based)
6. Encrypted shell session begins

```bash
# Basic connection
ssh user@hostname
ssh user@192.168.1.10
ssh -p 2222 user@hostname          # custom port

# Key-based authentication (more secure than password)
# Step 1: Generate key pair
ssh-keygen -t ed25519 -C "your@email.com"
# Creates: ~/.ssh/id_ed25519 (private) and ~/.ssh/id_ed25519.pub (public)

# Step 2: Copy public key to server
ssh-copy-id user@hostname
# This adds your public key to ~/.ssh/authorized_keys on the server

# Step 3: Now login without password
ssh user@hostname

# SSH tunneling — forward a remote port to local
ssh -L 8080:localhost:80 user@remote-server
# Now localhost:8080 = remote-server:80

# Reverse tunnel
ssh -R 9090:localhost:3000 user@remote-server

# Run a single command remotely
ssh user@server "df -h && free -h"

# SSH config file (~/.ssh/config) — avoid typing long commands
# Host myserver
#     HostName 192.168.1.10
#     User ubuntu
#     Port 2222
#     IdentityFile ~/.ssh/my_key
ssh myserver    # uses the config above
```

**Key SSH hardening practices:**
```bash
# In /etc/ssh/sshd_config:
PermitRootLogin no           # never allow root login
PasswordAuthentication no    # key-based only
Port 2222                    # non-default port (minor security)
AllowUsers deploy ubuntu     # whitelist specific users
```

> **Why SSH over password login?** Brute-force attacks against passwords are common. SSH key authentication is mathematically infeasible to brute-force with modern key sizes (Ed25519 or RSA 4096).

---

## 17. What is a Cron Job?

A **cron job** is a scheduled task that runs automatically at specified intervals on Unix-based systems. The `crond` daemon checks the crontab (cron table) every minute and executes any matching jobs.

**Crontab syntax:**
```
┌───────────── minute (0–59)
│ ┌───────────── hour (0–23)
│ │ ┌───────────── day of month (1–31)
│ │ │ ┌───────────── month (1–12)
│ │ │ │ ┌───────────── day of week (0–6, Sun=0)
│ │ │ │ │
* * * * *  command_to_execute
```

```bash
# Edit your crontab
crontab -e

# View your crontab
crontab -l

# View another user's crontab (as root)
crontab -l -u username

# Common examples:
# Run every minute
* * * * * /usr/bin/check-health.sh

# Run at 2:30 AM every day
30 2 * * * /usr/bin/backup.sh

# Run every Sunday at midnight
0 0 * * 0 /usr/bin/weekly-report.sh

# Run every 5 minutes
*/5 * * * * /usr/bin/monitor.sh

# Run at 9 AM on weekdays only
0 9 * * 1-5 /usr/bin/send-report.sh

# Run on the 1st of every month
0 0 1 * * /usr/bin/monthly-cleanup.sh

# Log output
30 2 * * * /usr/bin/backup.sh >> /var/log/backup.log 2>&1
```

**Special shortcuts:**
```bash
@reboot   /usr/bin/startup.sh    # run once at boot
@daily    /usr/bin/daily.sh      # once a day (midnight)
@weekly   /usr/bin/weekly.sh     # once a week (Sunday midnight)
@monthly  /usr/bin/monthly.sh    # once a month
@hourly   /usr/bin/hourly.sh     # top of every hour
```

```bash
# System-wide cron directories (no crontab needed)
/etc/cron.daily/      # drop scripts here for daily execution
/etc/cron.weekly/
/etc/cron.monthly/

# Check cron logs
grep CRON /var/log/syslog
journalctl -u cron
```

> **Pro tip:** Always use absolute paths in cron jobs (`/usr/bin/python3` not `python3`) because cron runs with a minimal `$PATH`. Always redirect stdout and stderr to a log file so you can debug when jobs fail silently.

---

## 18. What is Environment Variable and PATH?

**Environment variables** are dynamic named values stored in the shell's environment that can affect the behavior of running processes.

Every process inherits its parent's environment variables. They're key-value pairs, like a configuration layer for the OS and applications.

```bash
# View all environment variables
env
printenv

# View a specific variable
echo $HOME
echo $USER
echo $SHELL
echo $PATH

# Set a variable for current session only
export MY_VAR="hello"
echo $MY_VAR

# Make it permanent — add to ~/.bashrc or ~/.zshrc
echo 'export MY_VAR="hello"' >> ~/.bashrc
source ~/.bashrc   # reload without restarting terminal

# Unset a variable
unset MY_VAR

# Pass env variable to a single command
DB_HOST=localhost node server.js
```

### The `$PATH` Variable

`$PATH` is a special environment variable that lists directories the shell searches when you type a command, in order, left to right.

```bash
echo $PATH
# output: /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/home/user/.local/bin

# When you type `python3`, the shell looks in each directory until it finds it
which python3
# output: /usr/bin/python3
```

```bash
# Add a directory to PATH (current session)
export PATH="$PATH:/opt/my-tools/bin"

# Add permanently
echo 'export PATH="$PATH:/opt/my-tools/bin"' >> ~/.bashrc
source ~/.bashrc

# Prepend (highest priority — found first)
export PATH="/my/custom/bin:$PATH"
```

**Common environment variables:**
```bash
$HOME        # /home/username — your home directory
$USER        # current username
$SHELL       # /bin/bash — your shell
$PATH        # directories to search for commands
$EDITOR      # default text editor (vim, nano, code)
$LANG        # locale setting (en_US.UTF-8)
$PWD         # current working directory
$TERM        # terminal type
```

> **Interview insight:** Environment variables are how you pass configuration to applications without hardcoding it — think DB passwords, API keys, environment names (`NODE_ENV=production`). Never commit `.env` files with secrets to Git.

---

## 19. Explain fork() and exec()

`fork()` and `exec()` are the two fundamental system calls that create new processes in Unix. They're almost always used together.

### `fork()`
Creates an **exact copy** of the calling process. Both parent and child continue execution from the next line after `fork()`.

```
Before fork():   Parent Process
                 (PID 100)

After fork():    Parent Process   +   Child Process
                 (PID 100)            (PID 101)
                 returns 101          returns 0
```

### `exec()`
**Replaces** the current process image with a new program. The PID stays the same, but the memory, code, and stack are completely replaced.

```c
#include <stdio.h>
#include <unistd.h>

int main() {
    pid_t pid = fork();

    if (pid == 0) {
        // Child process — pid returned is 0
        printf("Child PID: %d\n", getpid());
        execl("/bin/ls", "ls", "-la", NULL);   // replace child with 'ls'
        // Nothing after execl() runs if exec succeeds
    } else {
        // Parent process — pid returned is child's PID
        printf("Parent PID: %d, Child PID: %d\n", getpid(), pid);
        wait(NULL);   // wait for child to finish
    }

    return 0;
}
```

```bash
# How your shell uses fork + exec for every command:
# 1. You type: ls -la
# 2. Shell calls fork() → creates child shell
# 3. Child calls exec("/bin/ls", ["-la"])
# 4. Child is now the ls process, runs, prints output, exits
# 5. Parent (shell) resumes, shows prompt
```

**`fork()` return values:**
- Returns `0` in the **child** process
- Returns `child's PID` in the **parent**
- Returns `-1` on failure

**exec() family:**
```c
execl()   // list of arguments
execv()   // array of arguments
execle()  // list + environment
execve()  // array + environment (the underlying syscall)
execlp()  // list + searches PATH
execvp()  // array + searches PATH
```

> **Why separate fork and exec instead of one call?** It gives you a window between fork and exec to set up redirections, close file descriptors, set user IDs, and configure the child environment before the new program starts — that's how shell pipes and redirections are implemented.

---

## 20. Production server is down — how will you debug?

This is the ultimate SRE/DevOps question. The interviewer wants to see a **systematic, calm, methodical approach** — not panic.

> **First rule: Don't make it worse. Always understand before you act.**

### Phase 1 — Triage (first 60 seconds)

```bash
# Is the server reachable at all?
ping production-server
ssh user@production-server

# If SSH works, check uptime and load immediately
uptime
# output: 14:32  up 42 days, load average: 45.3, 42.1, 38.2
# load > number of CPUs = overloaded

w             # who's logged in, what are they doing
last reboot   # when did it last reboot?
```

### Phase 2 — What's the symptom?

```bash
# Check all running services
systemctl status
systemctl --failed   # list failed services

# Check the specific service
systemctl status nginx
systemctl status myapp

# View recent logs
journalctl -xe                   # all system logs, with explanations
journalctl -u nginx --since "1 hour ago"
tail -f /var/log/nginx/error.log
tail -f /var/log/syslog
```

### Phase 3 — Resource exhaustion check

```bash
# CPU
top                              # press P to sort by CPU
htop
ps aux --sort=-%cpu | head -10

# Memory
free -h
ps aux --sort=-%mem | head -10
dmesg | grep -i "oom"            # OOM killer fired?

# Disk — VERY common cause of outages
df -h                            # disk space
df -i                            # inode exhaustion (sneaky!)
du -sh /var/log/* | sort -rh | head -10   # find large files

# Disk I/O
iostat -x 1 5
iotop                            # which process is hammering the disk

# Network
netstat -tuln                    # which ports are listening?
netstat -an | grep ESTABLISHED | wc -l   # connection count
ss -s                            # socket summary
```

### Phase 4 — Application-level checks

```bash
# Check HTTP response
curl -I http://localhost
curl -v https://production.example.com

# Check error logs with timestamps
grep "$(date +'%Y/%m/%d')" /var/log/nginx/error.log | tail -50

# Check application logs
tail -100 /var/log/myapp/app.log | grep -i "error\|exception\|fatal"

# Check if app is listening on its port
lsof -i :8080
ss -tlnp | grep 8080
```

### Phase 5 — Recent changes

```bash
# What changed recently?
git log --oneline -10            # recent deploys
rpm -qa --last | head            # recently installed packages
apt list --installed | grep ...

# Recently modified files (last 24 hours)
find /etc /opt /var/www -mtime -1 -type f

# Check deployment logs
cat /var/log/deploy.log

# Check cron for recent jobs
grep CRON /var/log/syslog | tail -20
```

### Phase 6 — Restore service, then investigate

```bash
# Restart the service
systemctl restart nginx
systemctl restart myapp

# If a runaway process is killing the server
kill -9 <offending_PID>

# If disk is full — quick relief
# Find and remove old logs
find /var/log -name "*.log" -mtime +30 -delete
# Clear journal logs older than 3 days
journalctl --vacuum-time=3d

# Roll back a bad deployment
git revert HEAD
./deploy.sh
```

### Systematic Debugging Checklist

```
□ Can I SSH in?
□ uptime / load average
□ systemctl --failed
□ df -h (disk full?) and df -i (inodes?)
□ free -h (RAM/swap exhausted?)
□ dmesg | grep oom (OOM killer?)
□ top / ps (runaway process?)
□ Service logs (tail -f, journalctl)
□ Application logs (errors, exceptions)
□ Network (ports listening? connections exhausted?)
□ What changed recently? (deploy, cron, config)
□ Restore → then do proper RCA
```

> **Interview answer summary:** Triage first (is it reachable?), check resources (CPU/RAM/disk), check service status and logs, check recent changes, restore service with the least risky action, then write a post-mortem. Always fix the symptom first, understand the root cause second — unless investigation can be done in under 2 minutes.

---

## Quick Reference Cheatsheet

```bash
# ── Process Management ─────────────────────────
ps aux                    # list all processes
top / htop                # real-time process monitor
kill <PID>                # graceful kill (SIGTERM)
kill -9 <PID>             # force kill (SIGKILL)
pkill <name>              # kill by process name
pgrep <name>              # find PID by name
nice -n 10 command        # run with lower priority
nohup command &           # run immune to hangups

# ── File & Permissions ─────────────────────────
ls -la                    # list with permissions + inodes
stat file                 # full inode info
chmod 755 file            # set permissions
chown user:group file     # change owner
ln file hardlink          # create hard link
ln -s file symlink        # create soft link
find / -name "*.log"      # find files
grep -r "error" ./logs    # search file content

# ── Disk & Memory ──────────────────────────────
df -h                     # disk space
df -i                     # inode usage
du -sh *                  # directory sizes
free -h                   # RAM and swap
vmstat 1                  # memory/swap stats
iostat -x 1               # disk I/O stats

# ── Network ────────────────────────────────────
ss -tuln                  # listening ports
netstat -an               # all connections
curl -I https://url       # HTTP headers
ping host                 # connectivity check
traceroute host           # network path
nmap -p 80,443 host       # port scan
ssh -L 8080:localhost:80 user@host  # tunnel

# ── Logs ───────────────────────────────────────
journalctl -xe            # system logs
journalctl -u nginx       # service logs
tail -f /var/log/syslog   # real-time log
dmesg | tail              # kernel messages
grep -i error /var/log/*  # search logs

# ── Cron ───────────────────────────────────────
crontab -e                # edit cron jobs
crontab -l                # list cron jobs
systemctl status cron     # cron daemon status

# ── Redirection & Pipes ────────────────────────
command > file            # overwrite to file
command >> file           # append to file
command 2> err.log        # stderr to file
command &> all.log        # stdout + stderr
cmd1 | cmd2               # pipe output to input
command > /dev/null 2>&1  # silence all output
```

---

## Resources

- 📖 [Linux man pages](https://man7.org/linux/man-pages/)
- 🎓 [The Linux Command Line (Free Book)](https://linuxcommand.org/tlcl.php)
- 🧪 [OverTheWire: Bandit (Linux CTF)](https://overthewire.org/wargames/bandit/)
- 🐧 [Linux Journey (Interactive)](https://linuxjourney.com/)
- 🔧 [Explain Shell — break down any command](https://explainshell.com/)
- 📘 [TLDR Pages — simplified man pages](https://tldr.sh/)

---

*Made with ❤️ for interview preparation. Own the terminal. 🚀*
