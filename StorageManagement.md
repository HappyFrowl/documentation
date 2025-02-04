# Storage

## The Linux File System

`/` - Root
- The root directory is the top-level directory in the Linux filesystem hierarchy.

`/bin` - Essential Binaries
- Contains binaries or executables that are essential to the entire OS.
- Commands like `gzip`, `curl`, `ls` are stored here.
- All users can by default run these 

`/sbin` - System Binaries
- Contains system binaries or executables for the superuser (root).
- Example: `mount`.

`/lib` - Libraries
- Shared libraries for essential binaries in `/bin` and `/sbin` are stored here.

`/usr` - User Directory
- Contains non-essential binaries or executables intended for the end user.
- Includes:
  - `/usr/bin`: Non-essential binaries.
  - `/usr/sbin`: Non-essential system binaries.
  - `/usr/lib`: Libraries for `/usr/bin` and `/usr/sbin`.
- `/usr/local`:
  - Contains manually compiled binaries in `/usr/local/bin`.
  - Provides a safe place to avoid conflicts with system-installed packages.
- Path and Precedence
  - All these binaries are mapped together using the `PATH` environment variable (`$PATH`).
  - `which` command shows the location of a binary and gives precedence to `/usr/bin` if the command exists in multiple places.

`/etc` - Editable Text Config
- Stores system configuration files (e.g., `.config` files).

`/home` - User Files
- Contains user-specific files and configurations.

`/boot` - Boot Files
- Contains files required to boot the system:
  - `initrd`
  - The Linux kernel:
    - `vmlinux`: Uncompressed kernel.
    - `vmlinuz`: Compressed kernel.

`/dev` - Device Files
- Provides access to hardware and drivers as if they are files.

`/opt` - Optional Software
- Stores optional or add-on software.

`/var` - Variable Files
- Contains files that change while the OS is running:
  - Logs are stored here.

- `/tmp` - Temporary Files
- Stores temporary files.
- Files in `/tmp` are non-persistent between reboots.

`/proc` - Process Filesystem
- An illusionary filesystem created in memory by the Linux kernel.
- Used to keep track of running processes.


## inodes
- A data structure in the file system that describes a file system object, such as a directory or file.
- Inodes store attributes:
  - Last time of change, access, modification.
  - Owner/permission data.
  - Physical disk location of the object data.
- Directory is a list of names assigned to inodes:
  - Contains entries for itself, its parents, and its children.
- A disk can run out of inodes before running out of disk space, which prevents new file creation.
- Inodes can be used to delete strangely named files that won’t tab-complete.

- `ls -i`: See the inode number.
- `find . -inum <inode number>`: Find file by inode number.


## File localization

* `type`
    - Determines if a command is a shell built-in, alias, or external command.

* `whereis`
    - Outputs the location of a command and its man pages.

* `locate`
    - Locate a file quickly (requires updatedb).

* `updatedb`
    - Must be run in order to use `locate`.

* `/etc/updatedb.conf`
    - Configuration for `updatedb`, which defines locations not to search.

- `stat`
    - Display file or file system status 
    - Output includes details like: 
    -   File name 
    -   File size 
    -   Blocks allocated 
    -   IO block size 
    -   File type 
    -   Device ID 
    -   Inode number 
    -   Links 
    -   Access, Modify, and Change timestamps 


## Hard and Symbolic Links
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


## Disk Quotas
* to start using disk quotes, the package `quotas` is required
* `quotacheck`: Scan a file system for disk usage and create/repair quota files.
* `edquota`: Edit the disk quota for a user.
  - Specifies disk usage, inode usage, and the specific file system to which it applies.
* `quotaon`: Enable quotas and apply the configuration set in `edquota`.
* To implement quotas:
    * modify `/etc/fstab` file
    * add `,usrquota` after `defaults`



## File system management

- **`df`**: 
    * Display filesystem usage.
    * `-h` - human-readable format (e.g., MiBs, GiBs).
    * `-i` - show inodes 

* `du` - disk usage 
    * `-h` - human readable 
    * `-s` - show only total 

* `mount`  
    * mount a file system
    * `-t` - specify target
    * `--mkdir` - create directory if not exists
    * `-a` - mount all file systems defined in `/etc/fstab`

* `umount` - unmount
    * 

* `smartctl` - SMART control
    * Self-Monitoring, Analysis, and Reporting Technology
    * tool for tracking disk drive health, predict failures, and log error and self-tests
    * `--health` - print overall health 
    * `-t (short|long)` - do a disk drive test 
    * `-all` - print all info, including test result

* `fsck`- file system check
    - Check the integrity of a filesystem or repair it
    - **Note**: Do not run on mounted disks.
    - `-a` - immediately repair all damaged blocks


* `lsblk` - list blocks
    - Lists information about storage devices
    - partially same info as `fdisk -l`

* `wipefs` - wipe file system
    * wipe the file system 
    * does not wipe the files themselves


* `mkfs.<type> /dev/sd#` - make file system
    - Build a Linux filesystem on a hard disk partition
    - `mkfs.ext4 /dev/sda1`
    - `-c` - check for bad blocks

## Partitioning

* `fdisk`
    * Manage partition tables and partitions on a hard disk
    * bit of an old tool
    * `fdisk -l` - list partitions 
    * within the fdisk tool, there are several options:
        * `g` - create gpt disk label type
        * `p` - print disk partition info
        * `n` - create a new partition table
        * `w` - write the changes

* `parted`
    * Newer standard for partition management of disks 
    * benefits over `fdisk`
        * in cotnrast to `fdisk`, `parted` allows for resizing of partitions
        * works better for GPT
        * works with large disks
        * allow scripting
    * `mklabel gpt` - create a new disk / partition table
    * `mkpart <name> <FS-type> <start> <end>` - create a partition
    * `resizepart <part number> <new size>` - resize a partition

So in order to take a used disk and repurpose it:
    * wipe disk using `dd`. If you don't need to delete the files `wipefs` works as well 
    * create a new drive label with `parted mklabel` and a new partition using `parted mkpart`
    * format the partition `mkfs.<type>`


* `/etc/fstab`
    - Dictates how mounting happens at startup.
    - Describes what devices are mounted, where, and with which options.
    - **dump**: If enabled, the dump command makes a backup.
    - **pass**: Used by `fsck` to determine the order of file system checks at boot:
        - `0`: No check.
        - `1`: Root disk.
        - `2`: After root disk


* `blkid` - block id
    * print the UUID of devices 
    * this is used to add the devices to `/etc/fstab`


* `dumpe2fs` 
    - Displays all information about a disk (for `ext2`, `ext3`, or `ext4`).
    - Includes block and superblock data, and metadata about the file system
    - 

* `tune2fs` 
    - Used to change variable file system parameters.
    - `tune2fs -l <disk>`: List all details of the disk.
    - `tune2fs <disk> -L <volume name>`: Assign a volume name to a disk.


## LVM
* `pvcreate`
* `vgcreate`
* `lvcreate`
* `resize2fs`


## RAID basics
* `mdadm`


## NFS & samba file sharing


## Swap management
* `swapon`
* `swapoff`
* `mkswap`


## Backup & Recovery

- **`tar`** - Archive files.
  - Common options:
    - `c`: Create an archive.
    - `v`: Verbose (list files being processed).
    - `f`: Specify the filename.
    - `z`: Compress or decompress with gzip.
    - `t`: List archive contents.
  - Examples:
    ```bash
    tar cvf archive.tar file1 file2
    tar zcvf archive.tar.gz file1 file2
    tar tvf archive.tar
    ```

- **`gzip`**: Compress files using the DEFLATE algorithm.
  - Use `gunzip` to decompress.


- **`dd`** - data duplicator / disk destroyer
    * commonly used for disk imaging, data recovery, and even wiping data securely
        * `if` - input file 
        * `of` - output file 
    * disk cloning and imaging
        * `dd if=/dev/sdX of=/path/to/backup.img bs=4M status=progress` 
            * create a disk image
    * converting files to lowercase
    * secure wiping
        * `dd if=/dev/zero of=/dev/sd# bs=1M status=progress`
    * Backup the MBR (Master Boot Record):
        * `dd if=/dev/sda of=mbr.bak bs=446 count=1`
    * disk performance tests
        * measuring read / write speed




