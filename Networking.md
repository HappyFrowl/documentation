# Networking

- [IP and routing](#ip-and-routing)
- [NetworkManager](#NetworkManager)
- [DNS](#DNS)
- [DHCP](#DHCP)
- [Firewall](#Firewall)
- [NGINX](#NGINX)


## IP and routing 
- `ifconfig`
    - Display or configure network interfaces.
    - `ifconfig interface_name ip_address` - assign an IP ddress to an interface
        - changes are non-persistent between reboots

- `ip`
    - `ip a` - same as ifconfig
    - `ip link set <interface> (up|down)` - bring an interface up or down 
    - Non persistent
    
- `ifdown / ipup`
    - Bring an interface down.

- `route` - Show route tables.
    - Like `ip` changes made with `route` are not persistent
    - `route -n` - Show the IP addresses, rather than `default`
    - `route add -net <ip> netmask <netmask> gw <gw_address>` - add a route rule
    - `route add default gw <ip>` - add a default gateway

- `iptables` - Configure tables, chains and rules of the Linux kernel IPv4 firewall

- `traceroute`
    - Trace the route packets take to a network host.

- `nc` - `netcat`
    - Read and write traffic over a network.
    - `nc –l [port]`: Listen mode.
    - `nc [ip] [port]`: Write mode.
    - Used to test connectivity.
    - Afges from a remote computer on the network using netcat.

- `netstat` - network interface stats.
    - Active internet connections.
    - What remote machines are you connected to.
    - Active UNIX domain sockets.
    - `Netstat -atn`
            - Get ports that are actively being listened to.
            - Shows all active internet connections.

- `nmap`
    - Look for open ports.

- `lsof`
    - Get open ports/files.

## NetworkManager
- Entire suits of programs to manage networking
- changes made are persistent
- `nmcli` - CLI tool 
- `nmtui` - Text user interface 

**Network Scripts**
- `network scripts` are scripts in `/etc/init.d/network` or `/etc/network` and any other script NetworkManager calls
- When `ifdown` is executed, scripts in `/etc/network/if-down.d` are run
- Although NetworkManager provides the default networking service, scripts and NetworkManager can run in parallel and work together
- Running Network scripts should only be done using `systemctl` 
    - `systemctl start|stop|restart|status network`

### `nmcli`
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


## DNS

- `/etc/hosts`
    - Hosts file.
    - Essentially a simplified local DNS.
    - Add an IP with a name here, and you can ping that name.

- `/etc/nsswitch`
    - Name service switch.
    - Specifies how name servers are looked up and in what order.
    - The ‘hosts’ section is most important:
    - First, it looks in its hosts file, then checks DNS.

- `/etc/hosts`
    - Local hostnames and IPs.

- `/etc/resolv.conf`
    - Set DNS name server to perform DNS queries.
    - This cannot be modified when using DHCP.

- `/etc/dhcp/dhclient.conf`
    - Add line `supersede domain-name-server [IP DNS servers]`.

- `getent hosts`
    - Get all known hosts.
    - Lists the contents of `/etc/hosts`.

- `host <domain>`
    - Look up IP addresses.
    - Shows which servers handle the mail.

- `nslookup`
    - Query DNS servers.

- `dig`
    - Domain Information Groper.
    - Query nameservers.
    - `dig @<server> <domain> <record-type>`
        - `@server` - for a remote query
    


## DHCP





## File & data transfer

* `scp` - secure copy
    * copying over SSH

* `sftp`
    * more complex


* `rsync`
    * supports resuming transfers and only copies changes


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








## nginx











