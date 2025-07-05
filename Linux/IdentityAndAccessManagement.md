# Identity and Access Management
- [User and Group Management](#user-and-group-management)
- [File permissions and ownership](#file-permissions-and-ownership)
- [LDAP](#ldap)

## User and Group Management

### **Users**

**User types**
* **Root**
  - Super user
  - Admin user
  - User ID 0 

* **Regular user:**
  - Runs apps
  - Configures databases, websites, etc

* **Service user:**
  - Specific to the service (webserver, database)
  - No interactive login
  - Runs in the background


**Substituting/ switching users:**
- `su - <username>` - switch to another user account 
  * using the `-` ensures full access to the target user environment
  * it starts a new login shell giving access to the user environment variables
- `su -`, `su - root` or `sudo su` - switch to root
- `sudoedit` - gain elevated editing permissions to a specific file if, and only if, the user is member of `editors` group and the group has editing permissions to the file
    - `%editors ALL = sudoedit /path/to/file`

- `visudo`
    - This is the command in order to edit `/etc/sudoers`
    - Add users to `sudo` group 
    - `visudo -c` checks the sudoers file for errors
    - The basic structure of a sudoers rule is as follows:
      - `<user> <host>= (<run-as user>) <command> : <SELinux role>`
    - Examples
      - Add john to sudoers
          - `john ALL=(ALL) ALL:ALL`
      - Ensure all users can use `command`
          - `ALL ALL=(ALL) /bin/command`
      - Give `john` access to `command` and to `updatedb`
          - `john ALL=/path/to/command, /bin/updatedb`
      - Let `user` use `updatedb` without prompting for password
          - `username ALL=(ALL) NOPASSWD: /bin/updatedb`


### **Creating and managing users and groups:**
- `useradd` - add a user
    - users are stored in `/etc/passwd`
        - it contains: `user:password:uid:gid:some_comment:homedir:defaultshell`
    - if a home directory is created, folders stated in `/etc/skel/` are created in it by default
    - `-D` - show default settings for creating a user
    - `-u` - choose a specific uid
    - `-s` - choose a specific shell
    - `-m` - explicitely create a home directory
    - `-M` - explicitely DO NOT create a home directory
    - `-r` - create system user
    - `-e` - set expiration date for the user
    - `-c` - add comment to the user, e.g. full name
  * `/etc/login.defs` 
    * Config file that contains all parameters that are used when creating a user
      * Such as password settings
- `userdel` - delete a user
    - `-r` - delete the home directory also
- `usermod` - modify a user
    - `-l` - set new login name
    - `-u` - set new uid
    - `-a -G` - add the user to a group
    - `-L` - lock user
    - `-U` - unlock user
- `groupadd` - create a group
    - groups are stored in `/etc/group`
        - `groupname:grouppassword:gid:usersingroup`
    - `--system` - create a system group
    - `--gid` - set gid
    - `-o` - create group with duplicate gid
- `groupmod` - modify group
    - `-g` - change name
    - `-u` - change gid
- `gpasswd` - modify group membership
    - `-A user1,user2 group` - define group admins
    - `-M user1,user2 group` - set list of group members
    - `-a user group` - add user to group
    - `-d user group` - delete user from group
- `chage` - change expiration date 
    - `-E` - set expiration date for user account
    - `-l` - list expiration date for user
    - `-w` - set a warning for a user
- `passwd` - change password for a user
    - Passwords are stored in `/etc/shadow`
        - It contains:
            - Username
            - Password
                - if the password is `!` and more nothing, the password is invalid
                - if it contains something like `!$y$j9T$TW4`, **then the user is disabled**
            - Total number of days since 1 jan 1970 since the password was changed
            - Min days of days required between password changes 
            - Max days of days a password is valid 
            - Number of days after password is expired that the account will be disabled 
            - Days after jan 1 1970 that the account will be disabled 
    - `-l | -u` - lock or unlock a user 
    - `-d` - make password blank
    - `-S` - see password status for a user
    - `--expire` - immediately expire a user's password, force password change on next login
- `groups <$USER>` - print groups of which someone is member 
- `id`- get user id and group membership
- `getent` - read entry from `/etc/` files
    - Options:
      * `getent group`
      * `getent passwd`
      * `getent hosts`
    * It is also possible to specify a group, user or host

* `vigr`
  * Manage groups by editing the `/etc/group`

* `vipw`
  * Manage users by editing the `/etc/passwd`

### **sudo groups**
- `sudo`
    - Group for granting users `sudo` right
    - Used on Debian-based systems
    - Group access is configured by `/etc/sudoers/` through `visudo` alone, never by editing the file itself
- `wheel` 
    - Group for granting users `sudo` right
    - Used on distros that do not by default have the `sudo` group
    - e.g. CentOS, RedHat

### Managing user sessions
- `who` - Print who is logged in and the related data (processes, boot time)
  - Prints name of the system from which they are connecting 
  - `-u` - print who is idle and for how long. 
    - `old` means in active for over 24h
- `w` - Print who is logged on and what they are doing.
  - Prints user login, TTY, remote host, login time, idle time, current process
* `loginctl` - login control
  * `list-sessions`
  * `show-session <id>`
  * `show-user <username>`
  * `terminate-session <session-id>`
- `lastlog` - print when users last logged in 



## File permissions and ownership

### **File ownership** 
-  `chown` / `chgrp`
  - `(chgrp|chown) testuser path/to/file_or_dir` - change owner or group of a file or directory
  - `(chgrp|chown) -R path/to/file_or_dir` - change group recursively
  - `chown user:group path/to/file_or_dir` - change owner and group of the file or directory 

### **File permissions**
- `ls –la` results in something like this:

  - ```bash
    drwxrwxr-x 2 ismet ismet    4096  Nov 20 20:19 .
    drwxrwxr-x 3 ismet ismet    4096  Nov 13 09:46 ..
    -rw-rw-r-- 1 ismet learners 11324 Nov 20 20:25 NOTES.md
    -rw-rw-r-- 1 root  hackers  1135  Nov 21 17:25 important.txt
    ```

- **How to read:** 
  - The first bit is the file type - `-` for file, and `d` for directory
  - The rest of the bits are grouped in pairs of three 
    - The first three being user permissions, second three group permissions, last three other 
  - The last bit is the access method. 
    - `.` - SELinux 
    - `+` - Other 

**Changing permissions**
- `chmod` applies to 
  - `u` - users
  - `g` - group
  - `o` - other
  - `a` - all

- **Permissions operators**
  - `+` - add permissions
  - `-` - remove permissions
  - `=` - set exact permissions

- **Permissions attributes**
  - `r` - read
  - `w` - write
  - `e` - execute

- `chmod` symbolic mode:
  - `chmod ug+w [file]` - add write permissions to user and group
  - `-v` - verbose for debugging
  - `-f` - change permissions recursively
  - `-f` - hide errors
  - `-c` - display output of changes

- `chmod` absolute mode:
  - `4` - read
  - `2` - write 
  - `1` - execute
  - `chmod -c 777 file.txt` - give rwxrwxrwx permissions to file.txt. Everyone can read, write and execute 

- `umask`
  - This sets the default file permissions for newly created files
  - It specifies what permissions are restricted
  - So it works inverted compared to `chmod` 
  - `umask` shows current value in numerics
  - `umask -S` - view symbolic mode
  - `umask 777` - restrict `rwx` for everyone
  - `umask  a-e` - remove default execute permissions for everyone

### **Access Control Lists**
- More granular file control than permissions
  - Set access to one or multiple directories
  - Grant permission to more than one user and/or group 
- `getfacl` - Get current file ACL. It outputs:
  - file, owner, group, and user/group/other permissions
- `setfacl` - set file ACL for a user or group specifically
  - `setfacl -m u:username:rwx /path/to/file_or_directory` - give `username` read, write, and execute permissions to `file`
  - `setfacl -x -all path/to/file_or_directory` - remove all permissions for all users. Same as `chmod 0000 path/to/file_or_directory`
  - `-b` - Remove all entries except standard permissions


**Attributes**
* Attributes are a POSIX style of adding security to files
- `lsattr`
  - list attributes of files and directories
  - Of the ones mentioned below, the `immutable` attribute is one of the most used
  - Many other ones are not supported or used any more

| Position | Attribute | Meaning                                                                |
|----------|-----------|------------------------------------------------------------------------|
| 1        | a         | Append-only: File can only be opened in append mode.                  |
| 2        | c         | Compressed: File is compressed on disk automatically by the filesystem.|
| 3        | d         | No dump: File will not be backed up by the dump program.              |
| 4        | e         | Extents: File is using extents for mapping blocks (ext4-specific).    |
| 5        | i         | Immutable: File cannot be modified, deleted, or renamed.             |
| 6        | j         | Data journaling: All data is written to the journal first.            |
| 7        | s         | Secure deletion: File contents are erased securely (if supported).    |
| 8        | t         | No tail-merging: Disable tail-merging for this file.                  |
| 9        | u         | Undeletable: File can be recovered after deletion.                   |
| 10       | A         | No atime updates: Access timestamp is not updated.                   |
| 11       | D         | Synchronous directory updates.                                        |
| 12       | S         | Synchronous updates: File updates are written synchronously.         |
| 13       | T         | Top-level directory: Reserved for ext3/ext4 directory indexing.      |
| 14       | h         | Huge file: Indicates a huge file (specific to ext4).                 |
| 15       | E         | Encrypted: File is encrypted (ext4 encryption).                      |


- `chattr` 
  - Change attributes of files or directories
  - `chattr +i path/to/file_or_directory` - make a file or directory imutable to changes and deletion, even by superuser
  - `chattr -i path/to/file_or_directory` - make it mutuable again
  - `chattr =abc /path/to/file_or_directory` - set the attributes to abc
  - `-R` - same but recursively


### **Special permissions**
* Less privileged users are allowed to execute a file by assuming the privileges of the file's owner or group
* Three types: **SUID**, **SGID**, **Sticky Bit**
* They are represented by the characters `s` and `t`  in place of the execute (x) permission for the user, group, and others in the permission string.
  * They can be upper or lower case
* Files with Set UID or Set GID can be read by using `ls`. The files will pop out with a red background
  * `-rwsr-xr-x 1 root root 59704 Nov 21 21:01 mount`
* Red background + white letters = set UID
* Yellow background + black letters = set GID
* Red background + yellow letters = set UID and set GID



1. **SUID - Set User ID** 
- `s`/ `S` on user position
- **Purpose**: Allows a file to execute with the permissions of its owner, regardless of who runs it.
- **Applicable to**: Executable files.
- **Usage**: Commonly used for programs that require elevated privileges (e.g., `passwd`).
- **Symbol**:
  - `s` replaces `x` in the user execute field if executable `rws------`
  - `S` appears if the file lacks execute permissions `rwS------`
    * This basically means it is inactive
- **Implementation**:
  ```bash
  # Symbolic
  chmod u+s file   
  # Numeric (4xxx)
  chmod 4755 file  # Sets rwsr-xr-x
  ```
---
2. **SGID - Set Group ID** 
- `s`/ `S` on group position
- **Purpose:**
  - For files: Ensures the file executes with the permissions of its group.
  - For directories: Files created inside inherit the group ownership of the directory, not the creator’s primary group.
- **Applicable to:** Files and directories.
- **Symbol:**
-   `s` replaces `x` in the group execute field if executable `rwxr-sr-x`
-   `S` appears if the file lacks execute permissions `rwxr-Sr-x`
  * The permissions do shit 
- **Implementation**:
  ```bash
  # Symbolic
  chmod g+s file   
  # Numeric (2xxx)
  chmod 2755 file  # Sets rwxr-sr-x
  ```
---
3. **Sticky Bit** 
- `t` / `T` on others execute position
- **Purpose:** Restricts deletion of files within a directory. Only the owner of a file, the owner of the directory, or the root user can delete files in that directory.
- **Applicable to:** Directories (rarely used on files).
- **Symbol:**
  - `t` replaces `x` in the others execute field if executable `rwxr-xr-t`
  - `T` appears if the directory lacks execute permissions `rwxr-xr-T`
- **Implementation**:
  ```bash
  # Symbolic
  chmod o+t file   
  # Numeric (1xxx)
  chmod 1755 file  # Sets rwxr-xr-t
  ```


## LDAP

* `sssd`
