# Host Security

## Securing the system

- `/etc/hosts.allow`
    - List hosts, domains, or IPs that are allowed services on the system
    - Takes precedence over `hosts.deny`
    -   `sshd: 192.168.1.0/24`
        `sshd: mytrustedhost.example.com`
    - kinda old shit

- `/etc/hosts.deny`
    - Deny access.
    - Access is granted by default.
    - `sshd: ALL` 
    - kinda old shit. Just use firewalls

- `/etc/nologin`
    - This is a file that locks down the entire system
    - If this file exists, only root can access.

- `xinetd`
    - X Internet Daemon.
    - Listens to incoming requests over the network.
    - Launches the appropriate program to handle the request.
    - Requests are often for specific ports.
    - Manages which services can be open at specific times.

- `tcpd`
    - Monitor incoming requests.
    - Program triggered by `xinetd`.
    - `xinetd` starts `tcpd` instead of the desired server.
    - `tcpd` logs the request.
    - Logs are sent to `syslog`.
    - After logging, `tcpd` runs the program.



##  Encryption

* `**openssl**`

* `


* `**gpg**`
- Free implementation of PGP.
- Used to get, create, and send encryption keys.
- Encrypt files using `gpg`.
























