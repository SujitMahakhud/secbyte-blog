---
title: "Troubleshoot Log Ingestion Drops to Microsoft Sentinel from Linux Machines: Addressing /var/log Capacity Issues"
date: "2024-06-06"
author: "Sujit Mahakhud"
description: "In this blog, we’ll discuss a specific issue that can cause a sudden drop in log ingestion from a Linux machine to Microsoft Sentinel: the /var/log di..."
slug: "troubleshoot-log-ingestion-drops-to-microsoft-sentinel-from-linux-machines-addressing-varlog-capacity-issues"
original_url: "https://secbyte.in/2024/06/06/troubleshoot-log-ingestion-drops-to-microsoft-sentinel-from-linux-machines-addressing-var-log-capacity-issues/"
---

# Troubleshoot Log Ingestion Drops to Microsoft Sentinel from Linux Machines: Addressing /var/log Capacity Issues

In this blog, we’ll discuss a specific issue that can cause a sudden drop in log ingestion from a Linux machine to Microsoft Sentinel: the /var/log directory capacity getting full. This problem often occurs when the machine in use does not have enough storage capacity.


### Why Does This Happen?
When the /var/log directory gets full, the system can’t store more logs, leading to a drop in log ingestion. This can be problematic, especially if you’re forwarding logs to Microsoft Sentinel for monitoring and analysis. While increasing the storage capacity of your machine can help, a more sustainable solution involves automating log maintenance.


### Solution: Automate Log Deletion with a Cron Job
To prevent the /var/log directory from getting full, you can set up a cron job that regularly deletes old logs after they have been forwarded. Here’s how to do it:


### Step 1: Modify rsyslog.conf
First, you need to update the rsyslog.conf file to properly configure log storage.

- Open the rsyslog.conf file located at /etc/rsyslog.conf.

```bash
sudo nano /etc/rsyslog.conf
```
- Add the following template below the TCP and UDP recipients:

```
$template remote-incoming-logs,"/var/log/%HOSTNAME%/SourceLogs.log". ?remote-incoming-logs
```

### Step 2: Create a Log Maintenance Script
Next, create a script that will delete the logs.

- Navigate to the /var/log directory and create a new directory for scripts:

```bash
cd /var/log
sudo mkdir scripts
```
- Inside the scripts directory, create a new script file:

```bash
cd scriptssudo touch sentinel_scriptssudo nano sentinel_scripts
```
- Add the following command to the script, which will truncate the log files:

```
find /var/log/ -name "SourceLogs.log" -exec truncate -s 0 {} \;
```
If other files such as syslog or messages are getting overly filled, you can edit the script to include those files by adding their names.


### Step 3: Schedule the Cron Job
Finally, create a cron job to run the script at regular intervals.

- Open the crontab editor:

```bash
sudo crontab -e
```
- Add the following lines to schedule the script to run four times a day:

```
0 0 * * * sh /var/log/scripts/sentinel_scripts0 6 * * * sh /var/log/scripts/sentinel_scripts0 12 * * * sh /var/log/scripts/sentinel_scripts0 18 * * * sh /var/log/scripts/sentinel_scripts
```

## Explanation of Commands
- sudo nano /etc/rsyslog.conf: Opens the rsyslog configuration file in the nano text editor with superuser permissions.
- $template remote-incoming-logs,"/var/log/%HOSTNAME%/SourceLogs.log": Defines a template for log file paths based on the hostname.
- cd /var/log: Changes the current directory to /var/log.
- sudo mkdir scripts: Creates a new directory named scripts within /var/log with superuser permissions.
- sudo touch sentinel_scripts: Creates a new, empty script file named sentinel_scripts in the scripts directory.
- sudo nano sentinel_scripts: Opens the sentinel_scripts file in the nano text editor with superuser permissions.
- find /var/log/ -name "SourceLogs.log" -exec truncate -s 0 {} \;: Finds all files named SourceLogs.log within /var/log and truncates (empties) them.
- sudo crontab -e: Opens the cron table (crontab) for editing with superuser permissions.
- 0 0 * * * sh /var/log/scripts/sentinel_scripts: Schedules the sentinel_scripts script to run at midnight every day.
- 0 6 * * * sh /var/log/scripts/sentinel_scripts: Schedules the sentinel_scripts script to run at 6 AM every day.
- 0 12 * * * sh /var/log/scripts/sentinel_scripts: Schedules the sentinel_scripts script to run at noon every day.
- 0 18 * * * sh /var/log/scripts/sentinel_scripts: Schedules the sentinel_scripts script to run at 6 PM every day.

### Conclusion
By setting up this cron job, you can ensure that the /var/log directory on your Linux machine does not fill up, preventing drops in log ingestion to Microsoft Sentinel. This automated maintenance helps in keeping your log forwarding process smooth and uninterrupted.


### Share this:
- Click to share on X (Opens in new window) X
- Click to share on Facebook (Opens in new window) Facebook
- Click to share on LinkedIn (Opens in new window) LinkedIn
- Click to share on Telegram (Opens in new window) Telegram
- Click to share on Threads (Opens in new window) Threads
- Click to share on WhatsApp (Opens in new window) WhatsApp
- Click to share on X (Opens in new window) X
- Click to share on Mastodon (Opens in new window) Mastodon
- 


---
💬 **Connect with Me**
- [LinkedIn](https://www.linkedin.com/in/sujitmahakhud/)
- [YouTube](https://www.youtube.com/@sujitmahakhud_official)
