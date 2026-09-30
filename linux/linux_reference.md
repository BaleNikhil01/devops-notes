# Day 1 — Linux Reference

## File & Directory Operations
* **`pwd`** — Print current working directory path.
* **`ls`** — List directory contents (`ls -la` for hidden files & details).
* **`cd`** — Change directory (`cd ~` for home, `cd -` for previous directory).
* **`mkdir`** — Create new directory (`mkdir -p path/to/dir` to build nested trees).
* **`touch`** — Create an empty file or update existing file timestamp.
* **`cp`** — Copy files/directories (`cp -r` for recursive folder copy).
* **`mv`** — Move or rename files and directories.
* **`rm`** — Remove files/directories (`rm -rf` to force recursive delete).

## File Viewing & Text Processing
* **`cat`** — Print entire file contents to terminal.
* **`less`** — View file page-by-page with interactive navigation (`q` to quit).
* **`head`** — View first N lines of a file (`head -n 20 file.txt`).
* **`tail`** — View last N lines of a file (`tail -f file.log` to stream live updates).
* **`grep`** — Search text patterns using regex (`grep -rn "error" /var/log/`).
* **`find`** — Search filesystem by filetypes (`find /var -name "*.log" -mtime -1`).
* **`locate`** — Fast file search using a pre-built index database (`updatedb` to sync).

## Permissions & Ownership
* **`chmod`** — Change file read/write/execute modes (`chmod 755 script.sh` or `chmod +x`).
* **`chown`** — Change user/group ownership (`chown user:group file.txt`).

## Process & Resource Monitoring
* **`ps`** — Snapshot of running processes (`ps aux` or `ps -ef`).
* **`top`** — Real-time process viewer and system resource utilization.
* **`kill`** — Send signal to terminate process by PID (`kill -9 PID` for forced kill).
* **`df`** — Display disk space usage per filesystem (`df -h` for human-readable).
* **`du`** — Summarize disk usage of file/directory (`du -sh *`).
* **`free`** — Show total, used, and available RAM/swap memory (`free -h`).

---

### Quick Summary
* **Need Processes?** `ps` (snapshot) vs `top` (live stream)
* **Need Disk Space?** `df` (entire drive capacity) vs `du` (folder size)
* **Need Memory?** `free` (RAM utilization)

---

## Networking & Transfer
* **`ip`** — Manage network interfaces/routing (`ip a` for addresses, `ip r` for routes).
* **`ping`** — Test network connectivity/latency to a destination host.ping uses ICMP.
* **`curl`** — curl is commonly used to make HTTP requests and test APIs/endpoints.Useful for checking whether an application is responding.
* **`wget`** — Non-interactive downloader commonly used to download large files from URLs to local disk.
* **`ssh`** — Connect securely to remote host (`ssh -i key.pem user@host`).

## Services and Logs 
* **'systemctl'** - Used to manage services controlled by systemd.(status/start/stop/restart/enable) eg. 'systemctl status nginx'
* **'journalctl'** - Used to view logs collected by systemd. eg. 'journalctl -u nginx'
  
---

## Core Q&A

* **What is the difference between `head`, `tail`, and `cat`?**
  * `cat` outputs the entire file at once. `head` displays the first N lines, while `tail` displays the last N lines (and can stream live updates using `-f`).

* **What does `chmod 755 script.sh` mean?**
  * Sets permissions to `rwxr-xr-x`: Owner gets Read/Write/Execute ($4+2+1=7$), while Group and Others get Read/Execute ($4+1=5$).

* **How do you recursively change the ownership of a folder to `appuser` and group `appgroup`?**
  * `chown -R appuser:appgroup /path/to/folder`

* **What is the difference between `curl` and `wget`?**
  * `curl` is a tool to transfer data to/from servers supporting various protocols and outputs to stdout by default. `wget` is primarily a non-interactive file downloader that saves files directly to disk.

* **`grep` vs `find`**
  * **`grep`:** Searches inside files for text patterns/content (e.g., `grep -rn "ERROR" /var/log/`).
  * **`find`:** Searches filesystem for files/folders by name, size, owner, or date metadata (e.g., `find /var/log -name "*.log" -mtime -1`).
  * **Rule of Thumb:** Use `find` to locate the file, and `grep` to read what's inside it.

* **What is the difference between a process and a service?**
* Process → is a running instance of a program.(python app.py) Once running, Linux creates a process with a PID.
* Service → A long-running background process managed by the OS/service manager. A service usually has lifecycle management such as start, stop, restart and boot startup. (systemctl status nginx)

*  What is 'PATH' **  PATH tells Linux which directories to search when you execute a command. 'echo $PATH' 

*   Package management
   *  How do you install software on Ubuntu? --> using **'apt'** (apt update- It refreshes the local package index so apt knows about the latest package/upgrade-Upgrade installed packages/install/remove)
     
*  '/etc' - Contains system and application configuration files. troubleshooting frequently involves /etc /etc/ssh/ /etc/nginx/ 
*   '/var' - Contains variable data such as logs, caches  /var/log/ 

* ** Need revision --> permission and ownership/grep vs find / process vs service **
