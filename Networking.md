# Networking

- [Configuring network interfaces](#Configuring-network-interfaces)
- [Network Troubleshooting](#Network-Troubleshooting)
- [Firewall](#Firewall)
- [DNS](#DNS)
- [DHCP](#DHCP)
- [SSH](#SSH)


## Configuring network interfaces
- `ifconfig` - interface configuration 
    - Display or configure network interfaces.
    - `ifconfig interface_name ip_address` - assign an IP ddress to an interface
        - changes are non-persistent between reboots

- `ip`
    - `ip a` - same as ifconfig
    - `ip link set <interface> (up|down)` - bring an interface up or down 
    - Non persistent
    
* `ifdown / ifup` - interface down / up 
    - Bring an interface down.

* `route` - Show route tables.
    - Like `ip` changes made with `route` are not persistent
    - `route -n` - Show the IP addresses, rather than `default`
    - `route add -net <ip> netmask <netmask> gw <gw_address>` - add a route rule
    - `route add default gw <ip>` - add a default gateway


###  **NetworkManager**
- Entire suite of programs to manage networking
- changes made are persistent
- `nmcli` - CLI tool 
- `nmtui` - Text user interface 
* **network scripts**
    - `network scripts` are scripts in `/etc/init.d/network` or `/etc/network` and any other script NetworkManager calls
    - When `ifdown` is executed, scripts in `/etc/network/if-down.d` are run
    - Although NetworkManager provides the default networking service, scripts and NetworkManager can run in parallel and work together
    - Running Network scripts should only be done using `systemctl` 
        - `systemctl start|stop|restart|status network`
* `nmcli`
    - `nmcli device` - manage device interface settings 
        - `status` - Print networking devices. Also see whether devices are managed by NetworkManger
        - `show` - Print extensive network information on the devices, e.g. IP, MTU, Gateway, DNS, Routes
    - `nmcli connection` - manage network connection settings
        - `show <ConnectionName>` - Print conenction info, DNS, DHCP server, lease time, etc.
        - `add type ethernet con-name <connection-name> ifname <interface-name>` - adding a dynamic connection
        - `modify <connection name> +ipv4.routes "<dst IP> <nhop IP>"` - add a route
    - options
        - `-t` - terse output: instead of a table format, seperate items with `:`
        - `-f` - field: specific which fields you wanted printed
            - especially good in combination for scripting
        - `-p` - make it pretty - always good to use in combination with `show`

* `netplan`
    * YAML network configuration abstraction for various backends
    * it is a way of declaratively ocnfigure interface devices 
    * `/etc/netplan/*` - location for the yaml files 

* network bonds
    * same as NIC teaming
    * several mode, but two are important:
        1. Mode 4: 802.3ad - industry standard for network bonds. It does require switch support
        2. Mode 5: balance-alb (all load balance). Incoming and outgoing traffic is distributed over all available NICs. This mode does not require switch support
    * configuring it:
        * modify `/etc/netplan` file   
        * add section `bonds:` etc 
            * just look this up 




## Network troubleshooting
* `ping` 

* `traceroute`
    - Trace the route packets take to a network host.

* `nc` - `netcat`
    - Read and write traffic over a network.
    - `nc –l [port]`: Listen mode.
    - `nc [ip] [port]`: Write mode.
    - Used to test connectivity.
    - Afges from a remote computer on the network using netcat.

* `netstat` - network interface stats.
    - Active internet connections.
    - What remote machines are you connected to.
    - Active UNIX domain sockets.
    - `Netstat -atn`
            - Get ports that are actively being listened to.
            - Shows all active internet connections.

* `ss` - socket statistics
  * prints similar information to `netstat`
  * prints simpler output and syntax than `netstat`
  * options:
      * `-t | -u` - print tcp / udp connections
      * `-l` - print listening connection
      * `-s` - print statistics 
      * `-4 | -6` - print ipv4 / ipv6 connection
      * `sport == : <port> and dport == : <port>` - specify specific ports

- `nmap` - network mapper
    - Look for devices on the network and open ports on devices.

* `iperf` 
    * test the maximum throughput of an interface

* `iftop` - interface top
    * display bandwidth usage infoirmation for a system

* `mtr` 
    * Combination of `ping` and `traceroute`
    * Enables testing the quality of a network connection
    * Identify packet loss

## DNS

- `/etc/hosts`
    - Hosts file.
    - Essentially a simplified local DNS.
    - Add an IP with a name here, and you can ping that name.


- `/etc/hosts`
    - Local hostnames and IPs.

- `/etc/resolv.conf`
    - File containing the IP address of one or more DNS servers
    - Set DNS name server to perform DNS queries.
    - This cannot be modified when using DHCP.

- `/etc/nsswitch`
    - Name service switch
    - File containing a couple different configurations
    - Specifies which name servers are looked up and in what order.
    - The ‘hosts’ section is most important:
        - First, it looks in `/etc/hosts`, then DNS.

- `getent hosts`
    - Get all known hosts.
    - Lists the contents of `/etc/hosts`.

- `host <domain>`
    - Look up IP addresses.
    - Shows which servers handle the mail.
    - simple and concise 
    - 

- `nslookup`
    - Query DNS server
    - simple and interactive 
    - `nslookup <custom_DNS_server> <host>` - query a custom DNS server 

- `dig` - Domain Information Groper.
    - Query nameservers.
    - `dig @<server> <domain> <record-type>`
        - `@<server_name>` - is used to query a custom DNS server 
    - verbose and detailed
    
- `whois <domain>` 
    - Print information about the owner of the domain name
    - Prints also the domain nameservers and registrar


## DHCP

* `/etc/dhcp/dhclient.conf`
    * configuration file for DHCP
    * Edited using `nmcli`
    * Add line `supersede domain-name-server <IP DNS servers>`.



## File & data transfer

* `scp` - secure copy
    * copying over SSH

* `rsync` - remote sync
    * transfer files either to or from a remote host
        * can also be entirely local or mounts for remote directories
    * supports resuming transfers and only copies changes
    * it uses the ssh protocol
    * `rsync <file or dir> <dir2>`d
    * `rsync <directory> -rv --dry-run user@192.168.1.10:/home/user/backup` - transfer the contents of directory recursively and verbosely to /home/user/backup on remote host with IP 192.1681.1.10, but dry run it   
        * this is not a sync utility, for it never removes files only appends and does nothing about similarly named duplicates
    * `rsync --delete -rv <dir> <host>:<dir>` - delete all the files in the target directory that are **not** included in the source directory
    * `-a` - archive option. This makes sure the metadata (the date) is the same between directories
    * `-z` - compress files 


* `sftp`
    * more complex


* `curl` - client url
    * interaction with REST API
    * By default, it perform the GET operation on the website's API
    * `-o </destination/file> <source.com>` - output website contents to a file 
    * `-O` - use original file name as specified on the server 
    * `-L` - follow any redirect
    * `-s` - silent mode
    * `-S` - show error (when using with `-s`)
    * `-F` - 
    * `-d` - push data to a REST API
    * `-H` - Header. It is defined as a case insensitive name, followed by a colon, followed by a value
        * `content-type:`
    * `-v` - view request headers and connection details, e.g. TLS handshake




## Firewall

* `iptables` - 
    * Configure tables, chains and rules 

* `nftables`
    * 


* `firewalld`
    * Red Hat firewall manager
    * Used by
        * RHEL
        * CentOS
        * Alma Linux
        * Rocky Linux
        * Oracle Enterprise Linux
    * **Terminology**
        * `netfilter`
            * actual Linux firewall
            * packet filtering code that is built-in the Linux Kernel
            * to interact with it, a helper program is needed
        * `iptables`
            * default firewall manager for many distros
        * `nftables`
            * newer, better, easier than `iptables`
        * `firewalld` 
            * command line frontend for `iptables` or `nftables`
            * this injects the rules into `iptables` or `nftables` 
    * **zones**
        * collection of open ports for that zone
        * located `/etc/firewalld/zones/`
        * by default, there always is a `public.xml` zone
    * **basic commands:**
        * `firewall-cmd --add-port=8080/tcp` - open up port 8080/tcp
            * this configuration will be visible in `nft list ruleset`
        * `--permenant` - make permanent changes 


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





## SSH
- `/etc/ssh/ssh.config`
    - Configure outbound SSH connections.
    - Example: Turn off password-based authentication, allowing only certificate-based authentication.

- `/etc/ssh/sshd_config`
    - Configure the SSH **server**
    - Manage the following:
        - **Port** - set the port to which the server listens for SSH connections
        - **PasswordAuthentication** - used to enable or disable password-based authentication 
        - **PubKeyAuthentication** - used to enable or disable public key-based authentication
        - **Hostkey** - used to reference the locations of the server's private keys
        - **UsePAM** - enables or disables support for PAM (Puggable Authentication Modules, see IAM)
        - **SyslogFacility**- Change the logging level of SSH event 
        - **ChrootDirectory** - reference a chroot jail path for a user 
        - **AllowUsers/AllowGroups** - Enable user-specific access by allowing the specified users or groups  access over SSH 
        - **DenyUsers/DenyGroups** - Restrict the specified users or groups from accessing the server over SSH
        - **PermitRootLogin** - enable or disbale the abiltiy for the root user to log in over SSH (best not to). 

- `ssh-keygen`
    - generate a public / private key pair
    - `ssh-keygen -t <ed25519> -f <path/to/file>` - Create SSH key
- `ssh-copy-id <IP>`
    - Copy and add the public key to a remote computer's `~/.ssh/authorized_keys` file
    - Enter the password of the account on the remote PC.
    - Once done, you can SSH without entering a password.
- `ssh-add` 
    - add the default SSH keys in `~/.ssh` to the ssh-agent
    - Add private key identities to the SSH key agent
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













