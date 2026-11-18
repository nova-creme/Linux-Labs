# Incident Report: Saint John — "What is writing to this log file?"

**Platform:** SadServers  
**Type:** Fix  
**Level:** Easy  
**Domain:** Linux System Administration / Process & Log Investigation

## The Scenario

A test program kept writing to `/var/log/bad.log` and filling the disk. The task
was to stop the program **without deleting the log**.

## Investigation & Fix

1. **Confirmed the file was growing:**

   ```bash
   ls -l /var/log/bad.log; sleep 3; ls -l /var/log/bad.log
   ```

2. **Found the process holding the file open:**

   ```bash
   sudo lsof /var/log/bad.log
   ```

   → showed `badlog.py`, PID `596`, running as `admin`.

3. **Stopped it:**

   ```bash
   sudo kill 596        # kill -9 as a fallback
   ```

4. **Verified the fix** — re-checked that the file size no longer changed, then
   clicked **Check My Solution**.

## Lesson

To find what is writing to a file, use `lsof <file>` or `fuser -v <file>`. Then
kill the process instead of deleting or truncating the file. If it respawns, look
for a parent process or service.
