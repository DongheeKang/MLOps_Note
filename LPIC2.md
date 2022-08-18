# Linux Administration


## Contents
  * [Capacity Planning](#capacity_planning)
  * [Linux Kernel](#linux_kernel)
  * [System Startup](#system_startup)
  * [Filesystem and Devices](#filesystem_devices)
  * [Advanced Storage Device Administration](#storage_devices)
  * [Network Configuration](#network_configuration)
  * [System Maintenance](#system_maintenance)
  * [Domain Name Server](#dns)  
  * [Web Services](#web_services)
  * [File Sharing](#file_sharing)
  * [Network Client Management](#network_anagement)
  * [E-Mail Services](#email_services)  
  * [System Security](#system_security)


<br/><a name="capacity_planning"></a>

### Capacity Planning
    ===============================================================================================
    LPIC 200: Capacity Planning
    ===============================================================================================
    - iostat
      monitoring system input/output device load
      $ iostat	: statistics since the last system reboot
      $ iostat 1 3    : interval(second), count(measure)
      $ iostat -c  	: cpu usage
      $ iostat -d 	: disk usage
      $ iostat -h	: human readable
      $ iostat -x	: erweitere Statistics

    - vmstat
      reports virtual memory statistics about processes, memory, paging, block I/O,
      traps, disks and cpu activity.
      values expressed in 1024 kilobyte (old kernel report other kilobytes)
      vmstat can NOT be used to show statistics per core (use mpstat or ps instead)
      $ vmstat 2 2    	       : delay(second), count(measure)
      $ vmstat -a		           : active
      $ vmstat -p /dev/sda1    : Partition
      $ vmstat -d		           : Disk table access
      $ vmstat -D		           : summary of disk statistics
      $ vmstat -s		           : events and statistics after system booting
      $ vmstat -f   	         : displays the number of forks since boot

      -Procs
      r: The number of runnable processes (running or waiting for run time).
      b: The number of processes in uninterruptible sleep.
      -Memory
      swpd :  the amount of virtual memory used.
      free :  the amount of idle memory.
      buff :  the amount of memory used as buffers.
      cache:  the amount of memory used as cache.
      inact:  the amount of inactive memory.  (-a option)
      active: the amount of active memory.  (-a option)
      -Swap
      si: Amount of memory swapped in from disk (/s).
      so: Amount of memory swapped to disk (/s).
      -IO
      bi: Blocks received from a block device (blocks/s).
      bo: Blocks sent to a block device (blocks/s).
      -System
      in: The number of interrupts per second, including the clock.
      cs: The number of context switches per second.
      -CPU
      These are percentages of total CPU time.
      us: Time spent running non-kernel code.  (user time, including nice time)
      sy: Time spent running kernel code.  (system time)
      id: Time spent idle.  Prior to Linux 2.5.41, this includes IO-wait time.
      wa: Time spent waiting for IO.  Prior to Linux 2.5.41, included in idle.
      st: Time stolen from a virtual machine.  Prior to Linux 2.6.11, unknown.

    - mpstat
      standard output activities for each available processor, 0 being the first one.
      $ mpstat -P 1
      $ mpstat -P ALL

    - netstat (see @ 205)
      list of network connection via tcp, udp, socket

    - ps
      list of the processes currently running
      $ ps -A 	: select all processes
      $ ps -a		: select all processes except session leaders
      $ ps -ef	: To see every process on the system (e = A)
      $ ps -u	        : select start time with full directory and user
      $ ps -x		: select process, which is not associated with terminal(cron or init)
      $ ps -C nfsd	: select process, which is given by command line
      $ ps -U	kang	: select process by a user
      $ ps -aux

    - pstree
      $ pstree -pn    : sort with PIDs
      $ pstree -a 	: show all
      $ pstree -G  	: VT100-Modus

    - top
      interactive option
      k : kill
      n : number of display
      r : renice
      q : quit

    - w
      list of users currently logged on
      $ w
      > 11:00:00 up 100 days, 19:00, 2 User, load average: 0.7, 0.11, 0.07
      systemtime since 100 days 19 hours, 2 users logged on, system load for past 1,5,15 Min
      $ w -s      : short form

    - uptime
      how long system has been running
      $ uptime

    - lsof
      Welche Dateien sind von wem auf einem System gerade geoeffnet?
      open files and corresponding processes.
      Prozesse anzeigen, die auf ein Geraet oder einen Netzwerk-Socket zugreifen
      $ lsof /tmp			   : information about process accessd tmp
      $ lsof | wc -l                     : count of all list of files
      $ lsof -i :80                      : list of internet address matches
      $ lsof /dev/sdc1                   : test of usb disk /media/usb-disk possible
      $ lsof -c abc		 	   : list of file for command start with "abc"
      $ lsof -F x		   	   : specifies a charater output
      $ kill -s 9 $(lsof -t /media/disk) : kill all processes related with disk.

    - free
      total amount of physical and virtual memory, free, use, and buffers(used by kernel)
      $ free -h      : human readable

    - sar
      collects, reports or saves system activity information
      $ sar 3 5        : interval(second), count(measure)
      $ sar -d 	 : output disk statistics
      $ sar -b	 : relasted to I/O and transfer rate
      $ sar -c 	 : System calls
      $ sar -p -w 	 : Paging and swapping activity
      $ sar -r 	 : free memory and swap over time
      $ sar -n 	 : network
      $ sar -A 	 : past 10 Minute

      $ apt-get install sysstat
      $ yum install sysstat
      For daemon
      $ vi /etc/default/sysstat
      > ENABLED="true"
      $ /etc/init.d/sysstat start

    - Network I/O modell
      blocking, non-blocking, syncronous, asyncronous

    - Monitoring solutions
      Nagios, MRTG(Multi Router Traffic Grapher ) and Cacti

      collectd(system statistics collection daemon for IT infrastructure)
      collectd daemon comes with over 100 plugins, allowing it to run on systems
      without scripting language or cron daemon, such as embedded systems.
      /etc/collectd.conf
      LoadPlugin lines are important to use it!

    - when is historical data of resource usage important


<br/><a name="linux_kernel"></a>

### Linux Kernel
    ===============================================================================================
    LPIC 201: Linux Kernel
    ===============================================================================================
    - Kernel
      kernel source, latest version 4.8.4 @ 22.Oct.2016
      /usr/src/linux/
      /usr/src/linux/linux-headers-3.16.0-4-amd64
      /usr/src/linux/Documentation/
      /usr/src/linux/arch/x86/boot/bzImage         : this fils will be copied into boot
      /usr/src/kernels/3.10.0-327.36.1.el7.x86_64  : location for centOS

      zImage   : a locally compiled, bootable linux kernel, size 512K        -> vmlunuz
      bzImage  : a locally compiled, bootable linux kernel, big kernel size  -> vmlunuz

      Boot partition you will find
      /boot/System.map-3.16.0-4-amd64     : system map
      /boot/vmlinuz-3.16.0-4-amd64   	    : linux kernel
      /boot/config-3.16.0-4-amd64         : kernel configuration
      /boot/initrd.img-3.16.0-4-amd64     : initial ram disk

      vmlinuz is used by a complete but unbootable form of the linux kernel
      generated as an intermediate step in preparing other kernel formats

      Kernel configuration file
      /usr/src/linux/.config

      For setting the configuration
      make config
      make menuconfig
      make xconfig (QT)
      make gconfig (GTK)
      make oldconfig        : preseve options choose during previous kernel build
      make cloneconfig      : same as oldconfig

      Standard procedure for install kernel
      make dep
      make clean
      make bzImage(zImage)  : build the kernel image into boot.
      make modules          : the device drivers that were configured as modules.
      make modules_install  : install modules that compiled under /lib/modules/kernel-ver.

      Remove kernel
      make clean     : deletes most generated files, but leaves to build external modules
      : removes old temporary files, but not old configuration files
      make mrproper  : deletes the current configuration and all generated files.
      make distclean : removes editor backup files, patch leftover files and the like.

      Some others
      make targets (all, rpm-pkg, binrpm-pkg, deb-pkg)

      After configuration and make, do update-grub of grub bootloader for new kernel,
      which is updated in menu.lst or grub.cfg

      For lilo case, just lilo
      $ vi /etc/lilo.conf
      > image = /boot/vmlinuz-2.6.28
      >  ...
      $ lilo           : lilo zu eintragen

    - initrd (initial RAM disk)
      if you either need a personalised configuration and kernel for each type of system
      or have to live with a bloated kernel, solution is a RAM disk(a chunk of memory)
      initrd is compressed archive and can be mounted like any other disk.
      The bootloader loads the initrd, it is mounted by the kernel as its root filesystem.
      If other root system is mounted, the previous root (from initrd) is then unmounted.

      RAM disk file is under /boot/initrd-2.6.18-i386.img
      To build initrd file, the mkinitrd script offers a convenient way
      mkinitramfs tool is much simpler than mkinitrd
      $ mkinitrd       : RPM based distributions
      $ mkinitramfs    : debian distribution, generates a gzipped cpio image
      /boot/initrd.img-3.16.0-4-amd64    <--- cpio file format

      Boot sequence within initrd
      1. The boot loader loads the kernel and the initial RAM disk.
      2. The kernel converts initrd into a “normal” RAM disk and
      frees the memory used by the initrd image.
      3. The initrd image is mounted read-write as root
      4. The linuxrc is executed
      5. After linuxrc terminates, the “real” root filesystem is mounted
      6. If a directory /initrd exists, the initrd image is moved there,
      otherwise, initrd image is unmounted
      7. The usual boot sequence(/sbin/init) is performed on the root filesystem

    - Patch of kernel
      patch can be used to apply the contents of a patch file to update the file
      $ bzip2 -dc patch-2.6.39-rc4.bz2 | patch -p1 --dry-run
      $ gunzip -c patch-2.6.39-rc4.gz  | patch -p1 --dry-run

      patch wieder entfernen
      $ bzip2 -dc patch-2.6.39-rc4.bz2 | patch -p1 -R

      patch option
      -p1    : stript the prefix containing number leading slashed from each file name.
      -R     : reserve

      - compile and installation of kernel including kernel-modules
      @ Debian
      $ cd /usr/src
      $ wget http://www.kernel.org/pub/linux/kernel/v3.5/linux-3.5.24.tar.bz2
      $ tar xvjf linux-3.5.24.tar.bz2
      $ ln -s linux-3.5.24 /usr/src/linux
      $ apt-get install libncurses5-dev make gcc     : curses needed menual config
      $ cd /usr/src/linux
      $ make menuconfig                 : or manage directly .config
      $ make
      $ make modules_install
      $ cp /usr/src/linux/arch/x86/boot/bzImage /boot/vmlinux-3.5.24
      $ cp /usr/src/linux/System.map            /boot/Sytem.map-3.5.24
      $ mkinitramfs -o /boot/initrd.img-3.5.34  : -o option (debian)
      $ mkinitrd /boot/initrd.img-3.5.34        : No option (rpm based)
      $ update-grub

      @ Fedora spcific, mit mkinitrd werden Sie dir initale RAM nicht erzeugen koennen,
      use dracut instead for fedora case
      $ yum install ncurses-dev make gcc dracut
      $ dracut  -o  initrd-3.5.34  3.5.34
      $ grub2-mkconfig         : neur Kernel beim Systemart zur Verfuegung steht

      If you want to use this new kernel in other system, then do
      $ make deb-pkg      : debian
      $ make rpm-pkg      : centOS
      $ make binrpm-pkg   : for binary rpm packet

    - kernel runtime management
      Where is Module des Kernels?
      $ /lib/modules/kernel-version/xxx
      $ /lib/modulee/kernel-version/kernel/drivers/acpi/video.ko
      $ /lib/modulee/kernel-version/kernel/fs    : data system
      $ /lib/modulee/kernel-version/kernel/net   : network card
      $ /lib/modulee/kernel-version/kernel/scsi  : scsi adapter
      $ /lib/modulee/kernel-version/kernel/video : graphic adapter

      $ uname -a		   : all about system information
      $ uname -r                 : print of current version of kernel
      $ uname -v 		   : /proc/sys/kernel/version
      $ lsmod                    : print of loaded modules via /proc/modules
      $ lsmod | grep xfs	   : show info. about loaded xfs modules
      $ modinfo          	   : print of loaded modules in detail (with su)
      $ modinfo -a module	   : show autor of kernel module
      $ modinfo -n module	   : show data name of module
      $ insmod /lib/.../usb.ko   : nur mit kompletter Pfad einen Modul laden
      $ rmmod -f ide_core.ko     : Modul forcely(erzwingt) entladen
      $ depmod    		   : generiert Datei dependency file under
      : /lib/modules/kernel-version/modules.dep
      $ depmod -n 		   : dry run

      lsmod displays information on drivers that are built as modules, not those that are built
      directly into the kernel that are used to other users' needs, that are loaded but not
      actively working, and on some types of external hardware devices, not internal

      rmmod is used to unload a module loaded by the kernel without unloading any module
      that is depends upon.

      Load/unload
      $ modprobe -l  : list all available and loaded modules
      $ modprobe     : Laden von Modulen (Ohne Pfadlangabe) inklusive Abhaengigkeiten
      $ modprobe xfs : load xfs module
      $ modprobe -a  : Laden alle
      $ modprobe -r  : Entladen von Moulen inclusive Abhangigkeiten geht nur
      : fuer unbenutzte Module Modulaabhaengigkeiten,
      : stehen in /lib/modules/Kernel-version/modules.dep

      module configuration files
      /etc/modules.conf	   : old version
      /etc/modprobe.conf	   : configuration file
      /etc/modprobe.conf.local   : not frontend, therefore manual configuration
      /etc/modprob.d/            : now a days, configuration data for modprod

      If you want to set I/O address and Interrupt for network card
      $ vi /etc/modprobe.conf
      > option 3c505   io=3x300 irq=10
      > alias eth0 3c505

      - /proc
      Prozessdateisystem, runtime kernel module infromation
      It contains files that reflect the state of the running system
      /proc allows you to modify some parameters at runtime by writing value
      but after restart of system everything will go back to orignal state
      $ cat /proc/sys/kernel     : kernel info in the system
      $ cat /proc/modules        : running module by current kernel
      $ cat /proc/cmdline        : from the bootloader to the kernel
      $ cat /proc/interrupts     : Info. about IRQs channels
      $ cat /proc/ioports        : Info. about I/O ports
      $ cat /proc/dma     	   : DMA channel
      $ cat /proc/cpuinfo        : info. about cpu
      $ cat /proc/meminfo        : info. about memory
      $ cat /proc/bus/usb        : info. about usb

    - Tools for system analysis
      $ dmesg    : write kernal message
      -c       : show then clean buffer
      -r       : show raw buffer
      -s xx    : set size of buffer
      -n 1     : message level 1-8
      $ lspci               : display information PCI buses in the system
      $ lsusb -v            : display information USB buses in the system
      $ lsusb -d 0c4d:0300  : Hersteller:Produkt-ID
      $ lsdev               : display information I/O address, IRQ/DMA channels

      Debug program by mornitoring system calls and reporting them
      $ strace touch testfile    : trace system calls and signals
      $ strace -c touch testfile : summary table
      $ strace -p program	   : to connect a running process

      $ ltrace cat /dev/null     : a library call tracer

      strings can extract tar, postscript, and binary fil, but cannot gzip!
      $ strings /bin/bash	   : print characters, useful for reading non-text files.
      $ strings program.exe	   : Evenv if you have exe file, you can read something
      $ lsof /tmp		   : display information about process accessd tmp

      - Analysis of protocol data, just reminder
      $ grep kernel /var/log/messages
      $ grep dhcp /car/log/syslog
      $ cat /var/log/apache/access.log
      $ cat /var/log/squid/access.log
      $ cat /var/log/samba/*
      $ cat /var/log/auth.log
      $ cat /var/log/mail.*

      Ruuning process with PID
      $ cat /proc/2352/environ
      $ cat /proc/2352/environ | tr "\000" "\n"

    - sysctl
      modify kernel paramaters at runtime, listed under /proc/sys/
      $ sysctl -w net.ipv6.conf.all.forwarding=1  : write into /proc/sys/net../forwarding
      $ sysctl -w net.ipv6.conf.all.forwarding=1  : write into /proc/sys/net../forwarding
      $ sysctl kernel.shmmax=212222222            : write into /proc/sys/kernel/shmmax

      $ sysctl -a  : display all values
      -e  : ignore error
      -p  : load /etc/sysctl.conf
      -w  : to change/apply setting

      alternatively, manage files in order to keep configuration of kernel parameter
      permanently after reboot
      /etc/sysctl.conf
      /etc/sysctl.d/

    - udev
      udev provides persistent naming for some device types, e.g. eth0
      userspace /dev can manage device more flexible and safe than devfs(obsolute)

      $ cat  /etc/udev/rules.d/99-my.rules
      > KERNEL=="video*", DRIVER=="saa7134", SYMLINK+="video-A180"
      > KERNEL=="xxxxx*", GROUP="user", MODE="0660"

      Explanation. create symbolik link /dev/video-A180 that points to the primary
      device file for a video device that have a dirver called saa7134
      but GRUOP and MODE use =, not ==

      If you want to set a name of network card with eth0 or eth1, have to modify
      $ cat  /etc/udev/rules.d/70-persistent-net.rules

      $ cat /etc/udev/udev.conf
      $ /etc/init.d/udev restart

      udevmonitor will print udev and kernel events to standard output.
      $ udevmonitor           : old monitor
      $ udevadm monitor       : new monitor

    - rdev
      query/set image root device, swap device, RAM disk size, or video mode

      A server was rebuilt using a full system backup but with a different disk setup
      The kernel won't boot, complaining it cannot find the root system.
      rdev is used to fix such error by pointing the kernel image to the new root partition


<br/><a name="system_startup"></a>

### System Startup
    ===============================================================================================
    LPIC 202: System Startup
    ===============================================================================================
    - How do I know whether systemd, SysVinit or upstart is used for system start?
      $ ps -A
      $ pstree
      $ sudo stat /proc/1/exe
      $ rpm -qf /sbin/init

    - System start
      BIOS(Basic Input and Output System) is a program and can make hardware conf.
      -> BIOS perform a test, that is called POST(Power on self test).
      -> BIOS accesses the initial sector of hard disk and find a bootloader in MBR.
      -> bootloader show an sequence to start program in a bootable partition.
      -> LILO or GRUB(boot loader) is loaded into RAM memory.
      -> Grub launch Linux Kernel on disk and loads Linux Kernel into memory
      -> Kernel control all hardwares on the system

    - UEFI
      In modern system, BIOS is replaced by UEFI(Unified Extensible Firmware Interface)
      UEFI have few advantage
      1. high graphic resolution in setup
      2. supprt GUID partition table
      3. Network connection
      4. possible integration of driver in firmware
      4. intergrated bootloader
      6. Digital right management allows to reject unwanted software

    - Boot Manager in Master Boot Records (MBR)
      Total 512 Byte, a partition table is located in the end of MBR (sector 0, spur 0)
      Partiton table start with 80 in the last 5th line and finish aa55 at the end.
      0-440(bootcode) 446-510(partition table 4primary x 16bytes = 64bytes) 510-512(Sign)

    - The Linux Boot process II
      0. BIOS -> MBR -> Boot loader
      1. Kernel loader loading, setup and execution
      2. Register setup
      3. Kernel decompression
      4. Kernel and memory initialization
      5. Kernel setup
      6. Enabling of remaining CPU’s
      7. Init process creation

      The kernel’s final step in the boot process tries to execute these commands in order
      1. /sbin/init   : frist program @ boot by linux kernel
      2. /etc/init
      3. /bin/init
      4. /bin/sh
      If none of these succeed, the kernel will panic.

    - Runlevels
      init erster Prozess im System als interakitver Befehl aufgerufen bewirkt init den
      Wechsel des Runlevels. Es kann auch telinit dafur verwendet werden.
      # runlevel 0 System halt
      # runlevel 1 Single user
      # runlevel 2 local multiuser without remote network
      # runlevel 3 full multiuser with network
      # runlevel 4 not used
      # runlevel 5 full multiuser with network and xdm
      # runlevel 6 system reboot

      $ init 0  : stop system
      $ init 1  : rescue mode
      $ init s  : super user mode
      $ init 6  : reboot

      - system start with SysV(init-process)
      $ cat /etc/inittab    : main configuration file
      > id:3:initdefault:   : set of default runlevel
      > 1:2345:respawn:/sbin/mingetty --noclear tty1
      > 1:2345:respawn:/sbin/mingetty tty1
      > 1:2345:respawn:/sbin/mingetty tty2
      > 1:2345:respawn:/sbin/mingetty tty3
      > 1:2345:respawn:/sbin/mingetty tty4
      > 1:2345:respawn:/sbin/mingetty tty5
      > 1:2345:respawn:/sbin/mingetty tty6
      > ca::ctrlaltdel:/sbin/shutdown -r -t 4 now (ctrl+alt+del)

      /etc/init.d/      : init process or
      /etc/rc.d/        : init process
      /etc/init.d/rc0.d : symbolic link of /etc/init.d/*  or
      /etc/rc0.d        : symbolic link of /etc/init.d/*

      init process or service in /etc/init.d
      /etc/init.d/apache2
      /etc/init.d/udev
      /etc/init.d/ancron
      /etc/init.d/cups

      symbolic links in /etc/rc3.d look like e.g.
      K04apache2        : K means stop
      S13named	  : S means start
      K05sendmail	  : two digital number shows order
      S18apache2        : apache called two times by stop and then by start later.

      You should start or stop manually by ACTIONs (stop, start, restart, status, probe)
      $ /etc/init.d/atd start        : at daemon start
      $ /etc/init.d/apache2 start    : aparche daemon start

      If something goes wrong for inittab
      $ telinit q    : read inittab configuration again and go back to previous state
      without restart, tell init to re-examine the /etc/inittab

      Install the init script links, use update-rc.d (Debian)
      $ update-rd.d foo defaults
      > adding system startup for /etc/init.d/foo
      > /etc/rc0.d/K20foo -> ../init.d/foo     : kill
      > /etc/rc1.d/K20foo -> ../init.d/foo	 : kill
      > /etc/rc6.d/K20foo -> ../init.d/foo     : kill
      > /etc/rc2.d/S20foo -> ../init.d/foo	 : start
      > /etc/rc3.d/S20foo -> ../init.d/foo     : start
      > /etc/rc4.d/S20foo -> ../init.d/foo	 : start
      > /etc/rc5.d/S20foo -> ../init.d/foo	 : start
      remove the init script links
      $ update-rc.d -f dovecot remove
      After update dovecot, the links will be restored, to prevent it, create stop links
      $ update-rc.d -f dovecot stop 24 2 3 4 5 .
      If you want to turn off automatic process, use enable/disable
      $ update-rc.d cron enable
      $ update-rc.d enable disable 23  : enable service will be disable in runlevel 2/3

      Install and remove the init script links, use this (RedHat)
      $ chkconfig --add  : add service
      --del  : delele service, script will remain
      --on   : turn on softlinks
      --off  : turn off softlinks
      --list : show all services and softlinks for each level

    - shutdown
      shutdown, reboot, poweroff, halt
      $ shutdown -f -r now      : reboot send a message
      $ init 6 		  : reboot without message
      $ shutdown -h now
      -r reboot
      -k konsolenmedung senden
      -f kein fsck (verhindern)
      -F fsck (erzwungen)
      -h halt
      -c cancel scheduled shutdown

    - LSB standard
      Linux Standard Base
      LSB compatible system
      libraries : libdl,libcrypt,libpthread
      binaries  : cp, tar, kill
      funtions  : init cron

    - Grub Legacy
      to modify kernel update by prompt or by hand
      ---------------------------------------------------------------------------
      #  /boot/grub/menu.lst or /boot/grub/grup.conf
      > timeout 5
      >
      > title Debina Linux 2.4.18
      > root (hd0,0)
      > kernel /vmlinuz-2.4.18-14 ro root=/dev/hda2
      > initrd /initrd-2.4.18-14.img
      > boot
      ---------------------------------------------------------------------------
      $ grub-install /dev/sda  : install grub legacy into the MBR of your first SATA
      : normally after emergency boot, you will do this

      If you want to boot with single user mode
      1. press e or press c
      2. find the line with "kernel"
      3. add "single"
      4. press b

    - Grub2
      stark modularisiert und automatisiert
      konfigurationsdatei /boot/grub/grub.cfg (nicht von Hand bearbeiten!)
      konfigurationsdatei /boot/grub/device.map (path of the filesystem partitions)
      weiere Konfigurationsdateien liegt in
      /etc/default/grub
      /etc/grub.d/*
      more complicated and divided accordinlgy by grub.d
      ---------------------------------------------------------------------------
      # GRUB2 grub.cfg
      > set timeout=5
      > set default=0
      > menuentry 'Debian Linux 3.10.12' {
      >   root=hd0,1
      >   kernel /boot/vmlinuz-3.10.12   ro   root=/dev/hda2 quiet
      >   initrd /boot/initramfs-3.10.12.img
      > }
      > menuentry 'Linux 4.10.12' {
      >   linux /boot/vmlinuz-4.10.12 root=/dev/sda2 single
      > }
      ---------------------------------------------------------------------------
      durchsucht alle Festplatten partionen Betriebsssytemen und findet sie in GRUB ein
      $ grub2-mkconfig

      The update-grub is a wrapper aroud grub-mkconfig
      Wenn Sie einem System manuell einen neuen Kernel hinzugefuegt haben, dann folgende
      $ update-grub2

      If you want to boot with single user mode
      1. press e or press c
      2. find line with "linux" (or "kernel")
      > linux    /boot/vmlunuz-3.16.0 root=UUID=xxxxx  ro quiet
      3. add "single" or "1"
      4. ctrl+x or F10 : reboot
      ctrl+c or F2  : grub console

    - Grub vs Grub2
      kernel (grub shell) <-> linux command
      (hd0,0)             <-> (hd0,1)
      /boot/grub/menu.lst <-> /boot/grub/grub.cfg
      root (root-device)  <-> set root (root-device)
      loaded with module  <-> insmod to load module

    - Device map
      To have GRUB rescan all of the devices, the device.map
      /boot/grub/device.map          : generated by grub-mkdevicemap
      This file tell the paths of the fs partitions in the GRUB syntax

    - Rescue boot and file system fix
      fsck returns an exit status
      0   - no errors
      1   - file system errors corrected
      2   - system should be rebooted
      4   - File system errors left uncorrected
      8   - operational error
      16  - usage or syntax error
      128 - shared library error
      Do not press ctrl+D, just do enter, then /sbin/sulogin is launched, and
      able to run fsck and fix the problem if the root filesystem is mounted read-only

      If your root filesystem is corrupt, then do following procedure
      1. boot with USB                : USB booting
      2. mount /dev/sda1 /target      : mount harddisk into RAM
      3. fsck -y /dev/sda1            : test and fix
      4. unmount /target		: unmount harddisk

      Consider when you meet a problem for file system and to fix it
      $ mount | grep usb-disk1
      $ umount /media/usb-disk1
      $ lsof /media/usb-disk1
      $ dmesg | tail -n 20
      $ umount /dev/sdb1
      $ fsck -t ext4 -V /dev/sdb1
      $ mount /dev/sdb1 && dmesg | tail

      $ mount /dev/sdb1 -o errors=remount-ro
      $ mount /dev/sdb1 -o remount,rw
      $ mouun /dev/sdb1 -o remount,ro

    - LILO
      LILO can be placed in the master boot record (MBR) or the boot sector of a partition
      but a single linux installation and no other operating systems, then located in MBR!

      When lilo is loaded, show error code in the screen
      > LILO    : OK
      > LI-     : such incomplete word means the descriptor table is bad

      $ lilo
      -C  : alternative konfiguration data, not in /etc/lilo.conf
      -R	: command line mode in next system start
      -M	: write LILO into MBR
      -q  : show current boot configuration

      /etc/lilo.conf
      > image = /boot/vmlinuz-2.6.11
      > label = 2.6.11
      > map = /boot/map
      > initrd = /boot/initrd
      > root =/dev/hda2
      > message, timeout, promp, default, boot, image, label, map, initrd, root

    - Alternative boot loader
      syslinux : boot possible by USB, which has Windows FAT format
      extlinux : syslinux derivative
      isolinux : ISO-9660 datasystem (CD), Eltorito specified
      pxelinux : preboot execution environment mit DHCP und TFTP (network boot)

      isolinux.bin : binary source
      isolinux.cfg : configuration file

      pxelinux.0   : source files
      pxelinux.cfg : configuration file



<br/><a name="filesystem_devices"></a>

### Filesystem and Devices
    ===============================================================================================
    LPIC 203: Filesystem and Devices
    ===============================================================================================
    - To display mounted filesystems
      $ fdisk -l          : all mounted list or can also specify with /dev/sda
      $ df                : simple and easy
      $ cat /etc/mtab     : check mount option from data table by command "mount"
      $ cat /proc/mounts  : mounted device in current session by kernel info.(up-to-dated)
      $ mount             : list all mounted device currently

    - File system
      boot block - super block - inode block - data block
      super block is system metadata and defines the file system type, size, status,
      and information about other metadata structures (metadata of metadata).

    - Mounting and Unmounting
      # CD mount
      $ mount -t iso9660 -o ro /dev/cdrom /mnt
      $ mount -t iso9660 -o loop pdf_collections.iso /mnt
      $ mount -o loop,ro /download/debian.iso /mnt
      $ mount -t iso9660 /dev/hdc /media/cdrom0
      $ umount /mnt

      # For network drive
      # one need to have an extra package "cifs-utils" in debian-sambda case
      $ mount -t smbfs -o username=kang,password=pa$$w0rd //kang/daten /mnt
      $ mount -t cifs -o user=student //10.2.11.16/share /mnt/share
      $ mount -t nfs archangel:/storage /daten

      # Standatd
      $ mount -a     :   mount all device in the /etc/fstab
      $ umount -a    : unmount all device in the /etc/fstab
      $ mount -t ext3 /dev/sdb1 /mnt      : to mount ext3 file system
      $ mount -v -o remount,rw /datein    : remount the mounted filesystem
      $ umount -f /media/usb       : forcely unmount of usb-storage
      $ mount /dev/sda1 /home	     : to mound /home directory
      $ mount -r /dev/sda6 /mydata : to mound read only
      $ mount -B /mydata /mnt      : bind mount points to a new directory
      $ mount -M /mydata /mnt      : access contents from new mount point
      $ mount -n /dev/sda6 /mydata : mount without writing entry into /etc/mtab

    - Automatische Mounten via /etc/fstab (6 fields)
      /etc/fstab is static file system information.
      fstab is checked at every boot and mounts any filesystem listed there
      swap has no mount point that are not assiciated with swap partitions!
      nfs file system can also export
      $ cat /etc/fstab
      > <device> <mount point> <type> <options> 		     <dump> <pass>
      > <device> <mount point> <type> <options> 		     <dump> <fsck>
      > proc 	        /proc 	proc 	defaults 		     0	    0
      > /dev/sda1 	/ 	ext3  	defaults,errors=remount-ro   0      1
      > /dev/sda6 	/home 	ext3 	defaults 		     0	    2
      > /dev/sda5 	none 	swap 	sw 			     0	    0
      > UUID=...  	/	ext4	errors=remount-ro   	     0      1
      > /dev/hda1 	/	ext4	acl,user_xattr      	     1      1
      > /usr/demo 	/demo	nfs	ro,bg      		     0      0

      here are some important mount options
      .auto
      .exec
      .usrquota   : after compile kernel with this option, quotaon has to be done
      .grpquota   : after compile kernel with this option, quotaon has to be done
      .suid
      .user  : attach by one, umount by one
      .users : attach by one, umount by anyone
      .nouser
      .nosuid
      .default=rw,suid,dev,exec,auto,nouser,async

      If you have two swap partitions in /etc/fstab, and they show asym. performance,
      then use an option priority option, let assume sda5 is much faster.
      > /dev/sda5 	none 	swap 	sw,pri=20    0	0
      > /dev/sda6 	none 	swap 	sw,pri=10    0	0

    - Swap
      To see information about swap disk
      $ cat /proc/swaps
      $ swapon -s    : list of swap data

      Make a swap disk
      $ dd if=/dev/zero of=/swapfile bs=1024 count=524288
      $ mkswap /swapfile
      $ swapon /swapfile
      or
      $ gdisk /dev/sdb
      $ mkswap /dev/sdb2
      $ swapon -v /dev/sdb2

      Inactive swap disk
      $ swapoff -f /dev/sdb2

      The startup script is below file to fit during start
      $ cat /etc/fstab
      Then you can use swap right now with command
      $ swapon -a              : active all in the /etc/fstab defined

      monitor the use of swap spaces or via meminfo
      $ free
      $ cat /proc/meminfo

    - UUID
      Universal Unique Identifier ist 126 bits
      Mit blkid lassen sich wichtige Informationen
      wie Gerätedatei, Identifikationsnummer, Name und Dateisystem
      ex) Find UUID to put into /etc/fstab directly
      $ blkid /dev/sdb1 | cut -d'' -f2 | tr -d '"' >> /etc/fstab

      $ ls -l /dev/disk/by-uuid     : for every pratition

      It is possible to creat a new filesystem and still make it have same UUID
      $ tune2fs /dev/sda5 -U 24ddfjiw-a23f-4134-ae45-90e1l1oi23bd

      - sync
      To improve performance of Linux filesystem, operation use buffers, stored in RAM
      To actually flush the data contained in buffers to disk, the sync command is used
      Sync is called automatically when rebooting or halting the system.
      You might use sync for force syncing data to USB before removing it from system.
      This forces to write data from cache to disk

    - fsck
      front-end utility to check and repair a linux filesystem, e2fsck is similar
      fsck.msdos and fsck.vfat sind Links zu dosfsck
      fsck put any unreferenced file or fragments on a ext4 into lost+found

      fsck options
      $ fsck -f : force
      $ fsck -A : alle Dateisysteme in /etc/fstab
      $ fsck -c : defekte Bloecke suchen (possible with "badblocks" but not recommand)
      $ fsck -b : Blocknummer alternativer Superblock
      $ fsck -y : Voreingestelltes "yes" fuer Fragen
      $ fsck -a : automatische, ohne jede Nachfrage
      $ fsck -t : Specifies the type of filesystem to be checked

      $ fsck.ext4 /dev/sdb1  : same as fsck
      $ unmount /dev/sdb1    : before check, better do unmout
      $ fsck -f /dev/sdb1
      $ shutdown -hf now     : no fsck check

      If you are looking for bad sectors in the partition, then do one of these
      $ fsck -c /dev/sdb1
      $ e2fsck -c /dev/sdb1
      $ badbocks /dev/dab1

    - tune2fs
      tune, configure, and modifying a ext2/3/4 filesystem
      -c 	: max-mount-counts (upto 20) between tests
      -C 	: set mount-counts
      -i  	: interval between checks[d|m|w], without -i then a regular test
      -T  	: reserved time at [YYYYMMDD]
      -m  	: reserved blocks percentage
      -r 	: reserved blocks count
      -e  	: error behaviour(continue, remount-ro, panic) by kernel reaction
      -s[0|1] : enable or disable the sparse superblock feature
      -L      : set volume label
      -U 	: set UUID

      $ tune2fs -l	       : superblock anzeigen
      $ tune2fs -c 5 -C 0    : After 5 times mounted, a test is planned, now first
      $ tune2fs -c 5 -C 1    : After 5 times mounted, a test is planned, now second
      $ tune2fs -j /dev/hdb3 : convert from ext2 to ext3

    - resize2fs
      resize ext2, ext3, or ext4 file systems, used to enlarge or shrink.
      it can be used expand the size of the mounted filesytem, too.

    - reiserfs
      tune for reiserfs file system
      $ reiserfstune

    - debugfs
      ext2/3/4 file system debugger, interactive test and restore deleted inodes
      $ umount /dev/sdb1
      $ debugfs /dev/sdb1
      > lsdel
      > dump <inode> /tmp/restored     : dump an inode out to a file
      > undelete <inode> file 	 : undelete inode
      > undelete file                  : undelete file
      you can modify the disk with direct disk writes. when the superblock is damaged
      $ debugfs -b 1024 -s 8193 /dev/hda1	: superblock at block 8193 will be used

      - dumpe2fs
      shows ext2/ext3/ext4 filesystem information
      $ dumpe2fs /dev/hda1 | less
      -b   : block reserved as bad
      -h   : only superblock information

    - btrfs
      BTree file system is a new copy on write filesystem, modern filesystem
      RAID, SSD, Snapshotting, sub-volumes, data compression, inspect while mounted
      NOT use fsck, but use btrfs
      $ btrfs subvolume create /home       : create /home

    - check current partition
      $ cat /proc/paritions
      $ fdisk -l /dev/hda

    - create new partion
      $ fdisk /dev/sdb  : MBR partition (4 Primary, 4T total and limit 2.2TB/partition)
      $ gdisk /dev/sdc  : GPT partion   (128 Primary, GUID partition, nur Primary)
      $ parted          : GUI

      $ fdisk -l           : to view all available partitions
      $ fdisk -s /dev/sda7 : go to special partition
      $ fdisk /dev/sda     : to do partitioning interactively
      ...inside the terminal, type one of following commends
      Command : d Deletes partition
      Command : n Add a new partition.
      Prompt follows as primary or extended;
      partition number; first cylinder; last cylinder.
      Swap partitions can be created with the t option.
      Command : p Displays partition table in the memory (not yet applied)
      Command : q Quit without saving
      Command : w write and exit
      Command : m list of menu
      Command : l list all of available filesystem in linux
      7 HPFS/NTFS (window)
      82 Linux swap
      83 Linux ext2/3/4, btrfs usw.
      85 Linux extended
      8e Linux LVM
      fd Linux RAID

      Possible and famous file systems are
      ReiserFS, BTRFS ,MSDOS, VFAT, NTFA, XFS, CRAMFS, JFS

    - Creating filesystem (formating)
      After partitioning via fdisk, it's needed to add a filesystem to the partition.
      $ mkfs -t ext2 -v /dev/fd0     : type and verbose
      $ mkfs -c /dev/sdb1            : forces a check for bad blocks
      $ mke2fs -j /dev/sda1	       : make ext3 on sdba1, -j means ext3
      $ mkfs -t ext3 -l /dev/sdb1    : make ext3 on sdb1 -l read bad block list
      $ mkfs.ext4 /dev/sdb1	       : make ext4 on sdb1
      $ mkfs -t ext2 -L system -cv /dev/hda3 : make ext2 labeled "sytem" on hda3

    - xfs (eXtended) file system,
      64 bit environment, modern system used in centos, support journaling
      $ apt-get install xfsporgs
      $ apt-get install xfsdump

      $ mkfs.xfs /dev/sdb1 -l logdev=/dev/sda1

      $ xfs_info   /dev/sdb1  : xfs Filesystem information
      $ xfs_growfs            : Expands an xfs filesystem
      $ xfs_admin             : Changes the parameters of an xfs filesystem
      $ xfs_repair /dev/sdb1  : Repairsfilesystem when mount checks are not sufficient
      $ xfs_db  /dev/sdb1     : Examines or debugs an xfs filesystem
      $ xfs_check /dev/sdb1 -l /dev/sda1   : journal is in sda1, use option -l to use it

      $ xfsdump -v trace -f /media/backup/sicherung1 /xfsdisk  : backup
      > Sicherung A
      > Medium 1
      $ xfsrestore -f /media/backup/sicherung1 /xfsdisk	 : restore

    - smart
      Self monitoring analysis and reporting technology to monitor hard drive
      $ apt-get install smartmontools
      $ smartctl -i /dev/sda    : info. about model, serial number, firmware ver.
      $ smartctl -H /dev/sda    : status of health for smart drive
      - a             : all parameter
      - x 	          : all but not drive
      --scan          : search drive

      smartd daemon and configuration
      $ vi /etc/default/smartmontools
      $ vi /etc/smartd.conf
      > enable_smart="/dev/hda"
      > start_smartd=yes
      > smartd_opts="--interval=3600"
      $ /etc/init.d/smartmontools start
      $ grep smartd /var/log/syslog

    - Automounter
      normally "hald" and "dbus" daemon has been used for mount-automatization of drives
      but network resouce "autofs" is better, consists of a kernel component and a daemon
      it has the indirect maps for the mounting of file system
      $ apt-get install autofs
      $ yum install aufofs

      Main configuration data is under
      $ vi /etc/auto.master
      > /misc   /etc/auto.misc
      $ vi /etc/auto.misc
      > cd -fstype=iso9660,ro,nosuid,nodev :/dev/cdrom
      > cd -fstype=auto :/dev/fd0

      If you want to access
      $ mkdir /netz
      $ vi /etc/auto.master
      > /netz   /etc/auto.netz   --timeout=60   : if filesystem do not use for 60 sec.
      then will be unmounted
      $ vi /etc/auto.netz
      > storage  -fs=nfs,defaults kang:/storage : exports data from NFS storage
      $ ln -s /netz/storage /home/kang/storage  : softlink as a convention
      $ /etc/init.d/autofs restart              : apply permanently

      /netz/storage will be created by auto.master and auto.netz

    - mkisofs
      creating an CD image
      $ mkisofs -r -o cd_image   private_collection/
      -r     	: save system info into ISO-data
      -l     	: allow long data name
      -ldots 	: allow name contains dots
      -J     	: Joliet, UTF8-format till 64 charater for windows
      -udf 	: UDF datasystem, e.g. DVD+RW

      test cd_image created above on the /cdrom, then mount
      $ mount -t iso9660 -o ro,loop=/dev/loop0  cd_image  /cdrom

    - cdrecord
      write image to a SCSI CD-Brenner
      $ cdrecord -scanbus                         : search CD
      $ cdrecord --devices                        : add CD device info
      $ cdrecord -v -dummy dev=/dev/scd0 etc.iso  : etc.iso to Brenner,-dummy self test

      From a CD-ROM to a CD-Writer, you shoud directly
      $ cdrecord -v dev=0,6,0 speed=2 -isosize /dev/scd0  : /dev/scd0 is here CD-ROM

      Read CD contents and create a cdimage file (only 1 CD-writer exists)
      $ dd if=/dev/scd0 of=cdimage

      $ wodim                               : alternative tool for Debian and ubuntu
      $ growisofs -Z /dev/dvdrw=distrib.iso : DVD burning simple way! only for read-only
      $ mkudffs			      : DVD+RW case

    - Encrypted file systems
      Encryption algorithms : Twofish, AES, DES
      Devicemapper is used for software RAID and LVM and is a generic linux framework to
      map one block device to another. It is used as the filter between filesystem on a
      virutal blockdevice and the encrypted data to be written to a harddisk.
      A virtual block is created in /dev/mapper. All data to and from it goes to an
      encryption or decryption filter before being mapped to another blockdevice

      Program
      $ apt-get install cryptsetup

      all relevant modules should be loaded at boot time
      # echo aes >> /etc/modules
      # echo dm_mod >> etc/moduls
      # echo dm_crypt >> /etc/moduls
      # modprobe -a aes dm_mod dm_crypt

      Set up an encrypted filesystem
      $ cryptsetup luksFormat /dev/sdb1         : use /dev/sdb1 partition
      > *****************                       : type the password
      $ cryptsetup luksOpen /dev/sdb1 safe      : create device mapper @ /dev/mapper/safe
      $ mkfs.ext2 /dev/mapper/safe		      : format filesystem
      $ mkdir /safe                             : need a mount point
      $ mount /dev/mapper/safe /safe		      : mount it

    - List out information all block devices
      block devices are the hard drive partitions and other storage devices
      $ lsblk
      |NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
      |sda      8:0    0 465.8G  0 disk
      |├─sda1   8:1    0    70G  0 part
      |├─sda2   8:2    0     1K  0 part
      |├─sda5   8:5    0  97.7G  0 part /media/4668484A68483B47
      |├─sda6   8:6    0  97.7G  0 part /
      |├─sda7   8:7    0   1.9G  0 part [SWAP]
      |└─sda8   8:8    0 198.5G  0 part /media/13f35f59-f023-4d98-b06f-9dfaebefd6c1
      |sr0     11:0    1  1024M  0 rom


<br/><a name="storage_devices"></a>

### Advanced Storage Device Administration
    ===============================================================================================
    LPIC 204: Advanced Storage Device Administration
    ===============================================================================================
    - RAID configuration
      Chunk size = 32K or 64K
      RAID-0 striping
      high performance and are able to tolerate lower reliability

      RAID-1 mirroing
      very good data reliability and improves performance for read-intensive applications
      but at a relatively high cost and no gain for writing

      RAID-4 striping
      Because the dedicated parity disk represents an inherent bottleneck, level 4 is seldom
      used without accompanying technologies such as write-back caching

      RAID-5 striping with distributed parity
      By distributing parity across some or all of the member disk drives of an array,
      RAID level 5 eliminates the write bottleneck inherent in level 4.
      For 4x2TB=8TB disk space only 6TB will be available

      Linear mode
      Linear RAID is a simple grouping of drives to create a larger virtual drive.
      RAID 1+0, RAID 0+1

      Software RAID vs Hardware RAID
      1. Software RAID need no special RAID-controller, thus cheaper
      2. CPU must control data storage, therefore a bit slow
      3. Software RAID can have a different size of harddisk
      4. Hardware RAID use full disk, but software RAID use different size

      RAID software all sequence
      $ apt-get install mdadm
      $ lsmod | grep md_mod
      $ fdisk /dev/sdb
      > n, 1, t   	: new partition nubmer 1 and type
      > fd         	: fd is Linux-RAID (OxFD)
      > w		: write

      $ mdadm --create --verbos /dev/md0 --auto=yes --level=5    \
      $ --raid-devices=4 /dev/sdb1 /dev/sdc1 /dev/sdd1 /dev/sde1

      $ mkfs.ext4 /dev/md0
      $ mkdir /raid5
      $ mount -t ext4 /dev/md0 /raid5/
      $ vi /etc/fstab
      > /dev/md0  /raid5  ext4  defaults   0   0

      Check RAID disk
      $ df -h /dev/md0
      $ cat /proc/mdstat
      $ mdadm --detail /dev/md0

      Configuration data
      $ vi /etc/mdadm/mdadm.conf
      > MAILDDR raid-admin@company.com
      > MAILFROM fileserver-07@company.com

      If something wrong for RAID, then do this
      $ mdadm --detail --scan >> /etc/mdadm/mdadm.conf

      RAID array extend with more partition by re-shape, start from below
      read partition table from sde, after that create new at next one
      $ sfdisk -d /dev/sde | sfdisk /dev/sdf      : fancy method
      $ mdadm --add /dev/md0 /dev/sdf1            : now extend md0 with adding sdf1
      $ mdadm --detail /dev/md0                   : test again
      $ mdadm --grow --raid-devices=5 /dev/md0    : extend the device from 4 to 5
      $ cat /proc/mdstat     : check mdadm procedure (simply above process takes time)
      $ resize2fs /dev/md0   : repossible expand without unmount
      $ df -h /dev/md0       : final check of RAID disk in your system

      We can’t remove a disk directly from the array, unless it is failed,

      $ mdadm --fail /dev/md0 /dev/sda1     : so we first have to fail it
      $ mdadm --remove /dev/md0 /dev/sda1   : now we can remove it!

      You can also create few primary partitions on this device
      md0p1, md0p2, md0p3 ... md0p9

    - Adjusting storage device access
      DMA(Direct Memory Access) for IDE including ATAPI and SATA
      UDMA(Ultra-Direct Memory Access), IPO(Parallel Input Output)
      To set IDE/SATA device parameter
      $ hdparm -v /dev/sda     : harddisk standard information
      $ hdparm -i /dev/sda     : manufacture information
      $ hdparm -I /dev/sda     : including Kernel information
      -a  		: set the sector count for read ahead configuration
      -Bnum		: num(1-255) of APM (1=energysave, 255=fullpower)
      -g		: geometry of harddisk
      -r[0|1]         : set read only flag
      -z 		: re-read parition table
      -d1 		: active or deactice DMA modus with process 1
      -X 		: change configuration with MDMA and SDMA

      To access SCSI device mode page
      $ sdparm --enumerate  : summary of SCSI device
      $ sdparm /dev/sda     : list of SCSI in sda

    - Configuration Kernel option for storage device
      /proc filesystem as a dynamic setting or sysctl by util

      $ echo value > /proc/kernel/parameter
      $ echo 1  >  /proc/sys/net/ipv4/ip_forward

      modify kernel paramaters at runtime, listed under /proc/sys/
      $ sysctl -w net.ipv6.conf.all.forwarding=1  : write into /proc/sys/net../forwarding
      $ sysctl -w net.ipv6.conf.all.forwarding=1  : write into /proc/sys/net../forwarding
      $ sysctl kernel.shmmax=212222222            : write into /proc/sys/kernel/shmmax

      $ sysctl -a  : display all values
      -e  : ignore error
      -p  : load /etc/sysctl.conf
      -w  : to change/apply setting

      alternatively, manage files in order to keep configuration of kernel parameter
      permanently after reboot
      /etc/sysctl.conf
      /etc/sysctl.d/

    - Device rule
      Device (upto 4)
      hda - hdd
      hda : primary master
      hdb : primary slave
      hdc : secondar master
      hdd : secondary slave

      Prtition (upto 4)
      hda1-hda4

      IDE  hda1-60 : 60 logical partition in one hard disk
      SCSI sda1-12 : 12 logical pratition in one hard disk

      The disk holds one primary, one extendted, and two logical partitions
      hda1 primary
      hda2 extended
      hda5 logical via haa2
      hda6 logical via hda2

      /dev/sr0    : a device on the scsi controller, SATA CD Writer
      /dev/cdrom  : a symlink to either /dev/sr0 or /dev/hdc

    - ISCSI
      Network transmission of scsi protocol via TCP connection
      SCSI (Small Computer System Interface)
      Server : iSCSI-Target
      Client : iSCSI-Initiator

      @ server
      $ apt-get install iscsitarget
      $ apt-get install iscsitarget-dkms
      $ vi /etc/default/iscsitarget
      > ISCSITARGET_ENABLE=true
      $ mkdir /targets && cd /tragets
      $ dd if=/dev/zero of=speicher-lun0 count=0 obs=1 seek=10G
      $ dd if=/dev/zero of=speicher-lun1 count=0 obs=1 seek=10G
      $ vi /etc/iet/ietd.conf
      > Target iqn.2013-11.com.domain:imagespeicher
      > LUN 0 path=/targets/speicher-lun0,Type=fileio,ScsiID=lun0,ScsiSN=lun0
      > LUN 1 path=/targets/speicher-lun1,Type=fileio,ScsiID=lun1,ScsiSN=lun1
      $ /etc/init.d/iscsitarget start

      @ client
      $ apt-get install open-iscsi                    : debian initiator
      # yum -y install iscsi-initiator-utils.x86_64   : redhat initiator
      $ vi /etc/iscsi/iscsid.conf			            : configuration
      > node.startup = automatic                      : or manual, important!
      $ /etc/init.d/iscsi  start			            : service start
      $ /etc/init.d/iscsid start			            : also daemon start
      $ iscsiadm -m discovery -t st -p 192.168.50.1   : find target system
      $ iscsiadm -m node --login			            : access target system
      $ fdisk -l 					                    : test partition
      $ cat /proc/scsi/scsi 				            : test scsi device
      $ dmesg | tail -n 4 				            : test after rebooting
      $ fdisk /dev/sdb
      $ fdisk /dev/sdc
      $ mkfs.ext4 /dev/sdb1
      $ mkfs.ext4 /dev/sdc1
      $ mkdir /iscsi-LUN1 && mount /dev/sdb1 /iscsi-LUN1
      $ mkdir /iscsi-LUN2 && mount /dev/sdc1 /iscsi-LUN2
      $ df -h | grep /dev/sd

      @ server
      you can also use "tgt" service at target system
      $ yum -y install scsi-target-utils-1.0.24-3.el6_4.x86_64
      $ vi /etc/tgt/targets.conf
      $ service tgtd start

      @ client
      If you want to mount iSCSI target in /etc/fstab
      a special mount option "_netdev" have to use
      > /iscsi   /dev/sdb1    ext4    rw,_netdev     0   0

    - WWID, WWN, LUN
      SCSI, SATA, SAS, Fibre has an unique identification by
      SAS (Serial Attached SCSI) and SATA (Serial ATA)
      WWID : World Wide ID        : find at /dev/disk/by-id/
      WWD  : World Wide Name 	    : correspond to WWID
      LUNs : Logical Unit Nummber : is used to address a SCSI device

      If there are multiple paths from a system to a device, a special kernel-module
      and associated daemons and other software will use the WWID to detect the paths.
      A single “pseudo device” in /dev/mapper/wwid will be created by them.

      LUN persistence with udev+scsi_id
      If your system is NOT using multipath, you can use "udev" to implement it
      udev is a device manager that listens to an interface with the kernel.
      If a new device is plugged in or a device is being detached, udev will receive
      a signal from the kernel and, using a set of rules, will perform actions.
      Before implementing LUN persistence you will need to know WWID by scsi_id
      After you required the WWIDs of your devices, you will need to write a rule
      so udev can assign a persistent name to it.

      $ scsi_id -g -u -s /block/sdc    : find WWID
      > 3200203045823580f
      $ /lib/udev/scsi_id --page=0x80 --whitelisted /dev/sdb
      $ /lib/udev/scsi_id --page=0x80 --whitelisted /dev/sdc
      --whitelistd    : kein Konfigurationdatei aufheben.
      --page          : 0x80(Unit Serial Number) or 0x83(Vital Product Data)

      you can do with configuration data
      $ vi /etc/scsi_id.conf

      LUN persistence can also be made with multipath
      $ multipath -l
      $ vi /etc/multipath.conf
      > multipaths {
      >   multipath {
      >     wwid   48304830248436436246783643832043d
      >     alias  backupdisk
      >   }
      > }
      $ cd /dev/mpath/backupdisk       : you should have a persistence device here!

    - LVM
      Logical Volume Manager is an abstraction of physical disk devices and volumes
      to volume groups (a virtual disk, multiple disks and storage pool)
      /sbin/pv*   : physical volume - a real partitions with administrative data
      /sbin/lv*   : logical volume - extend block introduced inside volume group
      /sbin/vg*   : volume group - mehreren physikalischen Volumen

      Device mapper at LVM /proc/mapper
      The device mapper is a kernel driver that provides a framework for volume management.
      It provides a generic way of creating mapped devices, which may be used as LV
      LVM logical volumes are activated using the device mapper.
      Each logical volume is translated into a mapped device.
      Each segment translates into a line in the mapping table that describes the device.

      0. LVM installation
      $ yum install lvm2
      $ apt-get install lvm2

      1.Partition of device(disk)
      $ fdisk /dev/sdb
      > n,p,1,t
      > 8e
      > w
      $ sfdisk -d /dev/sdb | sfdisk /dev/sdc
      $ sfdisk -d /dev/sdb | sfdisk /dev/sdd
      $ sfdisk -d /dev/sdb | sfdisk /dev/sde
      $ vgscan -v             : sucht angeschlossene Festplatten nach vg und auch pv!

      2.create a physical volume
      $ pvcreate -v /dev/sdb1
      $ pvcreate -v /dev/sdc1
      $ pvcreate -v /dev/sdd1
      $ pvcreate -v /dev/sde1
      $ pvdisplay

      3.configuration of volume group
      $ vgcreate vg_dig /dev/sdb1 /dev/sdc1 /dev/sdd1 /dev/sde1
      $ vgdisplay

      4.configuraion of logical volume
      $ lvcreate -n lv_big_1 -L 20G vg_big
      $ lvdisplay

      5.Format of LV
      $ mkfs.ext4 /dev/vg_big/lv_big_1
      $ mkdir /mnt
      $ mount /dev/vg_big/lv_big_1 /mnt  : mapper start to come into play
      > /dev/mapper/vg_big-lv_big_1 on /mnt type ext4 (rw)
      $ cp /boot/*  /mnt/

      Modifying of LV
      Q) If you add some more disk space within Logiral Volume? "lvextend"
      $ lvextend -L 25G /dev/vg_big/lv_big_1
      $ lvextend -L +5G /dev/vg_big/lv_big_1
      $ resize2fs /dev/vg_big/lv_big_1        : automatic
      $ xfs_growfs /dev/vg_big/lv_big_1       : if xfs filesystem is used

      Verkleinern of LV
      Q) if you want to resize/reduce LV? "lvreduce"
      $ umount /lvtest                         : absolutely neccessary umount
      $ e2fsck -f /dev/vg_big/lv_big_1         : test healthy
      $ resize2fs /dev/vg_big/lv_big_1 1024000 : here is a blcok x 4k = 4GB
      $ lvreduce -L 4G /dev/vg_big/lvbig_1	 : also do verkleinern for LV

      Delete PV
      Q) if you want to delete physics volume from volume group? "vgreduce, vgextend"
      $ vgreduce -a vg_big          : 4 partition to 1
      $ vgextend vg_big /dev/sdc1   : redefine only first partition into volume group

      LVM snapshots
      the snapshot logical volume only saves data blocks from the original logical volume
      that are changed in the original
      $ lvcreate -L 1G -s -n backup /dev/vg_big/lv_big_1
      $ mount /dev/vg_big/backup /backup
      $ tar -pzcf backup.tar.gz  /backup
      $ umount /backup
      $ lvremove /dev/vg_big/backup
      > y

      If file systems are upgraded and have to move data to new one.
      $ pvmove   /dev/hdb2  /dev/sdc1   : move hdb2 data into sdc1

      Activate If you meet machine crash, emergency boot, and activate LVM device
      $ vgchange -ay            : activate all known volume group's device

      Rename of VG and LV
      $ vgrename vg_orignal vg_new
      $ lvrename MyLVM debian fedora

      sdc (full device) can also be used a volume gruop, not only sdc1 or sdc2!

      Q) What command is used to make an exact copy of a logical volue for backup purposes?
      A) lvcreate (no lvsnapshot)


<br/><a name="network_configuration"></a>

### Network Configuration
    ===============================================================================================
    LPIC 205: Network Configuration
    ===============================================================================================
    - Basic network
        check all possilbe network services
        $ cat /et/services
        check https service
        $ grep ^https /etc/services

    - DNS client
      Ordering the way to find DNS server.
      $ cat /etc/hosts.conf
      > order hosts,bind     : first look hosts file, and if there are no information
      > multi on             : in there, then will look bind file

      List of IP address and hostname in localhost
      $ cat /etc/hosts
      > 127.0.0.1    localhost
      > ::1          localhost
      > 192.168.0.56 ipc.homelinux.net    ipcop
      > 192.168.0.1  www.uzuro.com        uzuro
      > 192.168.0.2  ftp.uzuro.com        ftp

      Domain Name Service switch functionality
      system information like host names, user passwords, group, etc.
      $ cat /etc/nsswitch.conf
      > passwd:   files
      > group:    files
      > hosts:    files dns
      > networks: files dns

      DNS client configuration file
      $ cat /etc/resolv.conf
      > domain schule.local       : local domain name
      > search rootman.org  	    : if use telnet, ask to DNS for linux.rootman.org first
      > nameserver 134.75.30.1    : DNS server address 1
      > nameserver 203.241.192.9  : DNS server address 2

    - Routing essential
      configure to get a correct way of IP packet
      $ route      : result of routing table of network
      $ route -n   : check routing table win number only
      $ route -C   : Cache of kernels is used for dynamical routing when DSL-Modem
      $ route add {-host| -net} destination gw gatewayIP dev devicename
      $ route add deault gw     : default means -net 0.0.0.0 netmask 0.0.0.0

      - add 192.168.1.0 network to the router in eth0 device
      - add a routing for network
      - add traffic vi eth1 with 172.16.0.1 IP address
      $ route add -net 192.168.1.0 netmask 255.255.255.0 dev eth0
      - add IP 192.168.1.1 as default gateway
      $ route add default gw 192.168.1.1
      - add IP 192.168.2.1 default gateway with different metric
      $ route add default gw 192.168.2.1 dev eth0 metric 1
      - add a host
      $ route add -host 192.168.10.10 dev eth0
      $ route add -host 192.168.10.10 netmask 255.255.255.255 dev eth0
      $ route add -host 192.168.10.10/32 dev eth0      <--- wrong(?) check(!)
      - host IP 172.16.0.1 has a router via gateway 192.168.0.1 in network
      $ route add -host 172.16.0.1 netmask 255.255.255.0 gw 192.168.0.1 dev eth0

    - Routing advanced
      you have a routing table and only exist standard host configuration
      Now you have to put a standard gateway and 2 internal netz in oder
      to use it as accessing other routers

      $ route
      > Destination  Gateway  Genmask          Flag Met Ref Use Iface
      > 20.14.5.64   *        255.255.255.192  U    0   0   0   eth1
      > 192.168.0.0  *        255.255.255.0    U    0   0   0   eth0

      $ route add default gw   20.14.5.65
      $ route add -net 72.16.0.0 netmask 255.255.0.0 gw 192.168.0.10
      $ route add -net 72.20.0.0 netmask 255.255.0.0 gw 192.168.0.10

      $ route
      > Destination  Gateway      Genmask          Flag Met Ref Use Iface
      > 20.14.5.64   *            255.255.255.192  U    0   0   0   eth1
      > 192.168.0.0  *            255.255.255.0    U    0   0   0   eth0
      > 172.16.0.0   192.168.0.10 255.255.0.0      U    0   0   0   eth0
      > 172.20.0.0   192.168.0.10 255.255.0.0      U    0   0   0   eth0
      > default      20.14.5.65   0.0.0.0          UG   0   0   0   eth0

      $ route
      > Destination  Gateway      Genmask          Flag Met Ref Use Iface
      > 192.168.1.2  -            255.255.255.255  !H   0   0   0   -
      The "!H" means that the host route 192.168.1.2 is rejected by the kernel

      For IPv6(128bit)
      automatische Behandlet einen Host oder ein Netzwerk
      $ route -A inet6 add 2001:6f8:1ce:1::/64 gw 2001:6f8:1ce:0::4       | Net
      $ route -A inet6 add 2001:6f8:1ce:1::47/128 gw 2001:6f8:1ce:0::47   | Host
      $ route -A inet6 | head -4
      > Destination         Next Hop         Flag Met Ref Use  Iface
      > 2001:6f8:1ce::/64   ::               U    256 0   1    eth1
      > 2001:6f8:1ce:1::/64 2001:6f8:1ce::5  UG   1   0   1369 eth1

    - ifconfig
      $ ifconfig -d          : to see interface that are down

      Netzwerkkarten Konfigurieren
      $ ifconfig eth1
      $ ifconfig eth1 up/down
      $ ifconfig eth1 201.14.5.88 netmask 255.255.255.192

      For IPv6
      $ ifconfig eth1 inet6 add 2001:6f8:1d2d::4
      $ ifconfig eth1 inet6 add 2a01:198:5dd:50::200/64

      Introduce a host with 192.168.50.1, you want to work also with this hosts
      in network 192.168.0.50, but currently it is not possble.
      You want to change your IP in order to access different network by configurating
      of network interface
      You should use a host IP 192.168.0.60 via 192.168.0.0/24 network
      $ ifconfig eth1:1 192.168.0.60
      $ ifconfig | grep ^eth1
      $ ifconfig eth1:1 del 192.168.0.60
      $ ifconfig eth1:1 down

      ifconfig is temporal, after new start all configuration from ifconfig will be gone.

      If you want to change some opetions for IP-packet forwarding and ICMP asking,
      there are 2 ways. Nothing to do with ifconfig in this case!
      $ /etc/sysctl.conf  or  sysctl -w
      or
      $ /proc/sys/net/ipv4/ip_forward
      $ /proc/sys/net/ipv4/icmp_ceho_ignore_all

    - ip
      Netzwerkinformation auslesen, Netzwerkeinstellungen Konfiguration verwenden.
      Einstllungen fuer Multicast vornehmen und auf Ethernet operieren
      Zur Ueberpruefung von Netwerkkomponenten immer gleich, unabhaengig davon,
      ob Sie IP-Adressen, routing-eintraege oder den ARP-Cache eines Computers
      diagonostizieren muessen.
      $ ip route show          : show routing table in CIDR notation
      $ ip neighbour show      : MAC-Adressn  (apr -a)
      $ ip addr show dev eth1  : show info. about network interface
      networkmask in CIDR, not dotte format.
      $ ip tunnel show         : capsule of Ipv6 packet into IPv4
      $ ip monitor             : live monitoring for connection of MAC and IP

      $ ip link set eth0 up    				         : activate network interface
      $ ip addr add 192.168.50.5/24 dev eth0    		 : set IP to netwrok inerface
      $ ip addr del 192.168.50.5/24 dev eth0     		 : delele IP
      $ ip route add 10.10.20.0/24 via 192.168.50.100 dev eth0 : add static router
      $ ip route add default via 192.168.50.100  		 : set default gateway

      ip supports IPv6, but not for arp!
      $ ip route add 2a01:198:5dd::47/64 dev eth0                     : for IPv6
      $ ip route add 2001:6f8:1cfe:4f44::/64 via 2001:6f8:1cfe::4     : for Ipv6

    - netstat
      list of network connection via tcp, udp, socket
      p --program   (PID & program)
      a --all       (all socket port)
      i --interface (show network interface)
      n --numeric   (no DNS, fast)
      r --routing   (show routing table)
      l --listen    (show only LISTEN status)
      c --eine      (fortlaufend Aktualisierung, stop ctrl+c)
      $ netstat -na                                          all open port
      $ netstat -an | grep ":80 "   			       port80
      $ netstat -at                                          tcp connected port
      $ netstat -na | grep LISTEN                            LISTEN port
      $ netstat -na | grep ESTABLISHED | wc -l               connected user
      $ netstat -na | grep *.80 | grep ESTABLISHED | wc -l   connected web user
      $ netstat -na | grep .1521 | grep ESTABLISHED | wc -l  connected DB user
      $ netstat -tun  				       tcp,udp,number
      $ netstat -l 					       route table
      $ netstat -pa		 			       open programm all

    - arp
      address resolution protocol - using IP one can access MAC add.
      ARP-cache between IP address and MAC address in network segment
      /sbin/arp or /usr/sbin/arp
      $ arp                     : show ARP-Cache
      $ arp -d 192.168.0.104    : delete ARP-Cache
      ARP does not support IPv6

    - iwconfig
      wifi configuration
      $ iwconfig wlan0                    : show WLAN adapter/interface
      $ iwconfig wland scan		        : scanning connect to public wi-fi
      $ iwconfig wlan0 txpower 30mW       : signal power of wlan card
      $ iwconfig wlan0 txpower on/off     : turn on and off
      $ iwconfig wlan0 essid "My Network" : a group of cells connected via repeaters
      $ iwconfig wlan0 mode Ad-Hoc        : set the operating mode of the device,
      which depends on the network topology

      Ad-Hoc    : network composed of only one cell and without Access Point
      Managed   : node connects to a network composed of many Access Points, with roaming
      Master    : node is the synchronisation master or acts as an Access Point
      Repeater  : node forwards packets between other wireless nodes
      Secondary : node acts as a backup master/repeater
      Monitor   : node is not associated with any cell and passively monitor all packets
      on the frequency or Auto

    - iwlist
      show an information about WLAN cards
      $ iwlist wlan0 frequency, scanning, rate, keys, power, txpower, retry, event, auth,
      wpakeys, genie, modulation

    - iw
      show wireless devices and their configuration
      $ iw dev wlan0

    - ping
      Ping packet TTL 64 Bytes(Windows 32 Bytes), zur 8 Router auf dem Weg zur Zieladresse
      $ ping -c 1 141.1.1.1

      For IPv6,
      $ ping6 2001:6f8:1d2d::10
      $ ping6 -c 1 2a00:1450:4017:801::100f
      $ ping6 ::1

      When the ICMP packets are send to a broadcast address and multiple host respond,
      then you will get DUP! output string from ping!

    - tcpdump
      show network flow into the screen using dump
      $ tcpdump -i eth0     : which interface
      $ tcpdump -n 	      : nummeric for fast search
      $ tcpdump -w          : write
      $ tcpdump tcp prot 80 -i eth1
      $ ctrl + c            : interrupt

      $ tcpdump host 1.2.3.4 : look for traffic based on IP address
      $ tcpdump src 2.3.4.5  : find traffic from only a source
      $ tcpdump dst 3.4.5.6  : find traffic from only a destination

      > source            >  destination    : host 10.0.3.246 send packet
      > 10.0.3.246.33323  >  10.0.3.1.22    : to client host IP 10.0.3.1

      - Port reminder!
      21  : FTP    : File Transfer Protocol
      22  : SSH    : Secure Shell
      23  : Telnet : Telnet remote login service, a plan text protocol
      25  : SMTP   : Simple Mail Transfer Protocol
      53  : DNS    : Domain Name System service
      80  : HTTP   : Hypertext Transfer Protocol
      110 : POP3   : Post Office Protocol
      119 : NNTP   : Network News Transfer Protocol
      123 : NTP    : Network Time Protocol
      143 : IMAP   : Internet Message Access Protocol
      161 : SNMP   : Simple Network Management Protocol
      194 : IRC    : Internet Relay Chat
      443 : HTTPs  : secure HTTPs
      993 : imaps  : secure IMAPs
      995 : pop3s  : secure POP3
      3128: Proxy server port  (Squid)
      7100: X-Font server port
      8080: extended HTTP port, personally hosted web server

      139  Samba
      445  Samba
      953  RNDC
      5900 VNC

    - losf
      Prozesse anzeigen, die auf ein Geraet oder einen Netzwerk-Socket zugreifen
      $ lsof /tmp			   : information about process accessd tmp
      $ lsof | wc -l                     : count of all list of files
      $ lsof -i :80                      : list of internet address matches, e.g. IP socket
      $ lsof /dev/sdc1                   : test of usb disk /media/usb-disk possible
      $ lsof -c abc		 	           : list of file for command start with "abc"
      $ lsof -P                          : verhindert die Aufloesung der Portnummber
      $ lsof -n                          : verhindert die Reverse-Aufloesung der Adressen
      $ kill -s 9 $(lsof -t /media/disk) : kill all processes related with disk.

    - nc(netcat)
      Standardein oder ausgabe über Netzwerkverbindungen zu transportieren
      network conneciton tool for "backend", script interaction with TPC or UDP service
      $ nc -z daum.net 80
      $ while true; do date; nc -w 5 -z www.google.com 80; done
      -z      : zero-I/O mode used for port scanning
      -w      : timeout of 5 sec. for connects and final net reads

      To test of POP3 without E-mail client porgramm
      $ nc pop3.mail.de 110
      > retr 1    : read 1st e-mail
      > dele 1    : delete 1st e-mail

      Need a correlation between two computers without firewall for test
      $ server1> nc -l 4444           : server 1 waits a data from port 4444
      $ server2> nc server1.com 4444  : server 2 waits a signal from user kang of server 1
      Now one of both computer give a stdin and stdout will be shown in other computer

      Typical application with dd backup remotely
      $ server1> nc -l 4444 | dd of=remodedisk.img
      $ server2> dd if=/dev/sdb1 | nc kang 4444

    - nmap
      port scanning and defending networks
      $ nmap -sP 192.168.7.0/24           : general Ping Sweeping
      $ nmap -sT 192.168.7.12             : general TCP-Port Scanning
      $ nmap -O 172.16.0.1 -o osscan.txt  : OS scan and output
      -P   : Ping mode
      -s   : scan mode
      -p   : port range (-p 20-30,6000)
      -T   : TCP mode
      -O   : Operating System mode (need root accout)
      -n   : no DNS resolution mode
      -F   : Fast mode, scan fewer ports only within /etc/services
      -A   : enable OS detection, script scanning, and traceroute
      -sL  : List Scan, simply list targets to scan
      -sn  : Ping Scan, disable port scan
      -sS  : stellth scan mode
      -sU  : UDP Scan (need root account)
      -Pn  : Treat all hosts as online, skip host discovery
      -PT  : TCP Ping (-PT21) call "penetration test"
      -PR  : ARP Ping
      -PU  : UDP discovery

      Option -P0  : IP protocol ping to new host discovery
      $ nmap -P0 test.test.net    : with domain name
      $ nmap -P0 192.168.0.0/24   : not only Host, but also network
      Welche Ports lauscht aktuell in mein System?
      $ nmap localhost
      $ nmap localhost -p 80

    - Wireshark
      network protocol and packet contents analyzer with GUI

    - Debian network interface configuration
      $ vi /etc/network/interfaces
      |# loopbackdevice
      |auto lo
      |iface lo inet loopback
      |# dhcp server
      |iface eth2 inet dhcp
      |auto eth2
      |# wi-fi
      |auto wlan0
      |iface wlan0 inet dhcp

    - CentOS network card configuration
      $ vi /etc/sysconfig/network
      NETWORKING=yes             # network functionality on or off
      HOSTNAME=ns1.rootman.org   # set hostname
      GATEWAYDEV=eth0            # gateway device most case eth0
      GATEWAY=203.241.205.254    # set gateway address
      $ vi /etc/sysconfig/network-scripts/ifcfg-eth0
      DEVICE=eth0         	          # device
      BOOTPROTO=static    	          # static IP
      HWADDR=XX:XX:XX:XX:XX:XX          # Mac address
      NM_CONTROLLED=no     	          # GUI mode
      ONBOOT=yes           	          # autostart during boot
      TYPE=Ethernet        	          # Ethernet
      UUID=XXXXXXX-XXX-XXX-XXX-XXXXXXX  # UUID automatic
      BROADCAST=192.168.0.255    	      # broadcase
      IPADDR=192.168.0.5 		          # IP address
      NETMASK=255.255.255.0   	      # subnetmask
      NETWORK=192.168.0.0     	      # network
      ETHTOOL_OPTS=wol g                # Wake On Lan, need Ethtool
      USERCTL=no    		              # allow user control for eth0
      IPV6INIT=no   		              # use IPV6

    - hostname
      $ hostname
      $ dnsdomainname
      $ hostname paketpolizist

    - traceroute
      ermittelt über welche Router und Internet-Knoten IP-Datenpakete bis zum abgefragten
      Rechner gelangen
      trace of IP packet till final destination during the track passes through
      TCP/IP is a connection-less service.
      If you use wrong IP, IP packet will create a infinite loop in the network.
      TTL make a subtracktion of packet number when packet passes through a router
      traceroute use reverse property of TCP/IP, so TTL value is added when
      the test signals passed through router.
      $ traceroute   www.xxxx.com
      $ traceroute6  www.xxxx.com
      $ traceroute -I www.lpi.org
      $ traceroute -n 217.147.216.241
      $ tracepath -n 217.18.182.170	       : ohne Root-Rechte ueberpruefen
      $ tracepath -n 2a00:1450:4017:800::e   : n numerische um schnell zu machen

    - TCP wapper
      Es bringt also nichts, ueber die hosts.allow einem einzelnem Host explizit Zugriff
      auf einene Dienst zu gewaehren, ohne es den anderen Hosts explizit zu verbieten.
      TCP-Wrapper liegt unter /etc/sbin/tcpd

      $ vi /etc/host.deny
      > ALL : ALL
      $ vi /etc/host.allow
      > ALL : LOCAL
      > ALL : [3ffe:505:2:1::]/64
      > ALL : *.example.com
      > sshd : 192.168.1.
      > sshd : 192.168.1.0/255.255.255.0

      If both files exist, hosts.deny doesn't work
      hosts.allow take precedence, the client is granted access to the server
      If both files doesn't exist, not allow to any users

    - DNS forward and reverse Lookup
      host - simple utility to get host information from name server
      $ host www.google.com
      $ host -t mx gfn.de
      $ host 172.217.21.228

      nslookup - tool to ask host information from name server
      $ nslookup redhat.com 8.8.8.8  : [looking host] [ask name server]
      reserve dns lookup, if do not indicate nameserver,
      will ask to name server defined in /etc/resolv.conf
      $ nslookup 111.222.333.444
      $ nslookup 				: interactive mode
      > server 198.151.35.5
      > set type=mx
      > yahoo.com

      dig - after finish DNS configuration, as a normal user, one can test DNS
      whether all configuration is correctly set or now
      $ dig yahoo.com                     : [domain name]
      $ dig @198.151.35.5 -t MX yahoo.com : [DNS server] [type] [domain name]
      $ dig -x 193.99.144.85  	        : -x option refer to reverse lookup
      $ dig @8.8.8.8 gfn.de mx            : SOA,NS instead of MX
      $ dig +trace +additional www.prolinux.de   : complete information

      whois - a program to find domain holder
      $ whois 218.145.31.100
      $ whois krnic.net

      getent is a tool for carry out the database of administrator
      (aliases, ethers, group, netgroup, networks, protocols, rpc, services, shadow)
      $ getent hosts
      $ getent passwd > password_data

    - Questionary
      Q) tools to check for open ports on a local computer?
      A) nmap, netstat, lsof

      Q) dhclient can bring up an interface on eth1? How?
      A) dhclient eth1

      Q) how to resolve DNS in time order?
      A) dig, time dig, time host, time nslookup

      Q) Program wii run through the port 5112, if you want to check whether this port
      is active and has been blocked by firewall, how can you check?
      (nmap, netstat, netcat, ifconf, wireshark ) -> answer is netcat


<br/><a name="system_maintenance"></a>

### System Maintenance
    ===============================================================================================
    LPIC 206: System Maintenance
    ===============================================================================================
    - tar
      option -f(file) must be at the end, and tar works in /dev/rmt0
      option -w is interactive mode
      $ tar -xvf data.tar
      $ tar -xvzf data.tar.gz
      $ tar xvzf data.tar.gz
      $ tar -xvjf data.tar.bz2

      $ tar -cvjf backup.tar.bz2 /etc/*
      $ tar -tvjf backup.tar.bz2 | less

      $ tar cvf /dev/st0 /home
      $ tar -xvf /dev/st0          : extracting a tar archive from a scsi tape drive

    - gzip
      $ gzip lpi101.doc -c > lop101.doc.gz
      $ gzip lpi101.doc
      $ gzip -l lpi101.doc.gz
      $ gzip -9 (--best,slow)      	: best Komprimierte Datei
      $ gzip A.doc                    : A.doc wird geloescht
      $ gzip -c A.doc > A.doc.gz     	: -c A.doc keep and create gz
      $ gzip -d A.doc.gz   		    : decompress
      $ gunzip A.doc.gz               : decompress
      $ zcat A.doc.gz         	    : anzeigen list (gzip -c -d)
      $ bzip2 B.doc                   : B.doc wird geloescht
      $ bzip2 -c B.doc > B.doc.bz2    : -c B.doc keep and create bz2
      $ bzip2 -d B.doc.bz2		    : decompress
      $ bunzip2 B.doc.bz2             : decompress
      $ bzcat A.doc.bz2        	    : anzeigen list (bzip2 -c -d)
      $ xz -z  			            : compress
      $ xz -d A.xz 			        : decompress

    - program
      $ ./configure
      $ make
      $ make install

      $ apt-get install linux-headers-$(unmae -r)

    - Was muss gesichert werden?
      /bin    : backup
      /boot	: backup
      /etc	: backup, but no /etc/mtab
      /home	: backup
      /lib	: backup
      /opt	: backup
      /root	: backup
      /sbin	: backup
      /usr	: backup
      /var	: backup weil mailservern
      /sys    : no
      /dev	: no
      /mnt	: no
      /proc	: no
      /tmp	: no

      Windows (NFS,FAT,VFAT) has an Archiv-Flag, but Linux is not
      backup style : Normal, difference, increment, copy backup

      /dev/st0   : SCSI Tape
      /dev/nst0  : SCSI Tape, non rewinding tape
      /dev/ft0   : Floppy Tape
      /dev/nft0  : Floppy Tape, non rewinding tape
      /dev/fd0   : Disket Tape

      cd /opt; tar xvf /dev/nst0;  2 times, what will happen?
      the contents will have additional content added from the next file
      cd /opt; tar xvf /dev/st0;   2 times, what will happen?
      the entire contents will be replaced with the contents of the next file on the tape

    - dd
      ibs = in block size
      cbs = stream block size
      $ dd                        : this is a interactive mode
      $ dd if=/dev/sda of=/dev/sdb
      $ dd if=/dev/hda of=mbr.backup ibs=512 count=1
      $ dd if=/home of=/dev/st0 cvs =16b

    - dump
      10 backup level, default 9
      5th field in /etc/fstab is dump and will be set to 1
      /etc/dumpdates is a history of dump state

      $ dump -0 -B 800000 -f /dev/st0 /home
      $ dump -1a -f /dev/st0 home/
      -1a     : week with level 1
      -f	    : file or device (backup media)
      -0~9	: level 0~9  0=entirely, 9=partly
      -B	    : kByte
      -u  	: write info. of backup into /etc/dumpdates

    - restore
      $ restore -i -f /dev/st0
      $ restore -r -f /dev/st0
      $ restore -C -f /dev/st0
      -i	: interactive mode (ls,delete,add,extract)
      -f	: file or device
      -r	: restore with recursive mode
      -c 	: compare the backup data with original data

    - mt
      magnetic tape
      $ mt -f /dev/st0 rewind : backup rewind into tape
      $ mt -f /dev/st0 eom    : can add eom into rewind

    - cpio
      $ find /snow | cpi0 -o > snow.cpio : copy all files under /snow
      $ cpio -ov list.txt /dev/sdb1      : copy all listed in list.txt
      $ cpio -itv < /dev/st0             : list the contents of SCSI tape

    - Amanda, Bacula
      Backup solution

    - rsync
      $ rsync -avz /storage/buch/ root@192.168.50.12:/backup
      $ rsync -avx 192.168.50.12:/backup /restore
      $ rsync -av /storage/buch/ /media/usb-hd
      $ rsync -avz -e ssh /storage/buch/ 192.168.50.12:/backup
      -a : active archive mode
      -z : compress data
      -x : decompress data
      -r : recursive
      -v : verbose
      -u : update
      -p : keep original permission
      -6 : IPv6
      -e : remote-shell

      normally rsync in inetd or xinetd super-daemon.
      but you can also run direcly via
      $ rsync --daemon

      You can defualt backup location
      $ vi /etc/rsyncd.conf
      > [backup]
      > path = /backup
      > comment = backup

      $ rsync -avz /storage/buch/ root@192.168.50.12:/backup --password-file=/etc/pw
      However ssh is much better security application

    - short message to user
      $ /etc/issue
      > Ubuntu 14.04.1 LTS /n /l     : n=hostname, l=tty
      \n - Hostname
      \o - Domainname
      \b - baudrate des Terminals
      \l - line name (tty)
      \s - systemname
      \m - machine architecture
      \r - release number of kernels
      \v - OS version
      \d - data
      \t - current time
      \u - number of logged users
      \U - number of logged users

      $ /etc/issue.net     : only logged users via network connection, ssh

      $ /etc/motd          : motto of the day, do not confuse with fortune

      $ echo "Heute User meeting 14:00"  | wall
      $ init 0                  : not send a message to logged user
      $ shutdown -k 10          : will send a message and not be able to login
      $ shutdown -f -r now      : reboot send a message


<br/><a name="dns"></a>

### Domain Name Server
    ===============================================================================================
    LPIC 207: Domain Name Server
    ===============================================================================================
    - DNS types
      Master (Primary) DNS Server
      Slave (Secondary) DNS Server
      Caching (hint) DNS Server
      Forwarding (Proxy, Client, Remote) DNS Server
      Stealth (DMZ or Split) DNS Server
      Authoritative Only DNS Server

      Recursive name server = cache name server = resolving name server

    - BIND @ debian
      $ apt-get install bind9  : @ Debian
      /etc/bind/named.conf     : top-level configuration   @ Debian
      /etc/bind/db.root        : Information root-server and IP in Bind
      /etc/bind/zones.rfc1918  : definition of zone data

      Zone identifies a zone for which the server is authoritative and points the server
      at the relevant zonf file
      Zone data file syntax
      $ vi /etc/bind/named.conf
      | options {                          // options
      |    directory "/var/cache/bind/";   // location of zone data file in BIND server
      |    forwarders { 10.0.0.1; };       // should forward DNS request to itself!
      |    notify no;                      // notification no
      | };
      | logging {                          // logging
      |    channel default_debug {         // definition
      |    file "data/named.run";          // directory + data/named.run
      |    severity warning;               // log level
      |    category Name-servers { null; };// reduce protocol error
      |    };
      | };
      | zone "linux.net" in {              // forward lookup zone
      |    type master;
      |    file "lunx.net.zone";
      | };
      | zone "50.168.192.in-addr.arpa" in {// reverse lookup zone
      |    type master;			 // in-addr.arpa is special domain form
      |    file "192.168.50.zone";         // for reverse lookup zone case
      | };
      | zone "127.in-addr.arpa" in {       // reverse lookup zone
      |    type master;
      |    file "db.127";
      | };
      | zone "localhost" in {              // all current origins in the zone
      |    type master;                    // will be localhost
      |    file "db.local";
      | };
      | zone "." in {                      // When BIND-server is a Root-Server
      |    type hint;                      // type of "hint" is used
      |    file "root.hint";               // or "named.ca" @ fedora
      | };

      Resource record formats in zone files (typically under /var/)
      $ vi /var/cache/bind/db.local
      | $TTL   2D
      | @   IN   SOA   dns01.linux.net.   root.localhost.  (
      |		20161010   ; Serial
      |		86400      ; Refresh (1 day)
      |		7200 	   ; Retry   (2 hours)
      | 		604800 	   ; Expire  (1 Week)
      |	        172800 )   ; Minimum (2 days)
      | @           IN   NS      debian-kang.kang.local.
      | 	          IN   A       192.168.0.1
      | debian-kang IN   A       192.168.0.1
      |             NS           dns01.linux.net.
      |             IN   MX  10  smtp01.linux.net. : work only with FQDN
      |             IN   MX  20  smtp02.linux.net. : if first down, then use second
      | ipcop       A            192.18.0.56
      | fedora      AAAA         12a01:198:5dd:dc89:f072:2b73:32a5
      | www         CNAME        ubuntu-server.linux.net.
      | 56          PTR          ipcop.linux.net.

      explanaiton --------------------------------------------------------------
      TTL      : default time to live for zone
      @        : current origin, which expands to linux.net
      1st add. : the dns name of master DNS machine
      2nd add. : email address to mail in case of trouble
      Minimum  : the negative caching TTL value, that a DNS server remembers
                if could not resolve a specific domain.
      IN       : Internet data class from (RFC4343)
      SOA      : Start of a Zone of Authority
      A        : Address record for IPv4
      AAAA     : Address record for IPv6
      PTR      : pointer record in Reverse-lookup-Zone
      NS       : Name Server record
      CNAME    : Canonical Name record, ein Alias von Host-A Eintrag zeigt
      MX       : Mail Exchanger record

      $ /etc/init.d/bind9 start  : start
      $ service bind9 start      : start  => one of this make run
      $ systemctl start bind9    : start

      Firewall open via iptables configuration
      $ vi /etc/iptables/rules         :  @ Debian

    - BIND @ fedora specific
      $ yum install bind9
      /etc/named.conf           : top-level configuration
      /var/named/named.ca       : Information root-name server and IP in Bind
      /var/named/named.rfc1912  : Information root-name server and IP in Bind

      $ vi /etc/named.conf
      | options {
      |   allow-query { localhost; }; //here can change localhost; to any;
      |   listen-on port 53 { 192.168.50.12; 127.0.0.1; };
      |	  listen-on-v6 port 53 { ::1; };
      |	  max-cache-size 256M;
      |	  directory "/var/named";   // zone data of BIND server
      |	  statistics-file "/var/named/data/named_stats.txt";
      |	  recursion yes;
      |	  dnssec-enable yes;
      |	  dnssec-validation yes;
      | };

      Caution in general!
      | listen-on port 53 { 127.0.0.1; };   <-- correct form
      | listen-on port 53 {127.0.0.1;};     <-- wrong because of no spacce

      compare listen-on with allow-query
      listen-on   : welche Adressen und Port ein BIND-server abhoeren soll
      allow-query : von welchen Netwerken aus auf den DNS zugegrifffen werden darf

      Firewall open via iptables configuration or in command line
      $ vi /etc/sysconf/iptables         :  @ CentOS, Fedora
      | -A INPUT -p udp --dport 53 -j ACCEPT
      | -A INPUT -p tcp --dport 53 -j ACCEPT
      $ iptables -t filter -A INPUT -p udp --dport 53 -j ACCEPT
      $ iptables -t filter -A INPUT -p tcp --dport 53 -j ACCEPT

      $ /etc/init.d/named start  : start
      $ service named start      : start  => one of this make run
      $ systemctl start named    : start

    - Using secondary zone, slave DNS
      @ master server
      |zone "anderen.net" in {
      |    type master;
      |    file "anderen.net.zone";
      |    allow-transfer { 192.168.50.14; 192.168.50.15; };
      |};
      @ slave server
      |zone "anderen.net" in {
      |    type slave;      	             // slave
      |    file "anderen.net.zone";        //
      |    master { 192.168.50.1; };       // access control on master
      |};

      - For forwording DNS
      Advantage of forwarding is no data transfer, therefore fast
      |zone "anderen.net" in {             //
      |    type forward; 	                 //
      |    forwarders { 192.168.222.1; };  // IP of DNS server
      |};

    - Creating subdomain by delegating
      subdomain can be created by delegating a DNS zone as a independent zone
      and delegating that domain from the parent domain.

      Berlin linux1 192.168.50.77  linux1.berlin.domain.net
      Bonn   linux2 192.168.70.44  linux2.bonn.domain.net

      To set Zone data in both server, you should put
      | @   IN   SOA   dns01.domain.net.   root.domain.net.  (
      |		20161010   ; Serial
      |		86400      ; Refresh (1 day)
      |		7200 	   ; Retry   (2 hours)
      | 		604800 	   ; Expire  (1 Week)
      |	    172800 )   ; Minimum (2 days)
      | @              NS      dns01.domain.net.
      | dns01	         A       192.168.50.1
      | linux1.berlin  A       192.168.50.77
      | linux2.bonn    A       192.168.70.44

      liegt die Zustaendigkeit fuer Subdomaenen automatisch in seinem Verwaltungsbereich.
      Es gibt Nachteil, wenn man Computer aus subdomaenen in Stammdomaenen mitverwaltet
      1. Administraion of DNS is not possible.
      2. dynamische Update through Client-computer have to be done in the primary zone.
      3. unuebersichtlich zone data file

      Loesung ist dafuer
      einsetzen jeweileigen Standortdomaenen selbst primaere Zonen zu hosten.
      Berlin dns-bln01.berlin.domain.net   192.168.50.5   berlin.domain.net
      B0nn   dns-bo01.bonn.domain.net      192.168.70.1   bonn.domain.net

      Die Stammzone domain.net muesste dann so werden
      | @   IN   SOA   dns01.domain.net.   root.domain.net.  (
      |		20161010   ; Serial
      |		86400      ; Refresh (1 day)
      |		7200 	   ; Retry   (2 hours)
      | 		604800 	   ; Expire  (1 Week)
      |	    172800 )   ; Minimum (2 days)
      | @                  NS      dns01.domain.net.
      | dns01	             A       192.168.50.1
      | berlin             NS      dns-bln01.berlin.domain.net.
      | dns-bln01.berlin   A       192.168.50.5
      | bonn               NS      dns-bo01.bonn.domain.net.
      | dns-bo01.bonn      A       192.168.70.1

    - Creating sub-sub-domain
      Master nameserver       : my.com
      192.168.0.3
      y.zone + my.rev
      Sub nameserver          : linux.my.com
      including Web + Email	  192.168.0.4
      linux.my.zone + linux.my.rev
      allow sub-sub-domain

      @ Master name server
      $ vi /var/named/my.zone
      | linux      IN   NS   ns.linux
      | ns.linux   IN   A    192.168.0.4
      $ vi /var/named/my.rev
      | linux      IN   NS   ns.linux.my.com.
      | 4          IN   NS   ns.linux.my.com.

      @ Sub name server
      $ vi /etc/named.conf
      |zone "linux.my.com" in {
      |    type master;
      |    file "linux.my.zone";
      |    allow-transfer { 192.168.50.14; 192.168.50.15; };
      |};
      |zone "linux.0.168.192.in-addr.arpa"{
      |    type master;
      |    file "linux.my.rev";
      |    allow-transfer { 192.168.50.14; 192.168.50.15; };
      |};
      $ vi /etc/named/linux.my.zone
      | @   IN   SOA   ns.linux.my.com.   root.linux.my.com.  (
      |		20161010   ; Serial
      |		86400      ; Refresh (1 day)
      |		7200 	   ; Retry   (2 hours)
      | 		604800 	   ; Expire  (1 Week)
      |	    172800 )   ; Minimum (2 days)
      |                    IN   NS      ns.linux.my.com.
      | linux.my.com.      IN   A       192.168.0.4
      | 	                 IN   MX  10  linux.my.com.
      | www.linux.my.com.  IN   A       192.168.0.4
      $ vi /etc/named/linux.my.rev
      | @   IN   SOA   ns.linux.my.com.   root.linux.my.com.  (
      |		- same as zone file -
      |                    IN   NS      ns.linux.my.com.
      | 4                  IN   PTR     linux.my.com.
      | 4                  IN   PTR     ns.linux.my.com.
      | 4                  IN   PTR     www.linux.my.com.
      $ vi /etc/httpd/conf/httpd.conf           <--- Apache configuration

    - BIND tip
      $ chkconfig --add named     : add automatic start bind/named in init process
      $ chkconfig named on        : turn on bind/named

      $ /etc/init.d/named restart : restart of bind/named
      $ kill -HUP `pidof named`   : restart of bind/named
      $ rndc reload               : restart of bind/named

      Need a write permission in name server
      $ chown named:named /var/naemd/ -R  : security @ redhat
      $ chown bind:bind   /etc/bind/  -R  : security @ debian

      verhindert SELinux Schreibzugriff to Zone
      $ setsebool -P named_write_master_zones=1  : @ redhat
      $ /etc/init.d/apparmor  stop               : @ debian

    - rndc
      name server control utility for BIND
      /usr/sbin/rndc
      $ rndc  reload     : read zonedata and BIND configuration file
      halt	   : writing and zonetransfer will be broken immediately
      stop       : writing and zonetransfer will be continue and finished
      freeze	   : stop writing into zone data
      thaw       : continue writing
      flush      : cache will be cleared

    - Alternative DNS server
      dnsmasq  : combine DNS+DHCP server, dnsmasq apply no Zonedata, use /etc/hosts
      djbdns   : developed by Daniel J. Bernstein

      PowerDNS : pdns-server for normal DNS
      pdns-recursor for cache-DNS

      Co-work @ Backend : LADP, mySQL, Oracle, PostgreSQL, SQLite, OpenDBX, IBM DB2
      PowerDNS server can manage through webfrontend with poweradmin.

    - DNS test
      $ host www.computer.de
      $ host 85.88.3.146
      $ host -t MX xxx.com

      $ dig @198.151.35.5 -t MX xxx.com

      Or interactively
      $ nslookup
      > server 198.151.35.5
      > set type=mx
      > xxx.com

    - DNS restriction & limiting access
      There are several ways to limit access to nameserver data.
      Here is typical example for limiting access
      $ vi /etc/bind/named.conf
      | options {
      |   // allow access for clinet queries
      |   allow-query   { any; };
      |   allow-query   { localhost; 192.168.50.0/24; };
      |   // update from a host or network via DHCP
      |   allow-update  { 192.168.50.1; };
      |   allow-update  { 192.168.50.0/24; };
      |   // zone tranfer limitation
      |   allow-transfer { 192.168.50.14; 192.168.50.15; };
      |   // recursion limitation
      |   allow-recursion  { 127.0.0.1; 192.168.50.0/24; };
      |   // one can use a ACL(Access Contrl list)
      |   acl "dns-server"  { 192.167.50.14; 192.167.50.15; };
      |   allow-transfer { dns-server };
      |   // usage of ACL list
      |   acl "dns-server"  { 192.167.50.14; 192.167.50.15; };
      |   allow-query { localhost; dns-server; };
      |   allow-update { dns-server; };
      |   allow-recursion { 127.0.0.1; dns-server; };
      |   // server will NOT respond to, or answer queries for
      |   blackhole { list };
      |   // deny access from specific addresses
      |   deny-answer-addresses { address_list }
      |       [ except-from { name_list } ];
      | }

      allow-query     : welech Clients den Server zur Namensaufloesung verwenden duerfen
      allow-update    : von welech Clients ein Server dynamische Updates entgegen nimmt
      allow-recursion : welech Clients eine rekursive Namenaufloesung durchfuehren soll
      allow-transfer  : welech Computer sekundaere Zonen von diesem Server beziehen duerfen
      it can be a master or slave, and allow transfer to other comfuters

      On the master nameserver you should use the allow-transfer statement 'ACL'
      to limit zone transfers to a list of known slave servers.
      @ On master nameserver
      | acl "my_slave_servers" {
      |     224.123.240.3; // cat.example.org
      | };
      | zone "example.org" IN {
      |     type master;
      |     allow-transfer { my_slave_servers; };
      | };
      Now only the slaves can request a zone transfer from this master nameserver.
      On a slave nameserver you should never allow any zone transfers.
      @ On slave nameserver
      | zone "example.org" IN {
      |     type slave;
      |     allow-transfer { none; };
      | };

      You may want to limit queries with 224.123.240. to a range of hosts
      | acl "myhosts" {
      |     224.123.240.0/24;
      | };
      | zone "example.org" IN {
      |     allow-queries { myhosts; };
      | };

      Both dig and host can initiate a zone transfer. To attempt a zone transfer for
      zonetransfer.me from the DNS server ns12.zoneedit.com
      $ host -l  zonetransfer.me   ns16.zoneedit.com
      $ dig  axfr  @ns12.zoneedit.com   zonetransfer.me

    - DNS restriction in chroot environment
      named without ROOT right
      $ /usr/sbin/named -u named      @ RedHat
      $ /usr/sbin/named -u bind       @ Debina

      Running BIND in a chroot jail i.e. named in the chroot sandbox environment
      If BIND malfunctions or is compromised, it is less likely to damage other
      parts of the computer

      1. preparng a chroot jail
      All files that BIND needs must be copied to the new root directory
      $ /etc/init.d/bind9 stop
      $ mkdir -p /var/sandbox/bind
      $ mkdir /var/sandbox/bind/etc
      $ mkdir /var/sandbox/bind/dev
      $ mkdir -r /var/sandbox/bind/var/cache/bind
      $ mdkir -r /var/sandbox/bind/run/bind/run
      $ mv /etc/bind  /var/sandbox/bind/etc
      $ ln -s /var/sandbox/bind/etc/bind /etc/bind
      $ mknod /var/sandbox/bind/dev/null   c 1 3
      $ mknod /var/sandbox/bind/dev/random c 1 8

      2. Bind zum Besitzer seiner Dateien machen, damit BIND den Syslog verwenden
      $ chown -R bind:bind /var/sandbox/bind/var/*
      $ echo "\$ADDUnixListenSocket /var/sandbox/bind/dev/log" > \
      /etc/rsyslog.d/bind-chroot.conf

      3. Startoptionen fuer gechrootenten BIND festlegen, und Daemonen starten
      $ rm -f /etc/default/bind9
      $ echo "RESOLVCONF=yes" > /etc/default/bind9
      $ echo "OPTIONs=\"-u bind -t /var/sandbox/bind\"" >> /etc/default/bind9
      $ /etc/init.d/rsyslog restart
      $ /etc/init.d/bind9 start

      4. Configuration for a chrooted BIND as an example
      | logging {
      |     channel some_log {
      |         file "bind.log" versions 3;
      |         severity info;
      |     };
      |     category default { some_log; };
      | };
      | zone "example.com" IN {
      |     type master;
      |     file "/etc/bind/example.com.zone";
      | };

    - DNS security
      Main task of DNSSEC (DNS Security Extensions) is addressed on Cache poisoning
      Attacker can inserts false data into the DNS cache of Non-authoritative servers
      DNSSEC is based on crytographic keys

      $ vi /etc/named.conf
      | ...
      | dnssec-enable yes;
      | dnssec-validation yes;
      | ...

      dnssec-keygen options
      $ dnssec -a Agorithmus mit RSA, RSAMDS, DH, DSA, RSASHA1, HMAC-MD5
      -b Key length
      -n Type mit ZONE, HOST, ENTITY, USER, OTHER

      Usage of dnssec using ZONE keys
      $ cd /etc/bind
      $ dnssec -a DSA -b 768 -n ZONE linux.net
      > Klinux.net.+003+18062.key            : public key will be created
      > Klinux.net.+003+18062.private        : private key will also be created
      $ cat Klinux.net.+003+18062.key >> db.linux.net
      Or put include into the zone file
      $ vi /var/cache/bind/db.kinux.net
      | $includ Khomelinux.net.+003+18062.key
      $ rndc reload                          : update of zone file
      Make a signature for zone file
      $ dnssec-signzone -r /dev/urandom -t -k Klinux.net.+003+18062.key db.linux.net

      If you use host keys, following process is assumed
      $ dnssec-keygen -a HMAC-MD5 -n HOST -b 512 example.com
      $ cat Kexample.com.+157+39941.private
      | Private-key-format: v1.2
      | Algorithm: 157 (HMAC_MD5)
      | Key: 7i3+IXiKmU00jA4f8VWHwA==
      Create a key file into your dns environment
      $ vi /var/cache/named/named.keys
      | key ddns_key {
      |     algorithm hmac-md5;
      |     secret "7i3+IXiKmU00jA4f8VWHwA==";
      | };
      Do configure your zones (aka domains) for dynamic updates
      $ vi /var/cache/named/named.conf
      | options { ... };
      |
      | include "/var/cache/named/named.keys";
      |
      | zone "example.com" {
      |     type master;
      |     file "masters/db.example.com";
      |     allow-update { key ddns_key; };
      | };
      | zone "2.0.192.in-addr.arpa" {
      |     type master;
      |     file "masters/rev.192.0.2";
      |     allow-update { key ddns_key; };
      | };

      To facilitate signature validation, DNSSEC adds a few new DNS record types:
      |   RRSIG 	  - Resource-Record-Signature, contains a cryptographic signature
      |   SIG       - Vorgaenger von RRSIG, obsolute
      |   DNSKEY 	  - Contains a public signing key
      |   KEY       - Vorgaenger von DNSKEY, obsolute
      |   DS		  - Contains the hash of a DNSKEY record
      |   NSEC & NSEC3  - For explicit denial-of-existence of a DNS record
      |   CDNSKEY & CDS - For a child zone requesting updates to DS records in parent zone

      DNS lookup
      $ dig +dnssec www.lpi.org | grep RRSIG

      Alternative Security Technique
      TSIG (Transaction Signature) : kein PKI zum Einsatz, sondern gemeinsame Geheimnisse.
      Servers using TSIG must be in sync (time zone!)

    - DNS server practice II
      Network practice using VBox
      Debian(DNS Server)       - Centos(Client)
      Debian(NAT and internes) - Centos(internes)

      ----------------------------------------------------------------------------------
      Preparation of DHCP @ Debian
      ----------------------------------------------------------------------------------
      $ apt-get remove dnsmasq
      $ apt-get remove network-manager
      $ apt-get install isc-dhcp-server      : ISC DHCP is the classical DHCP program
      $ vi /etc/network/interfaces
      | auto eth0
      | iface eth0 inet dhcp
      | auto eth1
      | iface eth1 inet static
      | address 192.168.0.1
      | netmask 255.255.255.0
      $ sudo ifup eth0
      $ sudo ifup eth1

      $ vi /etc/default/isc-dhcp-server
      | INTERFACES="eth1"
      $ vi /etc/dhcp/dhcpd.conf +/slightly
      | ... ... slighlty ... ...
      | subnet 192.168.0.0 netmask 255.255.255.0 {
      | range 192.168.0.101 192.168.0.200;
      | option domain-name-servers 8.8.8.8;
      | option domain-name "kang.local";
      | option routers 192.168.0.1;
      | option broadcat-address 192.168.0.155;
      | default-lease-time 600;
      | max-lease-time 7200;
      | }

      $ /etc/init.d/isc-dhcp-server       : Debian
      $ service isc-dhcp-server start     : ubuntu 14.04
      $ systemctl start isc-dhcp-server   : ubuntu 15.04

      iptables and forwarding
      $ vi /etc/sysctl.conf
      | net.ipv4.ip_forward=1
      $ vi /etc/network/interfaces
      | up /sbin/iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
      $ reboot

      $ service isc-dhcp-server status
      $ cat /var/log/syslog | grep "dhcp"

      ----------------------------------------------------------------------------------
      DNS BIND installation @ Debian
      ----------------------------------------------------------------------------------
      $ apt-get install bind9
      $ vi /etc/bind/named.conf.local
      |zone "kang.local" {
      |    type master;
      |    file "/var/cache/bind/db.kang.local"
      |};

      $ vi /etc/bind/named.conf.options
      |forwards {
      |   8.8.8.8;
      |};

      Reserve zone file of localhost, it based on the file /etc/bind/db.local
      $ vi /var/cache/bind/db.kang.local
      | $TTL   2D
      | @   IN   SOA   debian-kang.kang.local.   mail.kang.local.  (
      |		 18728   ; Serial
      |		 8H      ; Refresh
      |		 2H 	 ; Retry
      | 		 4W 	 ; Expire
      |	     3H    ) ; Negative Cache TTL
      | @              IN   NS   debian-kang.kang.local.
      | 	             IN   A    192.168.0.1
      | debian-kang    IN   A    192.168.0.1

      $ /etc/init.d/bind9 start

      ----------------------------------------------------------------------------------
      Let's go to CentOS, and try to access Debian-DNS server
      ----------------------------------------------------------------------------------
      $ cat /etc/resolv.conf
      $ nslookup debian-kang.kang.local
      $ nslookup 192.168.0.1

      - DNS with split view (example)
      // global options
      |options {
      |    directory "/var/named";
      |    version "not currently available";
      |};
      |logging{
      |    channel example_log{
      |	file "/var/log/named/example.log" versions 3 size 2m;
      |     	severity info;
      |    	print-severity yes;
      |    	print-time yes;
      |    	print-category yes;
      |    };
      |    category default{
      |	example_log;
      |    };
      |};

      // provide recursive queries and caching for internal users
      |view "goodguys" {
      |    match-clients { 192.168.0.0/24; }; // our network
      |    recursion yes;                     // recursion supported
      |    // required zone for recursive queries
      |    zone "." {
      |    	type hint;
      |	file "root.servers";
      |    };
      |    // private zone file including local hosts
      |    zone "example.com" {
      |	type master;
      |	file "view/master.example.com.internal";
      |    };
      |    // required localhost domain
      |    zone "localhost" in{
      |	type master;
      |	file "master.localhost";
      |	allow-update{none;};
      |    };
      |    // localhost reverse map
      |    zone "0.0.127.in-addr.arpa" in{
      |	type master;
      |	file "localhost.rev";
      |   	allow-update{none;};
      |    };
      |}; // end of view

      // external hosts view
      |view "badguys" {
      |    match-clients {"any"; };   // all other hosts
      |    recursion no;              // recursion not supported
      |    // only public hosts
      |    zone "example.com" {
      |        type master;
      |        file "view/master.example.com.external";
      |    };
      |}; // end of view


    - DNS rules
      Intenal DNS client resolve all hostname in internal network public server
      (mail, web, ftp etc.) in the company all hostname of internet
      External DNS client resolve public server(mail, web, ftp etc.) belongs to company
      no any hostname in internal network

    - DNS strategy
      2 physical servers
      : variante with differetn zone files for/from internal, public and exteranl
      1 physical server
      : 2 DNS instants, at least one of them must run under chroot-environment
      Split-Horizon/Spilit-Brain
      : 2 different Versions of zone files for internal and public case
      Split-View
      : 2 different versions of zone files from the internal and external client


<br/><a name="web_services"></a>

### Web Services
    ===============================================================================================
    LPIC 208: Web Services
    ===============================================================================================
    - Leading Web Server
      Apache, Microsoft-IIS, nginx, Tomcat, Node.js

    - Apache Webserver
      $ apt-get install apache2 (Debian)
      $ yum install httpd (CentOS)
      $ systemctl restart apache2/httpd

    - Apache
      support in Ruby, PHP, perl, python
      @ Debian Main configuration file is under
      /etc/apache2/apache2.conf
      /etc/apache2/*-avaiable
      /etc/apache2/*-enabled
      @ CentOS main configuration file is under
      /etc/httpd/conf/httpd.conf

      $ vi /etc/httpd/conf/httpd.conf
      | Port                          :
      | Listen  80                    : bind apache to specific IP and port
      | MinSpareServers               : 10 is default
      | ServerType                    : standalone or inetd
      | ServerRoot "/www/Apache22"    : server's configuration, error, and log are kept
      | DocumentRoot "/var/www/html"  : will serve your documents
      | // to handle a lot of requests during normal use with spike in acitivity
      | StartServers                  : Anzahle der Serverprozesse beim Start
      | MinSpareServers		: Anzahl, die als Reserve zur Verfuegung stehen
      | ServerLimit			: max Anzahl, zur Laufzeit des Servers ausfeguehrt
      | MaxClient			: max Anzahl, die gleichzeitig ausgefuert
      | MaxRequestPerChild		: max Anzahl, die an Serverprzess gesendet werden

    - Access control
      htpasswd can create a password.list to allow access
      $ ./htpasswd -c password.list kang  : -c create just for the first time
      $ ./htpasswd kim                    : kim will be added into password.list

      Authentification can then manage within httpd.conf
      $ vi /etc/httpd/conf/httpd.conf
      | <Directory/>
      |     ...
      |     AllowOverride None
      |     Order deny,allow                       <-- deny works first, and allow in next
      |     Deny from all                          <---will deny access to all content
      |     AuthName "Authentifizierung erfordelich"
      |     AuthType Basic
      |     AuthUserFile password.list
      |     require valid-user
      | </Directory>

      If you want to modify Apache settings for a single directory in your web's tree
      $ vi /etc/httpd/conf/httpd.conf
      | ...
      |     AllowOverride All   <---decide whether an ".htaccess" file can override settings
      | ...
      $ vi .htaccess
      | <Directory "/var/www/html">
      |     AuthName "Authentifizierung erfordelich"
      |     AuthType Basic
      |     AuthUserFile password.list
      |     require valid-user
      | </Directory>

      You can use group access control by .htaccess and .htgroup
      $ vi .htaccess
      | <Directory "/var/www/html">
      |     AuthName "zugriff fuer nur Agents"
      |     AuthType Basic
      |     AuthUserFile password.list
      |     AuthGroupFile .htgroup
      |     require group agents
      | </Directory>
      $ vi .htgroup
      | all:user1,user2,user3,user4
      | agetns:user1

      $ vi /etc/security/access.conf
      | + : root : .foo.bar.org  : root able to get access from domain
      | - : root : ALL           : denied to get access from all other sources

      Q. Explain what will be happen, if you try to access files under/to directory
      | <Directory /var/web/dir>
      | <Files note.html>
      |     Order allow,deny
      |     Deny from all
      | </Files>
      | </Directory>
      A. deny access to all files and subdirectory under /var/web/dir/*
      but allow access to /var/web/private.html, if it exists!

    - Apache log
      access logs and error logs
      /var/log/apache2/access.log
      /var/log/apache2/error.log
      /var/log/httpd/access.log
      /var/log/httpd/error.log

      logrotate can also use
      In access.log wird jede einzelne URL, die abgerufen wird, protokolliert.

      $ vi httpd.conf
      | <IfModule log_config_module>
      |     Log "logs/access_log" combined
      |     ErrorLog "logs/error_log"
      | </IfModule>

    - Apache Module
      /usr/src/mod_perl
      mod_php
      mod_perl
      mod_auth

      $ vi /etc/httpd/conf/httpd.conf
      | LoadMoule  perl_module   modules/mod_perl.so
      | LoadModule apache_ssl_module modules/libssl.so

      - Apache Virtual Host
      allow you to run multiple websites off of one web server as well as customize
      settings for each site. The term Virtual Host refers to the practice of running
      more than one web site (such as company1.example.com and company2.example.com)
      on a single machine

      $ vi /etc/httpd/conf/httpd.conf
      NameVirtualHost 172.24.21.78
      | <VirtualHost 192.168.0.4>
      |     ServerAdmin root@linux.my.com
      |     ServerName  www.linux.my.com
      |     DocumnetRoot /usr/local/apache/html
      |     DirectoryIndex index.html
      |     DirectoryIndex index.php default.php
      |     ErrorLog logs/linux.my.com-error_log
      |     CustomLog logs/linux.mybestone.com-access_log common
      | </VritualHost>

      Here are some other examples
      | <VirtualHost 24.215.7.162>
      | <VirtualHost [2a01:198:5dd:7a03:a00:27ff:fe2d:2987]>
      | <VirtualHost *:80>
      | <VirtualHost www.super-admin.org:82>

      Here is exmpale of redirect
      | <VirtualHost *:80>
      |     ServerName www.domain1.com
      |     Redirect / http://www.domain2.com
      | </VirtualHost>
      | <VirtualHost *:80>
      |     ServerName www.domain2.com
      |     ......
      | </VirtualHost>
      | <VirtualHost 111.22.33.44>
      |      ...
      |      VirtualDocumentRoot /var/httpd/%-2/%-1  : If you have http://www.luna.edu, then
      |     ...			                meanes /var/httpd/luna/edu/index.htm
      | </VirtualHost>

      If you would like to create a permanent redirect,
      you can do so in either of the following two ways:
      | Redirect 301 /oldlocation http://www.domain2.com/newlocation
      | Redirect permanent /oldlocation http://www.domain2.com/newlocation

      types of status code
      301 permanent redirects
      302 temporary redirects (default)
      303 seeother  redirects
      410 gone      redirects

    - start & stop
      $ /etc/init.d/httpd  start
      $ systemctl start apache2

      $ /usr/sbin/httpd -f /etc/httpd/conf/httpd.conf

      $ apache2ctl start
      stop
      restart
      configtest    : dry-run
      graceful      : restarts, but open connections are not aborted
      graceful-stop : stops, but permits in-progress transfers to complete

    - HTTPS & SSL
      A secure web server (Apache/SSL) uses HTTP over SSL, using port 443 by default.
      SSL(Secure Sockets Layer) provides secure communication between client and server
      by allowing mutual authentication and the use of digital signatures for integrity
      and encryption for privacy

      OpenSSL + Apache =  Apache-SSL -> mod_ssl
      mod_ssl consists of the SSL module and a set of patches for Apache.

      mod_ssl can be used to authenticate clients using client certificates.
      mod_ssl will validate the certificates against CA(Certificate Authority).
      Certificates are usually stored in /etc/ssl/ or /etc/pki/.
      /etc/ssl/       : the standard location for OpenSSL configuration
      /etc/pki/ 	: the standard location for PKI (Puclbic Key Infrastructure)

    - SSL configuration for HTTPS
      How to create a SSL server Certificate
      1. generating the RSA key file, which is used to encrypt and decrypt messages.
      2. create a Certificate Signing Request (CSR)
      3. The CSR is sent to a Certificate Authority (CA), verify the generated certificate.

      To create a CSR with the server RSA private key, do
      $ cd /etc/httpd/ssl
      $ openssl req -new -x509 -nodes -out  server.crt -keyout server.key
      To generate a public and private RSA key pair
      $ openssl genrsa -des3 -out private.pem 2048

      Here is more convenient way to perform SSL
      CA.pl(or CA) provide the openssl parameters will default use values it reads from
      the standard OpenSSL configuration file
      /etc/ssl/openssl.cnf(old) or /etc/pki/tls/openssl.cnf(new)
      $ /usr/lib/ssl/misc/CA.pl -newca     : use a perl script, old, obsolute

      Do following procedure to create keys
      $ cd /etc/pki/tls/misc
      $ CA -newca   : create cakey.pem (private key)
      $ CA -newreq  : a signing request, newreq.pem(request) & newkey.pem(new  private key)
      $ CA -signreq : sign the request, newcert.pem (signed CA)
      You will find the list of generated files under
      $ cd /etc/pki/CA/private/

      If you want to make more secuere, use PKC12 (public key cryptography standards)
      $ /etc/pki/tls/misc/CA  -pkcs12 "Pa$$W0rd"   : newcert.p12 (including password)

      You can now configure SSL via configuration file, which is integrated by mod_ssl
      $ vi /etc/httpd/conf.d/ssl.conf
      | SSLCertificateFile     /etc/pki/CA/private/newcert.pem
      | SSLCertificateKeyFile  /etc/pki/CA/private/newkey.pem
      $ aprchectl restart

      both a HTTP and HTTPS enabled site could be like the following in httpd.conf
      $ vi /etc/httpd/conf/httpd.conf
      | NameVirtualHost *:80
      | NameVirtualHost *:443
      | <VirtualHost *:80>
      |    ServerName     webserver.intranet
      |    DocumentRoot   /srv/http
      |    ErrorLog       /var/log/apache2/error.log
      | </VirtualHost>
      | <VirtualHost *:443>
      |    SSLEngine On
      |    SSLCertificateFile     /etc/pki/CA/private/newcert.pem
      |    SSLCertificateKeyFile  /etc/pki/CA/private/newkey.pem
      |    ServerName     webserver.intranet
      |    DocumentRoot   /srv/http
      |    ErrorLog       /var/log/apache2/error.log
      | </VirtualHost>

    - SSL directive by mod_ssl
      SSLEngine On            : usage of the SSL/TLS Protocol Engine.
      SSLCertificateKeyFile   : PEM-encoded private key used up to 3 times(by RSA, DSA, ECC)
      SSLCertificateFile      : certificate data used up to 3 times(by RSA, DSA, ECC)
      SSLCertificateChainFile : all-in-one assemble the certificates of CA
      SSLCACertificateFile    : all-in-one, whose clients you deal with
      SSLCACertificatePath    : Sets the directory where you keep the certificates of CAs
      SSLProtocol             : control the SSL protocol flavors mod_ssl
      SSLCipherSuite          : configure the Cipher Suite specification
      ServerTokens  		: controls Server response header field
      ServerSignature	  	: configure of a trailing footer under generated documents
      TraceEnable      	: overrides the behavior of TRACE for server and mod_proxy
      "off" causes 405 (method not allowed) error to the client.

      Q. How can I force clients to authenticate using certificates?
      $ vi /etc/httpd/conf/httpd.conf
      | ...
      | SSLVerifyClient require
      | SSLVerifyDepth 1
      | SSLCACertificateFile "conf/ssl.crt/ca.crt"
      | ...

    - Proxy server Squid
      web-cache used to reduce bandwidth demands or allow for finger-grained access control.
      acts as an intermediary for requests from clients seeking resources from other servers
      Web proxies is providing anonymity. HAProxy, Nginx, Squid, Varnish are mostly used

      Squid is a caching proxy for web clients, consists of squid and dnsserver, typically
      Squid is used bandwidth saving, handling traffic spikes, caching sites, load balancing

      @ CentOS
      /sbin/squid            : proxy server
      /bin/squidclient       : test program of squid server
      /etc/squid/squid.conf  : configuration
      /var/log/squid         : log data

      $ squid -z 	                         : initialization of cache
      $ squidclient  http://www.lpi.org    : test program from localhost via TCP 3128

      $ vi /etc/squid/squid.conf
      | http_port   : will listen for requests, default Port is 3128
      | cache_mem   : memory usage, default is 8MB
      | cache_dir   : to configure specific storage, where the cache files are stored
      | access_log  : location of protocel data
      | reply_body_max_size  : prohibit downloading the big data from the internet
      |
      | // here is example to configure size of cache!
      | cache_dir ufs /var/cache/squid 1024 16 256   ( dir, 1GB , 16dir, 256subdir)
      |
      | //If you want to provide access to an internal network,
      | //and deny access to anyone else, your options might look like this:
      | acl home src 10.0.0.0/255.0.0.0
      | http_access allow home
      |
      | //ACL (access control list)
      | //specifies which users or system processes are granted access to objects,
      | //as well as what operations are allowed on given objects
      |
      | //http_access is
      | //Mit http_access wird in Kombination mit allow bzw. deny ein Recht für die
      | //definierten ACL-Elemente festgelegt.
      |
      | acl localnet src 10.0.0.0/8     # RFC1918 possible internal network
      | acl localnet src 172.16.0.0/12  # RFC1918 possible internal network
      | acl localnet src 192.168.0.0/16 # RFC1918 possible internal network
      | acl localnet src fc00::/7       # RFC 4193 local private network range
      | acl localnet src fe80::/10      # RFC 4291 link-local (directly plugged)
      | acl SSL_ports port 443
      | acl Safe_ports port 443         # https
      |
      | acl accesses_to_search_engines dstdomain .google.com // NOT dst, but dstdomain
      |
      | http_access allow localhost manager
      | http_access deny manager
      | http_access deny all
      |
      | acl my_auth proxy_auth REQUIRED
      | acl my_user proxy_auth lisa sarah frank joe
      | acl daytime time 08:00-17:00
      | http_access allow my_auth daytime
      | http_access allow my_user
      | http_access deny all
      |
      | acl backlist urlpath_regex "/var/squid/url_blacklist"

      htpasswd can use to allow access
      $ htpasswd -c /etc/squid/passwd kang      : -c create just for the first time
      $ htpasswd /etc/squid/passwd kim          : kim will be added into password.list

      You need a plugin for authentification in order to set appropriate parameter,
      then configure squid.conf with passwd
      $ vi /etc/squid/squid.conf
      | auth_param basic program /usr/lib64/squid/basic_nsca_auth /etc/squid/passwd

      Here are some basic things to do
      | auth_param basic children 5             : 5 duaghter process will be started
      | auth_param basic casesensitive on       : passwords are case sensitive
      | auth_param basic credentialsttl 8 hours : require entering username and pwd
      | auth_param basic realm Authentifizierung erforderlich   : text of dialog

      Then you could set ACL
      | acl users proxy_auth REQUIRED
      | http_access allow users          : <--- position of "users "is very important
      | http_access allow localnet       : <--- then localnet in the middle
      | http_access allow localhost      : <--- then localhost at the end

      Q. How can you ensure that all access to normal web site (port 80) from your
      local netwrok through a proxy server
      A. Use iptables on your router to block or redirect all outgoing port 80 traffic
      except from the porxy server

    - Squid authenticator
      DB: Uses a SQL database.
      getpwam: Uses the old-fashioned Unix password file.
      LDAP: Uses the Lightweight Directory Access Protocol.
      MSNT: Uses a Windows NT authentication domain.
      NCSA: Uses an NCSA-style username and password file.
      NIS (or YP): Uses the NIS database
      PAM: Uses the Unix Pluggable Authentication Modules scheme.
      POP3: Uses an email server to validate credentials.
      RADIUS: Uses a RADIUS server for login validation.
      SASL: Uses SASL libraries.
      SMB: Uses a SMB server like Windows NT or Samba.
      SSPI: Windows native authenticator

    - Nginx
      can be used as HTTP reverse proxy or an IMAP/POP3 proxy.
      it is using an event driven (asynchronous) architecture, Netflix, Wordpress, GitHub

      Reverse Proxy typically sits behind the firewall in a private network and directs
      client requests to the appropriate back-end server.
      Load balancing, Web acceleration, Security and anonymity

      /etc/nginx/nginx.conf    : main configuration
      /etc/nginx/conf.d        : Any additional server blocks configuration
      /usr/share/nginx/html    : Files are placed, will be served on your web server

      Caution! Apache and Nginx use same port, so change port at apache session
      $ vi /etc/http/conf/httpd.conf
      | NameVirtualHost *:8000
      | Listen 8000
      | <VirtaulHost *.8000>

      Old configuration method, many predefined proxy options are there, just make a link
      $ ln -s /etc/nginx/sites-available/proxy  /etc/nginx/sites-enabled/proxy

      For reverse proxy server, when nginx use 80, local webserver use 8000
      $ vi /etc/nginx/nginx.conf
      | server {
      |     listen       80 default_server;
      |     listen       [::]:80 default_server;
      |     server_name  _;
      |     root         /usr/share/nginx/html;
      |     location / { proxy_pass http://127.0.0.1:8000; }  <-- reverse proxy specific!
      | }
      $ /etc/init.d/nginx start  : start by init or systemctl
      $ nginx -t                 : test of configuration data
      $ nginx -s reload          : reload or (stop, quit,reopen)

      For Nginx Web server
      $ vi /etc/nginx/nginx.conf
      | server {
      |     listen       80;
      |     server_name  www.lpic.de;
      |     index        index.html;
      |     root         /var/www.lpic.de;
      | }

    - Nginx with firewall @ CentOS (optional)
      If you are running a firewall, run the following commands to allow HTTP and HTTPS
      $ sudo firewall-cmd --permanent --zone=public --add-service=http
      $ sudo firewall-cmd --permanent --zone=public --add-service=https
      $ sudo firewall-cmd --reload


<br/><a name="file_sharing"></a>

### File Sharing
    ===============================================================================================
    LPIC 209: File Sharing
    ===============================================================================================
    - SAMBA
      SAMBA (Server Message Block) protocol
      smbd provides filespace and printer services to clients using the SMB protocol.
      nmbd reply to NetBIOS over IP name service requests, and can act as a WINS proxy.

      139/TCP : NetBIOS, file and printer sharing.
      137/UDP : NetBIOS, name service requests and responses
      138/UDP : NetBIOS, datagram services to transmit small amounts of data, announcements
      445     : CIFS

      $ vi /etc/smb/smb.conf
      |[global]
      |    workgroup = STURBAIN               : set the NetBIOS workgroup or "domail name"
      |    wins support = no
      |    wins server = 192.168.57.1
      |    log file = /var/log/samba-log.%m   : wins-server with IP
      |    log directory = /var/lock/samba
      |    security = User                    : Samba use its local account in the server
      |    security = Share                   : passwords are associated with shares
      |    security = Server                  : samba to authenticate without domain
      |    security = ADS                     : samba to join domain using Active Directory
      |    security = Domain                  : Samba to join domain using Windows NT 4
      |    guest accout = nobody
      |    hide files = /*.tmp/*.bak/*.old/   : set the hidden attribute of files on
      |    veto files = /*.tmp/*.bak/*.old/	: not allow users to see files during listing
      |    name resolve order = hosts		: uses hosts file for name resolution
      |    unix password sync = yes           : synchronise UNIX passwd with samba passwd
      |[Public]
      |    comment = Some useful files
      |    read only = yes                    : identical to writeable=no
      |    locking = no
      |    browseable = yes                   : whether allow to show the list of public
      |    path = /public
      |    guest ok = yes
      |[printers]
      |    comment = All Printers
      |    path = /var/tmp
      |    create mask = 0600
      |    printable = yes                    : whether to set the spool to the share
      |    browseable = yes

      Creating Hidden Shares in Linux, put the dollar sign as the name of share
      |[HiddenEnv$]

      smbusers allows you to map the client supplied username to another username.
      Add sambda server to an existing Windows network, can use all users of windows
      $ vi /etc/smb/smb.conf
      |[global]
      | username map = /etc/samba/private/usermap.txt
      | ...

      $ vi /etc/samba/private/usermap.txt
      | root = administraor admin
      | nobody = guest pcguest smbguest
      | alice.jones = alice
      | readonly = glen fred terry sarah
      | lachlan = "Lachlan Smith"
      | users = @sales

      Log and spool data
      /var/log/samba/
      /var/spool/samba

    - Samba Tools
      Monitoring of samba server
      $ smbstatus
      | Samba version 4.2.10-Debian
      | =================================================================================
      | PID   Username  Group   Machine                               Protocol Version
      | ---------------------------------------------------------------------------------
      | 23632 nobody    nobody  10.20.24.186(ipv4:10.20.24.186:49394)
      | =================================================================================
      | Service    pid     machine            Connected at
      | ---------------------------------------------------------------------------------
      | public     23632    10.20.24.186      Sat Oct 10 10:15:11 2015
      | =================================================================================

      Check an smb.conf configuration file for internal correctness
      $ testparm  	 : check an smbd.conf file for internal correctness
      $ testparm -v    : all options that were not used in smb.conf thus set to its default

      It is note possible for Samba to use /etc/passwd and /etc/shadow
      Samba uses it's own password system so users need to be added by root
      $ smbpasswd -a frank      : add user
      $ smbpasswd -x frank      : delete user
      $ pdbedit -w -L           : to list existing Samba users

      To query NetBIOS names and map them to IP using NetBIOS over TCP/IP
      $ nmblookup CH01                        : computername
      $ nmblookup -s CH-1-124   	        : with hostname
      $ nmblookup -A 192.168.1.124            : with IPaddress
      $ nmblookup NAS -R -U 192.168.1.198     : Recusive and Unicast

      Tool for administration of Samba and CIFS (common internet file system) servers
      $ net -S sambaserver -U alice share     : to get a list of shares from server
      $ net -S sambaserver time	        : to get the current time of server
      $ net -S CH01 TIME		        : to show time of filesystem
      $ net lookup ch01			: to find IP address
      $ net status shares	    : status of fileserver

      SWAT (Samba Web Administration Tool)
      http://myserver:901

    - Samba Client
      As a normal user, to access samba server
      $ smbclient //ourmachine/me             : To access my share
      $ smbclient -U you //ourmachine/you     : To access your share
      $ smbclient //ourmachine/ourfiles       : To access our shared share
      $ smbclient -L //sambaserver            : To create a listing of sambaserver
      $ smbclient //sambaserver/Printer_1/ -c "print /etc/hosts"   :	to test printing

      To access samba sharing data with mount/smbmout or fstab
      $ mount -t smbfs //server/data  /mnt
      $ smbmount //server/data /mnt
      $ mount -t smbfs -o username=kang,passpwrd=pa$$w0rd //server/data  /mnt
      $ vi /etc/fstab
      | //server/data  /mnt  cifs  rw,auto,username=kang,passpwrd=pa$$w0rd   0   0

      mount is much stronger option than smbclient, because you should not run program
      with smbclient accessing

    - NFS
      NFS (Network File System) make a remote disk as a part of your local filesystem
      NFS use client's user authetication and a list of trusted host
      To run NFS
      • support for NFS (several options) must be built into the kernel
      • a portmapper must be running, the portmapper is used to interface TCP/IP connections
      to the appropriate RPC calls. It is needed for all NFS traffic.
      • on systems with NFS-server support, an NFS daemon and a mount daemon must be active
      The mountd (or rpc.mountd) mount-daemon handles incoming NFS (mount) requests.

      Installation
      $ apt-get install portmap nfs-server

      The file /etc/exports contains the definitions of filesystems to be exported,
      the name of the host that is allowed to access it and how the host can access it.
      $vi /etc/exports
      | /            admincomputer(rw,no_root_squash)
      | /public      *(ro,all_squash)
      | /geheim      agent*.mydomain.com(rw)

      Interpretation : the root user on the computer called "admincomputer" will be
      able to r/w every file in the server's all(/) directory tree
      root_squash    : requests by user root on client will be done as user nobody on
      server, root on the client can only read files on the server
      no_root_squash : requests as root on the client will be done as root on the server
      This is necessary when backups are to be made over NFS.
      all_squash     : requests of any user other than root on client are performed as user
      nobody on server. Use if you cannot map usernames and UID’s easily.
      no_all_squash  : All requests of a non-root user on client are attempted as the same
      user on server.

      Export definitions are configured and will be activated by the exportfs command.
      $ exportfs -a                       : all directories (export or unexport)
      $ exportfs -o rw ubuntu:/home/kang  : export ubuntu server with rw
      $ exportfs                          : dry-run
      $ exportfs -r                       : reexport all directories
      $ exportfs -ua                      : de-activate the export list (unexport all)
      $ ls -ln /public                    : -> UID and GID = 1000

    - NFS client
      $ mkdir /mypublic
      $ mount -t nfs -o ro,rsize=8192 server:/public /mypublic
      : option rsizer for read transfer is depending on kernel version.
      $ cd /mypublic
      $ vi /etc/fstab
      | server:/public  /mypublic  nfs  default   0   0

    - NFS Tools
      information about the exported filesystems and active mounts to the host
      $ showmount           :               show names of clients with active mounts
      $ showmount server -d : --direcotires show directories that are mounted by clients
      $ showmount server -e : --export      show active export list
      $ showmount server -a : --all         show names of clients with active mounts

      reports RPC(remote procedure call) information, probing a system
      $ rpcinfo
      $ rpcinfo -p remotehost          : to probe server from a client system
      $ rpcinfo -u remotehost program  : to test a connection without doing any real work
      $ rpcinfo -t remotehost program  : same as -u
      $ rpcinfo -n remotehost          : to probe port in combination with -u or -t
      $ rpcinfo -b remotehost          : to probe RPC broadcast
      $ rpcinfo -d           		     : to delete registed RPC service

      lists statistics about nfs connections
      $ nfsstat -s     : --server show server information
      $ nfsstat -c     : --client show client information
      $ nfsstat -n     : --nfs    show nfs statistics
      $ nfsstat -r     : --rpc    show rpc informations
      $ nfsstat -2     : -3,-4    show nfs statistics with specific version
      $ nfsstat -m     : --mounts show statistics for mounted system
      $ nfsstat -o nfs : show informations for object nfs,rpc,net

    - Securing NFS
      The tcp wrapper / Firewall software can be used to limit access to an NFS server.
      You may consider blocking unwanted NFS connections at each NFS server host
      • the portmapper port    :   111/udp and   111/tcp
      • NFS connections        :  2049/udp and  2049/tcp
      • the mount daemon ports : 32771/udp and 32768/tcp

      via tcp wrapper
      $ vi /etc/hosts.allow
      | portmap : 192.168.50.0/24

      via firewall software (iptables)

      Q. What is the  over TCP Wrappers as a security tool
      A. You can write iptables rule that restrict the activities of clients & servers
      iptables accepts packet identification that can apply to incoming or outgoing to
      or from cleints and servers.
      TCP Wrappers works only on the initial incoming connection to servers

      Q. disadvantage of iptables
      TCP Wrapper restrict access by matching usernames, but iptables does not support.


<br/><a name="network_anagement"></a>

### Network Client Management
    ===============================================================================================
    LPIC 210: Network Client Management
    ===============================================================================================
    - NIS
      Network Information System (NIS) is designed to centralize administration
      is a Remote Procedure Call (RPC)-based client/server system that allows a group of
      machines within an NIS domain to share a common set of configuration files.
      NIS domain name and tools are rpcbind, ypbind, ypserv, rpc.yppasswdd

    - pppd
      Point-to-Point Protocol Daemon
      PPP is a protocol used for establishing internet links over dial-up modems,
      DSL connections, and many other types of point-to-point links

    - DHCP
      DHCP (Dynamic Host Configuration Protocol) is a protocol that automatically provides
      an IP with its IP address and other related configuration information
      DHCP servers deliver also network mask, gateway address, DNS server address, hotname,
      domain name, and also IP of NetBIOS name and NTP servers
      • DHCPDISCOVER  : the client find the server over the broadcast, By default, server
      listens for requests on udp port 67 and answers through udp port 68
      • DHCPOFFER     : Server ask to client the configuration via broadcast 255.255.255.255
      • DHCPREQUEST   : client response configuration
      • DHCPACK       : or DHCPNACK, server look configuration, client apply configuration

    - DHCP server
      $ apt-get install dhcp3-server
      $ yum install dhcp

      $ vi /etc/dhcp/dhcpd.conf
      | # A slightly different configuration for an internal subnet.
      | subnet 200.200.200.0 netmask 255.255.255.0 {
      |
      |   # standard
      |   range 200.200.200.130 200.200.200.139;
      |   option domain-name-servers  ns1.internal.example.org; //DNS server IP or dns name!
      |   option domain-name "kha5.net";                        //DNS-suffix
      |   option routers 200.200.200.254;
      |   option broadcast-address 200.200.200.255;
      |   default-lease-time 600;
      |   max-lease-time 7200;
      |
      |   # logging
      |   log-facility local7;     // look /etc/syslog.conf or /etc/rsyslog.conf
      |
      |   # other services
      |   option smtp-server smtp.company.com;  //SMTP
      |   option pop-server pop3.company.com;   //POP3
      |   option nntp-server news.company.com;  //NEWS
      |   option time-servers ntp.company.com;  //NTP
      |
      |   # subnet
      |   subnet 192.168.50.0 netmask 255.255.255.0 {
      |        range 192.168.50.101 192.168.50.199;
      |        option routers 192.168.50.3
      |   }
      |
      |   # printers
      |   host printer-finance {
      |        hardware ethernet 00:DD:HD:66:55:9B;
      |        fixed-address 192.168.100.50;
      |   }
      |
      |   # specific for window client
      |   option netbios-name-servers 192.168.50.1;
      |   option netbios-node-type  8;
      |
      | }

    - DHCP log
      $ cat /etc/rsyslog.conf  : Heute benutzen diese Konfiguration Datei
      | Syntax:
      | facility.priorityaction
      | Beispiel:
      | mail.warn             /var/log/mail
      | mail.news.warn        /var/log/messages
      | kern.crit 		/var/log/kernel.log
      | cron.*                /var/log/cron.log
      | lpr.*    		/var/log/lpr.log
      facility of syslog : login0, mail daemon, cron, user, lpr, news, auth, kern

      $ cat /var/log/syslog     : log by syslogd, non-debug and non-critical messages
      $ cat /var/log/messages   : log by syslogd, logs everything, except auth messages
      $ cat /var/log/daemon.log : error portocols are here

      DHCP Server keeps a persistent database of leases that it has assigned
      MAC Information is needed to always assign same IP when dhcpd is used
      $ vi /var/lib/dhcpd/dhcpd.leases (@ RedHat)
      $ vi /var/lib/dhcp3/dhcpd.leases (@ Debian)
      $ vi /var/db/dhcpd.leases        (@ Internet Systems Consortium)
      |
      | lease 192.168.16.39 {
      |
      |   starts 5 2013/04/08 07:00:00;
      |   ends 5 2013/04/08 07:00:00;
      |   tstp 5 2013/04/08 07:00:00;
      |   cltt 5 2013/04/08 07:00:00;
      |   binding state free;
      |   hardware ethernet 00:00:1c:da:46:17;    //require a mac address
      |   uid "\001\000\000\034\332F\027";
      |   client-hostname "R2PC2";    //lease process is for client!
      |
      | }

    - DHCP relay agent
      DHCP relay agent is typically a router, and communicate with DHCP server
      DHCP relay agent translate broadcast to unicast, and send to DHCP server
      DHCP server get the unicast message from DHCP server, provide IP for client
      1.DHCP Discover
      client ->  DHCP Relay Agent -> Unicast message -> server
      2.DHCP Offer Message
      server ->  Unicast message -> DHCP Relay Agent  -> Broadcast message-> client
      3.DHCP Request
      client -> Broadcast message -> DHCP Relay Agent -> Unicast message -> server
      4.DHCP Ack
      server ->  Unicast message -> DHCP Relay Agent  -> Broadcast message-> client

      No configuration data is required for DHCP relay agent.
      In router, you need to set the IP of DHCP server and network card of router
      $ /usr/sbin/dhcrelay -i eth0 192.168.50.1           : without 'p' !

    - DHCP client
      dhcpcd    : dhcp client daemon, download via http://www.isc.org
      dhclient  : not dynamical allocation, read /etc/dhclient.conf to configure netwrok
      pump      : similar as dhclient, /etc/pump.conf

    - LDAP schema
      • A relational database schema contains information about the structure of the
      database including information about the tables and the data types
      • An attribute syntax is the LDAP equivalent of a data type. Every attribute type
      is associated (either explicitly or implicitly) with an attribute syntax
      • An OID (object identifier) is basically a sequence of numbers separated by periods,
      like "1.23.456.7.89"
      • Object in the LDAP databank will be built in tree Hierarchy
      as Directory Information Tree (DIT)
      • A distinguished name (DN) is the name (set of attributes)
      which uniquely identifies an entry in the OpenLDAP directory, looks like this
      dn: uid=kang, ou=users, dc=linux, dc=net
      • LDIF (LDAP Data Interchange Format) conveys directory content as a set of records
      All modifications to the OpenLDAP database are formatted in the LDIF format.
      The LDIF is an ASCII text representation of LDAP entries.
      • SSHA is default method of encrypting password in LDAP

    - OpenLDAP server
      LDAP (Lightweigt Directory Access Protokolls) is directory service protocol that
      runs on a layer above the TCP/IP stack. It provides a mechanism used to connect,
      search, and modify Internet directories. Default Port is 389

      slapd  : LDAP daemon, listens for LDAP connections on any number of ports
      slurpd : Replicaton actualisation of openLDAP, control for many LDAP server

      slapd-config - configuration backend to slapd, can configure within
      /etc/openldap/slapd.d    @ RedHat
      /etc/ldap/slapd.d        @ Debian

      $ vi /etc/openldap/slapd.conf
      | database    bdb
      | suffix      "dc=linux,dc=net"
      | rootdn      "cn=admin,dc=linux,dc=net"
      | rootpw      secret

      General database directive for administrative task in slapd.conf
      suffix : is the DN (Distinguished Name) suffix of queries that will be passed to
      this backend database,
      rootdn : specifies the DN that is not subject to access control or administrative
      limit restrictions for operations on this database
      rootpw : to specifies a password for the DN for the rootdn

      If rootdn and rootpw are not defined, then administrator account of LDAP is defined
      by LDAP's ACL (permission)

    - OpenLDAP’s tools
      To add entries as a link to the ldapmodify tool
      $ slapadd -l user.ldif

      To export LDAP-databank with LDIF format
      $ slapcat -l alles.ldif

      To regenerate Index of LDAP-DB
      $ slapindex

      Im Notfall werden dein Eigentuemerschaften einfach wieder korrigiert
      $ chown  openldap:openldap  /var/lib/ldap/*

    - LDAP permission
      $ vi /etc/openldap/slapd.conf
      | access to <was> by <wer> <Zugriffsart>      //LDAP's ACL

      was ist data Objekt, auf das die Zugriffssteuerung konfiguriert wird
      wer ----------------------------------------------------------
      | *                  alle Benutzer
      | anonymous          nicht authentifizierte Benutzer
      | user               authentifizierte Benutzer
      | self               mit einem Eintrag verbundene Entitaet selbst
      | dn.regex=<regex>   sind Benutzer, auf dif der redulaere Ausdruck zutrifft

      Zugriffsart --------------------------------------------------
      | none          kein Zugriff
      | disclose      zur Informationsausgabe bei Fehlern benoetigt
      | auth          zur Authentifizierung benoetigt
      | compare       fuer Vergleichsoperationen benoetigt
      | search        Suchfilter zu verwenden
      | read 		Suchergebnisse anzuzeigen
      | write		zum Andern und Umbenennen
      | manage 	vollstaendige Verwaltungsrechte

      // defult setting
      | access to *
      |        by anonymous read
      |        by * none
      // standard method
      | access to *
      |        by self write
      |        by anonymous auth
      |        by * read
      // allow to read all
      | access to * by * read
      // Leserechte an net, und Suchrechte an linux und net
      | access to dn.children="dc=linux,dc=net"
      |        by * search
      | access to dn.children="dc=net"
      |        by * read

      SSSD (System Security Services Daemon) to identity and authentication remote resource
      provide caching and offline support, and also provide PAM and NSS network modules
      It is usde to authenticate user logins against an Active Directory via using sssd

    - LDAP server log
      loglevel
      1 		(0x1 trace) 	trace function calls
      2 		(0x2 packets) 	debug packet handling
      4 		(0x4 args) 		heavy trace debugging
      8 		(0x8 conns) 	connection management
      16 		(0x10 BER) 		print out packets sent and received
      32 		(0x20 filter) 	search filter processing
      64 		(0x40 config) 	configuration processing
      128 	(0x80 ACL) 		access control list processing
      256 	(0x100 stats) 	stats log connections/operations/results
      512 	(0x200 stats2) 	stats log entries sent
      1024 	(0x400 shell) 	print communication with shell backends
      2048 	(0x800 parse) 	print entry parsing debugging
      16384 	(0x4000 sync) 	syncrepl consumer processing
      32768 	(0x8000 none) 	only messages that get logged whatever log level is set

      $ vi /etc/openldap/slapd.conf
      | loglevel 129
      | loglevel 0x81
      | loglevel 128 1
      | loglevel 0x80 0x1
      | loglevel acl trace

    - LDAP client
      $ apt-get install ldap-client
      $ vi /etc/ldap/ldap.conf
      | BASE	dc=linux,dc=net
      | URI	ldap://ldap.linux.net:389

      Introduce new LDIF data and set this new Object into the LDAP system
      $ vi /etc/ldap/ldif/domain.ldif
      | dn:dc=linux,dc=net
      | uid: tsparker              <--- not number but this is still OK!
      | changetype: add
      | objectClass: top
      | objectClass: domain
      | dc:linux

      LDAP client tool options for ldapadd, ldapdelete, ldapsearch, ldappasswd
      -x     : normal Authentifizierung anstatt SASL
      -D     : kuendigt DN fuer die Anthetifizierung am LDAP an
      -W     : Passwort nach der Betaetigung der Eingabetaste abgefragt wird
      -w pw  : Passwort direkt auf der Kommandozeile angegeben werden
      -f     : kuendigt LDIF data/file an
      -n     : dry-run, die Ausfuehrung lediglich simuliert wird
      -a     : ein Eintrag hinzugefuegt wird

      LDAP add entry tool
      $ ldapadd -x -D "cn=admin,dc=linux,dc=net" -W -f domain.ldif
      $ ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/ldap/schema/nis.ldif
      $ ldapadd -h myhost -p 389 -D "cn=orcladmin" -w welcome -f jhay.ldif

      opens a connection to an LDAP server, binds, and performs a search using parameters
      $ ldapsearch -h myhost -p 389 -s base -b "ou=people,dc=example,dc=com" "objectclass=*"
      $ ldapsearch -x -b dc=linux,dc=net
      $ ldapsearch -x -LLL uid=kang
      $ ldapsearch -x -LLL -b
      $ ldapsearch loginShell=/bin/zsh

      change the password of an LDAP entry
      $ ldappasswd -x -h localhost -D "cn=root,dc=example,dc=com" \
      -s secretpassword -W uid=admin,ou=users,ou=horde,dc=example,dc=com
      $ ldappasswd -x -D "cn=admin,dc=linux,dc=net" -W \
      "uid=kang,ou=users,dc=linux,dc=net" -s NeuesPasswort

      LDAP delete entry tool
      $ ldapdelete -h myhost -p 389 -D "cn=orcladmin" -w welcome \
      "uid=hricard,ou=sales,ou=people,dc=example,dc=com"
      $ ldapdelete -D "cn=admin,dc=linux,dc=net" -W "uid=kang,ou=users,dc=linux,dc=net"

      SASL (Simple Authentication and Security Layer) a framework for authentication and
      data security in Internet protocols

    - PAM (Pluggable Authentication Modules)
      a flexible mechanism for authenticating users,
      authentication modules to be attatched at run-time in order to work

      configuration to pam within
      /etc/pam.d/          --- aktuelle!

      $ vi /etc/pam.conf   --- veraltet! will be ignored if /etc/pam.d/ is used
      | service   type   control      module-path    module-arguments
      | ssh       auth   sufficient   pam_unix.so    nullok

      Field  ------------------------------------------------------------------------
      service          : the name of the application involved (login, ssh, passwd)
      type             : the type of group (account, auth, password, session)
      control          : what the PAM should do in case authentication fails
      module-path      : the filename of the PAM, used by the application
      module-arguments : module specific arguments, separated by spaces

      Type --------------------------------------------------------------------------
      auth     : validate the user's authentication credentials.
      This means it checks if the user can supply valid credentials.
      account  : responsible for deciding if the account that is trying to sign in
      has access to the resources that it is requesting at this time.
      PAM allows you to specify controls that can deny or allow users based
      on predetermined criteria.
      session  : establish the environment that will be built up and torn down after
      user log in or log off. session files can determine which commands
      need to be run to prepare the environment.
      password : responsible for updating various services' authentication details.
      If a password needs to be changed, this module can assist in
      communicating with the service and modifying the correct values.

      auth, accout will be referenced every time a program successfully uses PAM for
      authentication. sesseion will be run if necessary after first two. password on-demand

      Control -----------------------------------------------------------------------
      required   : must be successful for authentication, If this module fails, a failure
      to application but it will continue to call the next module in the stack
      requisite  : If this module fails, the user is notified immediately with a message
      reflecting the failed module, no further modules in the stack are called
      sufficient : The module result is ignored if it fails, if the module success
      then, no further modules in the stack are called.
      optional   : The module result is ignored, only becomes necessary for successful
      authentication when no other modules reference the interface
      include	   : Unlike the other controls, this does not relate to how the module
      result is handled, just appends as an argument to the module

      PAM soll zur Authentifizierung zuerst LDAP oder Datei passwd zugreiffen
      $ vi /etc/nsswitch.conf
      | passwd: files ldap
      | shadow: files ldap
      | group:  files ldap
      Wenn PAM in den lokalen Datein nicht fuendig, dann wird zur Authentifizierung LDAP
      The Name Server Switch is responsible for mapping usernames to user ID values

    - PAM module
      /usr/lib64/libpam.so.0
      /usr/lib64/security/pam_xxx.so
      /lib/security/pam_xxx.so

      pam_ldap      : configures authentication via LDAP.
      pam_unix      : configures authentication via passwd & shadow (login, ssh, ppp, su)
      pam_cracklib  : provides strength-checking for passwords (against dictionary)
      pam_limits    : sets limits on the system resources obtained in a user-session
      pam_listfile  : allows an action based on the presence of the item in a listfile
      pam_nologin   : prevents users from logging into the system when /etc/nologin exists
      pam_console   : control access to devices via console in the user's session
      pam_securetty : user allow to log in as root, when /etc/securetty exists

      Those data location is related with PAM modules
      /etc/security/opasswd       : old password is saved into
      /etc/security/limits.d      : default limits are taken from
      /etc/security/limits.conf   : configuration of limitation

      If you want to deny some users from ssh connection,
      $ vi /etc/nosshuser
      | hateuser_name
      $ vi /etc/pam.d/sshd
      | auth required pam_listfile.so item=user sense=deny file=/etc/nosshuser

      If you want to allow users, then do this
      $ vi /etc/sshuser
      $ vi /etc/pam.d/sshd
      | auth required pam_listfile.so item=user sense=allow file=/etc/sshuser

      Simple way to deny any user login
      $ vi /etc/nologin
      $ vi /etc/pam.d/login
      | auth required pam_nologin.so

      PAM application example
      $ vi /etc/pam.d/login
      |#%PAM-1.0
      | 1 auth      required  pam_securetty.so
      | 2 auth      required  pam_unix.so nullok
      | 3 auth      required  pam_nologin.so
      | 4 account   required  pam_unix.so
      | 5 password  required  pam_cracklib.so retry=3
      | 6 password  required  pam_unix.so shadow nullok use_authtok
      | 7 session   required  pam_unix.so
      |
      | //interpretation
      | @ 2: nullok instructs the pam_unix.so module to allow a blank password.
      | @ 3: this is the final authentication step. It checks whether the /etc/nologin
      |      file exists. If it exists and the user is not root, authentication fails.
      | @ 4: account & pam_unix.so performs any necessary account verification.
      |      if shadow passwords have been enabled, the account interface of the pam_unix
      |      checks to see if the account has expired or if the user has not changed pwd
      | @ 6: PAM changes the user's password, shadow instructs the module to create shadow
      |      passwords when updating a user's password, and nullok allows a blank pwd
      |      use_authtok instructs the module not to prompt the user for a new password.
      |      Instead, it accepts any password that was recorded by a previous password.
      | @ 7: pam_unix.so module logs the user name and the service type to /var/log/secure
      |      at the beginning and end of each session.

    - PAM authetification with LDAP
      install libpam-ldap or libpam-ldapd to use pam_ldap.so (nss_ldap.so @ RedHat)
      $ vi /etc/nsswitch.conf
      | ...
      $ vi /etc/pam.d/system-auth-ac    (common-auth @ Debian)
      | auth required   pam_env.so
      | auth sufficient pam_ldap.so
      | auth sufficient pam_unix.so nullok try_first_pass
      | auth requisite  pam_succeed_if.so uid >= 500 quiet
      | auth sufficient pam_deny.so

      Introduce new LDIF data and set this Object into the LDAP system
      $ vi /etc/ldap/ldif/kang.ldif
      | dn: uid=kang,ou=users,dc=linux,dc=net
      | objectClass: person
      | ...
      | uidNumber : 1211
      | gidNumber : 1211

      $ cd /etc/ldap/ldif
      $ ldapadd -x -D "cn=admin,dc=linux,dc=net" -W -f kang.ldif


<br/><a name="email_services"></a>

### E-Mail Services
    ===============================================================================================
    LPIC 211: E-Mail Services
    ===============================================================================================
    - Mail system
      MUA - Mail User Agent
      Thunderbird, Outlook usw
      MDA - Mail Delivery Agent (Mail service)
      POP3(Post Office Protocol) or IMAP(Internet Message Access Protocol)
      MTA - Mail Transfer Agent (Mail Server)
      postfix,sendmail,exim,qmail

      MUA -> MTA by SMTP(Simple Mail Transfer Protocol)
      MDA -> MUA by POP3 or IMAP
      Smarthost ist ein Mailserver, der als SMTP-Relay fungiert.
      /var/spool/mail   : delievered by MDA
      /var/spool/mqueue : auszuliefernde Mail-Queue

    - E-mail Port (reminder)
      SMTP 25  : default SMTP
      SMTP 465 : to send messages using SMTP securely
      IMAP 143 : non-encrypted port
      IMAP 993 : using IMAP securely
      POP3 110 : default POP3 non-encrypted port
      POP3 995 : using POP3 securely

      Q. A user use random computers in the office, you want to store their messages
      on the specific mail server computer, what protocol would be best?
      A. IMAP ia a pull mail protocol, and supports directories and email organization
      POP deletes usually the email from the remote server

    - Mail alias by user
      ~/.forward
      $ vi .forward
      > aaa@gmail.com

    - Mail alias by root
      $ vi /etc/aliases
      | postmaster: root                  : postmater is root
      | kang: donghee.kang@gmail.com      : mail alias
      | anna: \anna,walter,testuser       : "\" needed
      | kunden: :include:/etc/kunden.txt  : list contain all e-mail of kunden
      | some: "/home/donghee/dailyreport" : daily report
      | major: "|/usr/bin/wrap mailing"   : Mailinglisten programm work with pipe
      $ newaliases                        : to make activation of this alias

    - mail (MUA, simple mail program)
      -a : attachment
      -c : cc
      $ mail -s "Test Subject" user@example.com < /dev/null
      $ mail -a /opt/backup.sql -s "Backup File" user@example.com < /dev/null
      $ echo "This is message" | mail -s "test" max@lucky.de
      $ sendmail -bq       : or mailq

    - Mail server security
      //Make sure that the MX records for the MTA’s are available in DNS
      $ dig MX domedomain.com

      //General way to test
      $ netcat localhost 25
      $ telnet localhost 25
      | HELO test
      | MAIL FROM:<me@telnet.com>
      | RCPT TO:<you@mail.telnet.net>
      | DATA
      | write e-mail, and finish only with "." at the end of new line
      | .
      | QUIT

      //block port 25, send a test mail, release firewall for port 25 again
      $ iptables -t filter -I OUTPUT -p tcp --dport 25 -j DROP
      $ send a test mail via mail server, you will see a blocked queue!
      $ iptables -t filter -D OUTPUT -p tcp --dport 25 -j DROP

    - sendmail (MTA, SMTP mail server)
      Service start and stop
      $ service sendmail restart
      $ /etc/init.d/sendmail stop

      configuration & database
      $ ls /etc/mail/*      	              <--- NOT /etc/sendmail/* !

      Smarthost(SMTP-Relay) configuration
      $ vi /etc/mail/sendmail.mc
      mc file can be used, but much easy with .cf file, therefore
      do convert sendmail.mc to sendmail.cf by m4-macro-processor
      $ m4 sendmail.mc > sendmail.cf
      $ vi /etc/mail/sendmail.cf
      | # "Smart" relay host (may be null)
      | DSmailrelay.xxxx.org
      $ /etc/init.d/sendmail reload

      access, domaintable, virtusertable need to convert *.db by makemap
      $ vi /etc/mail/access
      | Connect:localhost.localdomain  RELAY
      | Connect:localhost              RELAY
      | Connect:127.0.0.1	         	 RELAY
      | 192.168.50                     RELAY     (192.168.50.0-192.168.50.255)*
      | spammer.net	 		 		 REJECT
      | die-guten.spammer.net          OK
      | spammer@example.net            550 Wir wollen Eure Werbung nicht
      | spammer.net	 		 		 REJECT
      $ makemap hash /etc/mail/access.db < /etc/mail/access
      $ killall -HUP sendmail

      Simple way to use sendmail
      $ cat /tmp/email.txt
      | bla bla bla
      $ sendmail user@example.com < /tmp/email.txt
      $ sendmail -q   : veranlasst Senden von Mails
      $ sendmail -bq  : list of mail queue
      $ sendmail -bt  : test option

      $ /var/log/mail.log  	   : sendmail-server-related log protocol
      $ /var/spool/mqueue        : list of queue

    - Postfix (MTA, SMTP mail server)
      modular, gut gepflegt, juengster (MTA)

      Service start and stop
      $ /etc/init.d/postfix start

      Smarthost(SMTP-Relay) configuration
      $ vi /etc/postfix/master.cf   : configuration for runing postfix in a chroot jail
      $ vi /etc/postfix/main.cf     : generally main configuration
      | relayhost = mail.linux.net
      | mynetwork = 127.0.0.0/8 192.168.100.0/24 172.16.0.0/28
      | myorigin = luna.edu                : @luna.edu will be appended to username
      | mydomain = local.domain            : defines domain
      | mydestination = lpic1.de, lpic2.de, localhost, hash:/etc/postfix/moredomains
      | virtual_alias_domains = example.com, anotherexample.com
      | virtual_alias_domains = hash:/etc/postfix/virtual
      |
      | // GMX relay case!
      | alias_database = hash:/etc/aliases
      | inet_interfaces = loopback-only
      | mailbox_size_limit = 0
      | relayhost = mail.gmx.net:587
      | sender_canonical_maps = hash:/etc/postfix/sender_canonical
      | smtp_generic_maps = hash:/etc/postfix/generic
      | smtp_sasl_auth_enable = yes
      | smtp_sasl_password_maps = hash:/etc/postfix/sasl_password
      | smtp_sasl_security_options = noanonymous
      | smtp_tls_security_level = encrypt
      |

      $ /etc/init.d/postfix restart

      $ mailq   : management of postfix mail queue.

      $ /var/log/mail.log        : postfix-server-related log protocol
      $ /var/spool/postfix/*     : list of queue
      $ /var/spool/mail/*        : or list of queue

    - Exim (MTA, SMTP mail server)
      Expermental Internet Mailer
      Main Configuration via two ways
      $ dpkg-reconfigure exim4-config          : reconfigure already installed package
      $ vi /etc/exim4/update-exim4.conf.conf   : or classical way
      | dc_eximcofig_configtype='smarthost'
      | dc_local_interfaces='127.0.0.1 ; ::1 ; 192.168.50.55'
      | dc_relay_nets='192.168.50.0/24'
      | dc_smarthost='kang.linux.net'
      | dc_localdelivery='mail_spool'
      $ update-exim4.conf

      @ Fedora and CentOS specific
      $ vi /etc/exim4/exim.conf
      | //smarthost with SMTP server
      | dnslookup:
      |    driver = dnslookup
      |    domains = ! +local_domains
      |    tranport = remote_smtp
      |    ignore_target_hosts = 0.0.0.0 : 127.0.0.0/8
      |    no_more
      | //smarthost for external e-mail as a router
      | smarthost:
      |    driver = manualroute
      |    domains = ! +local_domains
      |    tranport = remote_smtp
      |    route_data = kang.linux.net
      |    no_more
      | //functionality for SMTP-relay
      | domainlist local_domains = kang.linux.net
      | domainlist relay_to_domains = mail.com : mymail.com
      | hostlist relay_from_hosts = kang.linux.net
      | //access and accept from one of functionality
      | accept hosts    = +relay_from_hosts

      $ /var/log/exim4/mainlog
      $ /var/log/exim4/paniclog
      $ /var/log/exim4/rejectlog   <--- handeln, als spam-verteiler zu missbrauchen?

      $ /var/spool/exim4/*  	     : list of queue

    - Procmail (MDA)
      a mail delivery agent (MDA), can sort incoming mail into various directories
      and filter out spam messages.

      $ vi ~/.procmailrc       : for single user
      $ vi /etc/procmailrc     : main configuration
      | PATH=$HOME/bin:/usr/bin:/usr/ucb:/bin:/usr/local/bin:.
      | MAILDIR=$HOME/Mail
      | DEFAULT=$HOME/MBox
      | LOGFILE=$HOME/from
      | LOCKFILE=$HOME/.lockmail
      |
      | //Filtering with a recipes
      | :0
      | * ^Subject:.*Viagra.*           <- '.*' is a joker
      | /dev/null                       <- will delete
      |
      | :0
      | * ^Subject:.*Bewerbung.*
      | ! $DELIVER +Bewerbungen         <- E-mail will move to this directory
      |
      | :0 c                            <- Um eine Kopie auf dem Server zu behalten
      | * ^Subject:.*Bewerbung.*
      | ! personalabteilung@linux.net   <- and forward to here
      |
      | :0
      | * ^To: mailinglist@linux.net
      | ! `cat mailingliste.txt`        <- txt datei enthält die mailing list
      |
      | :0
      | * ^From:.*@schlecht.com
      | ! $DELIVER +Extern              <- will move to external directory
      |
      | :0 w                            <- will pass along all other mail
      | * !^FROM_DAEMON                 <- not from this e-mail daemaon
      | ! weiterleitung@neu.com         <- forward to this e-mail
      | :0 w                            <- unbedingt die Fehlermeldungen von
      | /dev/null                       <- den Mail-Delivery-Systemen ausnehmen

    - Mbox and Maildir formats
      a generic term for a family of related file storage formats used for holding
      collections of email messages, consider two stucture for storage format

      Mbox     : historical, single, very fast
      Maildir  : directory structure, use program "maildirmake", have sub-structure

      Under this maildir directory by default 3more directories exist:
      tpm - short time E-mail
      cur - read E-mail
      new - noch nicht gelesen

    - dovecot (MDA, POP and IMAP server)
      open-source IMAP and POP3 server
      dovecot support MBox, Maildir, and also own DBox format

      $ apt-get intsall dovecot-common

      $ dovecot -n     : check current status of configuration
      $ dovecot -a     : list of default configuraiton

      Dovecot authentication
      $ vi /etc/dovecot/dovecot.conf
      | protocols: imap imaps pop3 pop3s
      | disable_plaintext_auth: no     <--- unauthentificate user and password possible
      | disable_plaintext_auth: yes    <--- unauthentificate user and password impossible
      |
      | auth default:
      |   passdb:
      |     driver: pam
      |   userdb:
      |     driver: passwd
      |
      | mail_location = maildir:~/Maildir   : tell Dovecot to use this directory

      $ vi /etc/pam.d/dovecot
      | auth      required    pam_unix.so    nullok
      | account   required    pam_unix.so

      Service start
      $ /etc/init.d/dovecot start
      $ chkconfig dovecot on
      $ chkconfig --list dovecot

    - courier (MDA, POP and IMAP server)
      an integrated mail/groupware server, such as ESMTP, IMAP, POP3, LDAP, SSL, and HTTP
      Courier as a MDA use Maildrop
      Courier as a MTA support Maildir++ format as well

      $ apt-get install courier-authdaemon courier-maildrop courier-imap courier-pop
      | courier-authdaemon      : for authentification
      | courier-maildrop        : for MDA function
      | courier-pop-ssl         : for pop3 over ssl
      | courier-imap-ssl        : for imap over ssl
      | courier-*	 	  : and more

      $ vi /etc/courier/pop3d   : main configuration for pop3
      $ vi /etc/courier/imapd   : main configuration for imap
      | ADDRESS = 192.168.0.31  : listen this ip
      | ADDRESS = 0             : listen all server
      | PORT = 143
      | MAXDAEMONS = 40         : To limit the number of simultaneous connections accept
      | IMAPDSTART = YES
      | MAILDIRPATH = Maildir
      $ vi /etc/courier/authdaemonrc
      | Authmodulelist = "authpam" : auth+(custom,cram,userdb,ldap,mysql)

    - other Mail server & tools
      UW-IMAP   : POP,IMAP,NNTP,SMTP
      Cyrus     : POP,IMAP
      Majordomo : mailing list manager (MLM)


<br/><a name="system_security"></a>

### System Security
    ===============================================================================================
    LPIC 212: System Security
    ===============================================================================================
    - Network Address Translation (NAT)
      a method of remapping one IP address space into another by modifying network address
      information in Internet Protocol (IP) datagram packet headers while they are in
      transit across a traffic routing device
      Computer on your network need to be able to run client programs and access the Internet
      but you run no servers that should be accessaible from the Interenet

    - IP forwarding
      allow router function and easy port-scan have to deactivate icmp(ping)
      $ echo 1 > /proc/sys/net/ipv4/ip_forward
      $ echo 1 > /proc/sys/net/ipv4/icmp_echo_ignore_all
      $ vi /etc/sysctl.conf
      | net.ipv4.ip_forward = 1
      | net.ipv4.icmp_echo_ignore_all = 1

    - Protocol and port check (reminder)
      $ cat /etc/services

    - iptables fundamental
      The Linux kernel usually posesses a packet filter framework called netfilter
      a user-space application program that allows a system administrator to configure
      the tables provided by the Linux kernel firewall

      Example ----------------------------------------------------------------------
      | iptables -t nat -A POSTROUTING -s 192.168.0.0/24 -0 eth1 -j MASQUERADE

      Table ------------------------------------------------------------------------
      | filter   : default      INPUT,OUTPUT,FORWARD
      | nat      : for NAT      PREROUTING,POSTROUTING,OUTPUT
      | mangle   : for special  INPUT,OUTPUT,FORWARD,PREROUTING,POSTROUTING

      Kommando ---------------------------------------------------------------------
      | -A   --append  : Append one or more rules to the selected chain
      | -I   --insert  : Insert one or more rules in the selected chain
      | -D   --delete  : Delete one or more rules from the selected chain
      | -R   --replace : Replace a rule in the selected chain
      | -L   --list    : List all rules in the selected chain
      | -F   --flush   : Flush the selected chain (Firewall is offen!)

      chain(ketten, hook points) ---------------------------------------------------
      | INPUT        : Incoming to firewall. packets coming to the local server.
      | OUTPUT       : packets generated locally and going out of the local server.
      | FORWARD      : For packets routed through the local server.
      | PREROUTING   : packets that just arrived at the network interface
      | POSTROUTING  : Just before forwarded packet leaves the machine it passes
      the POSTROUTING and then leaves through the network interface

      Matches ----------------------------------------------------------------------
      | -j, --jump          : specifies the target of the rule
      | -p  --protocol      : tcp,udp,icmp,gre
      | -s  --source        : source, where packet comes in
      | -d  --destination   : destination, where packet goes to
      | -i  --in-interface  : network card comes in
      | -o  --out-interface : network card goes out
      | -m  --match         : time,state
      | --state             : INVALID,NEW,ESTABLISHED,RELATED
      | --dport             : port destination, where packet goes to
      | --sport             : port source, where packet comes in

      Ziele ------------------------------------------------------------------------
      | ACCEPT : Let the packet through
      | DROP   : Absorb the packet and forget about it                       (*)
      | REJECT : send back an error packet in response to the matched packet (*)
      | SNAT   : mask to the packet of source, if you have a static IP address
      | DNAT   : mask to destination, for port-forwarding behind firewall
      | MASQUERADE : only be used with dynamically assigned IP (dialup) connections
      | REDIRECT   : alters destination IP address to the machine itself or proxy-server
      | RETURN     : the current packet to stop traveling through the chain

      Options ----------------------------------------------------------------------
      | --to-source <ipaddr>       : Options for SNAT
      | --to-destination <ipaddr>  : Options for DNAT
      | --to-portsfor <port>       : Options for REDIRECT

    - iptables as a security tools
      Securing computer
      $ iptables -F
      $ iptables -t filter -A INPUT -j REJECT
      $ iptables -t filter -A OUTPUT -j REJECT
      $ iptables -t filter -A FORWARD -j REJECT

      Step by step open the gate tcp,udp,http, possibly 12 combinations
      $ iptables -I OUTPUT -p udp --dport 53 -j ACCEPT
      $ ...
      $ iptables -I FORWARD -p tcp -sport http -j ACCEPT

      NAT router do masquerading (nat, post, masque)
      $ iptables -t nat -A POSTROUTING -s 192.168.0.0/24 -o ppp0 -j MASQUERADE

      Static IP
      $ iptables -t nat -A POSTROUTING -s 192.168.0.0/24 -o eth0 -j SNAT \
      --to-source 207.144.6.7

      Port-Forwarding (nat, pre, dnat)
      $ iptables -t nat -A PREROUTING -i ppp0 -p tcp --dport http -j DNAT \
      --to-destination 192.168.0.5:http

      SMTP server as a port forwarding
      $ iptables -t nat -I PREROUTING -i ppp0 -p tcp --dport 25 -j DNAT \
      --to-destination 192.168.40.27:25

      IP-Forwarding by server
      $ iptables -t nat -A PREROUTING -i ppp0 -j DNAT --to-destination 192.168.0.5

      To block all packet, active firewall
      $ iptables -t filter -A INPUT -j DROP

      Router restrict and send back error packet to client
      $ iptables -t filter -A FORWARD -J REJECT

      DDos Attack from internal network, do drop the packet from this IP
      $ iptables -t filter -I INPUT -s 192.168.40.87 -j DROP
      $ route add 192.168.48.87 gw 127.0.0.1

      Let suspect some Brute-Froce-Anfriff via ssh on the server. Normally stay
      the connection through ssh, but will be blocked when ssh access occur more
      than 3 times per hour, then finally drop port 22 according to iptables rule
      $ iptables -A INPUT -m tcp -p tcp --dport 22 -m state \
      --state ESTABLISHED,RELATED -j ACCEPT
      $ iptables -A INPUT -m tcp -p tcp --dport 22 -m state \
      --state NEW -m limit --limit 3/hour --limit-burst 3 -j ACCEPT
      $ iptables -A INPUT -m tcp -p tcp --dport 22 -j DROP

      To allow the filter rules and packet will be accepted in network
      $ iptables -t filter -I INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
      $ iptables -t filter -A INPUT -m state --stat ESTABLISHED,RELATED -j ACCEPT

      Saving and restoring firewall rules
      $ iptables-save > fwrules.saved
      $ iptables-restore < fwrules.saved

    - FTP
      FTP client Port 21 ---> FTP Server Port 20

      Active mode  : the client initates the control connection and the server initiates
      the data connection.
      Passive mode : the client initiates both connections.
      the client is behind a firewall, or A small network is portected
      by strict firewall, use passive mode, it may help you.

      To enable passive FTP connections when firewall(iptables) is used,
      the “ip_conntrack_ftp” module has to be loaded into the firewall and
      connections with the state “related” have to be allowed.

    - vsftp
      is very secure FTP daemon, GPL based FTP server for UNIX systems, including Linux
      It supports IPv6 and SSL.

      $ vi /etc/vsftpd.conf
      | listen=NO
      | listen_ipv6=YES
      | write_enable=YES
      | local_enable=YES          : to allow user on server
      | chroot_local_user=YES     : allow work only in home (*)
      | xferlog_enable=YES        : active log protocol
      | anonymous_enable=YES
      | anon_root=/var/ftp/pub
      | anon_upload_enable=YES
      | anon_mkdir_write_enable=YES
      | anon_world_readable_only=NO
      | rsa_cert_file=/etc/ssl/private/vsftpd.pem  :  for rsa authentification

      Be careful for permission of anonymous directory
      $ chmod 777 /var/ftp/pub -R

      vsftp server practice
      $ useradd --home /var/ftp --shell /bin/false ftp
      $ mkdir -p --mode 733 /var/ftp/pub/incoming
      $ vi /etc/inetd.conf
      |
      | ftp  stream  tcp  nowait  root  /usr/sbin/tcpd  /usr/sbin/vsftpd
      |
      $ killall -HUP inetd
      $ kill -HUP $(cat /var/run/inetd.pid)

    - Pure-FTPd
      Pure-FTPd doesn’t read any configuration file (except for LDAP and SQL when used).
      Instead, it uses command-line options. A wrapper is provided which reads
      a configuration file and starts Pure-FTPd
      $ ls /usr/sbin/pure*
      | pure-ftpd                 : pure-ftp server program
      | pure-ftpd-control         : Perl script to start and stop the server
      | pure-ftpd-virtualchroot   : start with chroot environment
      | pure-ftpd-wrapper         : generate command to be used in init-script
      | pure-ftpwho               : monitor current usage of ftp server

      important Pure-FTPd command line options
      $ pure-ftpd &
      $ pure-ftpd -4 -B
      $ pure-ftpd -S 42               : on a non-standard port
      $ pure-ftpd -S 192.168.0.42,21  : to be reachable on only these addresses,
      $ pure-ftpd -c 50 &             : to limit simultaneous connections
      -6 --ipv6only	      : allow ipv6
      -a --chrooteveryone   : locks itself into chroot jail except for root
      -B --daemonize        : start as daemon
      -c --maxclientsnumber :
      -C --maxclientsperip  : max number of connection from IP address
      -d --verboselog       : log protocol
      -D --displayfotfiles  : show hidden files
      -N --natmode          : NAT firewall active, difficult to access!
      -Y --tls              : 0=deactive, 1=accept, upto 3.
      -e --anonymousonly
      -E --noanonymous
      -i --anonymouscanupload
      -M --anonymouscancreatedirs

      pure-FTP server practice
      $ useradd --home /var/ftp --shell /bin/false ftp
      $ mkdir -p --mode 555 /var/ftp
      $ mkdir -p --mode 555 /var/ftp/pub
      $ mkdir -p --mode 755 /var/ftp/pub/incoming
      $ chown -R ftp:ftp /var/ftp/
      $ vi /etc/inetd.conf
      |
      | ftp  stream  tcp  nowait  root  /usr/sbin/tcpd  /usr/sbin/pure-ftpd -e
      |
      $ killall -HUP inetd
      $ kill -HUP $(cat /var/run/inetd.pid)

    - ProFTPD
      Professional configurable, secure file transfer protocol server.
      For graphical proftpd, use gadmin-proftpd for configuration or below classic way
      $ cd /etc/proftpd
      $ vi /etc/proftpd.conf

    - SSH client
      OpenSSH-client with version 2
      $ ssh -l kang -X server
      $ ssh -p 22 example.com
      $ ssh -o Port=2222 example.com
      $ SSH -2 kang@example.com    : force login with version 2
      $ SSH -XC server	     : -C enables compression
      $ vi /etc/ssh/ssh_confg      : care not *.config but _config

      SSH server can connect by root, however please do not access directly with root
      because somebody with root password but no other password can then break computer

      graphical ssh client : tsclient or PuTTy

    - SSH tunneling
      $ ssh dst -L 4711:currentserver:port
      local port 4711 direct to currentserver:port, then will connect to dst, securely

      Senario 1. -----------------------------------------------------------------
      office_pc  : private IP 192.168.0.101
      ssh_server : public IP 147.46.101.102
      server connection through port 3030 will redirect to office_pc 22 port
      @ server
      $ ssh kang@147.46.101.102 -R 3030:localhost:22
      here client(window) is at home, now client will connect office-pc by ssh-server
      it is also possible to connect 3030 port of ssh-server from window port 4040
      @ client(window)
      $ plink kang@147.46.101.102 -L 4040:localhost:3030

      Senario 2. -----------------------------------------------------------------
      Connecting to a database behind a firewall
      mysql client            :                                    127.0.0.1:13306
      Router with firewall  	: 101.102.103.104  192.168.0.1
      mysql server            :                  192.168.0.10:22   127.0.0.1:3306
      First open the tunnel by ssh with the "-L" option
      client$ ssh -L 13306:localhost:3306 kang@101.102.103.104
      client$ mysql -P 13306
      You are now connected to the remote MySQL server without the need to enable
      SQL connections over the network

      Senario 3. -----------------------------------------------------------------
      Remote port forwarding
      mysql client            :  192.168.0.10:22   127.0.0.1:13306
      mysql server            :  101.102.103.104   127.0.0.1:3306
      ssh tunnel is started on server to client with "-R" option
      client$ ssh -R 3306:localhost:13306 kang@101.102.103.104
      Then the mysql client can connect on localhost:13306 and gets a connection with
      the mysql server on port 3306 through the SSH tunnel

      SSH tunnel + GPG
      Specially care about SSH and GPG encrption for a tunneled SSH connection
      If you have SMTP and IMAP via a tunneled SSH to your email SERVER.
      Why might you still want to use GPG encryption for your emails on top of the
      encryption provided by SSH. The SSH tunnel reaches only as far as the email server,
      GPG encrypts data on all computers all the way to or from your email correspondents

    - SSH server (sshd)
      OpenSSH-server with version 2
      $ /etc/rc.d/sshd start

      $ vi /etc/ssh/sshd_confg               : care not *.config but _config
      | Port   22
      | Protocol   2,1                       : version 2 and 1
      | ListenAddress   192.168.0.58
      | PermitRootLogin  no                  : (*)  AllowRootLogin is in SSH client!
      | AllowUsers   kang
      | DenyUsers   Park
      | AllowGroups   Musician
      | PasswordAuthentication   no          : no login with authentication

      Explanation of configuration's option
      | PermitRootLogin         : yes,no,without-password,forced-commands-only
      | PubKeyAuthentication    : whether public key authentication is allowed.
      | PasswordAuthentication  : whether password authentication is allowed

      If you want to fully disable password based logins, following settings should be set
      | PasswordAuthentication  no
      | ChallengeResponseAuthentication no
      | UsePAM no

      Der Zufgriff auf SSH kann auch mit den Dateien gesteuert werden.
      /etc/hosts.allow         : hosts.allow Vorrang vor der hosts.deny
      /etc/hosts.deny          : deny hosts list
      /etc/nologin             : only root is allowed to connect
      /etc/ssh/ssh_known_hosts : direct connect to public hosts by RSA
      /etc/sshrc               : Commands in this file are executed by ssh

      $ vi /etc/hosts.allow
      | sshd : 192.168.1.
      | sshd : 192.168.1.0/255.255.255.0

    - SSH public key technic
      Authentifizierung der Server mit Schluesseln dsa oder rsa
      server $ ssh-keygen -t dsa
      server $ /etc/ssh/ssh_host_rsa_key and ssh_host_rsa_key.pub
      server $ /etc/ssh/ssh_host_dsa_key and ssh_host_dsa_key.pub
      server $ scp ssh_host_dsa_key.pub kang@cleient.company.com:~/
      client $ cat ssh_host_dsa_key.pub >> /etc/ssh/ssh_kown_hosts

      ssh_host_dsa(rsa)_key has a permission as 0666(-rw-rw-rw-) because of public
      known_hosts is located in client, and is generated at server, contains server info.

      Wie kann man ohne Eingabe eines Passwort bei ein Server anmelden?
      server $ ssh-keygen -t dsa
      server $ ssh-keygen -t ras
      server $ ~/.ssh/id_rsa and id_rsa.pub will be generated
      server $ ~/.ssh/id_dsa and id_dsa.pub will be generated
      server ~/.ssh$ cp id_dsa.pub temp1
      server ~/.ssh$ cp id_rsa.pub temp2
      server $ scp temp? kang@111.222.333.444:~/.ssh/
      server $ ssh -l kang 111.222.333.444
      client $ cat .ssh/tmp1 >> .ssh/authorized_keys
      client $ cat .ssh/tmp2 >> .ssh/authorized_keys

      Or one can easily same thing with ssh-copy-id
      Oeffentlich Schluessel fuer Authentifizierung zum Zielrechner Kopieren
      server $ ssh-copy-id kang@111.222.333.444
      client $ "NOthing to do"

    - SSH tools
      ssh-agent : in memory protect private key, start in the beginning of an X-session
      ssh-add   : add private key to ssh-agent, reads the contents of ~/.ssh/id_rsa(id_dsa)

      $ ssh-add        : without any option
      $ ssh-agent -l   : list of keys
      -d   : delete a key
      -D   : delete all keys
      -s   : list of keys in smartcards
      -e   : delete keys in smartcards
      -x   : sperrt den Agent
      -X   : entsperrt den Agent

    - Security Institution
      Bugtraq : moderated mailing-list at securityfocus.com for discussion of security
      CERT    : a center of Internet security expertise, publish security alerts
      CIAC    : U.S. Department of Energy’s Computer Incident Advisory Capability.

    - Security tools
      telnet ----------------------------------------------------------------------------
      an application layer protocol used on the Internet or local area networks to
      provide a bidirectional interactive text-oriented communication facility using
      a virtual terminal connection
      $ telnet  impa.lpic.de  143

      netcat (nc) -----------------------------------------------------------------------
      Utility for reading from and writing to network connections using TCP or UDP
      $ netcat web.lpic.de 4711
      $ netcat -l 25
      $ netcat system1
      $ netcat -vz localhost 75-85

      To test the correlation between two computers without firewall
      $ server1> nc -l 4444
      $ server2> nc server1.com 4444

      nmap ------------------------------------------------------------------------------
      A network port scanner and service detector offering stealth SYN scan,
      ping sweep, FTP bounce, UDP scan and operating system
      Nmap's best-known features is remote OS detection using TCP/IP stack fingerprinting
      use (-p 1-65535) in the range or (-p -) to scan all available ports
      $ nmap -sP 192.168.7.0/24           : general Ping Sweeping
      $ nmap -sT 192.168.7.12             : general TCP-Port Scanning
      Welche Ports lauscht aktuell in mein System?
      $ nmap localhost
      $ nmap localhost -p 80

      Q. you want to know, whether remote access via TCP-port 2704 through firewall is
      possible? which program is optimal for this task?
      A. netcat
      Q. What program uses local system calls to locate local ports that are currentl open?
      A. netstat is a scanner just for local ports
      nmap & nessus is a scanner for local ports and also for other computers in networks

    - Security advanced
      IDS (Intrusion Detection System)
      is based on a specific pattern to detect the attacker's intrusion

      IPS (Intrusion Prevention System)
      is an active defence to block the attacs detected

      Snort (IDS/IPS, NIDS)
      is an open source network intrusion detection system (NIDS)
      to detect a variety of attacks, such as buffer overflows, stealth port scans,
      CGI attacks, SMB probes, OS fingerprinting attempts and much more.
      listen direct to the Network card, while fail2ban is not.
      Snort System can monitor traffic on the local network, since the network is well
      portected by the NAT router, outside attacks against the local(samba, nfs, ssh)
      are unlikely to reach the protected network, so external attacks won't be detected

      OpenVAS (Open Vulnerability Assessment System)
      is a framework of several tools offering a vulnerability management solution
      over 30,000 in total

      fail2ban (IPS)
      Generally Fail2Ban is then used to update firewall rules to reject the IP
      addresses for a specified amount of time

    - fail2ban
      scans log files (e.g. /var/log/apache/error_log) and bans IPs that show
      the malicious signs too many password failures, seeking for exploits, etc.

      $ atp-get install fail2ban
      $ cd /etc/fail2ban
      | action.d        : contain data related fail2ban configuration
      | fail2ban.d      : contain data related fail2ban configuration
      | filter.d
      | jail.d          : filter definition and actions to take when filter is matched

      $ vi /etc/fail2ban/fail2ban.conf
      | loglevel = 3
      | #          1 = ERROR
      | #          2 = WARN
      | #          3 = INFO
      | #          4 = DEBUG

      $ vi /etc/fail2ban/jail.conf
      | [ssh]
      | enabled = true
      | port = ssh
      | filter = sshd
      | logpath = /var/log/auth.log
      | maxretry = 3
      | ...
      | bantime  = 600     : the number of seconds that a host is banned(forbidden)
      | ...

      Test via few different ways
      $ iptables -L
      $ iptables-save | grep fail2ban
      $ fail2ban-client status ssh
      $ cat /var/log/fail2ban.log
      | ... fail2ban.actions: WARNING [ssh] Ban 79.192.24.193
      | ... fail2ban.actions: WARNING [ssh] Unban 79.192.24.193
      | ... fail2ban.actions: WARNING [ssh] Ban 79.192.24.193
      so IP 79.192.24.193 looks pretty much problematic!

    - OpenVPN
      VPN(Virtual Private Network) allows you to connect remote networks securely
      over an insecure connection, e.g. public internet. The network connection acts a
      physical connection, but actually may traverse many physical networks and system.
      That is why we call "virtual"
      Solution and VPN implementations include IPSEC, VPND, SSH, Cisco Routers,
      SSL/TLS(Secure Sockets Layer/Transport Layer Security) as a cryptographic protocol
      Port 1194

      $ yum install openvpn easy-rsa   @ Red-Hat need to download
      $ apt-get install openvpn        @ Debian already integraged

      $ mkdir /etc/openvpn/cert
      $ cp /usr/share/easy-rsa/* /etc/openvpn/cert/
      $ vi /etc/openvpn/cert/vars
      | export KEY_COUNTRY="DE"
      | export KEY_PROVINCE="Berlin"
      | export KEY_CITY="Berlin"
      | export KEY_ORG="KDH"
      | export KEY_EMAIL="donghee.kang@gmail.com"
      $ source vars                   : load environment and create directory "keys"
      $ ./build-ca                    : certificate authentication "ca.cert"
      $ ./build-key-server vpn-server : server key for "vpn-server" crt & key
      $ ./build-key client-01         : client key for "client-01" crt & key
      $ ./build-dh	                : create Diffie-Hellman parameter
      $ ./clean-all	                : delete old keys and CAs

      Example file for server configuration
      $ cd /etc/openvpn/cert/
      $ cp /usr/share/doc/openvpn/examples/sample-config-files/server.conf.gz .
      $ gunzip ./server.conf.gz
      $ vi server.conf
      | ...

      Point-to-point (peer-to-peer) connection ----------------------------------
      @ computer 1
      $ modprobe tun
      $ cd /etc/openvpn
      $ openvpn --genkey --secret static.key
      $ vi p2p_vpn1.conf
      | mode p2p
      | remote computer1.linux.net 1194
      | proto udp
      | dev tun                               <--tun/tap/null
      | ifconfig 10.111.0.2 10.111.0.1        <--Umkehrung der Reihenfolge
      | secret static.key
      | ping 5
      | ping-restart 120
      | status openvpn-status.log	            <- route table & currently accessed clients
      | verb 3
      | mute 20
      @ computer 2
      $ vi p2p_vpn2.conf
      | mode p2p
      | remote computer1.linux.net 1194
      | proto udp
      | dev tun
      | ifconfig 10.111.0.1 10.111.0.2        <--Umkehrung der Reihenfolge
      | secret static.key
      | ping 5
      | ping-restart 120
      | ping-timer-rem
      | verb 3
      | mute 20
      @ both computer
      $ /etc/init.d/openvpn restart

      Server and client ---------------------------------------------------------
      @ Server
      $ openssl dhparam -out dh1024.pem 1024
      $ vi /etc/openvpn/cert/server.conf
      | port 1194
      | proto udp
      | dev tun
      | ca ca.crt
      | cert vpn-server.crt
      | key vpn-server.key
      | dh dh1024.pem
      | server 172.20.0.0 255.255.255.0
      | ifconfig-pool-persist ipp.txt
      | push "route 192.168.50.0 255.255.255.0"
      | push "dhcp-option DNS 192.168.50.1"
      | client-to-client
      | keepalive 10 120
      | comp-lzo
      | status openvpn-status.log
      | verb 3
      @ client
      $ vi /etc/openvpn/cert/client.conf
      | client
      | dev tun
      | proto udp
      | remote server.linux.net 1194
      | ca ca.crt
      | cert client-01.crt
      | key client-01.key
      | comp-lzo
      | verb 3
      @ both computer
      $ /etc/init.d/openvpn restart

    - IPSec
      (Internet Protocol Security) provides encryption and authentication at IP level.
      IPsec can run on routers, firewall machines, and application servers.
      ESP(Encapsulating Security Payload), AH(Autehntication Header) are standard protocol
      IKE(Internet Key Exchange) is used to handle tunneling as a higher level protocol.
      In configuraiton file, you should find left and right node for IPsec connection
