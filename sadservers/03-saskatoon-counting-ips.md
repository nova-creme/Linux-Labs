# Incident Report: Saskatoon — "Counting IPs"

**Platform:** SadServers  
**Type:** Do  
**Level:** Easy  
**Domain:** Linux System Administration / Log Analysis

## The Scenario

`/home/admin/access.log` has one line per HTTP request, with the client IP in
the first column. The task was to find the IP with the most requests and write
**only that IP** to `/home/admin/highestip.txt`.

## Investigation & Fix

1. **Checked the log format:**

   ```bash
   head -3 /home/admin/access.log
   ```

2. **Built a pipeline that extracts the IPs, counts them, and picks the top one:**

   ```bash
   awk '{print $1}' /home/admin/access.log | sort | uniq -c | sort -rn | head -1
   ```

3. **Wrote just the IP (second field) to the answer file:**

   ```bash
   awk '{print $1}' /home/admin/access.log | sort | uniq -c | sort -rn | head -1 | awk '{print $2}' > /home/admin/highestip.txt
   ```

4. **Verified it:**

   ```bash
   grep -c -F -f highestip.txt access.log
   # → 482

   sha1sum highestip.txt
   # → 6ef426c40652babc0d081d438b9f353709008e93
   ```

5. Clicked **Check My Solution**.

## Lesson

The standard way to rank values in a text file is `sort | uniq -c | sort -rn`.
You have to `sort` before `uniq -c`, because `uniq` only counts **adjacent**
duplicates. Keep the answer file to the bare IP — no count or extra text.
