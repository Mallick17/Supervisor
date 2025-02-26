# Supervisor
**System for Controlling Process State.**
- Supervisor is a system for controlling and maintaining process state, similar to what init does, but not intended as an init replacement.
- It will manage individual processes or groups of processes that need to be started and stopped in order, and it is possible to control individual process state via an rpc mechanism, thus allowing ordinary users to restart processes.

## Running Supervisor
- To run `supervisord` and `supervisorctl`, you need to know your Python installation's `BINDIR` (binary directory). For example, if Python is installed with `--prefix=/usr/local/py`, the `BINDIR` would be `/usr/local/py/bin`. Check the output of `setup.py install` if you're unsure of your `BINDIR`.

- **Adding a Program**
  - To use `supervisord`, you must add at least one program to its configuration file `supervisord.conf`. A program section defines a command that `supervisord` will manage.
  - For example, to run the UNIX `cat` command, add this to `supervisord.conf`:  
```ini
[program:foo]
command=/bin/cat
```  
- This is the simplest configuration, but there are many other options available for program sections.

### Running supervisord
- To start **supervisord**, run `$BINDIR/supervisord`. It runs in the background by default and logs to `$CWD/supervisor.log`. Use the `-n` flag to run it in the foreground for debugging.
- **Warning**: Supervisord looks for its config file in default locations, including the current directory. To avoid security risks, use the `-c` flag to specify an absolute path to the config file, especially when running as root.
- To update the programs managed by supervisord, edit `supervisord.conf` and restart supervisord (e.g., `kill -HUP`). Command-line options override settings in the config file.

**supervisord Command-Line Options**
<details>
     <summary>Click to view supervisord Command-Line Options</summary>



| **Option**                     | **Description**                                                                 | **Example Usage**                                                                 |
|---------------------------------|---------------------------------------------------------------------------------|-----------------------------------------------------------------------------------|
| `-c FILE`, `--configuration=FILE` | Path to the supervisord configuration file.                                     | `supervisord -c /etc/supervisord.conf`                                           |
| `-n`, `--nodaemon`              | Run supervisord in the foreground (not as a daemon).                            | `supervisord -n`                                                                 |
| `-s`, `--silent`                | Suppress output to stdout.                                                      | `supervisord -s`                                                                 |
| `-h`, `--help`                  | Display help information for supervisord commands.                              | `supervisord -h`                                                                 |
| `-u USER`, `--user=USER`        | Set the UNIX user (username or numeric ID) to run supervisord as.               | `supervisord -u myuser`                                                          |
| `-m OCTAL`, `--umask=OCTAL`     | Set the umask for supervisord (e.g., 022).                                      | `supervisord -m 022`                                                             |
| `-d PATH`, `--directory=PATH`   | Change to this directory before daemonizing.                                    | `supervisord -d /var/www`                                                        |
| `-l FILE`, `--logfile=FILE`     | Specify the path for the supervisord activity log file.                         | `supervisord -l /var/log/supervisord.log`                                        |
| `-y BYTES`, `--logfile_maxbytes=BYTES` | Set the maximum size of the log file before rotation (e.g., 1MB, 1GB).      | `supervisord -y 10MB`                                                            |
| `-z NUM`, `--logfile_backups=NUM` | Number of backup log files to keep.                                           | `supervisord -z 5`                                                               |
| `-e LEVEL`, `--loglevel=LEVEL`  | Set the logging level (trace, debug, info, warn, error, critical).              | `supervisord -e debug`                                                           |
| `-j FILE`, `--pidfile=FILE`     | Specify the file to store the supervisord process ID (PID).                     | `supervisord -j /var/run/supervisord.pid`                                        |
| `-i STRING`, `--identifier=STRING` | Set an identifier for this supervisord instance.                              | `supervisord -i mysupervisor`                                                    |
| `-q PATH`, `--childlogdir=PATH` | Directory for storing child process logs (must exist).                          | `supervisord -q /var/log/supervisor/childlogs`                                   |
| `-k`, `--nocleanup`             | Prevent cleanup of old AUTO process log files at startup.                       | `supervisord -k`                                                                 |
| `-a NUM`, `--minfds=NUM`        | Minimum number of file descriptors required for supervisord to start.           | `supervisord -a 1024`                                                            |
| `-t`, `--strip_ansi`            | Strip ANSI escape sequences from child process logs.                            | `supervisord -t`                                                                 |
| `-v`, `--version`               | Display the supervisord version number.                                         | `supervisord -v`                                                                 |
| `--profile_options=LIST`        | Run supervisord under a profiler with options (e.g., cumulative, calls, callers). | `supervisord --profile_options=cumulative,callers`                             |
| `--minprocs=NUM`                | Minimum number of OS process slots required for supervisord to start.            | `supervisord --minprocs=50`                                                       |

### Example Scenarios:
1. **Running with a Custom Configuration File**:
   - Command: `supervisord -c /etc/my_supervisord.conf`
   - Explanation: This starts supervisord using a custom configuration file located at `/etc/my_supervisord.conf`.

2. **Running in Foreground with Debug Logging**:
   - Command: `supervisord -n -e debug`
   - Explanation: This runs supervisord in the foreground with debug-level logging for troubleshooting.

3. **Setting Log File Rotation**:
   - Command: `supervisord -l /var/log/supervisord.log -y 10MB -z 3`
   - Explanation: This sets the log file to rotate when it reaches 10MB, keeping 3 backup copies.

4. **Running as a Specific User**:
   - Command: `supervisord -u myuser`
   - Explanation: This starts supervisord as the user `myuser`.

5. **Preventing Log Cleanup**:
   - Command: `supervisord -k`
   - Explanation: This prevents supervisord from cleaning up old AUTO process log files at startup.
     
</details> 

### Running supervisorctl
- To use **supervisorctl**, run `$BINDIR/supervisorctl`. This opens a shell to control processes managed by **supervisord**. Type `help` for a list of commands.
- You can also run one-time commands directly, like `supervisorctl stop all`. If arguments are provided, it skips the interactive shell, executes the command, and exits with `0` for success or a non-zero code for errors. For example, `supervisorctl status all` returns non-zero if any process isn’t running.
- If **supervisord** requires authentication, you’ll be prompted for credentials in interactive mode.
  
**supervisorctl Command-Line Options**
- Control Applications run by supervisord from the cmd line.
```sh
root@ip-172-31-12-132:/etc/supervisor/conf.d# supervisorctl -h
supervisorctl -- control applications run by supervisord from the cmd line.

Usage: /usr/bin/supervisorctl [options] [action [arguments]]

Options:

-c/--configuration FILENAME -- configuration file path (searches if not given)
-h/--help -- print usage message and exit
-i/--interactive -- start an interactive shell after executing commands
-s/--serverurl URL -- URL on which supervisord server is listening
     (default "http://localhost:9001").
-u/--username USERNAME -- username to use for authentication with server
-p/--password PASSWORD -- password to use for authentication with server
-r/--history-file -- keep a readline history (if readline is available)

action [arguments] -- see below
```
- Actions are commands like "tail" or "stop".  If -i is specified or no action is specified on the command line, a "shell" interpreting actions typed interactively is started.  Use the action "help" to find out about available actions.

<details>
     <summary>Click to view detaild information on the supervisorctl Command-Line options with example</summary>


| **Command**               | **Description**                                                                 | **Example**                                                                 |
|---------------------------|---------------------------------------------------------------------------------|-----------------------------------------------------------------------------|
| `supervisorctl -c FILENAME` | Specify the configuration file path.                                           | `supervisorctl -c /etc/supervisor/supervisord.conf`                        |
| `supervisorctl -h`         | Print the help message and exit.                                               | `supervisorctl -h`                                                         |
| `supervisorctl -i`         | Start an interactive shell after executing commands.                           | `supervisorctl -i`                                                         |
| `supervisorctl -s URL`     | Specify the URL on which supervisord server is listening.                      | `supervisorctl -s http://localhost:9001`                                   |
| `supervisorctl -u USERNAME`| Specify the username for authentication with the server.                       | `supervisorctl -u admin`                                                   |
| `supervisorctl -p PASSWORD`| Specify the password for authentication with the server.                       | `supervisorctl -p password123`                                             |
| `supervisorctl -r`         | Keep a readline history (if readline is available).                            | `supervisorctl -r`                                                         |
| `supervisorctl status`     | Check the status of all managed processes.                                     | `supervisorctl status`                                                     |
| `supervisorctl start <process>` | Start a specific process.                                                | `supervisorctl start my_app`                                               |
| `supervisorctl stop <process>`  | Stop a specific process.                                                | `supervisorctl stop my_app`                                                |
| `supervisorctl restart <process>` | Restart a specific process.                                             | `supervisorctl restart my_app`                                             |
| `supervisorctl reread`     | Reload the configuration files without restarting processes.                   | `supervisorctl reread`                                                     |
| `supervisorctl update`     | Update the process group and start/stop processes as necessary.               | `supervisorctl update`                                                     |
| `supervisorctl reload`     | Restart the supervisord service and reload the configuration.                  | `supervisorctl reload`                                                     |
| `supervisorctl shutdown`   | Shut down the supervisord service.                                             | `supervisorctl shutdown`                                                   |

### Example Usage:
1. **Check the status of all processes:**
   ```bash
   supervisorctl status
   ```

2. **Start a specific process:**
   ```bash
   supervisorctl start my_app
   ```

3. **Restart a specific process:**
   ```bash
   supervisorctl restart my_app
   ```

4. **Reload the configuration:**
   ```bash
   supervisorctl reread
   supervisorctl update
   ```

5. **Shut down supervisord:**
   ```bash
   supervisorctl shutdown
   ```

</details>

**supervisorctl Actions**
```bash
root@ip-172-31-12-132:/etc/supervisor/conf.d# supervisorctl
nginx:nginx_00                   RUNNING   pid 26350, uptime 0:00:00
supervisor> help

default commands (type help <topic>):
=====================================
add    exit      open  reload  restart   start   tail
avail  fg        pid   remove  shutdown  status  update
clear  maintail  quit  reread  signal    stop    version
```

<details>
     <summary>Click to view detaild information on the supervisorctl Actions with example</summary>

| **Command**  | **Description**                                                                 | **Example**                                      |
|--------------|---------------------------------------------------------------------------------|--------------------------------------------------|
| `add`        | Add a process/group to the supervisor control.                                   | `add nginx:nginx_00`                             |
| `exit`       | Exit the supervisorctl interactive shell.                                        | `exit`                                           |
| `open`       | Connect to a remote supervisor process.                                          | `open 172.31.12.132`                             |
| `reload`     | Restart the supervisor process and reload configurations.                        | `reload`                                         |
| `restart`    | Restart a specific process or group.                                             | `restart nginx:nginx_00`                         |
| `start`      | Start a specific process or group.                                               | `start nginx:nginx_00`                           |
| `tail`       | Display the log of a specific process.                                           | `tail nginx:nginx_00`                            |
| `avail`      | List all available programs/groups.                                              | `avail`                                          |
| `fg`         | Bring a process to the foreground (interactive mode).                            | `fg nginx:nginx_00`                              |
| `pid`        | Display the PID of a specific process.                                           | `pid nginx:nginx_00`                             |
| `remove`     | Remove a process/group from the supervisor control.                              | `remove nginx:nginx_00`                          |
| `shutdown`   | Shut down the supervisor process and all its child processes.                    | `shutdown`                                       |
| `status`     | Display the status of all processes/groups.                                      | `status`                                         |
| `update`     | Reload configurations and start/stop processes as necessary.                     | `update`                                         |
| `clear`      | Clear the log of a specific process.                                             | `clear nginx:nginx_00`                           |
| `maintail`   | Display the supervisor main log file.                                            | `maintail`                                       |
| `quit`       | Quit the supervisorctl interactive shell.                                        | `quit`                                           |
| `reread`     | Reload configuration files without restarting processes.                         | `reread`                                         |
| `signal`     | Send a signal to a specific process (e.g., STOP, KILL).                          | `signal TERM nginx:nginx_00`                     |
| `stop`       | Stop a specific process or group.                                                | `stop nginx:nginx_00`                            |
| `version`    | Display the version of the supervisor.                                           | `version`                                        |

This table provides a quick reference for supervisorctl commands and their usage.

</details>

## Understanding Signals and Signal Handlers in Supervisord
Supervisord is a process control system that allows you to manage and monitor multiple processes. It can respond to various **signals** sent to it, which trigger specific actions. These signals are a way to communicate with the `supervisord` process to perform tasks like shutting down, reloading configurations, or reopening log files.

### How to Send Signals to Supervisord
- To send a signal to `supervisord`, you need its **process ID (PID)**. This PID is stored in a file, typically located at `$CWD/supervisord.pid` (unless you've configured a different path in the `[supervisord]` section of the configuration file).
- You can send a signal using the `kill` command in Unix-like systems:

```bash
kill -<SIGNAL> <PID>
```
- `<SIGNAL>`: The signal you want to send (e.g., `SIGTERM`, `SIGHUP`, etc.).
- `<PID>`: The process ID of `supervisord`.

---

<details>
     <summary>Click to view Signal Handlers in Supervisord</summary>

### Signal Handlers in Supervisord

Here’s a breakdown of the signals and their effects on `supervisord`:

#### 1. **SIGTERM**
- **Action**: Gracefully shuts down `supervisord` and all its subprocesses.
- **Timeframe**: This may take several seconds as it ensures all processes are properly terminated.
- **Example**:
  ```bash
  kill -SIGTERM $(cat /path/to/supervisord.pid)
  ```

#### 2. **SIGINT**
- **Action**: Similar to `SIGTERM`, it gracefully shuts down `supervisord` and all its subprocesses.
- **Timeframe**: This may also take several seconds.
- **Example**:
  ```bash
  kill -SIGINT $(cat /path/to/supervisord.pid)
  ```

#### 3. **SIGQUIT**
- **Action**: Gracefully shuts down `supervisord` and all its subprocesses.
- **Timeframe**: This may take several seconds.
- **Example**:
  ```bash
  kill -SIGQUIT $(cat /path/to/supervisord.pid)
  ```

#### 4. **SIGHUP**
- **Action**: 
  - Stops all processes managed by `supervisord`.
  - Reloads the configuration from the first config file it finds.
  - Restarts all processes based on the new configuration.
- **Use Case**: Useful when you’ve made changes to the configuration file and want to apply them without fully restarting `supervisord`.
- **Example**:
  ```bash
  kill -SIGHUP $(cat /path/to/supervisord.pid)
  ```

#### 5. **SIGUSR2**
- **Action**: Closes and reopens the main activity log and all child log files.
- **Use Case**: Useful for log rotation, where you want `supervisord` to start writing to new log files without interrupting the processes.
- **Example**:
  ```bash
  kill -SIGUSR2 $(cat /path/to/supervisord.pid)
  ```
---

### Practical Example: Reloading Configuration

Suppose you’ve updated your `supervisord.conf` file and want to apply the changes without restarting `supervisord`. You can use the `SIGHUP` signal:

1. Update your configuration file (`supervisord.conf`).
2. Send the `SIGHUP` signal:
   ```bash
   kill -SIGHUP $(cat /path/to/supervisord.pid)
   ```
3. `supervisord` will reload the configuration and restart all processes accordingly.

---
### Practical Example: Log Rotation

If you’re rotating logs and want `supervisord` to start writing to new log files, use the `SIGUSR2` signal:

1. Rotate the logs (e.g., using `logrotate`).
2. Send the `SIGUSR2` signal:
   ```bash
   kill -SIGUSR2 $(cat /path/to/supervisord.pid)
   ```
3. `supervisord` will close the current log files and reopen them, ensuring logs are written to the new files.

---

</details>

### Summary Table of Signals

| Signal   | Action                                                                 | Example Command                                      |
|----------|-----------------------------------------------------------------------|------------------------------------------------------|
| SIGTERM  | Shuts down `supervisord` and all subprocesses gracefully.             | `kill -SIGTERM $(cat /path/to/supervisord.pid)`      |
| SIGINT   | Shuts down `supervisord` and all subprocesses gracefully.             | `kill -SIGINT $(cat /path/to/supervisord.pid)`       |
| SIGQUIT  | Shuts down `supervisord` and all subprocesses gracefully.             | `kill -SIGQUIT $(cat /path/to/supervisord.pid)`      |
| SIGHUP   | Reloads configuration and restarts all processes.                     | `kill -SIGHUP $(cat /path/to/supervisord.pid)`       |
| SIGUSR2  | Reopens the main activity log and all child log files.                | `kill -SIGUSR2 $(cat /path/to/supervisord.pid)`      |

---
## 1. Installation of Supervisor

### Update the Package List
Before installing Supervisor, ensure your package list is up to date:
```bash
sudo apt update
```

### Install Supervisor
Install Supervisor using the following command:
```bash
sudo apt install supervisor -y
```

### Verify Installation
Check the installed version of Supervisor to confirm the installation:
```bash
supervisord -v
```

### Check Supervisor Service Status
Ensure the Supervisor service is running:
```bash
sudo systemctl status supervisor
```

---

## 2. Basic Configuration of Supervisor

### Edit the Supervisor Configuration File
Open the main Supervisor configuration file for editing:
```bash
sudo nano /etc/supervisor/supervisord.conf
```

---

## 3. Setting Up the Web Interface

### Enable the Web Interface
Add the following configuration to `/etc/supervisor/supervisord.conf` to enable the Supervisor web interface:
```ini
[inet_http_server]
port=0.0.0.0:9001
username=admin
password=admin
```
- **port**: Specifies the IP address and port for the web interface. `0.0.0.0:9001` allows access from any IP address.
- **username** and **password**: Set the login credentials for the web interface.

<details>
   <Summary>Click to view Entire `supervisord.conf` Configuration File</Summary>

```bash
root@ip-172-31-12-132:/etc/supervisor# cat supervisord.conf
; supervisor config file

[unix_http_server]
file=/var/run/supervisor.sock   ; (the path to the socket file)
chmod=0700                       ; sockef file mode (default 0700)

[inet_http_server]
port=0.0.0.0:9001
username=mallick
password=mallick

[supervisord]
logfile=/var/log/supervisor/supervisord.log ; (main log file;default $CWD/supervisord.log)
pidfile=/var/run/supervisord.pid ; (supervisord pidfile;default supervisord.pid)
childlogdir=/var/log/supervisor            ; ('AUTO' child log dir, default $TEMP)

; the below section must remain in the config file for RPC
; (supervisorctl/web interface) to work, additional interfaces may be
; added by defining them in separate rpcinterface: sections
[rpcinterface:supervisor]
supervisor.rpcinterface_factory = supervisor.rpcinterface:make_main_rpcinterface

[supervisorctl]
serverurl=unix:///var/run/supervisor.sock ; use a unix:// URL  for a unix socket

; The [include] section can just contain the "files" setting.  This
; setting can list multiple files (separated by whitespace or
; newlines).  It can also contain wildcards.  The filenames are
; interpreted as relative to this file.  Included files *cannot*
; include files themselves.

[include]
files = /etc/supervisor/conf.d/*.conf
```

</details>

---
### Apply Configuration Changes
After editing the configuration file, reload Supervisor to apply the changes:
```bash
sudo supervisorctl reread
sudo supervisorctl update
```

### Restart Supervisor Service
Restart the Supervisor service to ensure the changes take effect:
```bash
sudo systemctl restart supervisor
```

### Access the Supervisor Web Interface
Open a web browser and navigate to `http://<your-server-ip>:9001`. Use the credentials (`admin/admin`) to log in and view the status of managed processes.

---

## 4. Managing Nginx with Supervisor

### Install Nginx
Install Nginx using the following command:
```bash
sudo apt install nginx -y
```

### Stop and Disable Nginx Service
Since Supervisor will manage Nginx, stop and disable the default Nginx service:
```bash
sudo systemctl stop nginx
sudo systemctl disable nginx
```

### Create a Supervisor Configuration for Nginx
Create a new configuration file for Nginx in the Supervisor configuration directory:
```bash
sudo nano /etc/supervisor/conf.d/nginx.conf
```

Add the following configuration to the file:
```ini
[program:nginx]
command=/usr/sbin/nginx -c /etc/nginx/nginx.conf
autostart=true
autorestart=true
startretries=5
numprocs=1
startsecs=0
process_name=%(program_name)s_%(process_num)02d
stderr_logfile=/var/log/supervisor/%(program_name)s_stderr.log
stderr_logfile_maxbytes=10MB
stdout_logfile=/var/log/supervisor/%(program_name)s_stdout.log
stdout_logfile_maxbytes=10MB
```
- **command**: Specifies the command to start Nginx.
- **autostart**: Ensures Nginx starts automatically with Supervisor.
- **autorestart**: Automatically restarts Nginx if it fails.
- **stderr_logfile** and **stdout_logfile**: Define log files for error and output logs.

### Apply Nginx Configuration
Reload Supervisor to apply the new Nginx configuration:
```bash
sudo supervisorctl reread
sudo supervisorctl update
```

### Manage Nginx via Supervisor
Use the `supervisorctl` command to manage Nginx:
```bash
sudo supervisorctl
```
- Stop Nginx:
  ```bash
  stop nginx:nginx_00
  ```
- Start Nginx:
  ```bash
  start nginx:nginx_00
  ```
- Exit the Supervisor control interface:
  ```bash
  exit
  ```

---

## 5. Verification and Testing
### Verify Nginx Processes
Check if Nginx is running under Supervisor:
```bash
ps aux | grep nginx
```
### Access the Supervisor Web Interface
- Open a web browser and navigate to `http://<your-server-ip>:9001`. Use the credentials (`admin/admin`) to log in and view the status of managed processes.
---
## 6. Troubleshooting
### Check Supervisor Logs
If issues arise, check the Supervisor logs for errors:
```bash
sudo tail -f /var/log/supervisor/supervisord.log
```
### Check Nginx Logs
Review Nginx logs for any errors:
```bash
sudo tail -f /var/log/nginx/error.log
```
### Verify Configuration Files
Ensure there are no syntax errors in the configuration files:
```bash
sudo supervisorctl reread
sudo supervisorctl update
```
---
## Configuration File
The Supervisor configuration file is conventionally named `supervisord.conf`. It is used by both supervisord and supervisorctl. If either application is started without the `-c` option (the option which is used to tell the application the configuration filename explicitly), the application will look for a file named `supervisord.conf` within the following locations, in the specified order. It will use the first file it finds.
### File Format
`supervisord.conf` is a Windows-INI-style (Python ConfigParser) file. It has sections (each denoted by a `[header]`) and key / value pairs within the sections. The sections and their allowable values are described below.
### Environment Variables
- Environment variables that are present in the environment at the time that supervisord is started can be used in the configuration file using the Python string expression syntax `%(ENV_X)s`:
```ini
[program:example]
command=/usr/bin/example --loglevel=%(ENV_LOGLEVEL)s
```
- In the example above, the expression %(ENV_LOGLEVEL)s would be expanded to the value of the environment variable LOGLEVEL. In Supervisor 3.2 and later, %(ENV_X)s expressions are supported in all options.

## Section Settings
### **Supervisor Configuration Sections Overview**  

### [unix_http_server] 
- This section configures an HTTP server that listens on a UNIX domain socket. If this section is missing, the HTTP server won't start.
```ini
[unix_http_server]
file = /tmp/supervisor.sock   ## Specifies the socket file location for communication.  
chmod = 0777   ## Sets the permission of the socket file (read/write for everyone).  
chown = nobody:nogroup   ## Defines the owner and group of the socket file.  
username = user   ## Sets a username for authentication.  
password = 123   ## Sets a password for authentication.
```
---
### [inet_http_server]  
- This section configures an HTTP server that listens on a TCP (internet) socket. If this section is missing, the HTTP server won't start.  
```ini
[inet_http_server] 
port = 127.0.0.1:9001   ## Defines the IP address and port for the HTTP server.  
username = user   ## Sets a username for authentication.  
password = 123   ## Sets a password for authentication.  
```
---
### [supervisord]  
- This section defines global settings for the supervisord process.  
```ini
[supervisord]
logfile = /tmp/supervisord.log   ## Specifies the path to the supervisord log file.  
logfile_maxbytes = 50MB   ## Sets the maximum size of the log file before rotation.  
logfile_backups = 10   ## Limits the number of rotated log backups.  
loglevel = info   ## Defines the logging level (debug, info, warn, error, critical).  
pidfile = /tmp/supervisord.pid   ## Stores the process ID (PID) of supervisord.  
nodaemon = false   ## Runs supervisord as a daemon (background process).  
minfds = 1024   ## Sets the minimum number of file descriptors available.  
minprocs = 200   ## Sets the minimum number of process descriptors available.  
umask = 022   ## Defines the file permission mask for new files.  
user = chrism   ## Runs supervisord as the specified user.  
identifier = supervisor   ## Names the supervisord instance for identification.  
directory = /tmp   ## Sets the working directory for supervisord.  
nocleanup = true   ## Prevents removal of child process log files on exit.  
childlogdir = /tmp   ## Defines the directory for storing child process logs.  
strip_ansi = false   ## Determines whether to remove ANSI escape codes from logs.  
environment = KEY1="value1",KEY2="value2"   ## Sets environment variables for supervisord.  
```
---
### [supervisorctl]  
- This section configures settings for the `supervisorctl` interactive shell program.
```ini
[supervisorctl]  
serverurl = unix:///tmp/supervisor.sock   ## Specifies the URL of the supervisord server (UNIX socket or TCP).  
username = chris   ## Sets a username for authentication.  
password = 123   ## Sets a password for authentication.  
prompt = mysupervisor   ## Customizes the command prompt in the `supervisorctl` shell.  
```
---
### [program:x]  
- This section defines a program that `supervisord` should manage. The program name follows the format `[program:<name>]`, where `<name>` is a unique identifier.  

```ini
[program:cat]  
command = /bin/cat   ## Specifies the command to run the program.  
process_name = %(program_name)s   ## Sets the process name (uses the program name).  
numprocs = 1   ## Defines the number of process instances to start.  
directory = /tmp   ## Sets the working directory for the process.  
umask = 022   ## Defines the file permission mask for new files.  
priority = 999   ## Determines the process start order (lower values start first).  
autostart = true   ## Starts the process automatically when `supervisord` starts.  
autorestart = unexpected   ## Restarts the process if it exits unexpectedly.  
startsecs = 10   ## Waits this many seconds before considering the process started.  
startretries = 3   ## Number of restart attempts if the process fails to start.  
exitcodes = 0   ## Specifies which exit codes are considered successful.  
stopsignal = TERM   ## Signal sent to stop the process (e.g., TERM, KILL).  
stopwaitsecs = 10   ## Time to wait before forcefully stopping the process.  
stopasgroup = false   ## If true, sends stop signal to the entire process group.  
killasgroup = false   ## If true, kills the entire process group on termination.  
user = chrism   ## Runs the process as the specified user.  

redirect_stderr = false   ## If true, redirects stderr to stdout.  
stdout_logfile = /a/path   ## Path to the standard output log file.  
stdout_logfile_maxbytes = 1MB   ## Maximum size before rotating stdout log file.  
stdout_logfile_backups = 10   ## Number of rotated stdout log backups to keep.  
stdout_capture_maxbytes = 1MB   ## Max bytes to capture for event monitoring.  
stdout_events_enabled = false   ## Enables event monitoring for stdout logs.  

stderr_logfile = /a/path   ## Path to the standard error log file.  
stderr_logfile_maxbytes = 1MB   ## Maximum size before rotating stderr log file.  
stderr_logfile_backups = 10   ## Number of rotated stderr log backups to keep.  
stderr_capture_maxbytes = 1MB   ## Max bytes to capture for event monitoring.  
stderr_events_enabled = false   ## Enables event monitoring for stderr logs.  

environment = A="1",B="2"   ## Sets environment variables for the process.  
serverurl = AUTO   ## Defines the server URL (AUTO uses default settings).  
```
---
### [include]  
- This section allows including additional configuration files in `supervisord.conf`.  
```ini
files = /an/absolute/filename.conf /an/absolute/*.conf foo.conf config??.conf  
## Specifies one or more configuration files to be included.  
## Supports absolute paths, wildcards (`*.conf`), and pattern matching (`??`).  
```
---
### [group:x]  
- This section groups multiple programs into a single unit for easier management.  
```ini
[group:foo]  
programs = bar,baz   ## Lists the programs that belong to this group.  
priority = 999   ## Determines the start order (lower values start first).  
```
- Notes:
  - A group allows controlling multiple programs as a single unit in Supervisor.  
  - The `[program:x]` sections for `bar` and `baz` must be defined elsewhere in the config.  
  - At runtime, `bar` and `baz` won't exist as separate groups but will be part of `foo`.  
---
### [fcgi-program:x]  
- This section configures a FastCGI program managed by Supervisor.  
- It allows multiple FastCGI processes to share a single socket for better performance and fault tolerance.  

```ini
[fcgi-program:fcgiprogramname]  
command = /usr/bin/example.fcgi   ## Specifies the command to run the FastCGI program.  
socket = unix:///var/run/supervisor/%(program_name)s.sock   ## Defines the shared socket for FastCGI processes.  
socket_owner = chrism   ## Sets the owner of the socket file.  
socket_mode = 0700   ## Defines the socket file permissions.  
process_name = %(program_name)s_%(process_num)02d   ## Names each process uniquely.  
numprocs = 5   ## Defines the number of FastCGI processes to spawn.  
directory = /tmp   ## Sets the working directory for the process.  
umask = 022   ## Defines the file permission mask for new files.  
priority = 999   ## Determines the process start order (lower values start first).  
autostart = true   ## Starts the process automatically when `supervisord` starts.  
autorestart = unexpected   ## Restarts the process if it exits unexpectedly.  
startsecs = 1   ## Waits this many seconds before considering the process started.  
startretries = 3   ## Number of restart attempts if the process fails to start.  
exitcodes = 0   ## Specifies which exit codes are considered successful.  
stopsignal = QUIT   ## Signal sent to stop the process (e.g., TERM, KILL).  
stopasgroup = false   ## If true, sends stop signal to the entire process group.  
killasgroup = false   ## If true, kills the entire process group on termination.  
stopwaitsecs = 10   ## Time to wait before forcefully stopping the process.  
user = chrism   ## Runs the process as the specified user.  

redirect_stderr = true   ## If true, redirects stderr to stdout.  
stdout_logfile = /a/path   ## Path to the standard output log file.  
stdout_logfile_maxbytes = 1MB   ## Maximum size before rotating stdout log file.  
stdout_logfile_backups = 10   ## Number of rotated stdout log backups to keep.  
stdout_events_enabled = false   ## Enables event monitoring for stdout logs.  

stderr_logfile = /a/path   ## Path to the standard error log file.  
stderr_logfile_maxbytes = 1MB   ## Maximum size before rotating stderr log file.  
stderr_logfile_backups = 10   ## Number of rotated stderr log backups to keep.  
stderr_events_enabled = false   ## Enables event monitoring for stderr logs.  

environment = A="1",B="2"   ## Sets environment variables for the process.  
serverurl = AUTO   ## Defines the server URL (AUTO uses default settings).  
```
- Key Benefits:
  - Allows multiple FastCGI processes to share a socket for improved performance.  
  - Enables graceful restarts since the socket remains open while child processes restart.  
  - Works with any web server, providing full process management without server dependency.  
---
### [eventlistener:x]  
- This section configures event listener processes that handle Supervisor event notifications.  - Similar to `[program:x]`, but listens for specific Supervisor events.  

```ini
[eventlistener:theeventlistenername]  
command = /bin/eventlistener   ## Specifies the command to run the event listener.  
process_name = %(program_name)s_%(process_num)02d   ## Names each process uniquely.  
numprocs = 5   ## Defines the number of listener processes to spawn.  
events = PROCESS_STATE   ## Specifies the event types this listener will handle.  
buffer_size = 10   ## Defines the event queue size before messages are dropped.  
directory = /tmp   ## Sets the working directory for the process.  
umask = 022   ## Defines the file permission mask for new files.  
priority = -1   ## Determines the process start order (lower values start first).  
autostart = true   ## Starts the process automatically when `supervisord` starts.  
autorestart = unexpected   ## Restarts the process if it exits unexpectedly.  
startsecs = 1   ## Waits this many seconds before considering the process started.  
startretries = 3   ## Number of restart attempts if the process fails to start.  
exitcodes = 0   ## Specifies which exit codes are considered successful.  
stopsignal = QUIT   ## Signal sent to stop the process (e.g., TERM, KILL).  
stopwaitsecs = 10   ## Time to wait before forcefully stopping the process.  
stopasgroup = false   ## If true, sends stop signal to the entire process group.  
killasgroup = false   ## If true, kills the entire process group on termination.  
user = chrism   ## Runs the process as the specified user.  

redirect_stderr = false   ## If true, redirects stderr to stdout.  
stdout_logfile = /a/path   ## Path to the standard output log file.  
stdout_logfile_maxbytes = 1MB   ## Maximum size before rotating stdout log file.  
stdout_logfile_backups = 10   ## Number of rotated stdout log backups to keep.  
stdout_events_enabled = false   ## Enables event monitoring for stdout logs.  

stderr_logfile = /a/path   ## Path to the standard error log file.  
stderr_logfile_maxbytes = 1MB   ## Maximum size before rotating stderr log file.  
stderr_logfile_backups = 10   ## Number of rotated stderr log backups to keep.  
stderr_events_enabled = false   ## Enables event monitoring for stderr logs.  

environment = A="1",B="2"   ## Sets environment variables for the process.  
serverurl = AUTO   ## Defines the server URL (AUTO uses default settings).  
```
- Key Benefits:
  - Listens for Supervisor events (e.g., process state changes).  
  - Helps automate tasks like logging, monitoring, and alerting.  
  - Supports multiple event listeners running in parallel.  
---
### [rpcinterface:x]  
- This section is used to extend Supervisor with custom Remote Procedure Call (RPC) interfaces.  
- It allows adding new functionalities beyond Supervisor’s default capabilities.  
```ini
[rpcinterface:supervisor]  
supervisor.rpcinterface_factory = supervisor.rpcinterface:make_main_rpcinterface   ## Required for Supervisor’s standard RPC functions.  
```
- The above section **must be present** in the configuration for Supervisor to function properly.  
- If no additional RPC features are needed, no further modifications are required.  

- **Adding Custom RPC Interfaces**  
  - To customize Supervisor, additional `[rpcinterface:x]` sections can be added, where `x` is the custom namespace.  
```ini
[rpcinterface:another]  
supervisor.rpcinterface_factory = my.package:make_another_rpcinterface   ## Specifies a custom RPC factory function.  
retries = 1   ## Custom parameter passed to the factory function.  
```
- **Example Python Factory Function (`my.package` module)**
```python
from my.package.rpcinterface import AnotherRPCInterface

def make_another_rpcinterface(supervisord, **config):
    retries = int(config.get('retries', 0))  # Gets retries from the config
    return AnotherRPCInterface(supervisord, retries)  # Returns an instance of the custom interface
```
- **Key Benefits**:
  - Allows Supervisor to support additional RPC commands.  
  - Enables automation and integration with external systems.  
  - Custom parameters (like `retries`) can be defined in the config file and passed dynamically.
    
### **Key Takeaways**  
1. **Supervisor Configuration is Modular** – Each section serves a specific function.  
2. **Authentication** – Secure with username/password for HTTP servers and `supervisorctl`.  
3. **Process Management** – `program`, `group`, `fcgi-program`, and `eventlistener` control different process types.  
4. **Logging & Debugging** – Logs are configurable for debugging (`stdout_logfile`, `stderr_logfile`).  
5. **Extensibility** – Custom RPC interfaces allow for advanced automation and integration.  
---

## Supervisor Email Alert System
## Directory Structure
Supervisor configuration files are located in `/etc/supervisor/conf.d/`, and the event listener script is stored in `/etc/supervisor/`.

```
/etc/supervisor/
├── supervisord.conf
├── eventlistener.py
└── conf.d/
    ├── eventlistener.conf
    ├── nginx.conf
```

---

## Supervisor Configuration Files

### 1. Event Listener Configuration
Located at `/etc/supervisor/conf.d/eventlistener.conf`, this configuration defines the event listener process.

<details>
  <summary>Click to View the Configuration File</summary>

```ini
[eventlistener:process_monitor]
command=python3 /etc/supervisor/eventlistener.py
events=PROCESS_STATE
autostart=true
autorestart=true
stderr_logfile=/var/log/supervisor/eventlistener.err.log
stdout_logfile=/var/log/supervisor/eventlistener.out.log
```

- **command**: Executes the Python script that listens for process state events.
- **events**: Listens for process state changes.
- **autostart**: Starts automatically when Supervisor starts.
- **autorestart**: Restarts if it crashes.
- **logfiles**: Stores stdout and stderr logs for debugging.

</details>

### 2. Nginx Process Configuration
Located at `/etc/supervisor/conf.d/nginx.conf`, this configuration manages the Nginx process.

<details>
  <summary>Click to View the Configuration File</summary>

```ini
[program:nginx]
command=/usr/sbin/nginx -c /etc/nginx/nginx.conf
autostart=true
autorestart=true
startretries=5
numprocs=1
startsecs=0
process_name=%(program_name)s_%(process_num)02d
stderr_logfile=/var/log/supervisor/%(program_name)s_stderr.log
stderr_logfile_maxbytes=10MB
stdout_logfile=/var/log/supervisor/%(program_name)s_stdout.log
stdout_logfile_maxbytes=10MB
```

- **command**: Defines the Nginx startup command.
- **autostart/autorestart**: Ensures the process runs continuously.
- **logfiles**: Manages error and output logs with size limits.

</details>

### 3. Supervisor Main Configuration
Located at `/etc/supervisor/supervisord.conf`, this file contains global Supervisor settings.

<details>
  <summary>Click to View the Configuration File</summary>

```ini
[unix_http_server]
file=/var/run/supervisor.sock
chmod=0700

[inet_http_server]
port=0.0.0.0:9001
username=admin
password=admin

[supervisord]
logfile=/var/log/supervisor/supervisord.log
pidfile=/var/run/supervisord.pid
childlogdir=/var/log/supervisor
eventlistener_buffer_size = 100

[rpcinterface:supervisor]
supervisor.rpcinterface_factory = supervisor.rpcinterface:make_main_rpcinterface

[supervisorctl]
serverurl=unix:///var/run/supervisor.sock

[include]
files = /etc/supervisor/conf.d/*.conf
```

- **inet_http_server**: Enables Supervisor’s web interface on port 9001.
- **logfiles**: Defines locations for logs and PID files.
- **eventlistener_buffer_size**: Controls the buffer size for event listeners.
- **include**: Loads additional configuration files.

</details>
---

## Python Event Listener Script
Located at `/etc/supervisor/eventlistener.py`, this script listens for Supervisor events and sends email notifications.

<details>
  <summary>Click to View the Configuration File</summary>

```python
import sys
import smtplib
from email.mime.text import MIMEText

def write_stdout(s):
    sys.stdout.write(s)
    sys.stdout.flush()

def write_stderr(s):
    sys.stderr.write(s)
    sys.stderr.flush()

def send_email(subject, message):
    sender = 'gyanaranjanmallick444@gmail.com'
    receivers = ['gyanaranjanmallick17@gmail.com']
    msg = MIMEText(message)
    msg['Subject'] = subject
    msg['From'] = sender
    msg['To'] = ', '.join(receivers)

    try:
        smtpObj = smtplib.SMTP('smtp.gmail.com', 587)
        smtpObj.starttls()
        smtpObj.login('gyanaranjanmallick444@gmail.com', 'your-app-password')
        smtpObj.sendmail(sender, receivers, msg.as_string())
        write_stderr("Successfully sent email\n")
    except Exception as e:
        write_stderr(f"Error: {str(e)}\n")

def main():
    while True:
        write_stdout('READY\n')
        line = sys.stdin.readline()
        write_stderr(line)
        headers = dict([x.split(':') for x in line.split()])
        data = sys.stdin.read(int(headers['len']))
        write_stderr(data)
        send_email('Supervisor Event', data)
        write_stdout('RESULT 2\nOK')

if __name__ == '__main__':
    main()
```

- **write_stdout/write_stderr**: Handles logging and debugging.
- **send_email**: Sends an email notification using Gmail SMTP.
- **main**: Listens for process state change events and triggers email alerts.

> **Note:** Replace `'your-app-password'` with a valid [Gmail App Password](https://myaccount.google.com/apppasswords) to avoid authentication issues.

</details>

---

## Deployment and Usage

### 1. Reload Supervisor Configuration
```sh
supervisorctl reread
supervisorctl update
supervisorctl restart process_monitor
```

### 2. Check Logs for Errors
```sh
tail -f /var/log/supervisor/eventlistener.err.log
```

### 3. Verify Supervisor Status
```sh
supervisorctl status
```
---


