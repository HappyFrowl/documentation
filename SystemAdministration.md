# System Administration





 
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

**sysfs**
- **Type**: Virtual Filesystem  
- **Purpose**: Exposes kernel device and subsystem information to user space.  
  - Mounted at `/sys` and provides a structured way to view and manipulate kernel objects.  
  - Presents information about:  
    - Various kernel subsystems  
    - Hardware devices  
    - Drivers  
- **Example**:  
  - Check `/sys/class/net` for network interface details.

**procfs**
- **Type**: Virtual Filesystem  
- **Purpose**: Provides an interface to kernel data structures.  
  - Mounted at `/proc` and allows user space to query or control the kernel.  
  - Presents information about:  
    - Processes  
    - System information  
- **Example**:  
  - Access `/proc/cpuinfo` for CPU details or `/proc/meminfo` for memory usage.  
  - Run `ls /proc` to list all running processes.  
  - Interface with the kernel to change parameters on the fly.  
  - The `cmdline` file contains options passed by GRUB during boot.  
  - Linux version and kernel are stored as files in `/proc`.

**tmpfs**
- **Type**: Temporary Filesystem  
- **Purpose**: A memory-based filesystem used for temporary data storage that does not persist after reboot.  
- **Example**:  
  - The `/tmp` directory is often mounted as `tmpfs`.

**devtmpfs**
- **Type**: Virtual Filesystem  
- **Purpose**: Automatically populates the `/dev` directory with device nodes at boot, which are then managed by `udev`.  
- **Example**:  
  - Provides base device nodes for hardware detected during boot.

**cgroups**
- **Type**: Resource Management Subsystem  
- **Purpose**: Limits, prioritizes, and accounts for resources (CPU, memory, I/O) used by groups of processes.  
- **Example**:  
  - Docker and Kubernetes use `cgroups` to manage container resource allocation.

**FUSE** (Filesystem in User Space)
- **Type**: Virtual Filesystem Framework  
- **Purpose**: Allows non-privileged users to create and manage filesystems in user space.  
- **Example**:  
  - Filesystems like `SSHFS` or `NTFS-3G` use FUSE.

## Modules
- **Type**: Loadable Kernel Module (LKM)  
- **Purpose**: Extend the running kernel without needing to recompile or reboot the system.  
  - LKMs are object files dynamically loaded and unloaded into the kernel as needed.  
- **Examples**:  
  - Use `lsmod` to list currently loaded kernel modules and their usage.  
  - Remove modules with `rmmod` and add them with `modprobe`.


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

-/tmp` - Temporary Files
- Stores temporary files.
- Files in `/tmp` are non-persistent between reboots.

`/proc` - Process Filesystem
- An illusionary filesystem created in memory by the Linux kernel.
- Used to keep track of running processes.



## Boot Targets

Boot targets are the modern equivalent of runlevels in `systemd`, which has replaced `SysVinit` in most modern Linux distributions. They provide a more flexible and descriptive way of managing system states. Boot targets are particularly useful when troubleshooting the system, especially when it does not want to boot properly.

- `systemctl get-default`
    - Get Current Boot Target

- `sudo systemctl isolate <target>`
    - Temporarily Change the Boot Target

    




## Localization and time 

* `locale`
  - Region-specific settings, including language and country formats.
  - View and configure locale settings.

- **Convert between encodings**:
  - `iconv -f utf-8 -t iso_8653-1 < file.txt > output.txt`
- **List supported encodings**:
  - `iconv -l`
- **Detect file encoding**:
  - `file <filename>`


- View current timezone:
  - `cat /etc/timezone`
- System timezone configuration:
  - `/etc/localtime` (symlinked to the appropriate timezone file).
- Select a timezone:
  - `tzselect`
- View time in another timezone:
  - Use tools like `date` with `TZ` environment variables or GUI tools.

- `date` - print or set date and time
- `date +%s` - print time as epoch time
- `timedatectl` - print or set local time, universal time, time zone, ntp info, etc. 

- Synchronize with NTP servers:
  - Install the client: `apt install ntpdate`
  - Sync with NTP pool:
    - `ntpdate pool.ntp.org`
- Configure as an NTP server:
  - Install the daemon: `apt install ntp`
  - Configuration file: `/etc/ntp.conf`

- The hardware clock is independent of the system and runs even when the machine is powered off.
- `hwclock --systohc --localtime` - Sync hardware clock with the local clock






 

## Process management

- `ps` - Lists running processes.
  - `ps`: Shows processes for the current user.
  - `ps a`: Displays all processes attached to a terminal.
  - `ps x`: Lists processes for all users, even those not attached to a terminal.
  - `ps aux`: Formats output for user-oriented listing.
  - Processes in **square brackets** are system or kernel processes.

* `lsof` - list open files
  * `-c` - filter on process name
  * `-p` - filter on PID
  * `-u` - filter on a specific user

* `fuser`
    * Display process IDs currently using files or sockets
    * 


* `top`
  * display Linux processes
  * print how much CPU, RAM, swap is used, its uptime, idle 
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
  * Columns information:
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
  * 

* `renice`
  * 

- `kill` Command - Sends signals to processes to perform specific actions.
    **Signals**:
    - **SIGINT**:
        - Interrupt signal (Ctrl + C).
        - Stops the process, allowing it to clean up resources.
    - **SIGKILL**:
        - Forces the process to terminate immediately.
    - **SIGSTOP**:
        - Pauses the process (Ctrl + Z).
    - **SIGTERM**:
        - Requests the process to terminate gracefully.
        - Allows time for cleanup, but the process can ignore it.

- `killall` - Kills all processes by name.
    - Only affects the current user's processes unless used with `sudo`.
    - `kill -9 brave` - SIGKILL brave

- `pkill`- Similar to `killall` but more flexible and potentially dangerous.
    - Allows matching processes based on name or other attributes.



## Service management

* `systemctl`
* `jounalctl`
* `chkconfig`
* `rc.local`

## System monitoring
* `uptime` 
* `vmstat` 
* `iostat` 
* `free`

## log management
* `rsyslog`
* `logrotate`
* 

## Kernel management
* `uname`
* `sysctl`
* `dmesg`
* `lsmod`
* `modprobe`

## Boot process

**1. BIOS/ UEFI**
* Hardware is booted 
* BIOS/UEFI runs POST 
    * Power On Self Test 
    * Tests whether all hardware pieces are working properly 
    * If there is an error it is shown on the screen 
* If no error was encountered, the BIOS/ UEFI must find the bootloader software 
    * Hard drive 
    * USB 
    * CD 
    * PXE 

**2. GRUB2** 
* GRand Unified Bootloader 
* Its jobs 
    * Locate the kernel on the disk 
    * Load the kernel into the computer memory  
    * Run the kernel code 
* The bootloader starts the OS by using initrd – initial ram disk 
    * This is a temporary file system which is loaded into memory to assist in the boot process 
    * It contains programs to perform hardware detection and load the necessary modules to get the actual file system mounted 
    * Once the actual file system is mounted, the OS continues to load from the real file system 
    * Initfs (initial file system) is the successor of initrd  

**3. Kernel** 
* Once the kernel is loaded into memory, the kernel takes to finish the startup process 
* It starts 
    * Kernel modules 
    * Device drivers 
    * Background processes 
* First it decompresses itself onto memory, check the hardware, and load device drivers and other device drivers 
* Then init takes off 

**4. Init**
* Initialization system 
* Brings up and maintains user space services 
* Once the kernel has attached the boot file system, init is run  
* Init is the first process run by Linux 
    * So process number is always pID 1 
    * Check with ps –aux | head 
    * Always run in the background 
* For many distros, systemd is the used init system 
    * Another is sysv or upstart 
    * Systemd was spearheaded by RedHat 
* Systemd is a collection of units (e.g. services, mounts, etc) 
    * Systemctl, journalctl, loginct, notify, analyze, cgls, cgtop, nspawn 
    * To query all units, run: systemctl list-unit-files 
    * Also see which are enabled, disabled, masked 
 


