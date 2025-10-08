# Networking
- [Networking](#networking)
  - [Configuring network interfaces](#configuring-network-interfaces)
    - [Introduction](#introduction)
    - [Configuration scripts](#configuration-scripts)
    - [NetworkManager](#networkmanager)
    - [Netplan](#netplan)
  - [Network troubleshooting](#network-troubleshooting)
  - [DNS](#dns)
  - [DHCP](#dhcp)
  - [File \& data transfer](#file--data-transfer)
  - [Firewall](#firewall)
    - [iptables](#iptables)
    - [Netfilter](#netfilter)
    - [Uncomplicated firewall](#uncomplicated-firewall)
    - [RHEL Firewall](#rhel-firewall)


## Configuring network interfaces

### Introduction
* There are multiple ways to configure network adapters
* It differs per distro and per init system
* Mainly there are three:
    1. Configuration scripts
    2. Netplan
    3. Network Manager 


### Configuration scripts
* This is the system used by SysV
* Configuration scripts are just that, bash scripts that configure the network adapters
* These run each time the system is booted 
* The scripts can be found: `/etc/sysconfig/network-scripts/`
    * Specifically, `ifcfg-<network-adapter>` is used for IP address configuration


###  NetworkManager
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
* The network configuration utility developed by Canonical
* It provides a network configuration abstraction over the currently supported two “backend” system (“renderer” in Netplan terminology): networkd and NetworkManager
* **netplan yml files**
  * Netplan provides the ability to set network configuration using yaml files
    * It is a way of declaratively configure interface devices 
    * There are three locations in which Netplan configuration files can be placed. In order of priority they are:
      * `/run/netplan`
      * `/etc/netplan`
      * `/lib/netplan`
* **Configuration**
    * `/etc/netplan/*.yml`
    * `netplan try` 
        * Command to run to test the changes
    * `netplan apply`
        * Apply changes
* Network bonds
    * Same as NIC teaming
    * Several mode, but two are important:
        1. Mode 4: 802.3ad - industry standard for network bonds. It does require switch support
        2. Mode 5: balance-alb (all load balance). Incoming and outgoing traffic is distributed over all available NICs. This mode does not require switch support
    * Add section `bonds:` etc 


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
    * Configuration file for DHCP
    * Edited using `nmcli`
    * Add line `supersede domain-name-server <IP DNS servers>`.


## File & data transfer

* `scp` - secure copy
    * Copying over SSH
    * `scp path/to/local_file remote_host:path/to/remote_file`
    * Not possible to send directories

* `rsync` - remote sync
    * Transfer files or directories either to or from a remote host
        * sshd must be running on the remote host
        * It can also be entirely local or mounts for remote directories
    * Supports resuming transfers and only copies changes
    * It uses the ssh protocol
    * `rsync <file or dir> <dir2>`
    * `rsync <directory> -rv --dry-run user@192.168.1.10:/home/user/backup` - transfer the contents of directory recursively and verbosely to /home/user/backup on remote host with IP 192.1681.1.10, but dry run it   
        * This is not a sync utility, for it never removes files only appends and does nothing about similarly named duplicates
    * `rsync --delete -rv <dir> <host>:<dir>` - delete all the files in the target directory that are **not** included in the source directory
    * `-a` - archive option. This makes sure the metadata (the date) is the same between directories
    * `-z` - zip or compress files 
    * `-P` - Show the progress

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

* **Overview**
  * Tool to manage firewall rules and network traffic
  * It lets admins configure rules that help how packets are filtered, translated, or forwarded
  * Control incoming and outgoing traffic, define port forwarding, and implement network address translation (NAT)
* **Components**
  * Tables
      * Collection of chains
      * aka name for a set of chains
  * Chain
      * Collection of rules 
  * Rule
      * Condition used to match packet
  * Target
      * Action taken when a rule matches
      * aka what must be done with a packet
      * This can be the name of a user-defined chain or one of the special values:
      * ACCEPT
      * DROP
      * QUEUE
      * RETURN
  * Policy
      * Default action taken in case of no match with the inbuilt chains 
      * Possible actions
      * ACCEPT
      * DROP

* **Tables**
  * * Five default tables - used for the `--table` / `-t` option
    * **Filter**
        * This  is  the  default table (if no -t option is passed) for packet filtering
        * Chains included: 
          * INPUT, OUTPUT and FORWARD.
        * `filter`
    * **NAT**
        * Related to Network Address Translation
          * It can also be used for IP forwarding
        * This table is consulted when a packet that creates a new connection is encountered
        * Chains included:
          * PREROUTING, POSTROUTING
        * `nat`
    * **Mangle**
        * For specialized packet alteration
          * Alter or rewrite packets' TCP/IP header
        * Included chains:
          * PREROUTING, OUTPUT
        * `mangle`
    * **Raw**
        * Configure exceptions involved in connection tracking
          * Used  mainly for configuring exemptions from connection tracking in combination with the NOTRACK target.
        * Chains included:
          * PREROUTING, OUTPUT
        * `raw`
    * **Security**
        * Used for MAC, e.g. SElinux, AppArmor
  
  * **Chains**
    * INPUT - A set of rules for packets destined to localhost sockets.
    * FORWARD - For packets routed through the device
    * OUTPUT - It is locally generated packets, meant to be transmitted outside
    * PREROUTING - It is used for modifying packets as they arrive
    * POSTROUTING - It helps in modifying packets as they are leaving

  * **User-defined chains**
    * User-defined chains can also be created. The following are the some of the possible one with examples:
      * `-A`, `--append`: Append to the chain provided in parameters
      * `-D`, `--delete`: Delete rule from specified chain
      * `-C`, `--check`: Check if rule is present in the chain or not
  * **Common used options**
    * `-p`, `--proto` - protocol that the packet follows
    * `-s`, `--source` - source IP of the packet
    * `-d`, `--destination` - destination IP of the packet
    * `-i`, `--in-interface` - Matches packets with specified in-interface 
    * `-o`, `--out-interface` - Matches packets with specified out-interface
    * `-j`, `--jump` - Specifies the action to be taken on a match
      * ACCEPT, DROP, MASQUERADE, DNAT, LOG, MARK, MIRROR, QUEUE, REDIRECT, REJECT, RETURN, SNAT, TOS, TTL, ULOG
    * `iptables [options] [-t table] [commands] {chain/rule spec}`

* **Saving iptable configurations**
    * By default, rule sets are lost on reboot; they are non-persistent
    * To save them, install `iptables-services` or `iptables-persistent` on RHEL and Debian respectively
    * Save them with `iptables-save > /etc/iptables/rules.v4` **as root**
    * Running just `iptables-save` dumps the contents of the configured iptables and does not save them
* **Logging:**
    * Events for iptables are written to `/var/log/messages` or `/var/log/kern.log` files
* **Examples**
* `iptables -t[able] nat -A[ppend] POSTROUTING -d[estination] <IP> -p[rotocol] <protocol>  -j[ump] DNAT --to-destination <IP:port>`
* `iptables -t nat -A PREROUTING -i <interface> -p <protocol> --dport <port> -j MASQUERADE`


### Netfilter
* Framework that allows various networking-related operations to be implemented in the form of customized handlers
* It is the framework underlying iptables
* All rules defined in iptables utilize Netfilter’s capabilities to process packets as they traverse the network stack.
* Moreover, as we will shortly see, the uncomplicated firewall and firewalld utilize and leverage netfilter to process packets as trey traverse the network stack.


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
                * For example: Public, DMZ, block, home, etc
                * More can be created manually with `firewall-cmd --new-zone=`
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
                * `sudo firewall-cmd --add-rich-rule="rule family="ipv4" source address="192.168.1.1/32" service name="ftp" accept"`
                * This allow incoming ftp traffic from 192.168.1.1/32
            * `firewall-cmd --add-rich-rule='<rule>'` 
            * Check `man 5 firewalld.richlanguage` for the syntax
    * **Changing rules:**
        * `firewall-cmd --zone=<zone> --add-port=8080/tcp ` - open up port 8080/tcp. 
          * A zone must always be provided for the change to be applied. **Moreover, it must be called FIRST**
          * Remember: `--permanent` was not provided, so this setting will last until reboot
          * Always check whether the configuration was applied correctly: `firewall-cmd --get-all-zones | grep <port/service>`
            * The success echo means very little, in my experience 
          * This configuration will be visible in `nft list ruleset`
        * `firewall-cmd --add-service=http` - open ports associated to the service
        * `firewall-cmd --list-all-zones`
            * View all available firewall zones and rules in their runtime configuration state 
        * `--permenant` - make permanent changes 
        * `firewall-cmd --runtime-to-permanent` - make changes made in the runtime permanent. Great for first testing a config without the `--permanent` flag, then after testing, making it permanent 
    * **Limiting rule scope**:
      * Not all rules should apply to all (incoming or outgoing) devices. In order to limit the scope of a rule, do the following:
        1. Create a new zone - `firewall-cmd --permanent --new-zone={zone-name}` 
        2. Limit the scope of this zone - `firewall-cmd --permanent [--source={source-IP} | --destination={destination-IP} --]`
        3. Add rules to this scope - `firewall-cmd --add-port={port/protocol}`
        4. Test it out
        5. Make permanent - `firewall-cmd --runtime-to-permanent`
      * This way firewall rules applied to this zone, only apply to a specific source or destination address  
    * **Checking config**
      * `firewall-cmd --list-ports` - list all the ports added.
      * `firewall-cmd --get-active-zones` - Print currently active zones altogether with interfaces and sources used in these zones.
      * `firewall-cmd --zone=public --list-all` - list everything added to a specific zone 
    * **Logging**
      * Checking and setting the log status
        * From command line
          * `firewall-cmd --get-log-denied` - get the current status for log handling
          * `firewall-cmd --set-log-denied=all` - set the log handling status to `all` 
        * From the config file
          * `/etc/firewalld/firewalld.conf`
          * Change `LogDenied=denied` to `LogDenied=all`
      * Reading logs:
        * `journalctl -xe` - check for the yellow lines starting with `filter_IN/OUT_public_REJECT` 






