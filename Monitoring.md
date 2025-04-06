# Monitoring
- [System logging](#system-logging)
- [Syslog](#syslog)
- [Journal control](#journal-control)

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
- Separates into different components:
  - **Message generator**: The software or process that generates the message.
  - **Message storage**: The storage location of the log message.
  - **Message reporter**: The system that reports the message.
  - **Message analysis**: Analyzing the logs for relevant information.
- Each message has a facility code indicating:
  - Software type.
  - Severity label.
  - Used for:
    - System management.
    - Security auditing.
    - General info.
    - Analysis.
    - Debugging messages.
* Current Versions of syslog
  - `rsyslogd` - older version
  - `syslog-ng` - replacement for syslogd

* **Syslog configuration**
  * `/etc/rsyslog.conf`
  * Here stuff can be configured such as:
    * Send logging to a central server
      * Add line at the bottom 
      * something like: `*.* @@IP:514`
    * Receive logging from servers
      * Uncomment `#$ModLoad imtcp` and `#$InputTCPServerRun 514`
    * Configure syslog rules
      * The rule structure is in a two column format
        * First column lists message facilities and/or severities
          - `.info`, `.warn`, `.err`, `.none`
          - `.*`: Log everything
        * Second column defines actions for the messages 
      * 




## Journal control
* `journalctl`
* Query and print logs 
* Logs are collected by `systemd` 
* `journald` is used in conjunction with `syslogd` or `rsyslogd`
* Commonly used commands:
  * `-n {number of lines}` - specify number of lines printed
  * `-o <output format>` - specify output format
  * `-f` - print nmost recent entries
  * `-p` - filter log by severity
  * `-u` - filter log output by name of the service
  * `-b` - Displays messages since the last boot.
  * `--since "2 days ago"` - Logs from 2 days ago.
  * `-xeu [service name]` - {-x} Augment log lines with explanation texts from the message catalog. {-e} Immediately jump to the end of the journal inside the implied pager tool
* Config File
  - `/etc/journalctld.conf`


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
