# Networking

- [Configuring network interfaces](#Configuring-network-interfaces)
- [Network Troubleshooting](#Network-Troubleshooting)
- [Firewall](#Firewall)
- [DNS](#DNS)
- [DHCP](#DHCP)


## Configuring network interfaces

### Introduction
* There are multiple ways to configure network adapters
* It differs per distro and per init system
* Mainly there are three:
    1. Configuration scripts
    2. Netplan
    3. Network Manager 


### **Configuration scripts*** 
* This is the system used by SysV
* Configuration scripts are just that, bash scripts that configure the network adapters
* These run each time the system is booted 
* The scripts can be found: `/etc/sysconfig/network-scripts/`
    * Specifically, `ifcfg-<network-adapter>` is used for IP address configuration

###  **NetworkManager**
- Entire suite of programs to manage networking
- Changes made are persistent
- `nmcli` - CLI tool 
- `nmtui` - Text user interface 
* **network scripts**
    - Network scripts are scripts in `/etc/init.d/network` or `/etc/network` and any other script NetworkManager calls
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
    - **Options**
        - `-t` - terse output: instead of a table format, seperate items with `:`
        - `-f` - field: specific which fields you wanted printed
            - especially good in combination for scripting
        - `-p` - make it pretty - always good to use in combination with `show`

### Netplan
* `netplan`
    * YAML network configuration abstraction for various backends
    * It is a way of declaratively ocnfigure interface devices 
* **Configuration**
    * `/etc/netplan/*`
    * `netplan try` 
        * Command to run to test the changes
    * `netplan apply`
        * Apply changes
* Network bonds
    * Same as NIC teaming
    * Several mode, but two are important:
        1. Mode 4: 802.3ad - industry standard for network bonds. It does require switch support
        2. Mode 5: balance-alb (all load balance). Incoming and outgoing traffic is distributed over all available NICs. This mode does not require switch support
    * Configuring it:
        * Modify `/etc/netplan` file   
        * Add section `bonds:` etc 
            * Just look this up 


## Network troubleshooting

- `ifconfig` - interface configuration 
    - Display or configure network interfaces.
    - Changes are non-persistent between reboots
    - Old, obsolete
    
* `ifdown / ifup` - interface down / up 
    - Bring an interface down or up

- `ip` - IP address
    - Changes made with `ip` are non-persistent
    - **Options**:
        - `ip a` - same as ifconfig
        - `ip link show` - show available interfaces. This does not show the IP address etc as `ip a` does
        - `ip link set <interface> (up|down)` - bring an interface up or down. Same as `ifdown`/ `ifup`
        - `ip a add` - add interface configuration      
        - `ip r` - show configured routes. This also shows the default gateway

* `ethtool` - 
    * Query or control network driver and hardware settings
    * Display and modify Network Interface Controller (NIC) parameters
    * legacy tool

* `lshw` - list hardware
    * `lshw -class network`

* `route` - Show route tables.
    - Like `ip` changes made with `route` are not persistent
    - `route -n` - Show the IP addresses, rather than `default`
    - `route add -net <ip> netmask <netmask> gw <gw_address>` - add a route rule
    - `route add default gw <ip>` - add a default gateway

* **Adding persistent routes**
    * **On Ubuntu**: 
        * Add persistent static routes to the `/etc/netplan/*.yaml` file
        * add a section
        ```bash
        routes:
        - to: 10.8.0.0/16
            via: 192.168.1.1
        ```
    * **On RedHat**
        * Use `nmcli connection modify <connection-name> +ipv4.routes "<dest> <router>"`
        * e.g.: `nmcli connection modify wlp4s0 +ipv4.routes 10.8.0.0/16 192.168.1.1` 

* `ping` 

* `traceroute`
    - Trace the route packets take to a network host.

* `nc` - `netcat`
    - Read and write traffic over a network.
    - `nc –l [port]`: Listen mode.
    - `nc [ip] [port]`: Write mode.
    - Used to test connectivity.
    - Afges from a remote computer on the network using netcat.

* `netstat` - network statistics
    - Active internet connections.
    - What remote machines are you connected to.
    - Active UNIX domain sockets.
    - `Netstat -atn`
            - Get ports that are actively being listened to.
            - Shows all active internet connections.

* `ss` - socket statistics
    * Newer version of `netstat`
    * Prints similar information to `netstat`, but prints more simple output and syntax than `netstat`
    * Options (these are actually the same for `netstat`)
        * `-t | -u` - print tcp / udp connections
        * `-l` - print listening connection
        * `-s` - print statistics 
        * `-p` - print the process using the 
        * `-4 | -6` - print ipv4 / ipv6 connection
        * `sport == : <port> and dport == : <port>` - specify specific ports
        * `-tunap` - Shows everything that is being offered by services

- `nmap` - network mapper
    - Look for devices on the network and open ports on devices.
    - 

* `iperf` 
    * Test the maximum throughput of an interface

* `iftop` - interface top
    * Display bandwidth usage infoirmation for a system

* `mtr` 
    * Combination of `ping` and `traceroute`
    * Enables testing the quality of a network connection
    * Identify packet loss

## DNS

- `/etc/hosts`
    - Hosts file.
    - Local hostnames and IPs.
    - Essentially a simplified local DNS.
    - Add an IP with a name here, and you can ping that name.

- `/etc/resolv.conf`
    - This file contains the IP of the DNS server(s).
    - This cannot be modified when using DHCP.

- `/etc/nsswitch`
    - Name service switch
    - This file specifies the order of host name resolution mechanisms
        - Specifies which name servers are looked up and in what order.
    - The `hosts` section is most important:
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

### iptables
* `iptables`
    * Applies to a certain context and consists of rule sets (aka chains) 
    * `iptables [options] [-t table] [commands] {chain/rule spec}`
    * Five default tables 
        * **Filter table**
            * Default table used to typical packet filtering functionality
        * **NAT table**
            * Implement NAT
        * **Mangle table**
            * Alter or rewrite packets' TCP/IP header
        * **Raw table**
            * Configure exeptions involved in connection tracking
        * **Security table**
            * Mark packets with SELinux security context 
    * By default, rule sets are lost on reboot
    * To get around this, install `iptables-services` or `iptables-persistent` on RHEL and Debian respectively
    * **Logging:**
        * Event for iptables are written to `/var/log/messages` or `/var/log/kern.log` files

### Netfilter
* `nftables` - net filter
    *  


### Uncomplicated firewall
* `ufw` 
    * The standard in Ubuntu-like distro's
    * interface for IP tables and is desgiend to simplify the process of configuring firewalls 
    * Like any firewall, allow and block traffic by port number and IP address
    * `status` - active/ inactive
        * `verbose` - print more info
        * `numbered` - print the rule numbersc
        * list the firewall rules
    * `enable` / `disable`
    * `reset` - resets firewall to its default configuration
    * `default (allow|deny) (incoming|outgoing)` - manage the default rules
    * **Managing incoming rules:**
        * `ufw (allow|deny) (service|subnet|IP)`  
        * `allow ssh` - allow incoming ssh traffic
            * `allow 22` - same but with the port number
        * `deny http` - deny http
        * `deny proto (tcp|udp) from (any|IP) to any port <port numbers>` 
        * `(allow|deny) from (subnet|IP) to any port <port numbers>`
    * **Managing outgoing rules:**
        * `ufw (allow|deny) out <to IP | on {interface}> <proto (tcp|udp)> <port {number}>`
        * `ufw deny out to 93.214.56.31 proto tcp port 443` - deny 443/tcp to 93.214.56.31
        * `ufw deny out on eth0 to 192.168.1.100 port 80 proto tcp` - deny based on interface
    * `delete <rule number>` - delete a firewall by specifying its number

### RHEL Firewall
* `firewalld`
    * The standard in RHEL, and used by
        * RHEL
        * CentOS
        * Alma Linux
        * Rocky Linux
        * Oracle Enterprise Linux
    * **Terminology**
        * `netfilter `
            * Actual Linux firewall
            * Packet filtering code that is built-in the Linux Kernel
            * To interact with it, a helper program is needed (e.g `iptables`, `nftables`, `firewalld`)
        * `iptables`
            * Default firewall manager for many distros
        * `nftables`
            * Newer, better, easier than `iptables`
        * `firewalld` 
            * command line frontend for `iptables` or `nftables`
            * this injects the rules into `iptables` or `nftables` 
    * **`firewalld` components:**
        * **Zones**
            * Collection of network cards that is facing a specific direction and to which rules can be assigned
                * For example: Public, DMZ, block
            * Located `/etc/firewalld/zones/`
            * By default, there always is a `public.xml` zone
        * **Interfaces**
            * Individual network cards
            * Always assigned to zones
        * **Services**
            * An XML-based configuration that specifies ports to be opeened and modules that should be used
        * **Forward ports**
            * Used to forward traffic on a specific port to another port, maybe on a different machine 
        * **Masquerading**
            * Provides NAT
        * **Rich rules**
            * Extension to the `firewalld` syntax to make more complex configuration possible
            * Create custom rules that cannot be created with the basic syntax
            * For example: configure logging, port forwarding, masquerading, rate limiting
            * `firewall-cmd --add-rich-rule='<rule>'` 
            * Check `man 5 firewalld.richlanguage` for the syntax
    * **Basic commands:**
        * `firewall-cmd --add-port=8080/tcp` - open up port 8080/tcp
            * this configuration will be visible in `nft list ruleset`
        * `firewall-cmd --list-all-zones`
            * View all available firewall zones and rules in their runtime configuration state 
        * `--permenant` - make permanent changes 






