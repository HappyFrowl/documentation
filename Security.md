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

* `**OpenSSL**`

* `**


* `**gpg**`
- Free implementation of PGP.
- Used to get, create, and send encryption keys.
- Encrypt files using `gpg`.

*


## SSH
- `/etc/ssh/ssh.config`
    - Configure outbound SSH connections.
    - Example: Turn off password-based authentication, allowing only certificate-based authentication.

- `/etc/ssh/sshd.config`
    - Configure the SSH server.
    - Manages incoming SSH connections.
    - Configure the port to which it listens for SSH connections.

-`ssh-keygen`
    - `ssh-keygen -t <ed25519> -f <path/to/file>` - Create SSH key
- `ssh-copy-id <IP>`
    - Copy and add the public key to `~/.ssh/authorized_keys` on the remote machine.
    - Enter the password of the account on the remote PC.
    - Once done, you can SSH without entering a password.
- `ssh-add` - add the efault SSH keys in ~/.ssh to the ssh-agent
- `ssh -X`
    - X11 forwarding.


SSH: 

Default root cannot use password to login 

Disallow root login completely 

Add ‘PermirootLogin no’ in /etc/sshd/ssh_config 

Allow root login using password 

Make sure the following lines are included: 

PermitRootLogin yes 

PasswordAuthentication yes 

After changing the config file restart sshd 

systemctl restart sshd 















## Firewall

* `ufw` - uncomplicated firewall
    * interface for IP tables and is desgiend to simplify the process of configuring firewalls 
    * Like any firewall, allow and block traffic by port number and IP address
    * `status` - active/ inactive
        * `verbose` - print more info
        * `numbered` - print the rule numbersc
        * list the firewall rules
    * `enable` / `disable`
    * `reset` - resets firewall to its default configuration
    * `default (allow|deny) (incoming|outgoing)` - manage the default rules
    * Managing incoming rules: 
        * `ufw (allow|deny) (service|subnet|IP)`  
        * `allow ssh` - allow incoming ssh traffic
            * `allow 22` - same but with the port number
        * `deny http` - deny http
        * `deny proto (tcp|udp) from (any|IP) to any port <port numbers>` 
        * `(allow|deny) from (subnet|IP) to any port <port numbers>`
    * Managing outgoing rules:
        * `ufw (allow|deny) out <to IP | on {interface}> <proto (tcp|udp)> <port {number}>`
        * `ufw deny out to 93.214.56.31 proto tcp port 443` - deny 443/tcp to 93.214.56.31
        * `ufw deny out on eth0 to 192.168.1.100 port 80 proto tcp` - deny based on interface
    * `delete <rule number>` - delete a firewall by specifying its number




