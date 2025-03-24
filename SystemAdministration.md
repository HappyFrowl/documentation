# System Administration
- [Kernel management](#kernel-management)
- [Boot process and GRUB management](#boot-process-and-grub-management)
- [Linux system Components](#linux-system-components)
- [Localisation and time](#Localization-and-time)
- [Process management](#Process-management)
- [Service management](#service-management)
- [System monitoring](#system-monitoring)
- [Log management](#log-management)


## Kernel management

**Kernel basics**:
* Kernel is the core of an OS
* **Tasks**
  1. Memory management: Keep track of how much memory is used to store what, and where
  2. Process management: Determine which processes can use the central processing unit (CPU), when, and for how long
  3. Device drivers: Act as mediator/interpreter between the hardware and processes
  4. System calls and security: Receive requests for service from the processes

* Software is divided into:
  * **Kernel space** where the kernel executes services
  * **user space** is the area of memory outside the kernel space
* **types of kernels**
  * **monolithic** - all system modules, e.g. device drivers or file systems, run in kernel space
    * Linux is a monolithic kernel
  * **microkernel** - kernel runs the minimum amount of resources necessary to run fully functional OS
    * smaller in size
    * more stable
    * worse performance
* `uname`
  * Print details about the current machine and the operating system running on it.
  * `-r` - print kernel version

**Kernel Layers**
* Sytem Call Interface (SCI)
  * Handles system calls sent from user apps to the kernel
  * Think: processing time, memory allocation
* Process management
  * Handles different processes by allocating separate execution space on the processor 
* Memory management
  * manages the computer's memory
* File system management
  * storing, organizing, and tracking files and data 
* Virtual File System (VFS)
  * Provides an sbstract view of the underlying data that is organized under complex structures
* Device management
  * Manages devices by controlling device access and in interfacing between user apps and hardware devices on the computer


**Kernel modules**
* `/usr/lib/modules` contains the modules of different kernel versions
* the parent directory is specific to a specific kernel version
* The kernel modules are stored in `./kernel`, e.g.
  * `./arch`    - architecture
  * `./crypto`  - cryptographic
  * `./drivers` - drivers 
  * `./net`     - networking

* `lsmod` - list modules
  * List all currently loaded kernel modules

* `modinfo` - module information
  * display information about a specific module 

* `insmod` - insert module
  * install a module into the kernel
  * Does not install dependencies
    * use `modprobe` for that

* `rmmod` - remove module
  * removes a module from the kernel

* `modprobe` - module probe
  * Add or remove modules from the Linux Kernel
  * options:
    * `-a` - add a module. This is the default use of `modprobe`, so even running it without `-a` adds a module
    * `-r` - remove a module
    * `-f` - force adding or removing
    * `-s` - print errors to syslog
    * `-n` - dry run
  * To add modules by default into the kernel each time the system boot add the module name to 
    * `/etc/modules`
  * To prevent modules from being loaded into the kernel, add them to a `blacklist` file in 
    * `/etc/modprobe.d`

* `sysctl` - system control
  * Interface to dynamically change kernel paramters
  * Kernel parameters are stored in `/proc/sys`
  * Kernel parameters can be configured to:
    * Improve performance
    * Harden security
    * Configure networking limitations
    * Change virtual memory settings 
    * etc
  * options: 
    * `-a`                    - print all parameters and the current values
    * `-w <parameters=value>` - set a parameter value 
    * `-p <filename>`         - load sysctl settings from a specified file
    * `-e`                    - ignore errors
  * `/etc/sysctl.conf` enables configuration changes to a running Linux kernel

* **`procfs`**
  * The `/proc` directory is a directory that is backed up by the **virtual file system** `procfs`
    * `/proc` is the mount point for procfs, meaning that all files and directories inside `/proc` come from `procfs`
    * `procfs` is a pseudo-filesystem, meaning it doesn’t store real files on disk but instead dynamically presents system and process-related information.
    - **Purpose**: 
      * Provides an interface to kernel data structures.  
      - Mounted at `/proc` and allows user space to query or control the kernel.  
      - Presents information about:  
        - Processes  
        - System information  
    - **Examples**:  
      - `/proc/cpuinfo`     - CPU details 
      - `/proc/meminfo`     - memory usage
      - `/proc/cmdline`     - contains options passed by GRUB during boot
      - `/proc/devices`     - contains a list of character and block device drivers loaded into the currently running kernel
      - `/proc/filesystems` - contains a list of file systems types that are supported by the kernel
      - `/proc/modules`     - contains informatiokns about modules currently installed on the system
      - `/proc/stat`        - contains various statistics about hte system's last reboot
      - `/proc/version`     - specifies several points of informations about the Linux Kernel. Similar to `uname`

* `dmesg` - display / driver messages
  * Prints messages that have been sent to the kernel's mesasge during andd after system boot
  * Drivers can also send diagnostics messages to the kernel when they encounter errors
  * Great for troubleshooting and driver validation

* **`sysfs`**
  - **Type**: Virtual Filesystem  
  - **Purpose**: Exposes kernel device and subsystem information to user space.  
    - Mounted at `/sys` and provides a structured way to view and manipulate kernel objects.  
    - Presents information about:  
      - Various kernel subsystems  
      - Hardware devices  
      - Drivers  
  - **Example**:  
    - Check `/sys/class/net` for network interface details.

* **`tmpfs`**
- **Type**: Temporary Filesystem  
- **Purpose**: A memory-based filesystem used for temporary data storage that does not persist after reboot.  
- **Example**:  
  - The `/tmp` directory is often mounted as `tmpfs`.

* **`devtmpfs`**
- **Type**: Virtual Filesystem  
- **Purpose**: Automatically populates the `/dev` directory with device nodes at boot, which are then managed by `udev`.  
- **Example**:  
  - Provides base device nodes for hardware detected during boot.


## Boot process and GRUB management

### Boot components 
Before going into the actual boot process, first some notes on some important components of the boot process.
The main components of the boot process are: BIOS/UEFI, which will be taken as general knowledge, the Linux kernel, discussed above,`initrd` and `grub2, which will be discussed here. 

`initrd` which plays a crucial role in it: 
* `initrd` - initial ram disk 
  * `initrd` is a root file system that is temporarily loaded into memory upon system boot 
    * It is a temporary file system 
  * The bootloader starts the OS by using `initrd` - see below
  * `initfs` - initial file system - is the successor of `initrd`  
  * **Features and functions** 
    * It contains programs to perform hardware detection 
    * It loads the necessary modules to get the actual file system mounted 
  * `initrd` image
    * Archive fiel that contains all the esxsential files that are required for booting the OS
    * It can be built or customized to include additional modules, remove unnecessary ones, or update yet other ones
    * `initrd` image is stored in the `/boot` directory

* `mkinitrd` - make initial ram disk
  * Tool for creating the `initrd` image for preloading the kernel modules
  * options: 
    * `--preload=<module>`  - Load a module in the `initrd` image *before* loading of other modules
    * `--with=<module>`     - Load a module in the `initrd` image *after* loading other modules
    * `-f`                  - Overwrite an existing `initrd` image file
    * `-nocompress`         - Disable the compression of the `initrd` image 
  * `mkinitrd [options] <initrd image name> <kernel version>` 

* `dracut` 
  * Tool for creating `initramfs` images to boot the Linux kernel
  * Analogous to `mkinitrd` but applies to `initfs`


* **`GRUB2`** - GRand Unified Bootloader
* A **boot loader** is a small program stored in ROM of which GRUB2 is an example
* Among other things, the boot loader enables users to choose which OS and kernel version to boot
* Moreover, it enables configuring boot laoder through scripts'
* GRUB knows three important files/ directories:f
  *  `grub.cfg`
    * File containing the main config for GRUB 
    * However, it is never edited itself; use scripts instead
    * It is located at 
      * `/boot/grub/grub.cfg`               - BIOS
      * `/boot/efi/EFI/<distro>/grub.cfg`   - UEFI

  * `/etc/grub.d` 
    * Directory containing scripts that are used to build the main `grub.cfg` file
    * Add custom scripts as ##_fileName 
    * Or add the script to the `40_custom` file

  * `/etc/default/grub`
    * Contains GRUB2 display menu settings that are read by the `/etc/grub.d`scripts
    * This file must be saved to the boot folder `/boot/grub2`. 
    * To generate a new or update an existing `grub.cfg`, run:  
      * In RHEL: `sudo grub2-mkconfig -o <location>/grub.cfg`
      * In Ubuntu: `sudo update-grub2` 


### **The Boot Process**

**1. BIOS/ UEFI**
* CPU checks the BIOS/UEFI firmware
* BIOS/UEFI runs POST 
    * Power On Self Test 
    * Tests whether all hardware pieces are working properly 
    * If there is an error it is shown on the screen 
* BIOS/UEFI checks for bootable media
    * Hard drive 
    * USB 
    * CD 
    * PXE 
    * NFS
  * BIOS/UEFI loads the primary boot loader from the MBR or GPT, and loads the partition table with it


**2. GRUB2** 
* GRUB2 selects the OS 
* GRUB2 determines the kernel and locate the kernel binary on the disk  
* GRUB2 loads the kernel into memory and runs the kernel code 


**3. Linux Kernel** 
* Once the kernel is loaded into memory, the kernel takes to finish the startup process 
* The kernel checks the hardware and decompresses the `initrd` image to load the hardware drivers and other device drivers into memory 
* Virtual devices such as RAID and LVM are initialized here
* The kernel mounts the main root partition and releases unused memory back into the system
* This starts `init` as the first process 


**4. init** - Initialization system 
* Background on `init`
  * `init` brings up and maintains user space services 
  * Init is the first process run by Linux, so it always has process ID 1
  * For many distros, `**systemd**` is the used init system 
      * Another is `sysv` or `upstart`
      * `systemd` was spearheaded by RedHat 
  * `systemd` is a collection of units (e.g. services, mounts, etc) 
      * Systemctl, journalctl, loginct, notify, analyze, cgls, cgtop, nspawn 
      * To query all units, run: systemctl list-unit-files 
      * Also see which are enabled, disabled, masked 
* `systemd` searches for the `default.target` file
  * This contains details on the services that need to be started
* Then it mounts the file system based on the `/etc/fstab` file
* When using a `graphical.target` then the login screen is loaded and the user can log in


 #### **Run levels**
* Way of booting the system 
* Only used in SysV systems, not in Systemd systems
* take particular note of run level 1 - single user mode
  * this boots the system into a single user mode, requiring no password
  * THIS is the way to reset the root's password 

* For Debian based systems:
  0. halt
  1. single user mode 
  2. full, multi user, GUI if installed
  3. nothing
  4. nothing
  5. nothing
  6. reboot (into system default)

* For CentOS/ RedHat:
  0. Halt
  1. single user mode
  2. multi user, no net
  3. mutli user, with net
  4. nothing
  5. multi user GUI
  6. reboot 


**Boot Targets**
* Boot targets are the modern equivalent of runlevels 
* used in systemd
* More flexible and descriptive way of managing system states
* Boot targets are particularly useful when troubleshooting the system, especially when it does not want to boot properly.
* available modes and its equivalent runlevel
  * poweroff    ~ runlevel 0 
  * rescue      ~ runlevel 1
  * multi-user  ~ runlevel 3
  * graphical   ~ runlevel 5
  * reboot      ~ runlevel 6
* like the associated runlevel, `rescue mode` is used to change root's password 
* `systemctl get-default`
    - Get default mode
* `systemctl set-default <mode_name>` - set default boot target
  * this only takes effect after rebooting
- `sudo systemctl isolate <target>`
    - Temporarily change the boot target
    - this takes effect immedadiately


## Linux system Components

**udev**
- **Type**: Device Manager  
- **Purpose**: Responsible for dynamically managing device nodes in the `/dev/` directory.  
  - Device nodes represent hardware devices like disks, USB drives, network interfaces, and more.  
- **Capabilities**:  
  - Low-level access to the Linux device tree  
  - Handles user-space events (e.g., loading firmware, adding hardware)  
- **Example**:  
  - Access is provided by a temporary filesystem (`tmpfs`) mounted to `/dev/`

**dbus**
- **Type**: Inter-Process Communication System  
- **Purpose**: Facilitates communication between applications and system services.  
  - Allows processes to send messages to each other.  
- **Example**:  
  - Applications like `NetworkManager` use D-Bus to communicate with the system's networking stack.

**cgroups**
- **Type**: Resource Management Subsystem  
- **Purpose**: Limits, prioritizes, and accounts for resources (CPU, memory, I/O) used by groups of processes.  
- **Example**:  
  - Docker and Kubernetes use `cgroups` to manage container resource allocation.

**FUSE** - Filesystem in User Space 
- **Type**: Virtual Filesystem Framework  
- **Purpose**: Allows non-privileged users to create and manage filesystems in user space.  
- **Example**:  
  - Filesystems like `SSHFS` or `NTFS-3G` use FUSE.


## Localization and time 
* `locale`
  - Region-specific settings, including language and country formats.
  - View and configure locale settings

- `iconv -f utf-8 -t iso_8653-1 < file.txt > output.txt`
  - Convert between encodings
- `iconv -l`
  -  List supported encodngs
- `file <filename>`
  - Detect file encoding

- `cat /etc/timezone`
  - View current timezone
- `/etc/localtime` 
  - Current timezone configuration
  - file is symlinked to the appropriate timezone file
- `tzselect`
  - Select a timezone

- `date` - print or set date and time
  - options:
    - `+%A` - print full weekday
    - `+%B` - print full month
    - `+%F` - print date in yyy-mm-dd format
    - `+%H` - print hour in 24hr
    - `+%I` - print hour in 12hr
    - `+%J` - print day of the year
    - `+%j` - print seconds
    - `+%V` - print week of the year
    - `+%x` - print date representation based on the locale
    - `+%S` - print time representation based on the locale
    - `+%Y` - print year 

- `timedatectl` 
  - print or set local time, universal time, time zone, ntp info, etc. 
  - this command should three clocks
    - local time      - local current time
    - Universal time  - UTC/ GMT
    - RTC time        - Hardware clock as measured by the motherboard

- `hwclock` 
  - view and set the hardware clock
  - best practice is to keep it in sync with universal time 

- Synchronize with NTP servers:
  - Install the client: `apt install ntpdate`
  - Sync with NTP pool:
    - `ntpdate pool.ntp.org`
- Configure as an NTP server:
  - Install the daemon: `apt install ntp`
  - Configuration file: `/etc/ntp.conf`


## Process management

- `ps` - Lists running processes.
  - By default, it only shows processes for the current user.
  - options:
    - `a` - displays all user-triggered processes
    - `u` - list proceses along with the username and start time
    - `x` - include processes without a terminal
  - Processes in **square brackets** are system or kernel processes.

- `pgrep <process name>` - process grep
  -  identify a process ID based on the process name

- `pidof` - PID of 
  - works similar to pgrep

* `fuser` - file user
    * Display process IDs currently using files or sockets

* `top`
  * Display Linux processes
  * Print how much CPU, RAM, swap is used, its uptime, idle 
  * **Row information:**
    * time, uptime, signed in users, load average
    * **tasks:** zombie tasks are orphan processes - unparented child processes 
      * show process relations with `Shift+V`
    * **CPU:** 
      * `us` - user space
      * `sys` - system space
      * `ni` - niceness aka priority
      * `id` - idleness - how much time has the CPU been idle for
      * `wa` - waiting: how much has a task been waiting for input/output. So, lower is better 
      * `hi` / `si` - hardware and software interrupts. Not so important nowadays
      * `st` - how much time has a virtual CPU been waiting for a physical CPU
    * **memory:** memory installed/ free/ used/ cached
    * **swap:** swap space installed/ free/ used/ total 
  * **Column information**:
    * `PR` - priority
    * `VIRT` - virtual memory being used
    * `RES` - physical memory used 
    * `SHR` - shared memory used
    * `%CPU` / `%MEM` - CPU / memory used
    * `TIME+` - uptime process
    * `command` - process name
  * Column sorting
    * `P` - sort by CPU usage
    * `M` - sort by memory
    * `k` - kill using the PID

* `nice`
  * Processes are prioritized based on a number ranging from -20 to 19
  * The lower the number, the *higher* the priority
  * 0 is the default
  * By default, `nice` prints the default nice number
  * `-n` - increment the nice value

* `renice`
  * Alter the scheduling priority of an already running process
  * options: 
    * `-n` - specify the new nice value for a running process
    * `-g` - alter the nice value of a processes in a process group
    * `-u` - alter the nice value of the processes associated to a user

* `systemd-analyze` 
  * Get performance stats for boot operations
  * Per unit it shows how long it took to start
  * Use it identify what slows down a boot process
  * `blame` - subcommand to identify services and other units that make the system really slow during the bootup process

* `lsof` - list open files
  * `-c` - filter on process name
  * `-p` - filter on PID
  * `-u` - filter on a specific user

- `kill` Command - Sends signals to processes to perform specific actions.
    **Signals**:
    - **SIGINT**:
      - Interrupt signal 
      - Stops the process, allowing it to clean up resources.
      - like Ctrl + C
      - value 2
    - **SIGKILL**:
      - Forces the process to terminate immediately
      - value 9
    - **SIGTERM**:
      - Request the process to terminate gracefully
      - Allows time for cleanup, but the process can ignore it.
      - value 15
    - **SIGSTOP**:
      - Pauses the process 
      - like Ctrl + z
      - value 17, 19, 23
    - **SIGSTP**
      - Pause a proces from the terminal
      - value 18, 20, 24

- `killall` - Kills all processes by name.
    - Only affects the current user's processes unless used with `sudo`.
    - `kill -9 brave` - SIGKILL brave

- `pkill`- Similar to `killall` but more flexible and potentially dangerous.
    - Allows matching processes based on name or other attributes.

* `fg` - foreground
  * Bring a process back to foreground

* `bg` - background
  * Move a process to the background
  * Alternatively, use ctrl+z
  * or start a command with `&` at the end 

* `jobs`
  * Prints all foreground and background jobs running

* `nohup` - no hiccup
  * prevent a command from stopping when the user that initiated it logs off
  * `nohup.script.sh` 


## Service management
**Service**
  * Running programs or processes that provide support for requests and monitoring from other processes or external clients
  * e.g., postfix, apache, and initd, like systemd

* `systemctl` - system control
  * enables the control of the systemd init daemon
  * subcommands:
    * `status`, `start`, `stop`, `restart`, `enable`, `disable` - speak for themselves 
    * `set-default`   - set the default target for the system to use on boot
    * `isolate`       - force the system to immedaitely change tot he provided target 
    * `mask`          - prevent the provided unit file from being enabled or activated
    * `daemon-reload` - reload the systemd init daemon, including all unit files

* `sysv` specific management: 
  * `/etc/init.d`
    * Directory storing initialization scripts for services
    * These scripts control the initiation of services 

  * `/etc/rc.local`
    * File that is executed tat the end of the init boot process
    * Typically used to start custom services
    * old deprecated shit

  * `chkconfig`
    * list all available services and view or update their run level settings
    * control services in each runlevel
    * start or stop services during system startup
    * subcommands:
      * `status`, `start`, `stop`, `restart`, `reload`


## System monitoring
* `uptime` 
  * print time since last boot



* `vmstat` 

* `iostat` 

* `free`





## Log management
* `rsyslog`
  * sent system logs from all kinds of devices to a centralized server

* `logrotate`
* 

