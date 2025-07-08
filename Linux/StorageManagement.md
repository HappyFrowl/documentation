# Storage
- [Storage](#storage)
  - [Storage and Filesystem Basics](#storage-and-filesystem-basics)
    - [The Linux Filesystem](#the-linux-filesystem)
    - [inodes](#inodes)
    - [Hard and Symbolic Links](#hard-and-symbolic-links)
    - [File types and localization](#file-types-and-localization)
    - [RAID](#raid)
    - [Swap management](#swap-management)
  - [Disk, partition, and filesystem management](#disk-partition-and-filesystem-management)
    - [Introduction and overview](#introduction-and-overview)
    - [Managing disks and partitions](#managing-disks-and-partitions)
    - [Mounting \& unmounting](#mounting--unmounting)
    - [Managing filesystems](#managing-filesystems)
    - [XFS-specific filesystem management tools](#xfs-specific-filesystem-management-tools)
  - [Logical Volume Manager](#logical-volume-manager)
  - [Archiving, Backup \& Recovery](#archiving-backup--recovery)
  - [Storage Troubleshooting](#storage-troubleshooting)
  - [Remote Filesystem Access](#remote-filesystem-access)


## Storage and Filesystem Basics
* **Section overview**
    * This section will offer a brief overview over storage and filesystem basics
    * It covers the Linux filesystem, inodes, hard and symbolic linking, how to localize files, and the essential of RAID configuration

### The Linux Filesystem
* To read up on the Linux filesystem:
    * `man hier`

`/bin` - Essential Binaries
- Contains binaries or executables that are essential to the entire OS.
- Commands like `gzip`, `curl`, `ls` are stored here.
- All users can by default run these 

`/boot` - Boot Files
- Contains files required to boot the system:
  - `initrd`
  - The Linux kernel:
    - `vmlinux`: Uncompressed kernel
    - `vmlinuz`: Compressed kernel

`/dev` - Device Files
- Provides access to hardware and drivers as if they are files.

`/etc` - Editable Text Config
- Critical startup and system configuration files

`/home` - User Files
- Contains user-specific files

`/lib` - Libraries
- Libraries, shared libraries, and comamnds by `/bin` and `/sbin`

`/media`
-  Mount points for filesystems on removable media 

`/mnt`
- Temporary mount points  

`/opt` - Optional Software
- Stores optional or add-on software.

`/proc` - Process Filesystem
- Information about all running processes
- More on these in the System Administration chapter

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

`/root` - Root
- Home directory for root 

`/run`
- Rendezvous point for running programs

`/sbin` - System Binaries
- Contains core system binaries or executables for the superuser (root).
- Example: `mount`

`/srv` 
- Files held for distribution through web or other servers

`/tmp` - Temporary Files
- Stores temporary files.
- Files in `/tmp` are non-persistent between reboots.

`/usr` 
- Hierarchy of secondary files and commands
    - `/usr/bin` - Most commands and executable files
    - `/usr/include` - Header files for compiling C programs
    - `/usr/lib` - Libraries; also support files for standard programs
    - `/usr/local` - Local software or config data
    - `/usr/sbin` - Less essential command for administration and repair
    - `/usr/share` - Items that might be common to multiple systems
    - `/usr/share/man` - On-line man pages
    - `/usr/src` - Source code  for nonlocal software (not widely used)
    - `/usr/tmp` - More temporary space (preserved between reboots)

`/var` - Variable Files
- system specific data and few config files
    - `/var/adm` - varies: logs, setup records, strange admin bits
    - `/var/log` - system log files
    - `/var/run` - same function as /run; now a symlink
    - `/var/spool` - Spooling (that is, storage) directories for printers, mail, etc.
    - `/var/tmp` - more temporary space (preserved between reboots)


### inodes
* **inode** - index node 
    - it is an object storing meta deta about a file or directory on a given filesystem
    - It contains:
      - Last time of change, access, modification.
      - Owner/permission data.
      - block-level location data, aka physical disk location of the object data.
    - Directory is a list of names assigned to inodes:
      - Contains entries for itself, its parents, and its children.
    - A disk can run out of inodes before running out of disk space, which prevents new file creation.
    - Inodes can be used to delete strangely named files that won’t tab-complete.
    - **inode related commands**
        - `ls -i` - See the inode number.
        - `find . -inum <inode number>`: Find file by inode number.
        - `df -ih` - see inode usage on a partition level

### Hard and Symbolic Links
- **Hard Link**
    - A link to another file’s inode.
    - Can only occur within the same filesystem.
    - `ls -la`: Displays hard link count (second column).
    - Directories have a minimum of 2 links: one for itself and one for its parent.
    - Files have a minimum of 1 link.
    - Create hard links with `ln <sourcefile> <destfile>`.
    - Check links with `ls -li`.
    - **Note**: Hard links are not allowed for directories.
  
- **Symbolic (Soft) Link**
    - A link to another file name.
    - Can span across different filesystems.
    - Akin to shortcuts in Windows.
    - Useful for linking files between filesystems.
    - Create soft links with `ln -s <sourcefile> <destfile>`.

### File types and localization

* **File types** - and their symbol
  * Regular files   - `-`
  * Directories - `d`
  * Character device fils   - `c`
  * Block device files  - `b`
  * Local domain sockets  - `s`
  * Named pipes (FIFOs) - `p`
  * Symbolic links  - `l`

* `type`
    - Determines if a command is a shell built-in, alias, or external command.

* `whereis`
    - Outputs the location of a command and its man pages.

* `locate`
    - Locate a file quickly (requires `updatedb`)
    - options:
        - `-r` - use regex
        - `-c` - display or count the bnumber of matching entries found
        - `-e` - return only files that exist at the time of search
        - `-I` - ignmore the casing in file names or paths

* `updatedb`
    - Must be run regularly in order to effectively use `locate`

* `/etc/updatedb.conf`
    - Configuration for `updatedb`, which defines locations not to search.
    - or add them to the $PRUNEPATH variable

- `find` 
    * combine the results with:
        * `-print`      - display the location of the files
        * `-exec`       - execute the comamnd that follows 
        * `-ok`         - execute the command that follow interactively
        * `-delete`     - delete the files found
        * `-fprint`     - tore the results in a target file 

* `which`
    * locate the location of an executable

* `whereis` 
    * Locate the binary, source, and manual page files for a command.
    * gives more info than `which`

- `stat`
    - Display file or filesystem status 
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

### RAID
* RAID types
    * Striping
    * Mirroring
    * Parity
* **RAID configurations**
    * RAID 0 - Striping 
    * RAID 1 - Mirroring
    * RAID 5 - Parity, min. 3 drives
    * RAID 6 - 5+1, min. 4 drives
    * RAID 10 - 1+0, striped mirrors. min. 4 drives

* `mdadm`
    * tool for managing software-based RAID arrays
    * options:
        * `-F` - Activate monitor mode
        * `-f` - Mark specified device
        * `-r` - Remove specified device
        * `-a` - Add device as hot-spare

* `/proc/mdstat`
    * offers information on the current RAID configuration 
    * contains a snapshot of the kernel's RAID/ md state
 

### Swap management
* Swap is a way of emulating RAM on the disk 
* Useful for when you're low on RAM

* `dd if=/dev/zero of=/swapfile bs=1M count=1024`

* `mkswap <file>`
    * Create swap space on a storage partition
    * options:
        * `-c` - check for bad sectors before making the swap space
        * `-p` - Set the page size to be used
        * `-L` - Active swap space using labels applied to partition or filesystems

* `swapon <file>`
    * Activate a swap partition

* `swapoff <file>`
    * Deactivate a swap partition

* swap partition
    * `fdisk <disk>`
    * `t` - change the type
    * select a partition
    * enter hex code `82` - this changes the partition type to swap
    * `w` - to write the changes
    * `swapon 


## Disk, partition, and filesystem management

### Introduction and overview
* The storage structure is governed by **three** main layers:
    * **Partition table** (GPT/MBT)
        * AKA disklabel or label
        * This defines how a disk is partitioned
        * it is the same for the whole disk 
        * Examples: MBR (old, limited to 2TB) and GPT (modern, supports larger disks and more partitions).
        * Created using `parted mklabel`. See below

* **Partition**
    * Section of the storage drive that logically acts as a separate drive 
    * **Partition types**
        * Primary   - contain one filesystem or logical drive. Sometimes called a Volume. 
            * Boot partition and swap space are normally created in the primary partition
        * Extended  - contains several filesystems which are referrred to as logical drives
        * Logical   - partitioned and allocated as an independent unit and functions as a seperate drive

* **Filesystem**
    * A data structure used by an OS to store, retrieve, organize,  and manage files and riectories on storage devices 
    * Common filesystems
        * ext2/3/4
            * Native Linux
        * XFS
            * high-performance journalisting filesystem
            * fast recovery
            * handles large files very well
        * BTRFS
            * supports huge volumes, 16 exabytes
    * Other filesystems
        * ZFS
            * Comparable to btrfs, but not supported by the Linux Kernel, only through fuse
            * see VFS
        * FAT
            * older
            * compatible with most other OS
        * ntfs-ng
            * Windows NRFS compatible filesystem
        * NFS
            * Network filesystem
            * Default for file sharing 
        * CIFS
            * Common Internet File System
            * USed to access Windows shares

    * **VFS** - virtual filesystem
        * VFS is a generic abstraction of all filesystems
        * It sits between the kernel and the actual filesystems, mediating communication between both
        * It translates the real filesystem's details over to the kernel
        * Attached to VFS are all the different filesystem modules
            * XFS, ext4, btrfs
            * One special filesystem module is fuse - filesystem in user space
                * fuse enables working with filesystems that are not directly supported by the Linux kernel, such as zfs 
        * They will be recognizible by their filesystem labels
            * These can be up to 16 characters long
            * `e2label` - see or change ext-based filesystems
            * `xfs_admin` - same but for XFS-based filesystems 

### Managing disks and partitions
* `lsblk` - list block
    - Lists information about storage devices, such as
        - `NAME` - Device and partition name
        - `MAJ:MIN` - Major Number (Maj): Identifies the device type (e.g., 8 is for SCSI/SATA disks) and Minor Number (Min): Differentiates individual devices or partitions under the same major number.
            - `/proc/devices` contains the Major numbers and linking them to block device modules. E.g. `259 blkext`
        - `RM` - is the device removeable. `0` for No , `1` for yes 
        - `SIZE` - size of the device or partition
        - `RO` - Read-Only value. `0` for read-write and `1` for read-only
        - `TYPE` - disk or partition
        - `MOUNTPOINTS` - mount points on the filesystem
    - Partially same info as `fdisk -l`
    - Options:
        - `-a` - list empty devices
        - `-r` - use raw output format
        - `-f` - output info about filesystems, e.g. UUID 
        - `-l` - output list instead of tree
        - `-m` - display device permission info
    * Common disk block device names include:
        * sda - first SCSI/SATA disk
        * vda - virtual disk that uses the KVM virtio driver
        * xvda - virtual disk in a Xen environment
        * nvme0n1 - first nvme disk 
    
* `blkid` - block id
    * Print the UUID of devices 
    * Use this to add the devices to `/etc/fstab` for mounting devices
    * See below on mounting

* `fdisk </dev>` / `gdisk </dev>`
    * Manage partition tables and partitions on a hard disk
    * `fdisk` is for MBR, `gdisk` for GPT
    * **options:**
        * `-l` - list partitions 
        * `-b` - Specify number of drive sectors
        * `-H` - specify number of drive heads
        * `-S` - specify number of sectors per tracks
        * `-s` - print partition size in blocks
    * Within the `f/disk` / `gdisk` tool, there are several options:
        * `m` or `?` - for help, displaying all options

* `parted`
    * Newer standard for table and partition management of disks 
    * Benefits over `fdisk`
        * in contrast to `fdisk`, `parted` allows for resizing of partitions
        * works better for GPT
        * works with large disks
        * allows scripting
    * `mklabel gpt /dev/sd#` - create a new disk / partition table
    * `mkpart <name> <FS-type> <start> <end>` - create a partition
        * <start> and <end> can be in (kilo/mega/giga/tera)bytes or in percentages (10%, 20%, etc)
        * Note: specifiying the filesystem is purely administrative. It can be left out or mismatch the specification by the `mkfs.` command. See below
    * `resizepart <part number> <new size>` - resize a partition

* `partprobe` 
    * Inform the OS of partition table changes
    * This updates the kernels with changes that now exist within the partition table
    * It eliminates the necessity to reboot the system

* `/proc/partitions`
    * Contains info about each partition attached to the system
    * Contains 
        * major     - class of device
        * minor     - divides partitions into physical devices
        * blocks    - number of physical blocks the partition takes up
        * name      
    * Similar to `lsblk  `

### Mounting & unmounting
* `mount [options] <dev> <mountpoint>`  
    * Mount a filesystem
    * Options
        * `-a` - mount all. Great way of testing a recently reconfigured `/etc/fstab` file
        * `-t` - specify target
        * `--mkdir` - create directory if not exists
        * `-a` - mount all filesystems defined in `/etc/fstab`
    * Mounting this way is non-persistent between reboot 
    * While booting, the `/etc/fstab` file is processed to persistently mount filesystems

* `etc/fstab` - filesystem table
    * On modern distros, this file is processed by systemd, which converts the mount in a systemd mount
    * If during the boot procedure, a filesystem that is specifided in `/etc/fstab` does not mount sucessfully, typically a troubleshooting prompt is shown where the error must be fixed mnaually
    * `/etc/fstab` dictates how mounting happens at startup
    * It describes what devices are mounted, where, and with which options.
    * **File components**
        * **Filesystem** - what to mount
            * Name or UUID of the partition
            * UUID can be found using `blkid`
            * This is best practice, since the device name might change while the UUID stays the name
            * *Persistent naming*
        * **Mount points** - where to mount it
            * Location on the filesystem where it needs to be mounted
        * **Type** - filesystem type
            * Type of filesystem used by the partition
        * **Options** - mount options
            * Set of comma-separated options that will be activated when the filesystem is mounted
            * See below for the full list 
        * **dump** - create backup 
            * Legacy, do not use. Just let it be `0`
        * **pass** - Used by `fsck` to determine the order of filesystem checks at boot
            * Legacy, do not use. Just let it be `0`
            * Order:
                - `0`: No check.
                - `1`: Root disk.
                - `2`: After root disk 
    * **Mount options:**
        * auto      - device must be mounted automatically
        * noauto    - device should not be mounted automatically
        * nouser    - Only root user can mount a device or filesystem
        * user      - all users can mount 
        * exec      - allow binaries in a filesystem to be executed
        * noexec    - do not allow binaries in a filesystem to be executed
        * ro        - mount filesystem as read only
        * rw        - mount filesystem with read/write permissions
        * sync      - input and output operations should be done synchronously
        * async     - input and output operations can be done asynchronously

* `findmnt` - find mount
    * Show all mounts on the system
    * `findmnt --verify` - verify syntax of `/etc/fstab`

* **UUIDs and labels**
    * Use UUIDs when adding devices to `/etc/fstab`
        * This is best practice, since device names are subject to change 
        * List the UUID through `blkid`
    * Admins can manually assign **labels** for *persistent naming*
        * Set labels
            * `tune2fs -L <labelname> <device>` - setting label on ext filesystems
            * `xfs_admin -L <name> <device>` - Same for xfs 
        * Then, instead of using the UUID, the label can used in the `/etc/fstab`
        * Please note, before changing the label, filesystems must be unmounted

* **systemd mounts**
    * `/etc/fstab` is used as as input file from which systemd mount units are created
    * systemd mounts are created in `/run/systemd/generator`
        * Mount files can be created manually as well
        * Note: the file `-.mount` is the file for the root mount point
            * Instead a `/` a `-` is used
    *  A reason to use systemd mounting is the dependency management of systemd
        * If you want the mount only to occur when certain services are running, this can be done through systemd, not `/etc/fstab`
    * `systemctl list-unit-files --type mount` - list all unit files for mounts

* **systemd automount**
    * 



* `umount <mount point>` - unmount
    * unmount a filesystem
    * options
        * `-f` - force
        * `-l` - perform a lazy unmount. Filesystem is detached from a hierarchy, but refereneces to that filesystem are not cleaned up until the filesystem is not being used
        * `-R` - recursively unmounts the directories mount points
        * `-t <fs type>` - unmount all filesystem types specified
        * `-O` - unmount only the filesystems with specified options in the `/etc/fstab` file
        * `--fake` - dryrun it

### Managing filesystems
* `wipefs` - wipe filesystem
    * Wipe the filesystem using the `--all </dev/>` option
    * It does not wipe the files themselves
        * Use `shred` or `dd` for that

* `mkfs.<type> /dev/sd#` - make filesystem
    - Build a filesystem on a hard disk partition
        - e.g. `mkfs.ext2/3/4` / `mkfs.xfs` / `mkfs.btrfs`
    - Some options:
        - `-v` - verbose
        - `-c` - check for bad blocks before building the filesystem

* `/etc/crypttab` 
    * A file containing information about encrypted devices and partitions that must be unlocked and mounted on system boot

* `/etc/mtab` 
    * reports the status of currently mounted filesystems

* `/proc/mounts`
    * Same but more accurate and includes more up-to-date info on filesystems

* `smartctl` - SMART control
    * Self-Monitoring, Analysis, and Reporting Technology
    * Tool for tracking disk drive health, predict failures, and log error and self-tests
    * `--health` - print overall health 
    * `-t (short|long)` - do a disk drive test 
    * `-all` - print all info, including test result

* **ext-specific filesystem management tools**
    * `e2fsck`
        * filesystem check

    * `fsck [options] <dev/fs name>`- filesystem check
        - Check the integrity of a filesystem or repair it
        - **Note**: cannot run on mounted disks.
        - `-a` - immediately repair all damaged blocks
        - `-r` - repair

    * `resize2fs [options] <dev> [<size>]`
        * resize ext2, ext3, or ext4 **filesystems**
        * leaving out the size, defaults to going max size
        
    * `tune2fs` 
        - Used to change variable filesystem parameters
            * add/ remove reserved blocks
            * alter reserve block counts
            * specify number of mounts between chjecks
            * specify the time interval between checks
            * etc  
        - `tune2fs -l` - List all options
        - `tune2fs -l <disk>` - List all details of the disk.
        - `tune2fs -L <labelname> <disk>` - Assign a label or volume name to a disk.

    * `dumpe2fs` 
        - Displays all super block and block group information about a disk
        - Includes block and superblock data, and metadata about the filesystem

* **How to's**
    * **So in order to take a used disk and repurpose it:**
        * wipe disk using `dd` (see below). Wiping the filesystem without deleting the files `wipefs` works as well 
        * create a new drive label with `parted mklabel` and a new partition using `parted mkpart`
        * format the partition `mkfs.<type>`
        * add formatted partition to `/etc/fstab` so it can configured by the system to run at boot up
        * run `partprobe` if needing to use the drive immediately without rebooting
        * mount it using `mount`

    * **Remove swap partition and expand root partition**
        * `swapoff <swap space>`
        * `fdisk` 
            * `d <swap partition>`
        * `parted` - you cannot resize partition in `fdisk`
            * `resizepart <partition number> <100%>` - now the partition is resizes
        * `resize2fs <dev>` - after expanding the partition, the filesystem must be expanded


### XFS-specific filesystem management tools
* `xfs_info`        - display details 
* `xfs_admin`       - change the parameters 
* `xfs_metadump`    -  Copy the superblock metadata to a file 
* `xfs_growfs`      -  expand the XFS filesystem to fill the drive size
* `xfs_copy`        -  copy the contents of the XFS filesystem to another location
* `xfs_repair`      -  repair and recover a corrupt XFS filesystem
* `xfs_db`          -  Debug the XFS filesystem


## Logical Volume Manager
* LVM - Logical Volume Manager
    * Tool for mapping whole physical devices and partitions into one or more virtual containers called **volume groups**
    * Within these volume groups, are one or more logical volumes
    * Its goal is to add flexibility to the storage layer
    * It is analogous to Storage Spaces in W*ndows
    * LVM benefits 
        * Easy resizing
        * Snapshots
        * Software RAID
        * Thin provisioning
        * Multi-device
    * LVM is built up as follows: 
        * Bunch of **disks** 
        * **Partitions** on those block devices
        * The partitions come together into **Physical Volumes**
        * **Volume Groups** comprising of multiple of these physical volumes
        * **Logical Volumes** carved from the Volume Groups
        * **Filesystems** created from these Logical Volumes
    * From the LVM management point of view, there are three layers:
        * Physical Volumes (PV) represent the block devices
        * The Volume Group (VG) represent all of the available storage
        * The Logical Volume (LV) are created as block devices fro mthe volume group and formatted with any filesystem
    * `/dev/mapper` contains all the logical volumes on a given system managed by LVM

* Physical volume tools
    * `pvscan`      - scan for all physical devices being used as physical volumes
    * `pvcreate`    - Initializes a drive or partition to use as a physical volume
         * It all starts with this
        * If creating a physical volume from a physical drive, the partitions and label must be wiped first
    * `pvdisplay`   - lists attributes of physical volumes
    * `pvchange`    - changes attributes of a physical volumes
    * `pvs`         - displays information about physical volumes
    * `pvck`        - checks the metadata of physical volumes
    * `pvremove`    - removes physical volumes

* Volume group tools
    * `vgscan`      - scan all physical devices for volume groups
    * `vgcreate`    - creates volumes groups
    * `vgdisplay`   - lists attributes of volume groups
    * `vgchange`    - changes attributes of volume groups
    * `vgs`         - displays information about volume groups
    * `vgck`        - checks the metadata of volume groups
    * `vgrename`    - renames a volume group
    * `vgreduce`    - removes physical volumes from a group to reduce its size
    * `vgextend`    - add physical volumes from a group to enlarge its size
    * `vgmerge`     - merges two volume groups
    * `vgsplit`     - splits a volume group into two
    * `vgremove`    - removes volume groups

* Logical volume tools
    * `lvscan`      - scan for all physical devices for logical volumes
    * `lvcreate`    - creates logical volumes in a volume group 
    * `lvdisplay`   - lists attributes of logical volumes
    * `lvchange`    - changes attributes of a physical volumes
    * `lvs`         - displays information about logical volumes
    * `lvrename`    - renames a logical volume
    * `lvreduce`    - reduces the size of logical volumes
    * `lvextend`    - extends the size of logical volumes 
    * `lvresize`    - resizes logical volumes
    * `lvremove`    - removes a logical volume


## Archiving, Backup & Recovery

- **`tar`** - Tape Archiver
    - Bundles together multiple files into a single tarball 
    - Common options:
        - `-c` - Create an archive.
        - `-x` - Extract file
        - `-t` - List archive contents.
        - `-f` - Specify the filename.
        - `-v` - Verbose (list files being processed).
        - `-z` - Compress or decompress with gzip
        - `-C` - Specify the output file
    - **Examples:**
        * `tar -cvf <file>.tar <files>` - Create a tarball from specified files  
        * `tar -xvf <file>.tar` - Extract **all** files in tarball
            * `tar -xvf <file>.tar <files>` - Extract only the mentioned files from tarball
            * `tar -xvf <file>.tar -C <directory>` - Extract the tarball into the specified directory 
        * `tar -tvf <file>.tar` - List files in tarball

* **Compression tools**:
    - **`gzip`**
        - Most common utility
        - Nice balance between compression ratio and take to compress
        - Compression tool that produces files with `.gz` extension 
        - Use with `tar` with `-z` option
            - `tar -czvf <file>.tar <files>` - archive and compress
        
    * **`bzip2`**
        * Better compression, but takes longer 
        * `-j` - when used as a option for `tar`
            * `tar -cjvf <file>.tar <files>` - archive and compress
             
    * **`xz`**
        * Best compression, but slow, like 10x slower than `gzip` for a +-20% better compression
        * `-J` - when used as a option for `tar`
            * `tar -cJvf <file>.tar <files>` - archive and compress
            
- **`dd`** - data duplicator / disk destroyer
    * Commonly used for disk imaging, data recovery, and even wiping data securely
    * Command components
        * `if={file name}` - Specify input file 
        * `of={file name}` - Specify output file
        * `bs={bytes}`     - Specify total block size to read and write in bytes
        * `count={count}`  - Specify the number of blocks to be written. Optional
        * `status={level}` - Specify information to print to standard error
    * Command examples
        * Create a disk image
            * `dd if=/dev/sdX of=/path/to/backup.img bs=4M status=progress` 
        * Secure wipe a disk  
            * `dd if=/dev/zero of=/dev/sdX bs=1M status=progress`
        * Copy or backup a disk to a file
            * `dd if=/dev/sdX of=/backup.bak bs=446 count=1`
        * Copy or backup a disk to another disk   
            * `dd if=/dev/sdX of=/dev/sdX`
    * Other things you do with `dd`
        * Converting files to lowercase
        * Disk performance tests
            * measuring read / write speed

* `rsync`  remote sync
    * Already discussed in the chapter on Networking,

* `mirrorvg` - Mirror volume group
    * 

## Storage Troubleshooting

- **`df`** - display filesystem
    * Print filesystem usage.
    * `-h` - human-readable format (e.g., MiBs, GiBs).
    * `-i` - show inodes 

* `du` - disk usage 
    * `-h` - human readable 
    * `-s` - show only total 

* **`quotas`** 
    * Package for managing user quotas
    * `quotacheck`: Scan a filesystem for disk usage and create/repair quota files.
    * `edquota`: Edit the disk quota for a user.
        - Specifies disk usage, inode usage, and the specific filesystem to which it applies.
    * `quotaon`: Enable quotas and apply the configuration set in `edquota`.
    * To implement quotas:
        * Modify `/etc/fstab` file
        * Add `,usrquota` after `defaults`


## Remote Filesystem Access




