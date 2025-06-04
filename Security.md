# Host Security

- [Authentication](#authentication)
- [Mandatory Access Control (MAC)](#mandatory-access-control-mac)
- [SSH](#SSH)
- [Pluggable Authentication Modules (PAM)](#pluggable-authentication-modules-pam)


## Hardening
* **Chroot jail**
    * Way to isolate a process and its children from the rest of the system
    * Change the root directory for a process, creating an isolated environment
    * This is particularly useful for system recovery, testing, and creating sandboxed environments.​
    * `chroot [options] <new root dir> [command]`
        * `chroot /home/me /usr/bin/bash`
        * This creates a new bash shell in the home folder as the root folder 

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

* Disabling ctrl+alt+delete
    * 

## Authentication
* **Kerberos** 
    * `kinit`
        * Authenticate Kerberos ticket if successful
    * `kpassword`
        * Change the user's Kerberos password
    * `klist`
        * List the user's ticekt cache
    * `kdestroy`
        * Clear the user's ticekt cache
    * `klist -v`
        * Verify ticekt 


##  Encryption

* **LUKS - Linux Unified Key Setup** 
    * Tool to encrypt storage devices
    * LUKS operations must be performed before creating a file system on it
    * `cryptsetup`
        * front-end to LUKS and dm-crypt
        * `isLuks`      - Identify if a device is a LUKS device
        * `luksFormat`  - Make a device formatted for the LUKS standard    
        * `luksOpen`    - Open or map a storage device 
        * `luksClose`   - Remove a LUKS storage device          
        * `luksAddKey` - Associate new key with a LUKS device
        * `luksDelKey`  - Remove key material from a LUKS device

* **`shred`** 
    * wipe a storage device
    * better to use it to wipe files only
    * Use `dd` for entire storage devices

* **`gpg`**
    - Free and open source implementation of PGP
    - Used to get, create, and send encryption keys, e.g. public keys, "use it to send shit to me" 
    - Encrypt files using `gpg`

* **`openssl`**
    * Installing cert on an Apache server:
        1. Generate OpenSSL
        2. Download an install mod_ssl package
        3. Point SSLCertificateFile
        4. Point SSLCertificateKeyFile
        5. Restart Apache
        6. Verify

## System auditing
* `auditd` - audit daemon
    * It enables records to be used to audit what is being written to storage and what actions are happening
    * Best practise is to have it enabled

* `ausearch`
    * 

- `xinetd` - X Internet Daemon.
    - Old shit, unsecure
    - Listens to incoming requests over the network.
    - Launches the appropriate program to handle the request.
    - Requests are often for specific ports.
    - Manages which services can be open at specific times.

- `tcpd` - tcp dump
    - Monitor incoming requests.
    - Program triggered by `xinetd`.
    - `xinetd` starts `tcpd` instead of the desired server.
    - `tcpd` logs the request.
    - Logs are sent to `syslog`.
    - After logging, `tcpd` runs the program.



## **Mandatory Access Control (MAC)**

* **MAC**
    * System-enforced access control based on subject clearance and object labels
    * Ways to restrict access to system processes into the files, directories, network, prots and other things like that
    * **Context-based permissions**
        * Permission scheme fthat defines various properties for a file or process
        * Grant or deny access 
    * **App-armor** and **SELinux** are the main examples
    * MAC differs from the default scheme in Linux which is known as **Discretionary Access Control (DAC)**
        * In DAC, each object has a list of entities that are allowed to access it
        * The object owner administrates this, using `chown`, `chmod`, etc.

* **SELinux**
    * Does not allow DAC, but enforces MAC to do its permissions and access control
    * SELinux is the default context-based permissions scheme provided within RHEL
    * **Three main context for each file and process**
        * **User** - Defines what users can access the object
                * `unconfined_u` - all users
                * `user_u` - unprivileged users
                * `sysadm_u` - system admins
                * `root` - root user
        * **Role** - permits or denies users access to domains
            * Users can be in roles, and those roles are typically used to permit or deny access to the given domain, or objects
            * `object_r` - role applies to files and directories
        * **Type** - Groups objects together that have similar securtity requirements or characteristics
            * Most important context
            * It is a label
        * **Level** - describes the szensitivity level called "multi-level security" 
            * It is completely optional
            * Further finegrained access control
    * **Three modes** 
        * **Disabled**     - SELinux is turned off and the DAC method will be prevalent
        * **Enforcing**    - SELinux security policies are enforced
        * **Permissive**   - SELinux is enabled but the security policies are not enforced. This means that processes can bypass the security policies. 
    * **Policy types**
        * **Targeted**  - Default SELinux policy used in RHEL. Targeted processes are run in a confined domain, those not targeted are not run in a confined domain
        * **Strict**    - System subject and object is enforced to operate on MAC. MAC is always enforced
    * Managing SELinux:
        * `semanage`    - Configure SELinux policies
        * `sestatus`    - Get SELinux status
        * `getenforce`  - Display SELinux mode
        * `setenforce`  - Change SELinux mode 
        * `getsebool`   - Display the on or off status
        * `ls-Z`        - List directory contents
        * `ps-Z`        - list running processes
        * `chcon`       - Change the security context of a file
        * `restorecon`  - Restore the default security context
    * Violation messages
        * These occur when an attempt to access or modify an object goes against an existing policy
        * `sealert` - alert message. Difficult to read.
        * `audit2why` - translate the violation
        * `audit2allow` - Used to gather information from the denied operations log        

* **AppArmor**
    * High-level overview:
        * Normally, a user account can write to its home directory... Makes sense
        * Often, that user can also write to other place on the hard drive. 
        * But some user accounts are not tied to a user, but to a service, e.g. Apache
        * Does that need to write to the whole directory
        * AppArmor locks down what applications can do
    * Alternative context-based permissions scheme and MAC implementation for Linux
    * Works with file system object, rather than referencing the inodes directly as SELinux does
    * AppArmor is much easier to administrate
    * Profiles are locted in the `/etc/apparmor.d/` directory
    * Managing AppArmor:
        * `apparmor_status`    - Display the current status    
        * `aa-complain`        - Place a profile in complain mode
        * `aa-enforce`         - place a profile in enforce mode
        * `aa-disable`         - disable profile
        * `aa-unconfined`      - list processes with open network sockets




## SSH

### Configuring SSH Server
- It starts with installing the openSSH server and client
- `/etc/ssh/sshd_config`
    - Config file for the SSH **server**
    - Manage the following:
        - **Port** - Set the port to which the server listens for SSH connections
        - **PasswordAuthentication** - Enable or disable password-based authentication 
        - **PubKeyAuthentication** - Enable or disable public key-based authentication
        - **Hostkey** - Reference the locations of the server's private keys
        - **UsePAM** - Enable or disable support for PAM (Puggable Authentication Modules, see IAM)
        - **SyslogFacility**- Change the logging level of SSH event 
        - **ChrootDirectory** - Reference a chroot jail path for a user 
        - **AllowUsers/AllowGroups** - Enable user-specific access by allowing the specified users or groups access over SSH 
        - **DenyUsers/DenyGroups** - Restrict the specified users or groups from accessing the server over SSH
        - **PermitRootLogin** - Enable or disable the abiltiy for the root user to log in over SSH (best not to).
    - Remember to open the port on the firewall when changing the default port 
        - `ufw` for Ubuntu
        - `firewall` for RHEL 

### Configuring SSH client
- `/etc/ssh/ssh.config`
    - Configure outbound SSH connections.
    - Example: Turn off password-based authentication, allowing only certificate-based authentication.
- `~/.ssh/` 
    - Location where SSH client configurations are stored
    -  `known_hosts` - list of all known hosts to which the client has connected. It stores all the other hosts' fingerprints
- `ssh-keygen`
    - Generate a public / private key pair
    - `ssh-keygen -t <ed25519> -f <path/to/file>`
- `ssh-copy-id <server IP>`
    - Copy and add the public key to a remote computer's `~/.ssh/authorized_keys` file
    - Enter the password of the account on the remote PC.
    - Once done, you can SSH without entering a password.
- `ssh-add` 
    - Add the default SSH keys in `~/.ssh` to the ssh-agent
    - Add private key identities to the SSH key agent on the ssh key client
- `ssh-agent`
    - Having a passphrase on the ssh key is secure, but it requires to be entered each time when you connect to a server
    - That is inconvenient
    - Using `ssh-agent` the passphrase can be cached for the duration of the bash session
    - Workflow
       - `ssh-agent /bin/bash`
       - `ssh-add` 
- `ssh -X`
    - X11 forwarding


## **Pluggable Authentication Modules (PAM)**
* PAM is used to help apps make proper use of user accounts 
  * Think programs like: Login, GDM, SSHD, FTPD
* LDAP is one of the ways, such as W*ndws' Active Directory
* `/etc/pam.d`
  * PAM config files
  * Each PAM-aware service or app will have its own file inside this directory
  * It is here where PAM is configured
  * Each of these files will contain directives
  * These directives are formatted as four different things
    * `<module interface>`
      * Defines the functions of the authentication and authorization proocess contained within a module
    * `<control flag>`
      * Inidcate what should be done upon a success or failure of the module
    * `<module name>`
      * Finds the module that the directive is going to apply to 
    * `<module arguments>`
      * Additional options that can pass into the module

  * Module example: **password policy directive** 
    * `password required pam_cracklib.so retry=5`

Let's dive into the three mentioned components
1. **Module interfaces**
  * **Account**
    * Verifies passwords and set credentials (Kerberos tickets)
    * May also set credentials (e.g., Kerberos tickets, tokens).
  * **Auth**
    * Verifies user identity, typically by prompting for and checking passwords.
    * May also set credentials (e.g., Kerberos tickets, tokens).
  * **Password**
    * Handles password changes and password verification
    * Verifies old passwords and enforces password strength policies.
  * **Session**
    * Manages actions that occur at the beginning and end of a user session.
    * Examples: mounting directories, logging, setting limits, starting user-specific services.

2. **Control flags**
* This tell PAM what to do
  * **Optional**
    * Result is ignored unless it's the only module for the service
  * **Required**
    * Result must be successful for authentication to continue
  * **Requisite**
    * Same as Required, but is going to notify that user immediately
  * **Sufficient** 
    * Result is ignored upon failure
  * **Include**
    * Include the rules from another PAM configuration file.

3. **Module Names or pam libraries**
  * **pam_pwquality.so**
  * **pam_history.so**
  * **pam_tally2**
  * **pam_faillock**
  * **pam_ldap**

  * These are located in `/lib64/security`
















