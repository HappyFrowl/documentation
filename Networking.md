# basic networking

## ifconfig
- Display or configure network interfaces.

## ifdown / ipup
- Bring an interface down.

## ipconfig [interface] [IP/]
- Change IP on a given interface.
- These changes are non-persistent.

## ip a
- Same as `ifconfig`.

## ip link set [interface] up/down
- Use `ifdown/ifup` to manage interfaces.

## route
- Show route tables.

## route –n
- Show just the IP addresses, rather than “default”.

## route add default gw [IP]
- Add default gateway.

## /etc/hosts
- Hosts file.
- Essentially a simplified local DNS.
- Add an IP with a name here, and you can ping that name.

## /etc/nsswitch
- Name service switch.
- Specifies how name servers are looked up and in what order.
- The ‘hosts’ section is most important:
  - First, it looks in its hosts file, then checks DNS.

---

# Network Troubleshooting

## host [domain]
- Look up IP addresses.
- Shows which servers handle the mail.

## nslookup
- Query DNS servers.

## dig
- Domain Information Groper.
- Query nameservers.

## dig [@server] [domain] [record-type]
- `@server` is optional for a remote query.
- DNS lookup.

## info
- Also TTL.

## Netstat
- Network interface stats.
- Shows:
  - Active internet connections.
  - What remote machines are you connected to.
  - Active UNIX domain sockets.

## traceroute
- Trace the route packets take to a network host.

## netcat
- Read and write traffic over a network.
- `netcat –l [port]`: Listen mode.
- `netcat [ip] [port]`: Write mode.
- Used to test connectivity.
- After initiating listen mode, you can send messages from a remote computer on the network using netcat.

---

# Configure Client-Side DNS

## /etc/hosts
- Local hostnames and IPs.

## /etc/resolv.conf
- Set DNS name server to perform DNS queries.
- This cannot be modified when using DHCP.

## /etc/dhcp/dhclient.conf
- Add line `supersede domain-name-server [IP DNS servers]`.

## getent hosts
- Get all known hosts.
- Essentially lists the contents of `/etc/hosts`.

---

# Security Administration Tasks

## nmap
- Look for open ports.

## Netstat -at
- Get ports that are actively being listened to.
- Shows all active internet connections.

## lsof
- Get open ports/files.
