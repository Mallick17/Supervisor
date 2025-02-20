# Supervisor
**System for Controlling Process State.**
- Supervisor is a system for controlling and maintaining process state, similar to what init does, but not intended as an init replacement.
- It will manage individual processes or groups of processes that need to be started and stopped in order, and it is possible to control individual process state via an rpc mechanism, thus allowing ordinary users to restart processes.

## supervisorctl
### supervisorctl Command-Line Options
- control applications run by supervisord from the cmd line.
- Usage: `/usr/bin/supervisorctl [options] [action [arguments]]`
```bash
-c/--configuration FILENAME -- configuration file path (searches if not given)
-h/--help -- print usage message and exit
-i/--interactive -- start an interactive shell after executing commands
-s/--serverurl URL -- URL on which supervisord server is listening
     (default "http://localhost:9001").
-u/--username USERNAME -- username to use for authentication with server
-p/--password PASSWORD -- password to use for authentication with server
-r/--history-file -- keep a readline history (if readline is available)
```
- `action [arguments] -- see below`
- Actions are commands like "tail" or "stop".  If -i is specified or no action is specified on the command line, a "shell" interpreting actions typed interactively is started.  Use the action "help" to find out about available actions.

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


