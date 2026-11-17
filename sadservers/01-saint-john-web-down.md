#  Incident Report: Saint-John (Web Server Down)

**Platform:** SadServers   
**Difficulty:** Easy  
**Domain:** Linux System Administration / Web Servers  

##  The Scenario
The `saint-john` server is hosting a web application, but the website was unreachable. The objective was to SSH into the server, diagnose the root cause of the outage, fix the configuration, and restore web service.


 Initial Triage & Investigation

1. Checked Service Status:
   Upon logging into the server, I checked the status of the Nginx service to confirm the outage:
   ```bash
   systemctl status nginx
   ```
   Result: The service was in a `failed` state.

2. Inspected System Logs:  
   To determine *why* the service crashed, I queried the `systemd` journal for specific error messages:
   ```bash
   journalctl -xe
   ```
   Result: The logs indicated a fatal configuration error on **line 12** of the main Nginx configuration file.

3. Validated Configuration Syntax:  
   To isolate and confirm the exact syntax issue before making edits, I ran Nginx's built-in configuration test:
   ```bash
   nginx -t
   ```
   Result: Confirmed the syntax error on line 12 of `/etc/nginx/nginx.conf`.

Root Cause Analysis (RCA)
The web server outage was caused by a syntax error in the primary Nginx configuration file (`/etc/nginx/nginx.conf`). The invalid directive on line 12 prevented the Nginx daemon from parsing the configuration and starting the service.



Resolution

1. Edited the Configuration File:  
   I opened `/etc/nginx/nginx.conf` and fixed the syntax error on line 12:
   ```bash
   sudo vim /etc/nginx/nginx.conf
   ```

2. Tested the Updated Configuration:  
   Before restarting the service, I validated the configuration file to ensure no other syntax errors existed:
   ```bash
   sudo nginx -t
   ```
   Result: `nginx: configuration file /etc/nginx/nginx.conf test is successful`

3. Restarted and Verified the Service:  
   With the syntax verified, I restarted Nginx and confirmed it was running properly:
   ```bash
   sudo systemctl restart nginx
   sudo systemctl status nginx
   ```
   Result: The service entered the `active (running)` state, restoring access to the website.

Commands Summary
```bash
systemctl status nginx
journalctl -xe
nginx -t
sudo vim /etc/nginx/nginx.conf
sudo nginx -t
sudo systemctl restart nginx
```

##  Key Takeaways
* **Always validate before restarting:** Running `nginx -t` (or `apachectl configtest`) is essential before restarting a web server so a minor syntax typo does not take down a live service.
* **Use `journalctl` for fast root-cause isolation:** Checking `journalctl -xe` (or `journalctl -xeu nginx.service`) immediately reveals the exact file and line number causing a `systemd` unit failure.
