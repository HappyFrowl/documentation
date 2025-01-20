# System Logging

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

### Current Versions
- `rsyslogd`
- `syslog-ng`

### Configuration Files
- Config files are located in `/etc/rsyslog.d`.

### Meaning of the Config File
- `.*`: Log everything.
- Compare it to `.info`, `.warn`, `.err`, `.none`.

---

## /var/log/syslog
- Location of the logs.

## Logrotate
- Program for rotating logs.
- Allows you to add or modify the configuration for individual log rotations.
- Configuration files can be found in `/etc/logrotate.d`.

## Journalctl
- Query and display logs from the `systemd` journal.

### Examples:
- `journalctl -b`: Displays messages since the last boot.
- `journalctl -1 / 2 / 3`: Displays messages from previous boots.
- `journalctl --since "2 days ago"`: Logs from 2 days ago.
- `journalctl -u cron`: Logs from the cron service.

### Config File
- `/etc/journalctld.conf`

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






last 

See reboots and who logged in  

Shows time and date  



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
