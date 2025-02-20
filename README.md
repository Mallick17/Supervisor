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

## Installation & Configuration
### Step 1: Update the Package List
Before installing any software, it's a good practice to update the package list to ensure you have the latest versions of the software.

```bash
sudo apt update
```

---

### Step 2: Install Supervisord & Verify the Installation
Install Supervisord using the `apt` package manager. Check the installed version of Supervisord to confirm the installation was successful.

```bash
sudo apt install supervisor -y
```

```bash
supervisord -v
```
- This command should output the version of Supervisord installed.
---

### Step 3: Check Supervisord Service Status
Ensure that the Supervisord service is running.

```bash
sudo systemctl status supervisor
```

You should see an output indicating that the service is active and running.

---

### Step 4: Configure Supervisord
Edit the Supervisord configuration file to customize its behavior.

1. Open the configuration file in a text editor (e.g., `nano`):

   ```bash
   sudo nano /etc/supervisor/supervisord.conf
   ```

2. Add or modify the `[inet_http_server]` section to enable the web interface. This section should look like this:

   ```ini
   [inet_http_server]
   port=0.0.0.0:9001
   username=admin
   password=admin
   ```
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

   - `port`: The IP address and port on which the web interface will run. `0.0.0.0:9001` means it will be accessible on all network interfaces on port 9001.
   - `username` and `password`: Credentials for accessing the web interface.

3. Save and exit the editor (in `nano`, press `CTRL + X`, then `Y`, and `Enter`).

---

### Step 5: Apply Configuration Changes
After modifying the configuration file, reload Supervisord to apply the changes.

1. Reread the configuration:

   ```bash
   sudo supervisorctl reread
   ```

2. Update the configuration:

   ```bash
   sudo supervisorctl update
   ```

3. Restart the Supervisord service:

   ```bash
   sudo systemctl restart supervisor
   ```

---

### Step 6: Access the Supervisord Web Interface
Once the configuration is complete, you can access the Supervisord web interface using a web browser.

- Open a browser and navigate to `http://<your-server-ip>:9001`.
- Use the username `admin` and password `admin` to log in.

## Troubleshooting
- If the web interface is not accessible, ensure that the firewall allows traffic on port `9001`.
- Check the Supervisord logs for errors:
  ```bash
  sudo tail -f /var/log/supervisor/supervisord.log
  ```

---


