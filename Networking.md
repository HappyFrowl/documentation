# Networking

- [IP and routing](#ip-and-routing)
- [DNS](#DNS)
- [NGINX](#NGINX)

## IP and routing 
- `ifconfig`
    - Display or configure network interfaces.
    - `ifconfig interface_name ip_address` - assign an IP ddress to an interface
        - changes are non-persistent between reboots

- `ip`
    - `ip a` - same as ifconfig
    - `ip link set <interface> (up|down)` - bring an interface up or down 
    
- `ifdown / ipup`
    - Bring an interface down.

- `route` - Show route tables.
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
    - After initiating listen mode, you can send messages from a remote computer on the network using netcat.


- `netstat` - network interface stats.
    - Active internet connections.
    - What remote machines are you connected to.
    - Active UNIX domain sockets.
    - `Netstat -at`
            - Get ports that are actively being listened to.
            - Shows all active internet connections.

- `nmap`
    - Look for open ports.

- `lsof`
    - Get open ports/files.



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


- `host <domain>`
    - Look up IP addresses.
    - Shows which servers handle the mail.

- `nslookup`
    - Query DNS servers.

- `dig`
    - Domain Information Groper.
    - Query nameservers.
    - `dig @<server> <domain> <record-type>`
        - `@server` is optional for a remote query.
    
- `/etc/hosts`
    - Local hostnames and IPs.

- `/etc/resolv.conf`
    - Set DNS name server to perform DNS queries.
    - This cannot be modified when using DHCP.

- `/etc/dhcp/dhclient.conf`
    - Add line `supersede domain-name-server [IP DNS servers]`.

- `getent hosts`
    - Get all known hosts.
    - Essentially lists the contents of `/etc/hosts`.


## NGINX


