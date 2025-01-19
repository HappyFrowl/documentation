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
