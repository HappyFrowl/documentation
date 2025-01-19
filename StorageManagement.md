# Storage Management 

## File systems

### Inode
- A data structure in the file system that describes a file system object, such as a directory or file.
- Inodes store attributes:
  - Last time of change, access, modification.
  - Owner/permission data.
  - Physical disk location of the object data.
- Directory is a list of names assigned to inodes:
  - Contains entries for itself, its parents, and its children.
- A disk can run out of inodes before running out of disk space, which prevents new file creation.
- Inodes can be used to delete strangely named files that won’t tab-complete.

### Commands:
- `ls -i`: See the inode number.
- `find . -inum <inode number>`: Find file by inode number.
- `df -i`: Check the inodes being used on a disk (lists inode space).

- `df`
    - Report file system disk space.

- `lsblk`
    - List block devices.
    - Displays the tree of disks and partitions.

- `fdisk`
    - Manipulate disk partitions.

- `mkfs`
    - Create file systems.
    - Standard is `ext2`, but can be specified as `ext4` with `-type ext4`.

### mount and unmounting 
- Mount file systems.
- Unmount file systems.
` mount -a`
- Mounts everything according to the fstab file.

### /etc/fstab
- Dictates how mounting happens at startup.
- Describes what devices are mounted, where, and with which options.
  - **dump**: If enabled, the dump command makes a backup.
  - **pass**: Used by `fsck` to determine the order of file system checks at boot:
    - `0`: No check.
    - `1`: Root disk.
    - `2`: After root disk.


### du
- Disk usage.
- Estimates file space usage and shows file sizes.

### Examples:
- `du -s`: Shows just the total.
- `du / --max-depth=1 -h`: Checks only the first level of the directory.

## fsck
- Check and repair file system problems (integrity check).
- **Note**: Do not run on mounted disks.

## dumpe2fs
- Displays all information about a disk (for `ext2`, `ext3`, or `ext4`).
- Includes block and superblock data, and metadata about the file system.

## tune2fs
- Used to change variable file system parameters.
  
### Examples:
- `tune2fs -l <disk>`: List all details of the disk.
- `tune2fs <disk> -L <volume name>`: Assign a volume name to a disk.

---




---

# Fuser
- Shows which process is using a particular directory.
- Outputs the process ID.

### Examples:
- `ps -aux | grep <pid>`: Find the process details.
- `ps -auxf`: Shows a tree overview of all processes.

---

# Disk Quotas

## Changing fstab
- After `defaults`, add `,usrquota`.

## Prerequisite
- Install the package `quotas`.

### Commands:
- `quotacheck`: Scan a file system for disk usage and create/repair quota files.
- `edquota`: Edit the disk quota for a user.
  - Specifies disk usage, inode usage, and the specific file system to which it applies.
- `quotaon`: Enable quotas and apply the configuration set in `edquota`.

---

## Creating Hard and Symbolic Links
- Hard Link
    - A link to another file’s inode.
    - Can only occur within the same file system.
  
- Symbolic (Soft) Link
    - A link to another file name.
    - Can span across different file systems.
    - Akin to shortcuts in Windows.

- Hard Links
    - `ls -la`: Displays hard link count (second column).
    - Directories have a minimum of 2 links: one for itself and one for its parent.
    - Files have a minimum of 1 link.
    - Create hard links with `ln <sourcefile> <destfile>`.
    - Check links with `ls -li`.
    - **Note**: Hard links are not allowed for directories.

- Soft Links
    - Useful for linking files between file systems.
    - Create soft links with `ln -s <sourcefile> <destfile>`.

---

# Finding System Files and Placing Files in the Correct Location

## type
- Determines if a command is a shell built-in, alias, or external command.

## whereis
- Outputs the location of a command and its man pages.

## locate
- Locate a file quickly (requires updatedb).

## updatedb
- Must be run in order to use `locate`.

## /etc/updatedb.conf
- Configuration for `updatedb`, which defines locations not to search.
