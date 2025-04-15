# Monitoring
- [System logging](#system-logging)
- [Syslog](#syslog)
- [Systemd logging](#systemd-logging)

## System logging
* **File locations**
  - Location of all system events, except for authentication messages
    * `/var/log/syslog`
    * `/var/log/messages` - for RHEL-based
  * Authentication messages
    * `/var/log/auth.log` - for Debian-based distros
    * `/var/log/secure`   - for RHEL-based distros
  - Config files
    *  `/etc/rsyslog.d`
  * Kernel messages
    * `/var/log/kern.log`
  * Misc apps (cron, firewalld, apt)
    * `/var/log/[app]`


* **Log rotation**
  * Practice of creating new versions of a log file
  * Allows you to add or modify the configuration for individual log rotations.
  * Configuration files can be found in `/etc/logrotate.d`
  * `logrotate`
    * Program used to perform log rotation


## Syslog 
- Message logging standard.
- Completely separate and different from `systemd` 
  * `systemd` logging is stuff like `journalctl`
* **Syslog versions**
  - `syslogd` - The original 
  - `syslog-ng` - Improved syslogd 
  - `rsyslogd` - Remote Syslog - latest and most used version of syslog. Allows for remote use of syslog
* Syslog log files are stored in `/var/log`

* **Reading syslog**
  * A syslog message usually has two values attach to it. 
  * Both are just numbers, but then represent different things and used to filter traffic
    * **Facility**
      * Tied to a service
      * They range from 1 to 23 
      * Some are pre-programmed, e.g. 15 for cron, others are local 
    * **Severity** 
      - 8 severity numbers, 0 to 7
      - emerg, alert, crit, err, warning, notice, info, debug
 
  - Syslog separates into different components:
  - **Message generator**: The software or process that generates the message.
  - **Message storage**: The storage location of the log message.
  - **Message reporter**: The system that reports the message.
  - **Message analysis**: Analyzing the logs for relevant information.

* **Syslog configuration**
  * `/etc/rsyslog.conf`
  * Config file for rsyslog, where the following can be configured:
    * ***Send* logging to a central server**
      * Add line at the bottom 
      * Something like: `*.* @@<IP>:514`
    * ***Receive* logging from servers**
      * Uncomment `#$ModLoad imtcp` and `#$InputTCPServerRun 514`
    * **Configure syslog rules**
      * The rule structure is in a two column format
        * First column lists message facilities and/or severities
          - `.info`, `.warn`, `.err`, `.none`
          - `.*`: Log everything
        * Second column defines actions for the messages 



## Systemd logging
* `journald` - journal daemon
  * `journalctl` - command line utility to control journald
  * Query and print logs 
  * Logs are collected by `systemd` 
  * `journald` can be used in conjunction with `syslogd` or `rsyslogd`
* **Commonly used commands:**
  * `-n {number of lines}` - specify number of lines printed
  * `-o <output format>` - specify output format
  * `-f` - print nmost recent entries
  * `-p` - filter log by severity
  * `-u` - filter log output by name of the service
  * `-b` - Displays messages since the last boot.
  * `--since "2 days ago"` - Logs from 2 days ago.
  * `-xeu [service name]` - {-x} Augment log lines with explanation texts from the message catalog. {-e} Immediately jump to the end of the journal inside the implied pager tool
  * `PID=<#number>` - look for a specific process ID
* Configuration
  * `/etc/systemd/journald.conf`
  * `storage=auto` means it is writing logs persistently to the default journald log folder `/var/log/journal/`
  * If this folder exists, journald is writing logs to disk to this folder, if the folder is not there, journald writes to RAM


* `last`
  * Print the user's history of loging and logout events
  * See reboots and who logged in and to which TTY  
  * It shows time and date  
  * 


---

## Routing Logs to TTY
- Useful for routing all logs to a TTY.
- Steps:
  1. `cd /etc/rsyslog.d`
  2. `sudo nano 60-console.conf`
  3. Add the following line:
     - `*.* /dev/tty10` (Log all severities and facilities to TTY 10).
  4. Run the following commands:
     - `sudo chown syslog /dev/tty10`
     - `sudo service rsyslog restart`









### `top`
- Provides a live feed of running processes and their resource usage.
- Useful for real-time process monitoring.


## Modifying Process Execution Priorities

### Nice Value
- Determines the CPU time allocated to a process.
- **Range**:
  - `-20` (highest priority).
  - `19` (lowest priority).
  - Default is `0`.
- **Impact**:
  - The **nice value** impacts the kernel's **priority (PR)** calculation:
    - `PR = Nice Value + 20`
  - Higher priority (lower nice value) processes get more CPU time.
  - Long-running processes are deprioritized over time.

### `nice` Command
- Starts a new process with a specified nice value.
- **Usage**:
  - `nice -<value> <command>`
  - Example: `nice --15 ping google.com` (sets a nice value of `-15`).

### `renice` Command
- Changes the nice value of a running process.
- **Usage**:
  - `sudo renice -<value> -p <PID>`
  - Example: `sudo renice -0 -p 1234` (sets the nice value of process `1234` to `0`).
