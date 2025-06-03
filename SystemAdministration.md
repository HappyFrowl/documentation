# System Administration
- [Kernel management](#kernel-management)
- [Boot Process and GRUB Management](#boot-process-and-grub-management)
- [Localization and Time](#Localization-and-time)
- [Process Management](#Process-management)
- [systemd Management](#systemd-management)
- [System Monitoring](#system-monitoring)
- [Log Management](#log-management)
- [Application management](#application-management)
- [Task Automation](#task-automation)


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


### **Kernel modules**
* `/usr/lib/modules` contains the modules of different kernel versions
* the parent directory is specific to a specific kernel version
* The kernel modules are stored in `./kernel`, e.g.
  * `./arch`    - architecture
  * `./crypto`  - cryptographic
  * `./drivers` - drivers 
  * `./net`     - networking

* `lsmod` - list modules
  * List all currently loaded kernel modules

* `modprobe` - module probe
  * Add or remove modules from the Linux Kernel
  * options:
    * `-a` - add a module. This is the default use of `modprobe`, so even running it without `-a` adds a module
    * `-r` - remove or unload a module
    * `-f` - force adding or removing
    * `-s` - print errors to syslog
    * `-n` - dry run
  * To add modules by default into the kernel each time the system boot add the module name to 
    * `/etc/modules`
  * To prevent modules from being loaded into the kernel, add them to a `blacklist` file in 
    * `/etc/modprobe.d`

* `modinfo` - module information
  * display information about a specific module 

* `depmod` - dependency modules
  * Generate the modules.dep kernel module depenencies file

* `insmod` - insert module
  * install a module into the kernel
  * Does not install dependencies
    * use `modprobe` for that

* `rmmod` - remove module
  * removes a module from the kernel


### Tuning the kernel
* The `/proc` pseudo filesystem is used for kernel tuning and optimization
* This can be done by modifying specific kernel parameters directly
* This is not persistent however
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
  * All the numbers shown in the directory are process IDs. Inside them you will find process info that can be shown by `ps` as well

* `sysctl` - sys control
  * Not be confused with `systemctl`
  * Interface to dynamically change kernel parameters **persistently** 
  * Kernel parameters are stored in `/proc/sys`
  * Kernel parameters can be configured to:
    * Improve performance
    * Harden security
    * Configure networking limitations
    * Change virtual memory settings 
  * options: 
    * `-a`                    - print all parameters and the current values
    * `-w <parameters=value>` - set a parameter value 
    * `-p <filename>`         - load sysctl settings from a specified file
    * `-e`                    - ignore errors
  * `/etc/sysctl.conf` enables configuration changes to a running Linux kernel


### Managing Devices 
* **`sysfs`**
  - **Type**: Virtual Filesystem  
  - **Purpose**: Exposes kernel device and subsystem information to user space.  
    - Mounted at `/sys` and provides information about devices and their attributes
    - Presents information about:  
      - Various kernel subsystems  
      - Hardware devices  
      - Drivers  
  - **Example**:  
    - Check `/sys/class/net` for network interface details.
  - **Commands**
    - `udevadm` is the command that provides an interface to query device information from `/sys`


* **`udev`**
  - **Type**: Device Manager  
  - **Purpose**: Responsible for dynamically managing device nodes in the `/dev/` directory.  
    - Device nodes represent hardware devices like disks, USB drives, network interfaces, and more.  
  - **Capabilities**:  
    - Low-level access to the Linux device tree  
    - Handles user-space events (e.g., loading firmware, adding hardware)  
  - **Example**:  
    - Access is provided by a temporary filesystem (`tmpfs`) mounted to `/dev/`

* **`devtmpfs`**
  - **Type**: Virtual Filesystem  
  - **Purpose**: Automatically populates the `/dev` directory with device nodes at boot, which are then managed by `udev`.  
  - **Example**:  
    - Handles module loading for hardware devices

* **`tmpfs`**
- **Type**: Temporary Filesystem  
- **Purpose**: A memory-based filesystem used for temporary data storage that does not persist after reboot.  
- **Example**:  
  - The `/tmp` directory is often mounted as `tmpfs`.

* `dmesg` - display / driver messages
  * Prints messages that have been sent to the kernel's mesasge during andd after system boot
  * Drivers can also send diagnostics messages to the kernel when they encounter errors
  * Great for troubleshooting and driver validation


## Boot process and GRUB management

### Boot components 
Before going into the actual boot process, first some notes on some important components of the boot process.
The main components of the boot process are: BIOS/UEFI, which will be taken as general knowledge, the Linux kernel, discussed above,`initrd` and `grub2`, which will be discussed here. 

* `initrd` - initial ram disk boot 
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
  * GRUB knows three important files/ directories:
    *  `grub.cfg`
      * File containing the main config for GRUB 
      * However, it is never edited itself; edit `/etc/default/grub` instead - see below
      * It is located at 
        * `/boot/grub/grub.cfg`               - BIOS
        * `/boot/efi/EFI/<distro>/grub.cfg`   - UEFI

    * `/etc/grub.d` 
      * Directory containing scripts that are used to build the main `grub.cfg` file
      * Add custom scripts as ##_fileName 
      * Or add the script to the `40_custom` file

    * `/etc/default/grub`
      * Contains GRUB2 display menu settings that are read by the `/etc/grub.d` scripts
      * This file must be saved to the boot folder `/boot/grub2`. 
      * To generate a new or update an existing `grub.cfg`, run:  
        * In RHEL-based systems: `sudo grub2-mkconfig -o /boot/path/to/grub.cfg`
        * In Debian-based systems: `sudo update-grub2` 


### **The Boot Process**

**1. BIOS/ UEFI**
* When a machine is powered on, the CPU is hardwired to execute boot code stored in ROM.
* CPU checks the BIOS/UEFI firmware
* BIOS/UEFI is loaded from NVRAM
* BIOS/UEFI probes for hardware
  * BIOS/UEFI runs POST 
      * Power On Self Test 
      * Tests whether all hardware pieces are working properly 
      * If there is an error it is shown on the screen 
* BIOS/UEFI checks for bootable media and the user must select one
    * Hard drive 
    * USB 
    * CD 
    * PXE 
    * NFS
  * EFI system partition is identified
  * BIOS/UEFI loads the primary boot loader from the MBR or GPT, and loads the partition table with 


**2. GRUB2** 
* GRUB2 selects the OS 
* GRUB2 determines the kernel and locates the kernel binary on the disk  
* GRUB2 loads the kernel into memory and runs the kernel code 

**3. Linux Kernel** 
* Kernel is loaded
* Kernel data structures get instantiated
* Kernel checks the hardware, and decompresses the `initrd` or `initramfs` image to load the hardware drivers and other device drivers into memory \
  * `initramfs` contains all the drivers
* Virtual devices such as RAID and LVM are initialized here
* Kernel mounts the main root partition and releases unused memory back into the system
* This starts `init` as the first process 

**4. init** - Initialization system 
* init/ systemd executes startup scripts

* **Background on `init`**
  * `init` brings up and maintains user space services 
  * Init is the first process run by Linux, so it always has process ID 1
  * For many distros, `**systemd**` is the used init system 
      * Others are `sysv` and `upstart`
      * `systemd` was spearheaded by RedHat 
  * `systemd` is a collection of units (e.g. services, mounts, etc) 
      * Systemctl, journalctl, loginct, notify, analyze, cgls, cgtop, nspawn 
      * To query all units, run: systemctl list-unit-files 
      * Also see which are enabled, disabled, masked 
* `systemd` searches for the `default.target` file
  * This contains details on the services that need to be started
* Then it mounts the file system based on the `/etc/fstab` file
* When using a `graphical.target` then the login screen is loaded and the user can log in


### **Run levels**
* Way of booting the system 
* Only used in **SysV** systems, not in systemd systems
* take particular note of run level 1 - single user mode
  * this boots the system into a single user mode, requiring no password
  * THIS is the way to reset the root's password 

* **For Debian-based distros:**
  * 0. halt
  * 1. single user mode 
  * 2. full, multi user, GUI if installed
  * 3. nothing
  * 4. nothing
  * 5. nothing
  * 6. reboot (into system default)

* **For RHEL-based distros:**
  * 0. Halt
  * 1. single user mode
  * 2. multi user, no net
  * 3. mutli user, with net
  * 4. nothing
  * 5. multi user GUI
  * 6. reboot 


### **Boot Targets**
* Boot targets are the modern equivalent of runlevels used in systemd
  * More flexible and descriptive way of managing system states
  * Boot targets are particularly useful when troubleshooting the system, especially when it does not want to boot properly.
  * Some targets are isolatable which ameans that you can use them to defined the state your system should be started in 
* There's **four** of them:
  * **emergency.target** - minimal environment, no services, only a root shell   
  * **rescue.target**    - single-user mode with basic services, **used to change root password**
  * **multiuser.target** - multi-user mode with no GUI
  * **graphical.target** - multi-user mode with GUI
* Besides these, there are **two** targets that are not **boot targets**
  * These are: 
    * poweroff.target
    * reboot.target
  * Like the name suggests, you do not **boot into** them. 
* **Boot target vs runtime equivalence**
  * poweroff.target    - runlevel 0 
  * rescue.target      - runlevel 1
  * multi-user.target  - runlevel 3
  * graphical.target   - runlevel 5
  * reboot.target      - runlevel 6

* **systemctl command to manage boot targets**
  * `systemctl get-default`
      - Get default mode
  * `systemctl list-dependencies <target>` - see contents and dependencies of a systemd target
  * `systemctl set-default <mode_name>` - set default boot target
    * this only takes effect after rebooting
  - `sudo systemctl isolate <name.target>`
      - Temporarily change the boot target
      - this takes effect immediately

### Troubleshooting the boot process
* If the **bootloader** does not show or load
  * Then you need a rescue disk
  * This is new copy of installation disk of the Linux distro
  * From there you can access a prompt through which you can access the hard drive and troubleshoot or reinstall grub2
* If grub2 is shown, but the **kernel** is not
  * Make changes to the **grub2 menu** so that it loads the OS properly
* If **systemd** is not loading
  * Use `init=/bin/bash` if systemd is not loading at all
  * Use `emergency.target` or even `rescue.target` if systemd is loading but there are still issues
  * Both can be added as kernel parameters in the **grub2 menu**

## Localization and time 

* **`iconv`** - convert 
  - Convert text from one encoding to another
  - `iconv -f utf-8 -t iso_8653-1 < file.txt > output.txt`
  - Convert between encodings
  - `iconv -l`
    -  Print supported encodngs

- **`file <filename>`**
  - Detect file encoding

* **`locale`**
  - Print and configure locale settings
  - Region-specific settings, including language and country formats.


### Time
- **`hwclock`** - hardware clock
  - View and set the hardware clock
  - Hardware time is measured on the motherboard using a battery
  - Best practice is to keep it in sync with universal time 

- **`timedatectl`**
  - print or set local time, universal time, time zone, ntp info, etc. 
  - this command should three clocks
    - local time      - local current time
    - Universal time  - UTC/ GMT
    - RTC time        - Hardware clock as measured by the motherboard

- **`date`** - print or set date and time
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
  - Mainly good for printing time, not for setting it, use `timedatectl` for that 

- **`tzselect`**
  - Select a timezone

- **`cat /etc/timezone`**
  - View current timezone
  
- **`/etc/localtime`**
  - Current timezone configuration
  - File is symlinked to the appropriate timezone file
  - File cannot be read since it is a binary 


### NTP
* **Configure NTP *server*:**
  - Install the daemon: `apt install ntp`
  - Configuration file: `/etc/ntp.conf`

* **Configure NTP *client*:**
  - Install the client: `apt install ntpdate`
  - `ntpdate`
    - Sync and set the date and tiem via NTP
  - Not necessary to use or configure, when using `systemd`
  - 

    
## Process management

- `ps` - Lists running processes.
  - By default, it only shows processes for the current user.
  - options:
    - `a` - displays all user-triggered processes
    - `u` - list proceses along with the username and start time
    - `x` - include processes without a terminal
    - `fax` - shows parent child relationship 
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


### Resource Limits
* POSIX resource limits
  * `ulimit` - user limit 
    * limits the system resources for a user in a Linux-based server
    * It applies persistent `ulimit` settings to `/etc/security/limits.conf`    
      * Soft limits can be modified by the users
      * Hard limits implement an absolute ceiling
    * **Commands**
      * `ulimit -n 512`   - limit for max open files is put for a particular user
      * `ulimit -a`       - see all currently  limits configured

* PAM resource limits
  * `pam_limits` 
  * Applied from PAM session to configure **persistent** limits
  * It works with `/etc/security/limits.conf` and `/etc/security/limits.d/*.conf`
  * For the how to, see `man 5 limits.conf`
    * For example: limit the amount of concurrent logins (`maxlogins`) for a certain group or user


* Cgroup resource limits
  * Cgroups place resource in controllers that represent the type of resource, like cpu, memory, blkio
    * By default, processes get an equal amount of resources
    * Cgroups are used to limit the availability of resources using limits and weights
    * Integrated with systemd
    * systemd uses 3 slices to identity resource types:
      * System for system services and daemons
      * VMachine for VMs and containers
      * User for user sessions
  * See below on its own section

### Inter-Process Communication
* This is about how different processes communicate with each other in user space
* Current options are:
  * **Remote Procedure Calls (RPC)**
  * **D-Bus**
* IPC mechanisms:
  * **Shared files:** two programs read/write to a shared file
  * **Shared memory with semaphores:** values in memory that can be tested and set by mutliple programs
  * **Named and unnamed pipes:** output of one program is used as input by another
  * **Message queues:** One process writes to the queue, multiple processes can access
  * **Sockets:** Local or network port used for communication
  * **Signals:** Signals that are generated to alert processes
* Commands:
  * `ipcs` - show information on IPC facilities
  * `ipcs -p` - see names of processes using these system v IPCs
  * `ipcsc` - Print summary of usage of all IPC resource types


* **D-Bus**  
  * Modern way of managing IPC
  * Applications like `NetworkManager` use D-Bus to communicate with the system's networking stack.


### Managing Out-of-Memory
* **OOM**
  * When a Linux system runs out of memory, the OOM killer activates and kills the process with the highest OOM score
  * What process this is, can be manipulated
  * `/proc/sysrq-trigger` provides commands that can be used for testing specific functionality
  * In some distro's it is disabled
  * Commands:
    * `echo 1 > /proc/kernel/sys/rq` - enable full functionality 
    * `echo h > /proc/sysrq-trigger` - print sysrq-trigger help
    * `dmesg` - see the help output
    * `echo f > /proc/sysrq-trigger` - trigger the OOM killer
    * `journalctl | grep -A 10 "Out-of-Memory"` - Monitor what has happened


### I/O Monitoring
* `top` 
  * Check the `weight` parameter
* `iotop`
  * top like interface for I/O
* `vmstat` 
  * Print various statistics about virtual memory, CPU, and I/O 
  * See section of system monitoring


## systemd management
### **Managing Services**
* Services:
  * Running programs or processes that provide support for requests and monitoring from other processes or external clients
  * e.g., postfix, apache, and systemd
* `systemctl` - system control
  * Utility for controlling the systemd init daemon
  * **Options**:
    * `status`, `start`, `stop`, `restart`, `enable`, `disable` - speak for themselves. All used in conjunction with a service or unit 
    * `set-default`         - set the default boot target for the system to use on boot, e.g. when you want to change the root password
    * `mask`/ `unmask`      - prevent/ enable the provided unit file from being enabled or activated
    * `list-unit-files`     - Showing all the unit files used on the system and their status

### **Targets**
* A target is group of services
  * Besides the four boot targets mentioned previously, there's a bunch more
  * But you cannot put your system into that target
  * e.g. Bluetooth.target
* `systemctl` options for managing targets:
  * `start <target.type>`         
  * `isolate <target.type>`           
  * `list-dependencies`         
  * `get-default`         
  * `set-default`         

### * **Editing systemd units**
* `systemctl show <unit.type>`
  * Print all editable parameters
* **`man systemd.directives`**
  * Look up information on the parameters
* `systemctl cat <unit.type>`
  * Show the currently configured parameters
* `systemctl edit <unit.type>`
  * edit the unit 
* `daemon-reload`
  * Reload the systemd init daemon, including all unit files. 
  * Required when needing to reapply theconfiguration after using `systemctl edit`

### systemd sockets
* A socker is used witha service to start the service when traffic comes in on a specific socket
* In other words: sockets are listeners that trigger service startup on incoming traffic
  * e.g.: sshd.socket / sshd.service
* The service and socket files need to have the same name 
* `systemctl list-unit-files  -t socket`
  * This just print all units with `-[t]ype` socket
* Custom sockets can be created easily. 
  * Just tell them what to monitor and what to start when they detect incoming traffic
  * Note: the socket and service must have the same name
  * Create a `servicename.socket` under `/lib/systemd/system`

### systemd timers
* `systemctl cat logrotate.timer`
  * When logrotate triggers 
* `systemctl cat fstrim.timer`
  * When fstrim triggers
* It basically replaces cron jobs
* To create it, create a file in `/lib/systemd/systemd`

### Control groups
* Cgroups place resources in controllers that represent the type of resource
* Common default controllers are: cpu, memory, blkio
* These controlelrs are dubdivided ina  tree structure where differenw eights or limits are applied to each branch
  * Each of thewse branches is a Cgroup
  * One or more processes are assigned to a Cgroup
* Cgroups can be applied from the commnad line or from systemd
  * Manual creation happened through the **`cgconifg`** service and the **`cgred`** process
* Docker and Kubernetes use `cgroups` to manage container resource allocation 
 
 ### Depdendency management
 * Different statements can be used in hte unit sectiopn to define unit file dependencies:
  * **before**: This unit starts before the specified unit 
  * **after**: This unit starts when the specified unit is started
  * **requires**: when this unit starts, the unit listed here is also started. If the specified unit fails, this one also fails
  * **wanted**:  when this unit starts, the unit listed here is also started
* To create such a dependency:
  * `systemctl edit <name.service>`
  * add lines
  ```bash
  [Unit]
  Requires=other.service
  ```
* Targets  are better for a more consistent dependency management

### systemd self-healing
* Get service to restart when they get into a failed state
* To configure:
  * `systemctl edit <service>`
  * add lines
  ```bash
  [Service]
  Restart=always
  RestartSec=<TimeInSeconds>
  ```













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

* `free`
  * print free memory space 
  * it uses the `/proc/meminfo` file

* `lscpu` - list cpu 
  * list cpu information

* `lsmem` - list memory
  * list memory card information

* `vmstat` - virtual memory statistics
  * Print various statistics about virtual memory, CPU, and I/O 
  * It prints *averages* since the last bootup

* `iostat` - input-output statistics
  * Report statistics for devices and partitions.
  * Display a report of CPU and disk statistics since system startup

## Log management
* `rsyslog`
  * sent system logs from all kinds of devices to a centralized server

* `logrotate`
  * 


## Application management

### RedHat-based systems
* `rpm`
  * low-level package management program using .rpm files 
  * `-i` - install
  * `-e` - erase, remove or uninstall
  * `-v` - verbose
  * `-h` - print hash marks to indicate a progress bar
  * `-V` - verify software components
  * `-q` - query whether package is installed
  * `-f` - file, often used together, `-qf` querying a file. Identify owner of a file and show version of the package
  * `-U` - upgrade and install
  * `-F` - refreshen *installed* packages

* `yum`
  * Frontend package manager for `rpm`
  * Benefit over `rpm` in that it automatically handles dependencies
  * It uses repos
  * `install`     - self-evident 
  * `localinstall`- install package from *local* repo
  * `remove`      - self-evident 
  * `update`      - self-evident 
  * `info`        - self-evident 
  * `provides`    - report what package provides specified files/libraries

* `dnf`
  * improved version of `yum`
  * same if not similar options as `yum`

### Debian-based systems
* `dpkg`
  * low-level package management program using .deb files 
    * analogous to `rpm`
  * `-i` - install 
  * `-r` - remove
  * `-l` - list all packages/ info on one package
  * `-s` - report whether package is installed

* `apt`
  * Frontend manager for `dpkg`
  

### Repos
* Storage location for available software packages
* Types
  * **Local repos**
    * Stored on the system's local storage drive  
  * **Centralized internal repos**
    * Stored on one or more systems withtin the internal LAN
  * **Vendor repos**
    * Maintained on the internet often by the distribution vendor

* `createrepo`
  * Updates the XML files used to reference the repository location
  * `.repo` configuration file will be created in the `/etc/yum/repo.d` directory

* `reposync`
  * Manages the mirroring process
  * This enables the syncronization of an online repo to a local storage location


### Compiling software
* **compling**
  * Compiling is the process of translating the source code into binary, something the computer can actually understand
  * `gcc` - GNU Compiler Collection. This is the built in Linux compiler
* All dependencies of the software are in the `.h` or the `.a` file - the header or library file
* **Program libraries**
  * Chunks of compiled code that can be used in programs to accomplish common tasks
  * **Shared libraries**
    * Enabels more modular program builds and reduce time when compiling the software
    * `/usr/lib`  - general access
    * `/lib`      - essential binary access
  * `ldd <binary>` - view shared library dependencies for a porgram

### Software troubleshooting
* **Package mananger config file**
  * **`apt`**
    * `/etc/apt/sources.list.d`
    * `/etc/apt.conf`
  * **`yum`**
    * `/etc/yum.repo.d`
    * `/etc/yum.conf`
  * **`dnf`**
    * `/etc/dnf/dnf.conf`
  
**Verification and dependencies**
  * `rpm -V <package name>`
    * Verify that all the package components and its dependencies were properly installed
  * `rpm -qR <package name>`
    * Identify what dependencies are required
  * `yum deplist <package name>`
    * Identify what dependencies are required
  * `apt-cache depends <package name>`
    * Identify what dependencies are required


## Task Automation 

* `at`
  - Execute commands at a specified time by reading them from `STDIN` or a file.
  - All jobs scheduled to run at the same time are executed simultaneously.
  - Great tool for **one-time** tasks
  - `at <options> <time>`
    - `-m` - send mail to user
    - `-M` - prevent sending mail to user
    - `-f <file name>` - Execute a file 
    - `-t <time>` - Run the job at the specified time
  - Time can be given in words and text
    - `Noon`
    - `Teatime` - 4 PM
    - `Midnight`
    - `3 minutes from now`
    - `1 hr from now`
  - `echo "command" | at -m 1000`: Schedule a command to execute at 10 AM.
    - The result will be mailed to the user. If no mail server is available, check logs in `/var/log/syslog`.
  - `atq`: Check the list of scheduled jobs
  - `atrm <job_id>`: Remove a scheduled job
  - **Access Control**:
    - `/etc/at.deny`: Users listed here cannot use `at`
    - `/etc/at.allow`: Users listed here can use `at`. Overrides `at.deny` if present.

* `batch`
  - Similar to `at`, but jobs are executed only if the system load drops below 1.5 or a specified threshold set in `atd`.
  - Jobs are executed sequentially, not simultaneously.

* **Cron**
  - Schedules recurring jobs at specified times and intervals.
  - Best for repetitive tasks
  - Works by command `crontab <options> <file / user>`
    - `-e` - Edit or create scheduled jobs.
    - `-l` - List current cron jobs.
    - `-r` - Delete current crontab file
    - `-u` - Create crontab file for the specified user
  - **Access Control**:
    - `/etc/cron.allow`: List of users who can use cron. Overrides `/etc/cron.deny`.
    - `/etc/cron.deny`: List of users who cannot use cron.
  - **Configuration**:
    - Similar access rules as `at`.
