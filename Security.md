# Host Security

## /etc/hosts.allow
- List hostnames or domains that can access the system.

## /etc/hosts.deny
- Deny access.
- Access is granted by default.

## /etc/nologin
- Lock down system.
- If this file exists, only root can access.

## xinetd
- X Internet Daemon.
- Listens to incoming requests over the network.
- Launches the appropriate program to handle the request.
- Requests are often for specific ports.
- Manages which services can be open at specific times.

## tcpd
- Monitor incoming requests.
- Program triggered by `xinetd`.
- `xinetd` starts `tcpd` instead of the desired server.
- `tcpd` logs the request.
- Logs are sent to `syslog`.
- After logging, `tcpd` runs the program.

---

# Securing Data with Encryption

## SSH

### /etc/ssh/ssh.config
- Configure outbound SSH connections.
- Example: Turn off password-based authentication, allowing only certificate-based authentication.

### /etc/ssh/sshd.config
- Configure the SSH server.
- Manages incoming SSH connections.
- Configure the port to which it listens for SSH connections.

### SSH Keys
- Private and public keys.
- Used for secure authentication.

#### ssh-keygen
- Create SSH keys.

#### ssh-copy-id [IP]
- Copy and add the public key to `~/.ssh/authorized_keys` on the remote machine.
- Enter the password of the account on the remote PC.
- Once done, you can SSH without entering a password.

#### ssh -X
- X11 forwarding.

---

## gpg
- Free implementation of PGP.
- Used to get, create, and send encryption keys.
- Encrypt files using `gpg`.
