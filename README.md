# Supervisor
**System for Controlling Process State.**
- Supervisor is a system for controlling and maintaining process state, similar to what init does, but not intended as an init replacement.
- It will manage individual processes or groups of processes that need to be started and stopped in order, and it is possible to control individual process state via an rpc mechanism, thus allowing ordinary users to restart processes.

## Installation & Configuration
## Step 1: Update the Package List
Before installing any software, it's a good practice to update the package list to ensure you have the latest versions of the software.

```bash
sudo apt update
```

---

## Step 2: Install Supervisord & Verify the Installation
Install Supervisord using the `apt` package manager. Check the installed version of Supervisord to confirm the installation was successful.

```bash
sudo apt install supervisor -y
```

```bash
supervisord -v
```
- This command should output the version of Supervisord installed.
---

## Step 3: Check Supervisord Service Status
Ensure that the Supervisord service is running.

```bash
sudo systemctl status supervisor
```

You should see an output indicating that the service is active and running.

---

## Step 4: Configure Supervisord
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

   - `port`: The IP address and port on which the web interface will run. `0.0.0.0:9001` means it will be accessible on all network interfaces on port 9001.
   - `username` and `password`: Credentials for accessing the web interface.

3. Save and exit the editor (in `nano`, press `CTRL + X`, then `Y`, and `Enter`).

---

## Step 5: Apply Configuration Changes
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

## Step 6: Access the Supervisord Web Interface
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


