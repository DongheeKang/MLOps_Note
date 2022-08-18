# Linux Administration: Fundamental


## Contents
  * [System Architecture](#System_Architecture)
  * [Linux Installation and Package Management](#Installation)
  * [GNU and Unix Commands I](#unix_command)
  * [Devices, Linux Filesystems, Filesystem Hierarchy Standard](#Filesystems)
  * [Shells, Scripting and Data Management](#Data_management)
  * [User Interfaces and Desktops](#user_interfaces)
  * [Administrative Tasks](#administrative_tasks)
  * [Essential System Services](#system_services)
  * [Networking Fundamental](#networking_fundamental)  
  * [Security](#security)  


<br/><a name="System_Architecture"></a>

### Linux System
    ===============================================================================================
    LPIC 101: System Architecture
    ===============================================================================================
    - Alles in Unix ist eine Datei!

    - 1024 Cylinder Limit, Some BIOS can not read over the 1024th cylinder
      drive can have up to 4 primary partitions and up to 512 logical
      SCSI /dev/sda (disk1), /dev/sdb (disk2), /dev/sda1 (first partition on disk 1)
      Logical partitions just start from 5 (/dev/sda5)

    - A server would keep all executable files for the workstations and share via NFS.
      This was very used when workstation storage was an issue.
      It also helped distributing updates

    - senario (old fashion)
      . /boot 100MB, keeps kernel within the 1024cylinder
      . swap 1GB
      . / 500MB, minimum
      . /usr 4GB, all executables are shared to workstations via readonly NFS
      . /var 2GB, keeping log files in their own partition helps making sure that their size will not affect the system
      . /tmp 500MB, same as /var
      . /home 90GB, used to provide the /home directory to all the users

    - LVM special
      . dynamically change the size of logical volumes
      . dynamically create/delete logical volueme
      . create snapshots

    - BIOS
      1. Boot device order
      2. Hardware configuration
      3. System's hostname

    - Kernel
      uname       : Information ueber den verwendeten Kernel
      lsmod       : geladene Module anzeigen, Quelle ist /proc/modules
      modinfo     : Naehere Informationnen ueber ein Modul erhalten
      insmod      : kompletter Pfad Modul laden
      rmmod       : Modul entladen
      modprobe    : Laden von Modulen (Ohne Pfadlangabe) inklusive Abhaengigkeiten
      modprobe -r : Entladen von Moulen inclusive Abhangigkeiten geht nur
                  : fuer unbenutzte Module Modulaabhaengigkeiten,
                  : stehen in /lib/modules/<Kernelversion>/modules.dep
                  : Erstellen dieser Datei mit depmod
      depmod      : generiert Datei modules.dep

      $ cat /proc/modules        : running module by current kernel
      $ cat /etc/modprob.d       : configuration data for modprod

      $ uname -r                 : print of current version of kernel
      $ lsmod                    : print of loaded modules
      $ lsmod | grep xfs	   : show info. about loaded xfs modules
      $ modinfo          	   : print of loaded modules in detail (with su)
      $ modinfo -a module	   : send an E-mail to autor of kernel module
      $ modprobe xfs             : load xfs module
      $ modprobe -r kvm	   : extract kvm module
      $ depmod -n 		   : wie depmod die Datei modules.dep konfig.

    - Kernel location where?
      Module des Kernels
      $ cat /lib/modules/<kernel version>/xxx
      $ cat /lib/modulee/<kernel version>/kernel/drivers/acpi/video.ko
      Kernel-Quelldateien
      $ /usr/src/linux-2.6.11.4-21.9

    - /proc
      Prozessdateisystem, runtime kernel module infromation
      $ cat /proc/cmdline        : from the bootloader to the kernel
      $ cat /proc/interrupts     : Info. about IRQs
      $ cat /proc/ioports        : Info. about I/O ports

    - IRQs serial port
      2 keyboard
      3 RS-232
      4 RS-232
      8 Real time clock
      16 modern device

    - /sys
      benutzer Treibermodell des laufendern Kernels
      dynamische generiert wird

    - PCI-Bus info
      $ lspci -v oder -vvv
      Device IRQ information and vernder information

    - USB info
      $ lsusb -t          : USB hiracy
      usb-uhci.o 	    : for USB 1.0
      usb-ohci.o          : for USB 1.0
      usb-ehci.o	    : for USB 2.0
      usb-storage.o	    : for Memory stick
      hid.o 		    : for USB Maus, keyboard
      usbmgr und hotplug  : USB daemon
      hald                : hardware abstraction layer for hotplug
      dbus 		    : daemon for moveable device

    - /dev
      Primaere patition ist max. 4 moeglich in ein Disk(Platte)
      # /dev/sr0       :  ROM-1.Zal oder 1.Optisches
      $ sudo fdisk -l
      $ parted -l

      udev : To manage the /dev tree

      /dev/mapper     : hold device files related to LVM and RAID configuration

    - /etc
      Configuration files unique to the system needed at boot
      /etc must be an ordinary directrory on the root partition, not on a separate
      partition, if you have / in a partition and /etc in another partition,
      then system won't be boot!

    - /boot
      Konfigurationsdatei fuer GRUB Legacy :
      $ /boot/grub/menu.lst (bei RedHat grub.conf)
      Jetzt mit grub2, steht alles Konfigurationsdatei unter
      $ /etc/grub.d/       (contents)
      $ /etc/default/grub  (von Hand bearbeitbar)

    - Forget password as administrator !
      In the processing of bootloader with grub, change ro single as init
      # linux=....UUID=xxxxx ro single
      # linux=....UUID=xxxxx init=/bin/bash
      $ mount -o rmount,rw /dev/sda1
      $ passwd
      one can without root passwd boot and then set passwd after mounting

    - Daemon
      Ein Daemon in Linux ist ein Hintergrunddienst, dem kein Terminal
      als Ein/Ausgabeeinehit zugeordnet ist.

    - 3 Wesentliche Initialisierungsverfahren fuer den Linux-Systemstart:
      SysVinit (wird kamm mehr benutzt)                 : linux system
      Upstart  (Ubuntu, in zwischen night mehr aktuell) : linux system
      Systemd  (inzwischen von allen grossen Distributionen eingesetzt)

    - initrd (init ram disk)
      In der initrd befinden sich Kernel-module, die Linux beim Boot-vorgang benoetigt.
      Die initrd wird von Kernel beim System start in den Hauptspeicher geladen
      Die initrd muss bei jedem Kernel-Update nue erstellt werden

    - Hauptkonfigurationsdatei fuer SysVinit:
      $ cat /etc/inittab       : to-do list of init process after system start
      $ cd /etc/rc.d/          : data of init process
      $ cd /etc/init.d/        : data of init process
      $ /etc/init.d/atd start  : at daemon start
      init process
      .test of file system stucture
      .mount file system
      .daemon start
      .login console
      .after login signal, load shell
      If it is something wrong with inittab
      $ telinit q    : read configuration, go back previous inittab without
      tell init to re-examine the /etc/inittab

    - How do I know whether SysVinit or Systemd are used in starting system?
      $ ps -A
      $ pstree

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

      $ init 3   : wechselt in den runlevel 3
      $ init 1   : emergency situtation, no users connect

    - shutdown
      $ shutdown -f -r now      : reboot send a message
      $ init 6 		  		  : reboot without message
      $ shutdown -h now
      -r reboot
      -k konsolenmedung senden
      -f kein fsck
      -F fsck
      -h halt
      -c cancel scheduled shutdown
      $ echo "System will be shut down" | wall : send some message to user

    - Systemd
      wichtigster Befehl ist "systemctl".
      $ Systemctl start|stop|status <Subsystem>
      $ Systemctl isolate multi-user.target    : runlevel 3 no graphic
      $ Systemctl isolate graphical.target     : runlevel 5 with graphic
      $ Systemctl isolate rescue.target        : runlevel 1 rescure single mode
      $ systemctl set-default multi-user.target: legt der Runlevel 3 als Standard fest
      $ systemctl get-default                  : get info. about current default runlevel
      $ systemctl enable/disable <service>     : sorgt fuer Ausfuehrung eines Dienstes
      $ ls -l /etc/systemd/system/
      wenn Sie systemd statt SysVinit, dann kommt journald & journalctl

      At kernel command line you can change default as rescue mode by grub
      > systemd.service=rescue.target


<br/><a name="Installation"></a>

### Linux Installation and Package Management
    ===============================================================================================
    LPIC 102: Linux Installation and Package Management
    ===============================================================================================
    - /usr
      unix system resource - suggest read only

    - /var
      temporally used system related files
      /var/log         : system log files
      /var/spool/mail  : grosser platz empfolen
      /var/named       : DNS zone configuration file
      /var/spool/cron  : cron temporal configuration data
      /var/log/messages  : Hauptprotokolldatei in Linux
      /var/log/syslog    : ausser bei Debian und Derivaten

    - Boot Manager in Master Boot Records (MBR)
      Total 512 Byte, a partition table within the end of MBR
      BIOS -> MBR -> LILO or GRUB(boot loader) is loaded into RAM
      -> Launch Linux Kernel on disk -> Kernel control hardware on disk
      0-440(bootcode)
      446-510(partition table 4primaryx16bytes=64bytes)
      510-512(Sign)

    - Grub legacy
      altes GRUB
      one problem for update 3rd linux in a partition,
      one has to modify kernel update by hand in
      /boot/grub/menu.lst
      ---------------------------------------------------------------------------
      #GRUB booting
      grub> root (hd0,0)
      grub> kernel /vmlinuz-2.4.18-14 ro root=/dev/hda2
      grub> initrd /initrd-2.4.18-14.img
      grub> boot
      ---------------------------------------------------------------------------

    - Grub2
      stark modularisiert und automatisiert
      konfigurationsdatei /boot/grub/grub.cfg (nicht von Hand bearbeiten!)
      weiere Konfigurationsdateien liegt in
      /etc/default/grub
      /etc/grub.d/*
      ----------------------------------------------------------------------------
      #GRUB2 grub.cfg (more complicated and divided accordinlgy by grub.d
      grub2> kernel /boot/vmlinuz-3.10.00327.el7.x86_64 ro root=/dev/hda2 quiet
      grub2> initrd /boot/initramfs-3.10.00327.el7.x86_64.img
      ---------------------------------------------------------------------------

      Wenn Sie einem System manuell einen neuen Kernel hinzugefuegt haben,
      dann muessen Sie
      $ update-grub2

    - Grub vs Grub2
      (hd0,0) <-> (hd0,1)
      /boot/grub/menu.lst <-> /boot/grub/grub.cfg

    - Tip for Grub
      $ grub-mkconfig    : durchsucht alle Festplatten partionen Betriebsssytemen
      und findet sie in GRUB ein, (many OS in a machine)
      $ grub-install /dev/sda  : install grub legacy into the MBR of your first SATA

    - Shared Library : LD_LIBRARY_PATH
      $ ldd /usr/bin                  : print shared library dependencies
      $ ldd /usr/bin/grep             : print shared library dependencies of grep

      $ ldconfig                      : updates the linker cache of shared libraries
      $ ldconfig -p | less            : print the lists of libraries stored in Cache

      Textdatei mit Eintraegen fuer Verzeichnisse, die dynamische Bibliotheken
      enthalten /etc/ld.so.conf bzw Dateien in /etc/ld.so.conf.d/*.conf
      Aus dieser Datei wird mit Hilfe des Kommandos "ldconfig" eine Binaerdatei
      erstellt namens /etc/ld.so.cache
      Diese Binaerdatei wird vom dynamischen Linker benutzt um Biblotheken zu finden

    - dynamic linker/loader shared object
      # ld.so/ld-linux.so

    - Software Paket
      ist in Linux-Distributionen in Paketen organisiert
      Diese Paket enthalten ausser dem eigentlichen Programm meist
      Konfigurationsdateien, Installations- und Deinstallationsskripte,
      generelle Informationen, Dokumentation, und einiges mehr.
      Die Pakete liegen in sogenannten "Repositories" auf Servers im Internet
      (naeturlich auch auf Installationsmedien)

    - Packet verwaltung
      dpkg     : Low-level-Werkzeug fuer die Debian-Paketverwaltung
                beherrscht keine Aufloesung von Abhaengigkeiten
      apt-get  : High-Level-Werkzeug fuer die Debian-Paketverwaltung
                beherrscht die Aufloesung von Abhaengigketen
      apt-get  : Uses package names and obtain them from an external source(CD, ftp, etc...)
      dselect  : Uses an interactive menu (similar to aptitude)
      aptitude : wie apt-get but interactive menu possible

    - dpkg (deb installation package)
      /etc/dpkg/dpkg.cfg                 : not conf but cfg!
      $ dpkg -i <folder/package name>    : install
      $ dpkg -G <folder/package name>    : upgrade
      $ dpkg -r <package name>           : remove
      $ dpkg --purge(-P) <package name>  : purge complete remove
      $ dpkg -l                          : list of installed package
      $ dpkg -l | grep password          : list of installed package
      $ dpkg -L mc                       : List files in a package
      $ dpkg -L apt | grep '^/usr/bin'   : List files in a package
      $ dpkg -S /bin/bash                : Search, welche packet stammt aus?
      $ dpkg -S /usr/bin/find            : Search, welche packet stammt aus?
      $ dpkg -S apt-get                  : Search, a package with specific files
      $ dpkg -s mc		           	   : get status of mc (midnight command)
      $ dpkg -s apt                      : get status information on a package
      $ dpkg -C                          : not completely installed list
      $ dpkg --get-selections 	       : list of all of the installed packages

      /var/log/dpkg.log
      /var/lib/dpkg/info
      /var/lib/dpkg/status
      /var/lib/dpkg/available
      dpkg-reconfigure                   : reconfigure an already installed package

    - APT-GET
      /etc/apt/apt.conf
      /etc/apt/sources.list
      $ apt-cache pkgnames vsftpd
      $ apt-cache search vsftpd
      $ apt-cache show netcat
      $ apt-cache status
      $ apt-get depends
      $ apt-get install <package>
      $ apt-get install packageName --only-upgrade
      $ apt-get install vsftpd=2.3.5-3ubuntu1
      $ apt-get remove <package>
      $ apt-get update
      $ apt-get purge  <package>
      $ apt-get upgrade
      $ apt-get dist-upgrade
      $ aptitude install <package>       : same as apt-get
      $ aptitude 	  	                   : interactive mode

    - Red Hat Package Manager (rpm package)
      /etc/rpm/
      /usr/lib/rpm/rpmrc
      ~/.rpmrc
      $ rpm -i <dependency 1> <dependency 2> <dependency 3> <desired package>
      $ rpm -i 		              (installation)
      $ rpm -U 	                  (installiert auch ohne vorhandenen Vorver.)
      $ rpm -e                      (e-erase)
      $ rpm --force --nodeps        (force and kein test fuer abhaengigkeiten)
      $ rpm -q fortune              (quary)
      $ rpm -qp aaa.rpm             (quary package)
      $ rpm -V samba                (V-verifiy)
      $ rpm -qf /usr/X11R6/startx   (which package contains startx?)
      $ rpm -ql bzip2               (list for bzip2 package)
      $ rpm -qa                     (list all installed rpm package in current system)
      $ rpm -qa | grep apache
      $ rpm -qR                     (requires abfrages, Abhaengigkeit listen)
      $ rpm -qc                     (c-configures, zeigt nur Konfigurations Datei)
      $ rpm -qd                     (d-docfiles, zeigt nur Doku.datei)
      $ rpm -qi                     (i-information in details)
      $ rpm -ql zip                 (Dateien auflisten installierten paket)
      $ rpm -qpl zip.2.2.2.2.2.rpm  (Dateien bis jetzt nicht installierten Paket)
      $ rpm -ivh                    (zuzaetzlich "Hashmarks" ausgeben) new packgae
      $ rpm -Uhv                    (h-hash v-verbose) upgrade or new packgage
      $ rpm -F  --freshen           (installiert nur vorhandenener Vorver.)

      /var/lib/rpm

    - yum
      /etc/yum.conf
      /etc/yum.repos.d
      $ yum search <fortune>
      $ yum install <fortune-mod.i386>
      $ yum remove <fortune-mod>
      $ yum list <package>
      $ yum provides <dataname>
      $ yum upgrade 		      : upgrade and update are identical in yum
      $ yum update <package>        : upgrade and update are identical in yum
      $ yum check-update <package>  : possibility
      $ yum clean <package>
      $ yum list updates
      $ yum list available
      $ yumdownloader               : easy to download

    - alien : a program that converts between Redhat rpm, Debian deb
      $ alien [--to-deb] [--to-rpm] [--to-tgz] [--to-slp] file
      $ alien --to-deb foo1.2.34.i386.rpm


<br/><a name="unix_command"></a>

### GNU and Unix Commands I

    ===============================================================================================
    LPIC 103: GNU and Unix Commands I
    ===============================================================================================
    - Terminal
      $ reset
      $ clear
      $ ctrl + l

    - set & env
      $ set : local
      $ env : global
      $ env TZ=Asia/Tokyo date
      $ env -u myname 'echo $myname'
      $ unset myname
      $ unset PATH
      $ echo $PATH
      $ set -x			# activate debugging
      # your commands go here...
      $ set +x			# stop debugging

      $ A=100       : yes
      $ let A=100   : yes
      $ set A=100   : No just A=100 enough

    - About shell
      a) Login Shell
      /etc/profile     -> Systemweit, erste Konfigurationsdatei, die bei Anmeldung
      -> hier drei sind benutzerbezogen
      .bash_profile    (Centos)
      .bash_login
      .profile         (Debian)

      b) Interactive Shell
      /etc/bash.bashrc -> Systemweit (Debian)
      /etc/bashrc                    (Centos)
      ~/.bashrc       -> benutzerbezogen

    - Shell expands double quotes ("), however single quotes (') are not expanded
      $ echo "$SHELL" '$SHELL'   ->   /bin/bash  $SHELL
      $ echo '$USER'   ->   $USER
      $ echo "$USER"   ->   kang

    - Shell extention
      $ echo $PPID : Parent process ID of the current process
      $ echo $$    :        process ID of the current process

      When passing to a child shell, double quotes are expended before passing command
      (in the parent shell), while single quotes are expended in the child process
      $ bash -c echo 'parent $$ $PPID'
      >
      $ bash -c "echo parent $$ $PPID"
      > parent 23033 23011
      $ bash -c 'echo child $$ $PPID'
      > child 25798 23033
      $ echo "$USER_/this is my user name"
      > /this is my user name
      $ echo "${USER}_/this is my user name"
      > root_/this is my user name

      # Runs a command that replaces the current shell
      $ echo $$
      > 27316
      $ bash
      $ echo $$
      > 27369
      $ exec ls
      > anaconda-ks.cfg clearlooks.tar.gz Desktop install.log set1 um2\
      > bluecurve.tar.gz declare1 icons install.log.syslog um1 umdois
      $ echo $$
      ->27316

    - Echo
      $ echo $HISTSIZE
      $ echo $HISTFILESIZE
      $ echo $PS1 : primaeres Prompt
      $ echo $PS2 : sekundaeres Prompt

    - Ruckgabewert des letzten Kommandos
      $ echo $?

    - kommand wie macht man
      option1 option2 : parameter1,2 des Befehls, parameter0 $0 ist commando
      $ <commando> $1 $2
      $ ba TAB TAB...
      dann vorschlagte Kommandos mit "ba*" angezeigt wird.

    - history
      $ .bash_history
      $ echo $HISTOFILE
      $ history
      his> !100               : re-use command of 100 line
      his> !echo              : re-use echo command used before
      his> !?test             : fueht den letzten Befehl aus, in dem test vorkommt
      his> !!                 : re-use of last command
      his> !n			: re-use of command in line number n
      his> ^string1^string2   : repeat the last command replacing
      with first occurrence of string1 with string2
      his> !xxxxx:s/$$/$PPID/ :
      his> Ctrl + r           : find contents

    - PATH
      Namen der Verzeichnisse, in denen Bash nach Befehlen sucht

    - kommando Ersetzung (cd command substitution)
      $ ls -l $(which passwd)
      Der Befehl in der Klammer wird zusert ausgefuehrt (which passwd)
      und die Ausgabe dieses Befehls (/usr/bin/passwd) wird an der selben Position
      in die Befehlszeile eingefuegt. Das resultierende Kommando ist dann:
      $ ls -l /ust/bin/passwd
      $ ls -l `which passwd`

    - touch
      erzeuge ein leere Datei!
      $ touch newfile

    - date
      $ date "+DATE: %m/%d/%y%nTIME: %H:%M:%S"

    - manpage
      $ man
      > /Jahres
      > n,N,<,>    : keyboard 'n' find contiunue next one

      $ man 1 command   : utilities and commands
      $ man 5 command   : file format and convention

      path of manpage
      $ manpath                 : show all path of manpage
      > /usr/share/man          : classic
      > /usr/local/share/man	  : modern

    - Hilfesysteme in Linux
      1.Manuel Pages (manpages): Klassisches Unix-Hilfesystem
      $ man <command>
      2.Info Pages": Hilfesystem des GNU-Projekts
      $ info apt-get
      3.In viele Befehle eingebante kurzhilfe
      $ <command> --help
      4.kurzhilfe fuer interne Befehle der Bash:
      $ help echo

    - Verwaltung der Kommnados
      $ whatis <command>     : sucht Erklaerung der Befehl, search the whatis database for complete words
      $ whereis <command>    : zeigt Pfade zu Binaer und /oder Konfigurationsdateien, Pfade der Manpages und Pfade zu Programmquellen
      $ which <command>      : zeigt Pfade zu ausfuehrbaren Dateien.
      $ ls -l 'which locate' : list of location indicated by 'which locate'

    - Wenn ich Hilfe brauche unbekannte Programm Name!
      # search the whatis database for strings with apropos
      $ apropos <string>
      $ man -k <string>

    - Process Text Streams Using Filters
      $ ls -li  : Inode show
      $ ls -lS  : sort by Size
      $ ls -lc  : sort by last modification of file status information

      $ cat -n  : number all line
      $ cat -b  : number non-blank line
      $ tac     : wie cat but reverse print

      $ head -n1                    : just first line or "head -1" is same
      $ tail -f /var/log/messages   : -f (follow) "Live" stream

      $ expand    : wandelt Tabulatorzeichen in Leerzeichen um
      $ unexpand  : wandelt Leerzeichen in Tabstopps
      $ expand    : Converts tabs to spaces (usually by default, 1 tab = 8 spaces)
      $ unexpand  : Converts spaces to tab

      # fmt       : a formatter for simplifying and optimizing text files
                    formatiert Text in der angegebenen Spaltenzahl, standarmaesig 75 Zeichen breite
      $ fmt -w 35 text.txt
      pr bereitet Textdateien fuer eine Druckansgabe vor. Es zeigt Zeit, Datum, Seiten
      $ fmt -w 35 text.txt | pr -h "Title" -2

      # nl  : numbers of lines of files
      $ nl data1 > data2  : put number in every line

      # wc : counts of bytes, characers, words and lines of a file
      -c, --bytes
      -m, --chars
      -l, --lines
      -L, --max-line-length
      -w, --words

      $ hexdump /dev/sda | head -n 32 | tail -n 5 : Hexdump(16bit) important for programmer
      $ od -tx(-txC) /etc/passwd

      $ locale : Language and local setting

      # sort    : sort lines of text files alphabetically
      $ sort -n sortiert nach numerisschen, sort reads the number and not the value
            -o outfile
            -r reverse file
      $ sort -nr : displays summary and sorts the result in order of largest to smallest numeric and reversal.

      $ uniq      : removes consecutive duplicate lines
      $ uniq file : reduziert mehrere identische aufeinanderfolgende Zeilen auf eine Zeile

      # split      : splits a file into different groups/files
      $ split -b 11m README README_
      $ split -l 1000 README README_
      $ cat READAME_* > README

      # cut
      $ cut -d'' -f2
      $ cut -d:  -f2
      $ cut -d:  -f1,6 /etc/passwd
            -d Feldtrennzeichen
            -f Feldnummer
      $ cat /etc/passwd | grep user | cut -f1 -d:

      # paste : horizontalles cat, paste together lines on a file into vertical columns
      $ paste file1 file2
      $ paste test1 test2
      >1 	a
      >2 	b
      >3 	s
      $ paste -s test1 test2
      >1 	2 	3
      >a 	b 	s

      # join : horizontalles cat, prints a pair of input lines
      $ join -t : -j 1 file1 file2

      # tr : translate or delete character
      $ cat file | tr -s '[:blank:]' : exchange spaces as one space
      $ cat file | tr -s e           : exchange ee/eee as e
      $ cat file | tr n x            : exchange n as x
      $ cat file | tr -d n           : delete n character in file
      $ cat file | tr a-z A-Z        : exchange small to big
      $ cat file | tr -d [:blank:]   : delete space and tap, normal case use
      $ cat file | tr -d [:space:]   : delete all white space, only one line after this
      $ cat file | tr -d \a          : pipeton aus

      # pr      : Convertsa text file for printing few page printing

    - Perform basic file management
      # cd, ls, pwd, mv, rm
      # wichtig! : Dateien haben Metastrukturen (inodes), in denene alle
      wichitgen Informationen Stehen ueber die jeweiligen Datei.
      (ausser dem Dateinamen) ---- why mv very fast!
      Der Dateiname sthet im Verzeichnis. Das Verzeichnis ist eine
      einfache Tablelle mit Inode-Namenszuordnung.

      $ cp -u    : copy only when source is newer than destination or missing file
      $ cp -p    : preserve ownership and timestamps

      $ mkdir -m 700 aaa
      $ mkdir -p work1/work2/work3/mywork : create all subdirectory
      $ rmdir -p work1/work2/work3/mywork : delete all work1 work2 work3 directory

      $ touch file    : neue Datei
      $ touch -m file : andert nur die letzte Anderungszeit
      $ touch -a file : andert nur die letzte Zugriffszeit
      $ stat file     : Status of this files who access, modify, etc
      # change the access time, below 3 are equivalent!
      $ touch -a
      $ data -r
      $ ls -lu

    - find
      $ find / -name myfile1
      $ find / -name "*.log"
      $ find / -name "sambda*.rpm"
      $ find / -name "samb[a-z]*"
      $ find / -type l     : type symbolic
      $ find / -type d     : type directory
      $ find / -type f     : type regular file
      $ find / -name <whatever> -exec chmod 777 {} \;
      $ find / -name <whatever> | xargs chmod 777

      # after find some options for displaying
      -print
      -print0      : find a file contained null character
      -path
      -d or -f or -l
      -empty
      -executable
      -mtime or -atime
      -maxdepth    : directory maximum depth level

      # for executing
      -exec ls -l {}\;
      -exec cmd {}\;　
      -exec rm -i {} ＼;
      {} will be replaced by the file name
      \ is used to comment out the ";"
      ; is used to end the command

      # example - change from htm to html within the folder.
      $ find /var/www/ -name *.html -exec chmod 500 {} \;
      $ find . -name '*.htm' -exec mv '{}' '{}l' \;

    - tar
      # Tape Archiver
      -c erstellen
      -x entpacken
      -t Archivinhalt auflisten
      $ tar -cvzf backup.tar.bz2 /etc/*
      $ tar -xvzf xm.tar.gz
      $ tar -xvjf xm.tar.bz2
      $ tar -tvzf xm.tar.gz

    - compressing
      $ gzip -9 (--best,slow)      	: best Komprimierte Datei
      $ gzip A.doc                    : A.doc wird geloescht
      $ gzip -c A.doc > A.doc.gz     	: -c A.doc keep and create gz
      $ gzip -d A.doc.gz   		: decompress
      $ gunzip A.doc.gz               : decompress
      $ zcat A.doc.gz         	: anzeigen list (gzip -c -d)
      $ bzip2 B.doc                   : B.doc wird geloescht
      $ bzip2 -c B.doc > B.doc.bz2    : -c B.doc keep and create bz2
      $ bzip2 -d B.doc.bz2		: decompress
      $ bunzip2 B.doc.bz2             : decompress
      $ bzcat A.doc.bz2        	: anzeigen list (bzip2 -c -d)
      $ xz -z  			: compress
      $ xz -d A.xz 			: decompress

    - cpio
      Archive copy and backup
      $ cpio -i			   	    : extract
      $ cpio -o 				    : create
      $ cpio -d 				    : directory
      $ cpio -p  				    : pass through
      $ find /etc | cpio -ov > /tmp/etc.cpio      :
      $ find /home -name *.ods | cpio -pd /backup : copy ods file into /backup
      $ cpio -ocv > backup.cpio                   : backup all into cpio
      $ cpio -it < backup.cpio                    : print contents backup.cpio
      $ cpio -iv < backup.cpio                    : restore complete cpio
      $ cpio -iv "*file" < backup.cpio	    : restore just few file from rmt

    - dd
      Blockweises Daten Kopieren und Konvertieren
      $ dd if=/dev/sda of=/dev/sdb
      $ dd if=/dev/hda of=mbr.backup ibs=512 count=1  | MBR backup
      $ dd if=mbr.backup of=/dev/sda
      $ dd if=/dev/sr0 of=debian.iso bs=1M            | cd image copy
      $ dd if=/dev/zero of=nulldatei bs=1M count=100  | create 100 MB data !

    - file & type
      file zeigt den Typ eier Datei an, file show info about file
      $ file /etc/cron.daily/backup

      type zeigt fuer ausfuehrbare Dateien an, ob sie Shell-intern
      oder als ausfuehrbare Datei im Dateisystem vorhanden sind
      $ type (ls, echo, firefox, [)

    - Use streams, pipes and redirects
      standard-eingabe und -ausgabe STDIN(0) -> STDOUT(1) -> STDERR(2)
      Es ist moeglich, die Ein-u.ausgabekanaele umzuleiten.
      Ausgabeumleitung geht mit > (Datei)
      Eingabeumleitung geht mit < (Datei)

      Bei Ausgabeumleitung wird eine bereits bestehande Datei des gleichen Namens
      ueberschreiben
      $ ls -lR /etc > etcfiles
      $ ls -lR /etc/ 2> etcfiles.errs
      $ ls -lR /etc 2>> etcfiles

      configure ">>" meaning whether cannot overwrite or can overwrite by set!
      $ set -o noclobber : not allow
      $ set +o noclobber :     allow

      Doppelte Ausgabeumleiungszeichen haengen die Ausgabe am eine bestehende Datei an
      $ cat << string
      $ >a
      $ >b
      $ >string

      Redirection, can use "&" to send both stdout and stderr to a file (&> or &>>)
      $ ps >  file                : create or overwrite
      $ ps >> file                : create or append
      $ ps &> file                : stdout and stderr into file
      $ cmd > file 2>&1           : stdout and stderr into file
      $ cmd 1> file1 2> file2     : stdout into 1 and stderr into 2
      $ ls /etc >/dev/null 2>&1   : no print

      Pipes with tee kann man Datenstrom in einer Textdatei ausgeben
      Both display to monitor and also to the file, important!
      $ grep "kernel" /var/log/messages | tee kernel_messages.txt

      xargs build and execute command lines from standard input
      xargs uebernimmt die Standadausgabe des vorhergehenden Kommandos aus einer Pipe
      und ubergibt diese als Argumente an den naechsten Befehl
      $ find . -name '*.mp3' | xargs rm
      $ find . -name "*.c"   | xargs grep foo1foo2foo3

    - Monitor and kill processes
      $ ps : aktuellen laufenden Prozesse anzeigen
      $ ps -A | head -4    : Select all process
      $ ps -aux            : To see every porcess on the system using BSD syntax
      $ ps -C <prozess>
      $ ps -U <benutzer>
      $ ps -j --forest     : jobs format with forest
      $ pstree -a          : zeigt arguemnt without PID
      $ pstree -pn         : zeigt sortierten PID

      # top
      $ top -i     	: zeigt nur die aktiv Prozess
      $ top           : @interative mode
      r = renice
      k = kill
      q = quit

      # signal
      $ ctrl + c = inerrupt       # stop running
      $ ctrl + d = suspend        # stop but ready to continue
      $ ctrl + z = stop           # ready to continue
      $ ctrl + x = kill?          #

      Kill sendet eine Siganl, nicht sendet kill(nur Name)
      $ kill -l          : list of all kill option (1,9,15)
      -1  SIGHUP  : bei Diensten Neueeinlesen der Konfiguration
      -2  SIGINT  : ctrl+C bewirkt Terminierung
      -9  SIGKILL : Abbruchsignal benoetigt root-Rechte, arbeitet auf der Kernel
      -15 SIGTERM : selbst zu beenden
      -18 SIGCONT : fortsetzen einen Prozess durch fg and bg
      -19 SIGSTOP : anhaelten einen Prozess
      -20 SIGSTP  : ctrl+Z anhaelten einen Prozess
      $ kill -15 3167
      $ kill -s SIGTERM 3167
      $ kill -SIGTERM 3167
      $ kill -SIGHUP $(cat /var/run/dhcpd.pid)
      $ kill -HUP `cat /var/run/httpd.pid'
      $ kill $(pidof top)
      $ killall -s 9 mc

      To kill the process but allows the process to "clean up" before existing
      $ kill -TERM 1111     : this is default signal of kill

      Wie viele Prozess fuer Kang im Lauf?
      $ pgrep -u kang -c
      $ pkill -u kang

    - Hintergrund prozess
      $ <command> &

      jobs zeigt alle Hintergrund prozesse mit ihrer Job-ID [n] an
      $ jobs -l

      $ bg 1   : process to background
      $ fg 1   : process to foreground
      $ fg %<job-id>  : kann ein angehaltener Prozess zum Wieterarbeiten
      in den Vordergrund geholt werden
      $ bg %<job-id>  : ein angehaltener Prozess im Hintergrund weitergefuerhrt werden
      $ bg %glxgears
      $ fg %glxgears

      Wenn ein Prozess Ausgaben auf dem Terminal machen mochte, kann er nicht im Hintergrund
      ausgefuehrt werden, Deshalb kann man mit "nohup <command> &" dafuer sogen, dass dieser
      Prozess im Hintergrund laufen kann, er macht seiner Ausgaben in die Datei "nohup.out"
      $ nohup updatedb &
      $ logout

      # screen (remote shell)
      # (login)-(screen)-(top)-(other ssh login)-(screen -r)-(runing now top!)
      # one application with screen
      (login)-(screen -S job)-(command)-(ctrl+ad)-(logout)-
      (login again)-(screen -r job)-(job finish!)-(exit or ctrl+a\)

    - Quick info about total service times and users
      $ uptime       : runing time, count user and system load
      $ w            : who, aktuell angemeldeten Benutzer

    - Memory and Sway usage
      $ free -mts (m-MByte t-Total s-automatische Aktualisierung nach x sec)

    - Prozessprioritaet -20(high) ~ +19(low), default is 10
      $ nice -n  12 nano    : 12
      $ nice -n -15 nano    : -15
      $ nice -12 nano       : 12
      $ nice --12 nano      : -12
      $ renice -10 5309     : hier ist -10
      $ renice 5 -u dominik : hier ist +5
      * renice do NOT use "-"
      * Users do NOT increase the priority of their own processes

    - Regular expression
      [:blank:]   : all horizontal whitespace
      [:xdigit:]  : all hexadecimal digits
      [:alpha:]   : all letters
      [:alnum:]   : Alphanumeric characters

      $ ^      : start of line
      $ $      : end of line
      $ \<     : start of word
      $ \>     : end of word
      $ .      : match any single character, equivalent to ?
      thus, "m.a" matches "mpa" and "mea" but not "ma" or "mppa".
      $ ?      : any single character. "hd?" -> (hda, hdb, hdc)

      # what has meaning for this?
      $ ^[^#]  : Wenn am Zeilenanfang keine Raute # steht
      $ $[.]   : Wenn am Zeilenende Raut . steht
      $ \      : Maskierung, schuetzt das folgende Metazeichen vor der Interpretation
      $ june\? : Matches June or Jun (? matches zero or an instance of e)
      $ [a-zA-Z]      : Match any letter
      $ [^0-9A-Za-z]  : Any non alphanumeric character
      $ [A-Z][a-z]*   : An uppercase character followed by zero or lower case character
      $ \$[ 0-9]*\.[0-9]\{2\} : Matches $xx.xx or $ xxx.xx,  whereas the chracters
      following $ could be a space or number
      $ [0-9]\{2}-[0-9]\{4\}-[0-9]\{3\} : Matches numbers on the pattern xx-xxxx-xxx

    - grep every '<regular expression>' can be used in vi or grep
      $ grep -i w.lly  list 		      : willy oder WILLY egal!
      $ grep -v [#] list    		      : without # in a line
      $ grep '[^#]' list                    : without # in a line
      $ grep '^[^#]' list                   : without # first position in a line
      $ grep '^[^#]' list | grep -v ^$      : but will empty line will be disappeared!

      $ grep 'Wort[1-9]*\>' Datei           : find Wort, Wort1,Wort1234 etc
      $ grep 'Wort[1-9]\?\>' Datei          : find Wort1,Wort2
      $ grep '^Dec 10'  /var/log/messages   : find "Dec 10" in each line
      $ grep '1\{3,5\}' Datei               : find 1111 oder 11111
      $ grep '[0-9]\{3\}' Datei             : find any numbers 258 389
      $ grep '[A-Z]\{3\}' Datei	      : find any string  ACB UIC
      $ grep '\$[0-9]\{3,\}\>' Datei        : find more than 3 digits $123 or $12345
      $ grep '\/var\/log\/' Datei           : find "/var/log" in the data
      $ grep 'c:\\windows\\' Datei          : find "c:\windows\"
      $ grep '[A-Z]\+' Datei                : find line contain any Capital character

      $ grep '^Linux' file                  : Displays all lines that start with Linux
      $ grep '$x' file                      : Displays all lines that end with an x
      $ grep -c '^$' file                   : Displays the number of empty lines
      $ grep '^null$' file                  : containing the word null by itself
      $ grep '[Ll]inux' file                : containing Linux, turbolinux, LinuxOS
      $ grep '[0-9][0-9][0-9]' file         : files that contain 3 consecutive digits
      $ grep '^[^0-9]' file                 : lines that do no begin with a numeral
      $ grep '\<[Ll]inux\>' file            : Linux, linux but not turbolinux, LinuxOS
      $ grep '.....' file                   : Matches a line with 5 or more characters
      $ grep '.' file                       : Displays all non blank lines
      $ grep '\.' file                      : Displays all lines that have a period

    - sed
      Der Stream-Editor arbeitet nach dem Texfilter-Prizip, die zu bearbeitende Datei kann als
      Befelszeilen-Argument angegeben werden, die Ausgabe erfolgt an der Standardausgabe
      $ sed 's/rumba/samba/g' data1 > data2         : rumba to samba then save
      $ sed -i 's/rumba/samba/g' data               : in-place, open->substitute->save
      $ set 'y/,/;/'   commadata > semicommadata    : commadata to semicommadata
      $ sed -e '/^$/d' -e '/^#/d' /etc/insserv.conf : del. containing number and empty line
      $ sed '25,47d' data1 > data2                  : del. line 25 upto 47
      $ sed 's/ue/uee/' data                        : substitue one time e to ee
      $ sed 's/ue/uee/g' data                       : whole sentence check
      $ sed -f sed-script umltext > htmltext        : -f allow to use script

      $ sed '2d;s/a/A/g' data            : Delete line 2 and substitue a for A
      $ sed '2,3s/a/A/g' data            : Substitute a for A on lines 2 to 3
      $ sed '2{ s/r/R/g; s/e/E/g }' data : do two substitutions for line 2
      $ sed '=' data | sed 'N;s/\n//g'   : print number then number put the line
      $ sed '3,5d' file         	   : delete lines 3 throug 5
      $ sed '^# ' file         	   : delete lines that start wit #
      $ sed 'y/abc/xyz/' file  	   : translate characters; a->x, b->y and c->z
      $ sed 's/^$/@/' file      	   : writes @ to all empty lines
      $ sed 's/"//g' file      	   : removes all double quotation

      $ sed '/^$/d' file             : delete blank lines
      $ sed '/#keepme/!d' file       : delete lines that doesn't have #keepme
      $ sed '/^[ tab]*$/d' file      : delete lines that contain white space or tab
      $ sed '/^[[:blank:]]*$/d' file : delete lines that contain white space or tab
      $ sed '/^[\.#]/d' file         : delete lines beginning with . or #
      $ sed 's/ */ /g' file          : substitute multiple spaces for a single space
      $ sed 's/ \{2,\}/ /g' file     : substitute multiple spaces for a single space
      $ sed '1,4s/abc/def/g' file    : substitutes abc for def on lines 1 to 4
      $ sed '11,20y/abc/@@@/' file   : translate a,b,or c on lines 11 through 20 for @
      $ sed '/123/{s/ab/AB/g}' file  : replaces ab for AB only on lines that have 123
      $ sed '/@#%/{ s/.*//g }' file  : remove nonblank line(.*) when lines contain @#%

    - vi editor
      $ vi> :[range]s[ubstitute]/{pattern}/{string}/[flags] [count]
      $ vimtutor
      $ vi text1 +/Pinguin             : start vi with start line of Pinguin
      $ vi>:%s/.$//                    : exchange all "." in the sentence
      $ vi>:s/I/We/gi                  : substitution of a text within a single line
      $ vi>:1,10s/helo/hello/g         : substitution of a text within a range of lines
      $ vi>:'<,'>s/helo/hello/g        : crtl+V then select then substitute
      $ vi>:s/helo/hello/g 4           : substitution of a text only 1st 4 number of lines
      $ vi>:%s/\(good\|nice\)/best/g   : good or nice replace with best
      $ vi>:s/\<his\>/her/		 : substitute only the whole word
      $ vi>:%s/\<\(hey\|hi\)\>/hai/g   : but they or this will not change
      $ vi>:%s/awesome/wonderful/gc    : interactive find and replace in vim
      $ vi>:%s/^/\=line(".") . ". "/g  : substitute all lines with its line number
      $ vi>:set repetition=4           : repeats every subsequent command 4 times
      $ vi>:4,$s/\d\+/\=submatch(0)+1/ : Alter sequence number while inserting a new item
      $ vi>:%s!\~!\= expand($HOME)!g   : subst. special character with its equivalent value
      $ vi>:%s/\.\s*\w/\=toupper(submatch(0))/g : start beginning with upper case


<br/><a name="Filesystems"></a>

### Devices, Linux Filesystems, Filesystem Hierarchy Standard
    ===============================================================================================
    LPIC 104: Devices, Linux Filesystems, Filesystem Hierarchy Standard
    ===============================================================================================
    - check current partition
      $ cat /proc/paritions
      $ fdisk -l

    - create new partion
      $ fdisk /dev/sdb  : MBR partition (4 Primary, 4T total and limit 2.2TB/partition)
      $ gdisk /dev/sdc  : GPT partion   (128 Primary, GUID partition, nur Primary)
      $ parted          : nice method

      $ fdisk -l           : to view all available partitions
      $ fdisk -s /dev/sda7 : go to special partition
      $ fdisk -l /dev/sda/ : to do partitioning!
      ...inside the terminal, type one of following commends
      Command : d Deletes partition
      Command : n Add a new partition.
      Prompt follows as primary or extended;
      partition number; first cylinder; last cylinder.
      Swap partitions can be created with the t option.
      Command : p Displays partition table in the memory (not yet applied)
      Command : q Quit without saving
      Command : w write and exit

      Displaying supported filesystem types
      $ ll /sbin/mk* | awk '{ print $8 $9 $10 }'

      Possible and famous file systems are
      ReiserFS, Btrfs ,msdos, vfat, ntfs, xfs, cramfs, jfs

    - Formatting/Creating filesystems 
        After partitioning, it's needed to add a filesystem to the partition.
      Format will be
      $ mkfs -t ext2 -L system -cv /dev/hda3 : make ext2 labeled "sytem" on hda3
      $ mkfs -t ext3 -l /dev/sdb1		   : make ext3 on sdb1
      $ mkfs.ext4 /dev/sdb1			   : make ext4 on sdb1
      $ mke2fs -j /dev/sda1			   : make ext3 on sdb1 -j is ext3

    - Tip about partitions
      something wrong for mkfs(mke2fs) then
      $ mv /sbin/mkfs.ext2 /sbin/mkfs.ex2
      $ mv /sbin/mkfs.ext3 /sbin/mkfs.ex3

    - Swap fundamental
      To see information about swap disk
      $ cat /proc/swaps

      Make a swap disk
      $ dd if=/dev/zero of=/swapfile bs=1024 count=524288
      $ mkswap /swapfile
      $ swapon /swapfile
      or
      $ gdisk /dev/sdb
      $ mkswap /dev/sdb2
      $ swapon -v /dev/sdb2
      $ swapoff -f /dev/sdb2
      then control below file to fit during start
      $ cat /etc/fstab

    - fsck chceking filesystems
      check and repair a Linux file system
      available checking command of fsck
      $ ls -1i /sbin/*fsck* | sort -n

      Standard way of disk check/test
      normally one can perform below for main partition after LINUX CD booting
      $ umount /dev/sda1
      $ fsck -f -c /dev/sda1
      $ shutdown -hf now

      fsck options
      $ fsck -f : force
      $ fsck -A : alle Dateisysteme
      $ fsck -c : defekte Bloecke suchen
      $ fsck -b : Blocknummer alternativer Superblock
      $ fsck -y : Voreingestelltes "yes" fuer Fragen

      Part of the information that describes the filesystem is the superblock and it is
      saved on block 1 of the partitions. If this bock becomes corrupted the filesystem is
      unaccessible. By default filesystem makes copies of the superblock every 8192 blocks
      (1, 8193, 16385, ...) which can then be used by fsck to restore the main superblock

      fsck runs at boot on the filesystems configured at /etc/fstab. The sixth field(dump)
      provides information of which filesystems should be checked and in what order.

    - Modyfying, Checking And Repairing Filesystems
      debugfs - ext2/ext3 file system debugger, test and restore deleted inodes
      $ umount /dev/sdb1
      $ debugfs /dev/sdb1
      > lsdel
      > dump <inode #> /tmp/restored

      more detail information about partition
      # dumpe2fs   :  dump ext2/ext3 filesystem information
      $ dumpe2fs /dev/hda1 | less

      To check a linux ext2/ext3/ext4 file system
      $ e2fsck  -l /dev/sda1

      To save critical ext2/ext3 filesystem metadata to a file
      $ e2image

      Integritaet test with format program
      $ mke2fs -c /dev/sdb1     : do format and then goes to test

      tune2fs is a utility used to modify existing extX filesystem
      after converting from ext2 to ext3, then need a format with mkfs at frontend
      $ tune2fs -j /dev/hdb3    : ext3 journal
      $ mkfs -t ext3 /dev/sdb1  : format
      through a full filesystem check while booting, that make changing number of days
      $ tune2fs -c 200 /dev/sda1
      converting from ext3 to ext4 using tune2fs
      $ tune2fs -0 extents,unint_bg,dir+index /dev/sdb1

    - ext file systems
      ext2 and ext3 has a fixed number of inodes
      ext4 supports online defragmentaiton

    - reiserfs file systems
      support journaling most likely for server
      # reiserfstune  - Displays and adjusts filesystem parameters
      # debugreiserfs - Similar to dumpe2fs and debugfs

    - xfs (eXtended) file system,
      64 bit environment, used in centos means quite modern system, support journaling
      xfs_info   Filesystem information
      xfs_growfs Expandsan xfs filesystem
      xfs_admin  Changes the parameters of an xfs filesystem
      xfs_repair Repairsfilesystem when mount checks are not sufficient
      xfs_db     Examines or debugs an xfs filesystem

    - vfat (virtual) file system
      optimal for shared file system  (Ext2 + MSDOS), not support journaling

    - Disk free (df)  : Displays information on mounted filesystems
      $ df -i   : zeigt mit Inode nicht mit Block
      $ df -m   : zeigt die Bloecke in Mega
      $ df -h   : human readable
      $ df -a   : all list

    - Disk utilization (du) : esimate file space usage
      $ du -a   : all
      $ du -m   : Mega
      $ du -h   : human readable
      $ du -s   : summary
      $ du -sch : c=summenbildung
      $ du -sh /home/kang/* | less
      $ du -s /etc   : include subdirectories and displays only the summary
      $ du -Ss /etc  : do not include subdirectories and display a summary
      $ du -csh /etc : displays a summary of all subdirectories with human readable
      $ du -cs | sort -nr : displays summary and sorts result by reverse numerical number

    - To display mounted filesystems
      $ fdisk -l
      $ df                : simple and easy
      $ cat /etc/mtab     : check mount option from data table
      $ cat /proc/mounts  : mounted device in current process/session
      $ mount             : list all mounted device currently

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
      $ umount /media/usb          : to unmount of usb-storage
      $ mount /dev/sda1 /home	     : to mound /home directory
      $ mount /dev/sda6 /mydata -r : to mound read only
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
      > proc 	/proc 	proc 	defaults 			0	0
      > /dev/sda1 	/ 	ext3  	defaults,errors=remount-ro 	0	1
      > /dev/sda6 	/home 	ext3 	defaults 			0	2
      > /dev/sda5 	none 	swap 	sw 				0	0
      > UUID=...  	/	ext4	errors=remount-ro   		0       1
      > /dev/hda1 	/	ext4	acl,user_xattr      		1       1
      > /usr/demo 	/demo	nfs	ro,bg      			0       0

      here are some important mount options
      .auto
      .usrquota   : after compile kernel with this option, quotaon has to be done
      .grpquota   : after compile kernel with this option, quotaon has to be done
      .suid
      .user  : attach by one, umount by one
      .users : attach by one, umount by anyone
      .nouser
      .nosuid
      .default=rw,suid,dev,exec,auto,nouser,async

      $ vi /etc/fstab
      > device    mountpoint datatype   mountoption      dump   fsck
      > UUID=...  /data	ext4	    default	     0      0
      $ mount -a      : mount all device in the /etc/fstab
      $ df

      Mit blkid lassen sich wichtige Informationen
      wie Gerätedatei, Identifikationsnummer, Name und Dateisystem
      ex) Find UUID to put into /etc/fstab directly
      $ blkid /dev/sdb1 | cut -d'' -f2 | tr -d '"' >> /etc/fstab

    - Set and View Disk Quotas
      Disk quota can be assigned based on:
      . filesystems listed on /etc/fstab;
      . users listed on /etc/passwd;
      . groups listed on /etc/group;

      Do write explictly "usrquota, grpquota" (not userquota and groupquota) in /etc/fstab

      Die Ueberschleitung des "Soft-Limits" ist nur fuer eienen festgelegten Zeitraum
      ('Grace Period' Gnadesfrist) moegliche, das Hard-Limit darf nie ueberschnitten werden

      Displaying quota
      $ quota 	   //see your quota
      $ quota -uv root   //for  root
      $ quota -uv kang   //for  user kang
      $ quota -gv kang   //for group kang

      scan a filesystem for disk usage, create, check and repair quota files
      $ quotacheck -augv        : all user group verbose
      $ quotacheck -vu /datein  : directory quota

      Turn on & off quota
      $ quotaon  -avug       : all user group verbose
      $ quataoff -avug       : all user group verbose
      $ quotaon  -vug /data  : for directory
      $ quataoff -vug /data  : for directory

      Edit user quotas
      It looks like nano editor, one can edit size of file by soft and hard normaly
      soft(0->5000) and hard(0->7000) also one can exchange Inode part
      Inode hard 10 mean that will be limited by file number 10 in quota postion
      $ edquota -u root 			: for root
      $ edquota -u kang                       : for user
      $ edquota -f /data -u kang              : edit standard
      $ edquota -p user1 -u user2 user3 user4 : prototype fuer anderen Benutzer

      How to check Block size
      $ dumpe2fs -h /dev/sdb1 | grep "Block size"

      Generates quota reports
      $ repquota -v /home

      warnquota - Usually run under cron

      Step 1  Add the option 'usrquota' and/or 'grpquota' to the filesystem
      $ vi /etc/fstab
      > /dev/sda3 /home ext2 defaults,usrquota,grpquota 1 2
      $ shutdown -r now

      Step 2  Create the proper configuration files (quota.user and/or quota.group)
      in the root of the file system.
      Add the proper permissions (set quota.group 644
      if you want users to be able to examine quotas on groups they belong to)
      $ touch /home/quota.user
      $ touch /home/quota.group
      $ chmod 600 /home/quota.*

      Step 3  Initialize the database with 'quotacheck'
      $ quotacheck -avug
      > Scanning /dev/sda9 [/home] done
      > Checked 236 directories and 695 files
      > Using quotafile /home/quota.user
      > Using quotafile /home/quota.group

      Step 4  Verify that database was created, whether file size is big enough
      $ ls -l /home/quota.*
      > -rw------- 1 root root 16192 Dec 27 19:53 /home/quota.group
      > -rw------- 1 root root 16192 Dec 27 19:53 /home/quota.user

      Step 5  Enable quota
      $ quotaon -avug

      Step 6  Add quota to the desired runlevel(shell or login? need to check!)
      if [ -x /sbin/quotacheck ]; then
      echo "Checking quotas."
      /sbin/quotacheck -avug
      echo " Done."
      fi
      if [ -x /sbin/quotaon ]; then
      echo "Turning on quotas."
      /sbin/quotaon -avug
      fi

      Step 7  Add quotacheck to a script on
      $ vi /etc/cron.weekly/0anacron (preferably weekly)
      > #!/bin/bash
      > exec /sbin/quotacheck -avug
      or
      $ vi /etc/crontab (run quotacheck weekly)
      > 0 3 * * 0 root /sbin/quotacheck -avug

    - Identifying User And Groups
      $ id
      $ groups

      # Generates a compact listing of all the users on the system.
      $ cut -d: -f < /etc/passwd | sort | xargs echo

      Zugriffsrechte, changing access mode by chmod
      # 4-read(100) 2-write(010) 1-execute(001)
      # '+'permission, '-'divesture, '='keep.
      # 0644 (rw-r--r--)
      # 0770 (rwxrwx---)
      # 0660 (rw-rw----)

      $ chmod -Rv 755 /adir
      $ chmod -R -v o-rwx /adir
      $ chmod -v u=rw,go=r /afile
      $ chmod -v o= file4
      $ chmod a+x script
      $ chmod u+x,g+x,o+x script
      $ chmod g-rwx,o-rwx /adir -R

      - SUID(Set User ID)
      Spezille Zugriffsrechte fuer Dateien und Verzeichnisse gesetzt auf manche
      Programme, bewirkt, dass das  Program nicht mit den Rechten des Aufrufenden
      ausgefuehrt wird, sondern mit den Rechten des Besitzers des Programms
      - SGID(Set Gruop ID)
      Auf ausfuehrbare Dateien gesetzt bewirkt es, dass dei Datei mit den Rechten
      der Gruppe ausgefuehrt wird (egal, wer es aufruft)
      Auf Verzeichnisse gesetzt bewirkt es, dass in diesem Verzeichnes angelegte
      Dateien die Gruppenmitgliedschaft des Verzeichnisses "erben"
      - Sticky Bit (/tmp)
      wird normalerwiese auf Verzeichnisse gesetzt, in denen others Schreibrecht hat
      Mit dem Sticky-Bit wird erreicht, dass Dateien in solchen Verzeichnissen nur
      vom jeweiligen Datei eigentuemer geloescht werden koennen.

      SUID   : -rwsr-xr-x   -> s or S(nicht ausfuehrbar) 4XXX
      SGID   : -rwxr-sr-x   -> s or S(nicht ausfuehrbar) 2XXX
      Sticky : -rwxr-xr-t   -> t or T(nicht ausfuehrbar) 1XXX

      SUID allow to access file and process temporally
      (passwd crontab sudo)
      SGID allow to access file by group user
      (sh, bash, python, doveconf, sendmail)

      $ chmod 4755 script
      $ chmod u+s script
      $ chmod g+s /dir/script
      $ chmod o+t /group

      chown - change file owner and group
      $ chown kang:kang myfile     : group and owner
      $ chown kim myfile           : just owner
      $ chown :kim myfile          : just group
      $ chown kang /home/kang -R   : recusive all files under kang

      chgrp - change group ownership
      $ chgrp kim myfile           : nur gruppe aendern
      $ chgrp -R users /adir

      Setting access mode by umask
      umask will be shown one of this number 002, 007, 020, 070, 022
      # umask 022  : we can interpret like this
      file   666 - 022 = 644
      folder 777 - 022 = 755
      Fuer Datien gilt : es wird zusaetzlich zur umask noch 111 abgezogen,
      damit eine neue Datei nicht automatsch Ausfuehrungsrechte bekommt Ausfuehrtungsrecht
      muss immer explizit gesetzt werden.
      # umask 077  : more security and equivalant file/folder view 600/700
      to change default umask then have a look /etc/profile

    - Attribute of directory
      $ lsattr     			: list of attribute
      $ chattr -aAS dir1 dir2  	: change of attribute
      $ chattr -V +u dir1 dir2	: change of attribute
      a-append only (add possible but no delete)
      u-undeletable
      i-no change, no delete
      A-no atime updates
      D-synchronous directory updates

    - Create and Change Hard and Symbolic Links
      Hardlinks sind nur innerhalb eines Daeisystems (Partition) moeglic wegen der
      eindeutigen Inode. Auf Verzeichnisse Koennen Keine Hardlinks gesetzt werden
      $ ln file hardlink1
      $ ln file hardlink2

      Softlinks wird ein neuer Inode erstllt, der auf die selben Datenbloecke zeigt
      wie das Ziel des Links, und Koennen dateisystemuebergrefend sein, d.h. Link und
      "Originaldatei" Koennen auf verschiedenen Partitionen liegen
      Es kann auch Symlinks auf Verzeichnisse geben
      $ ln -s file softlink
      $ stat file

    - find (extend)
      The command 'ls -l' will also provide a total number of pointers to that file
      $ find . -inum 421422 -exec ls -li {} \;
      -> 421422 -rwxr-xr-x 3 root root 47288 May 24 2008 ./mkfs.ext3
      -> 421422 -rwxr-xr-x 3 root root 47288 May 24 2008 ./mkfs.ext2
      -> 421422 -rwxr-xr-x 3 root root 47288 May 24 2008 ./mke2fs

      Finding links to a file
      -> lrwxrwxrwx 1 root root 5 Feb 7 15:36 saran-l -> saran
      $ find . -lname saran
      $ find . -samefile mke2fs
      $ find . -inum 421422

      $ find /home -name '*.sxw'
      $ find /home -amin 10  : zugegriffenen Datei vor n Min
      $ find /home -cmin 20  : geaendertene Datei
      $ find /home -mtime 1  : vor n mal 24 Stunden gmodifiziert
      $ find / -ctime -2     : vor weniger als 24 Stunden geaendert
      $ find / -user kang    : ownership by kang
      $ find / -perm +4000   : Document with SUID-bit format
      $ find / -size +500M | xargs ls -l : Datei grosser als 500M

    - Simple find using internal data base
      # similar find but light version and use databank
      $ locate *.sxw

      # Database can be created and updated via the 'updatedb' command
      # Database location can vary with systems
      # /var/lib/mlocate/mlocate.db
      # /var/lib/slocate/slocate.db  : debian, centos

      # configuration file
      # /etc/updatedb.conf
      $ updatedb &

    - Programme zum Auffinden von Datein
      $ whatis <name>
      $ whereis <command>
      $ which <command>
      $ apropos <string>
      $ man -k <string>

    - Directories
      # Essenstial directories
      /bin    Contains essentials executable commands that may be needed in a problem
      /dev    Device files
      /etc    Configuration files unique to the system needed at boot
      /lib    Sharedlibraries
      /mnt    Provides a centralized mount point to system administrators
      /root   Recommended root's home
      /sbin   Essential system administration utilities (fsck, fdisk, mkfs)
      /proc   kernel or process information
      /media  cdrom

      # Nonessential directories
      /boot   Contain files for the boot loader. also contains the kernel.
      /home   User personal directory
      /opt    Software that is not packaged with system (3rd party software)
      /tmp    temporary files (recommended to be deleted after system reboot)
      /usr    Executable programs that are not needed on boot
      (or not needed to recover systems).
      Contains user and system administration commands and daemons
      that are only used under normal operation of the OS
      Usually mounted as read only via NFS from a network location
      Large software packages cannot place a directory directly
      under /usr/ (except X11)
      /var    Data that varies over time (web sites, ftp, logs, mail, spool)
      Because data keeps growing and changing, this directory is usually
      not included with / to prevent the / partition from filling
      /srv    www-root, ftp-root

      # Important directories
      /dev/null     Data will be passed through without any action
      /dev/zero     generate 0 bit
      /dev/tty      represent Terminal, which is runing in the process
      /usr/X11R6    Contains directories for XFree86
      /usr/bin      User commands that are not necessary for emergency maintenance
      /usr/include  Standard location for header files used for C and C++
      /usr/lib      Shared libraries for various programs. Creation of subfolders is
      also allowed (eg: /usr/lib/vlc/)
      /usr/local    A top level of a second hierarchy. Contains subdirectories
      with same name as found in /usr/
      /usr/sbin     System administration commands that are not essential for emergency
      /usr/share    Datafiles that are independent of hardware architecture and
      operating system versions
      /usr/src      Optional directory on newer gibcbased systems. Older libc4 and
      libc5 systems required it to have a copy of the kernel source or
      include/asm and include/linux for kernel header files
      /var/account  Processaccounting data
      /var/cache    Used by programs to store intermediate date. Programs should be
      able to regenerate the date so the system admin can delete files
      as needed. this folder does not need to be backed up
      /var/crash/   Crash dumps
      /var/games/   Saves game data like state information, scores, etc..
      /var/lock/    Used by applications to signal their existence to other processes
      File are usually empty
      /var/log/     System log files
      /var/mail/    System mailbox. Replacement for /var/spool/mail/
      /var/opt/     Location for temporary files created by programs in /opt/
      /var/run/     Contains various files describing present state of the system
      (like pid files)
      /var/spool/   Queued information (like cron, printer, etc...)
      /var/state/   Information that helps applications maintain state
      across multiple instances
      /var/tmp/     Same as /tmp/, owever data here is more persistent
      and may not be deleted after system boot
      /var/yp/      Database files for NIS Network Information Service


<br/><a name="Data_management"></a>

### Shells, Scripting and Data Management
    ===============================================================================================
    LPIC 105: Shells, Scripting and Data Management
    ===============================================================================================
    - env, set, export
      $ env             : global variable
      $ env NAME=VALUE  : global variable define
      $ env -u NAME     : delete NAME in current shell
      $ env -u A ./run  : run without variable A in current shell
      $ evn -i          : empty global variable

      $ set           : shell variable
      $ unset A       : delet shell variable

      $ A=100         : yes
      $ let A=100     : yes
      $ set A=100     : No  just A=100 enough

      $ export A      : Die Exportfunktion nur auf subshells wirkt und
      nicht auf uebergeordnete Shells.
      $ export A=100  : standard way of setting global variable

    - alias, function, shell
      $ alias frei='free; df -h'
      $ alias -p     : display all defined aliases in the current shell

      $ alias ls='ls --color=auto'
      $ ls           : with color
      $ /bin/ls      : withtout color
      $ unalias ls   : deactive alias
      $ builtin ls   : origianlkommando zurueck

      $ function addiere ()
      > {
      > let summe=$1+$2
      > echo -e "Summe ist $summe"
      > }
      $ function addiere {let summe=$1+$2; echo -e "Summe ist $summe"}

      Konfigurationsdateien der Bash
      /etc/profile     : Login shell
      ~/.bash_profile
      ~/.bash_login
      ~/.profile
      /etc/bash.bashrc : Interactive shell
      ~/.bashrc
      ~/.bash_logout

      Skeleton /etc/skel
      $ echo "du bist also $USER" > /etc/skel/userfile
      $ useradd -m max
      Dann userfile wird in /home/max kopieren.

      Befehlseingabe mit lists
      $ command1; command2; command3

    - environment shell
      Der Befehl "source" kann Konfigurationsdateien in die aktuelle Shell "on the fly"
      einlesen. Damit wird eine veranderte Konfiguration sofort guelitg.
      Start des Befelhs "source" kann aus ein Punkt direkt am Prompt verwendet werden,
      also z.B. ". .bashrc

      Mit "source" bzw "." koennen Skripte in der aktuellen shell ausgefuerhbar sein
      (es muss kein Executable-Bit gesetzt sein).
      Die Ausfuehrung mit bash (oder sh) <skipt> benoetigt ebenfalls kein Executable-Bit

      I have one file "shell environment script"
      > -rw-r--r-- kang kang env_script
      $ bash env_script      : @ sub-shell
      $ . env_script         : @ current shell
      $ source env_script    : @ current shell
      $ ./env_script 	       : NOT work because of permission!

      Command for executive file below two are identical!
      $ source /usr/bin/aaa
      $ /usr/bin/aaa

    - write simple shell scripts
      Wenn ein Skript durch Eingabe seines Namens ausgefuehrt werden soll,
      muessen folgende Bedingunggen erfuellt sein
      1. Es muss das Ausfuehrungsgerecht am Skiprt gesetzt sein
      2. Das Scipt muss ein "Shebung #!" enthalten in der ersten Zeile muss der
      Kommandointerpreter eingegeben sein
      3. Das Skript muss "im Pfad" sein oder der Pfad zum Skript muss angegeben werden

      $ cat script
      > #!/bin/bash         : unbedingt eine neue Shell aufgerufen wird.
      > Parameters $1 $2 $3 : parameter
      > $#                  : Anzahl der Paremeter
      > $?		      : Aktuelle Errorlevel
      > exit 0
      $ ./script A B C

      Typical application of errorlevel $?
      $ locate myprogram
      $ echo $?
      if myprogram exist and ok, then will be 0, otherwise will be 1 im Fehlerfall

      Program kann auch logische Test durchfuehren.
      $ date && program : wird nur ausgefuehrt, wenn erfolgrich ausgefuehrt wurde
      $ date || program : wird ausgefuehrt, wenn nicht erfolgreich ausgabe von erste
      ausgefuehrt wurde

    - Shell script
      In shell Skript gibt es vielle wichtige Anweisung
      for while until
      ----------------------
      for i in {1..5}
      do
      >     echo "Welcome $i"
      done
      ----------------------
      > while test $1 -gt 0
      > while [ $1 -le 10 ]
      > do
      >     let c=a+b
      >     i=$(($i+1))
      >     clear;
      > done
      > exit
      ----------------------
      > read a b c
      > if [$operator = a];
      > then
      >   echo "condition 1"
      > elif [$1 -eq $2];
      > then
      >     echo "condition 2"
      > else
      >     echo "condition 3"
      >     exit 1
      > fi
      > exit 0
      ----------------------
      > case "$var" in
      >     a) echo $a;;
      >     b) echo $b;;
      >     ...
      >     *) echo $x;;
      > esac
      > exit 0
      ----------------------

      For test
      $ test -e FileName : file exists
      $ test -f FileName : file is a regular file
      $ test -s FileName : file exists and non-zero size
      To test whether a file is nonexistent or empty, type in the shell
      ----------------------
      > if test ! -s "$1"
      ----------------------

      For read
      $ read -p "Name :" name	 : prompt asking and get name
      $ read -s                : do not display for scripting

      Mit shift werden der Parameter eine Position nach Rechts verschieben
      > shift

      In einem Skript eine Sequenz aufeinander folgender Zahlen benoetigen,
      > for i in $(seq 1 10)   : one can use in for-loop
      $ seq 1 10               : print out 1 2 3 ... 10 in vertical line
      $ seq -s , 7 27          : -s --separator
      $ seq 10 10 100		 : start increment last (10 20 30.... 100)

    - MySQL
      Tables consist of columns, each of which holds attributes, and rows
      each of which defines a pecific database items
      -----------------------------------------------------------
      | column                                                  |
      ----------------------------------------------            |
      | vorname | nachname | rufnummer | kategorie | row header |
      ----------------------------------------------            |
      | Hans    | Panz     | 12345567  | Freunde   | row items  |
      | Willi   | Wichtig  | 98765542  | Freunde   |            |
      | Natasha | Pashke   | 44545454  | NULL      |            |
      -----------------------------------------------------------
      Installation and start
      $ rpm -qa | grep mysql-server
      $ yum install mysql-server mysql-client
      $ mysql --user=root -p -h localhost

      MySQL passwd change and autostart during boot
      $ service mysqld start
      $ /usr/bin/mysqladmin -u root password 'P@ssw0rd'
      $ mysql -uroot -p
      $ chkconfig --list mysqld
      $ chkconfig mysqld on
      $ chkconfig --list mysqld

      If I lost password for mysql then do this
      $ service mysqld stop
      $ /usr/bin/mysqld_safe --skip-grant &
      $ /usr/bin/mysql -uroot mysql
      $ mysql> update user set password=password('xxxxxx') where user='root';
      $ mysql> flush privileges;
      $ mysql> quit
      $ service mysqld restart

      -----------------------------------------------------------------------
      sql> status;
      sql> help data types;
      sql> show databases;
      sql> CREATE DATABASE mydb;
      sql> USE mydb;

      sql> CREATE TABLE ruf (
      sql> id INT(10) NOT NULL AUTO_INCREMENT PRIMARY KEY,
      -> vorname VARCHAR(128),
      -> nachname VARCHAR(128),
      -> vorwahl VARCHAR(128),
      -> rufnum VARCHAR(128)
      -> );

      sql> INSERT INTO ruf (id,vorname,nachname,vorwahl,rufnum)
      -> VALUES (1,'Hans','Panz','030','122334');
      sql> INSERT INTO ruf (id,vorname,nachname,vorwahl,rufnum)
      -> VALUES (2,'Will','Ralf','030','444444');
      sql> INSERT INTO ruf (vorname,nachname,vorwahl,rufnum)
      -> VALUES ('Susi','Lecker','080','2347845');
      sql> INSERT INTO ruf (id,vorname,nachname,rufnum)
      -> VALUES (4,'Dieter','Durstig','134324');

      sql> SELECT * FROM ruf';
      sql> SELECT vorname,nachname FROM ruf;
      sql> SELECT vorname,nachname FROM ruf WHERE vorname='will';
      sql> SELECT * FROM ruf WHERE vorname='will';
      sql> SELECT * FROM ruf WHERE id > 1 AND id < 4;
      sql> UPDATE ruf SET vorwahl = '02131' WHERE vorname = 'Dieter';
      sql> UPDATE ruf SET vorwahl = '0221'  WHERE id=2;
      sql> SELECT * FROM ruf ORDER BY nachmane;
      sql> SELECT vorname,nachname FROM ruf ORDER BY vorwahl LIMIT 2;
      sql> SELECT * FROM ruf GROUP BY vorwahl;

      # create second table
      sql> CREATE TABLE kat (
      -> id INT(10) NOT NULL AUTO_INCREMENT PRIMARY KEY,kategorie VARCHAR(128) );
      sql> INSERT INTO kat (kategorie) VALUES ('Freunde');
      sql> INSERT INTO kat (kategorie) VALUES ('Kollegen');
      sql> INSERT INTO kat (kategorie) VALUES ('Bekannte');

      sql> source /home/kang/sql/kat.sql
      sql> drop table kat;    //if somethign goes wrong!

      sql> show tables;
      sql> select * from kat
      sql> ALTER TABLE ruf ADD (kategorie_id INT(5));
      sql> SELECT * FROM ruf;
      sql> UPDATE ruf SET kategorie_id='1' WHERE vorname='Will';
      sql> UPDATE ruf SET kategorie_id='1' WHERE vorname='Hans';
      sql> UPDATE ruf SET kategorie_id='3' WHERE vorname='Susi';
      sql> UPDATE ruf SET kategorie_id='2' WHERE vorname='Dieter';
      sql> CHECK TABLE ruf EXTENTED;
      sql> SELECT vorname,vorwahl,rufnum FROM ruf WHERE kategorie_id
      -> (SELECT id FROM kat WHERE kategorie='Freunde')
      sql> SELECT r.vorname,r.nachname,r.vorwahl,r.rufnum,k.kategorie,
      -> FROM ruf r LEFT JOIN kat k ON r.kategorie_id=k.id;

      # will not show empty (see LPIC book! page.324)
      sql> SELECT r.vorname,r.nachname,r.vorwahl,r.rufnum,k.kategorie,
      -> FROM ruf r INNER JOIN kat k ON r.kategorie_id=k.id;

      sql> DELETE FROM ruf WHERE vorname='Dieter' AND nachname='Durstig';
      sql> DELETE FROM ruf;
      sql> DROP TABLE ruf;
      sql> DROP TABLE kat;
      sql> DROP DATABASES mydb;


<br/><a name="user_interfaces"></a>

### User Interfaces and Desktops
    ===============================================================================================
    LPIC 106: User Interfaces and Desktops
    ===============================================================================================
    - X system (sometimes call X-window or X11)
      X Server  = da, wo die Grafikkarte ist
      X Clients = Programme, die fuer die grafische Ausgabe einen X Server benutzen.
      Windowmanager = X Clients
      Displaymanger = xdm,gdm,kdm is started by the init system, this a service
      Desktop Env.  = GNOME,KDE,LXDE,Cinnamon,Xfce,Mate

    - X configuration
      # /etc/X11/xorg.conf      : old Debian or Centos version
      # /etc/X11/Xsession.d     : Xorg integrated in Xsession @ Ubuntu distribution
      # /etc/X11/xinit.d
      # /etc/X11/xdm,gdm,kdm
      # /etc/X11/XF86Config     : Legacy configuration for XFree86 System

      If you want to test X-configuration, suggest this procedure
      1. Shut down X by switching to a runlevel 1,2,3
      2. reconfigure X or recover from xorg.conf.backup
      3. startx to test X startup

      history : X-system has been XFree86 to X.org @ since 2004

      How to start X automatically when the system boots?
      start an X-server via a system stratup script or from init

      xinitrc is a X-login script used when starting X from the command line via startx
      ~/.xinitrc will run when startx is running after loading  Xserver.

      How to change system greeting displayed by XDM?
      In Xresource change the text in the xlogin*greeting line

    - X-window precess case by case
      -runlevel 3
      1. Strat X-window via startx
      2. ~/.xinitrc via /etc/X11/xinit/xinitrc
      3. ~/.Xresources via /etc/X11/Xresources
      4. ~/.Xclients via /etc/X11/xinit/Xclients
      5. /etc/sysconfig/desktop  GNOME or KDE

      -runlevel 5
      1. start /etc/inittab configuration
      2. start window display manager via /etc/X11/prefdm
      3. window manager(gdm,kdm,xdm) start via /etc/sysconfig/desktop
      4. run /etc/X11/xdm/Xsetup_0 before login session
      5. login session for user, start /etc/X11/xdm/Xsession
      6. Xseesion manage run xinitrc and xinitrc.d scripts
      7. xinitrc manage desktop by .Xclient

    - X-Fontsserver (Legacy)
      # /etc/X11/fs/config

      By maintaining fonts on one font server, one can reduce administrative cost
      XFree86 4.x supports TrueType fonts.
      For fonts smoothing one need XFT software, but not in font server.

    - Xorg.conf
      $ cat /etc/X11/xorg.conf
      > Section "FontPath"   : Files, InputDivice, Modes, Screen, Device, Monitor
      >   Driver             : ServerLayout etc...
      >   Option             :
      > EndSection           :

    - Displaymanager
      -stellt einen Anmeldebildschirm fuer die grafische Obeflaeche zur Verfuegung
      -wird im (frueheren) Runlevel 5 beim Systemstart mitgestartet
      (heute: graphical.target beim systemd)
      -frueher xdm(X Window display manager),
      heute meist kdm(KDE), gdm(Gnome), lightdm(LXDE)

      Standard xserver location
      $ crtl+alt+f7

      Start xserver
      $ startx         : ein Frontend fuer das eigentliche Startskript xinit

      If you want to use more xserver in local then
      $ startx -- :1   : new 2nd xserver in tty8
      $ ctrl+alt+f8    : but VBox case is in tty1-tty6
      $ startx -- :2   : now 3rd xserver in ttyx
      $ ctrl+alt+f?

      If you want to use other xserver from centos in debian local
      then follow this instruction
      ------Centos with gnome(gdm)-----------
      $ vi /etc/gdm/custom.conf
      > [security]
      > DisallowTCP=false
      > AllowRemoteRoot=true
      > [xdmcp]
      > Enable=true
      $ systemctl stop firewalld
      $ systemctl disable firewalld
      $ shutdown -f -r now
      Now VBox network setting have to be bridge(Bruecke)
      Centos IP must be known
      ------Debian---------------------------
      $ x :1 -query 10.2.11.21  : show a xserver from Centos in tty8 (differ @ VBox)
      $ ctrl+c                  : then will be terminated in CentOS

      Beziehung der X-server
      <ip-addresse|hostname>:NummerDisplay.NummerdesBildshirms
      Zaehlung immer ab 0
      $ echo $DISPLAY
      > :0:0

      Display log under
      $ cat /var/log/Xorg.0
      $ cat /var/log/Xorg.1

    - X-Display export (Debian run -> CentOS show)
      @ Debian
      $ export DISPLAY=10.2.11.21:0 : (CentOs IP)

      @ CentOS
      $ xhost +10.2.11.23           : Serverseitige Massnahmen (Debian IP)

      @ Debian
      $ <progamm>                   : will be shown in CentOS side

      Senden der Ausgabe eines X-clients an einen entfernten X-server
      Auf dem PC des X-clients muss die DISPLAY-Variable entsprechend gesetzt werdern,
      z.B. mit "export DSIPLAY=<ip address>:0"
      Auf dem PC do die Ausgabe stattfinden soll, muss der Zugriff fuer den anderen PC
      erlaubt werden xhost +<ip address>
      X-client im entsprechend Terminal starten

    - X-display infor
      $ xdpyinfo  : X-display variables
      $ xwininfo  : window information utility for X-server

    - Best way of display is simply "SSH"
      $ ssh -X 10.2.11.2

    - Hilfe fuer Behinderte
      Screenreader : Orca, Emacspeak
      GOK          : on screen keyboard
      Sticky Keys  : zur Kombination hintereinander gedrueckter Tasten  als Tastenkombinationen
      Tastenversoegerung : schnell wiederholte Tastenanschlaege werden  als einziger anschlag
      gewertet
      Hocherkontrast der Anzeige
      Bildschirmlupe
      Tonsignale optisch umgesetzt


<br/><a name="administrative_tasks"></a>

### Administrative Tasks
    ===============================================================================================
    LPIC 107: Administrative Tasks
    ===============================================================================================
    - User and group
      - /etc/passwd (Benutzerdatenbank) 7 Fields
      Name : PW : UID : GID : Address&Tel : HomeDir : LoginShell
      - /etc/shadow (Passwortdatenbank) 9 Fields
      Name : PW : lastch : Minch : Maxch : Warning : remainday : expireddate : future
      - /etc/group
      Name : PW : GID : Migglieder1,2,3
      - /etc/gshadow
      Name : PW : GID : Migglieder1,2,3
      - /etc/default/useradd (Resourcendatei fuer useradd)
      - /etc/login.defs      (Resourcendatei fuer login)

      Caution!
      1 ein "x" in 2nd field for activate status, but with "*" user will be deactivated.
      2 ein "!" at shadow, if superuser makes a lock via usermod -L for that user.
      3 user name and home dir need not match
      4 loginshell part can also be /bin/passwd, this  might be used on a samba or pop to
      enable user to change their password via ssh wihtout granting login shell access

      -rw-r--r-- 1 root root   1872 Sep  8 23:45 passwd
      -rw-r----- 1 root shadow 1070 Sep  8 23:45 shadow

      $ passwd 	       : change password
      $ passwd -l user       : lock and -u for unlock
      $ passwd -e user       : immediately expire
      $ passwd -w 100 user   : set the number of days of warning
      $ passwd -x 100 user   : maximum number of days a password remains valid

      # chfn & finger  : can change personal data as like name, telefone, office number

      $ useradd -m -c "Donghee Kang" -s /bin/sh kang
      -c Comment
      -m take option in /etc/default/useradd
      -p password (depends on distribution)
      -s alternative shell
      $ useradd user    		: without -m means NO home directoy
      $ adduser user 			: some distribution also possible

      $ userdel -rf user     		: delete recursively home and also mail spool
      $ usermod -L dominik 		: Lock user account
      $ usermod -U dominik   		: Unlock user account
      $ usermod -e 2000-01-01 user 	: expiredate
      $ usermod -aG video kang    	: give an extra group to user kang
      $ usermod -G management michala : michaele is now only belong to group management
      she will left previous group

      $ chage -l kang              : administrative tool for passwd and expiring date
      $ chage -M 100 kang          : set Password Expiry Date for an user (100 Days)
      $ chage -I 6 kang            : force account to be locked after 6 inactivity days
      $ chage -E "2009-05-31" kang : set the Account Expiry Date for an User
      $ chage -E -1 -I -1 kang     : no password inactive and no account expire

      $ groupadd produktion -g 333 : create with GID 333
      $ groupdel produciton        : delete
      $ groupmod mtkg -n marketing : rename gorup from mtkg to marketing
      $ groupmod -p sales	         : remove password of group sales
      $ gpasswd -r sales           : remove password of group sales

      Not only superuser but also normaluser can change their group
      $ newgrp newgroup            : change primary group by newgroup

      Befehle zur Verwaltung des Shadow-Systems
      $ pwconv         : transferieren der Passwort-Hashes aus passwd -> nach shadow
      $ pwunconv       : shadow -> passwd
      $ pwck		     : Konsistenz von passwd u. shadow ueberpruefen
      $ grpconv
      $ grpunconv
      $ grpck

      Check a user whether exist or not
      $ getent passwd kang    : if exist will show passwd info, if not nothing shows
      $ echo $?		: if it is wrong then will show "2"

      $ id kang		: display UID and GID
      $ groups  		: list of all group for this user

      Generates a compact listing of all the users on the system.
      $ cut -d: -f < /etc/passwd | sort | xargs echo

    - crontab
      regular scheduled jobs as deleting old temporary files and monitoring disk space
      $ cat /etc/crontab    : crontable
      > Mim  Stunde  Tag  Mon  Woche  User Command
      0=Sun, 1=Mon, ..., 6=Sat, 7=Sun

      # For system cron need username
      # Sonntag um 4:22 cron weekly
      > 22  4  *  *  0 root run-parts /etc/cron.weekly
      # Monta - Freitag immer um 8:00,10:00,13:00,16:00 Uhr einen Virenscan
      > 0  8,10,13,16  *  *  1-5 root myscripts
      # Jede 30 minuten zwischen 8-16 Uhr einen virusscan in der Woche
      > 0/30  8-16  *  *  1-5 root virusscan
      # Monthly virusscan by root
      > @monthly root virusscan

      # cron jobs for user specification do not need username,
      # execute every other hour(every 2 hours interval)
      > 15  */2  *  *  * /user/local/bin/cleanup

      $ crontab -l -u kang    : display scheduled crontab by kang
      $ crontab -e 		: edit crontab by current user (nano or vi)
      $ crontab -r 		: remove crontab datei of this user
      $ cat /var/spool/cron/crontab/kang  : user specific crontab via crontab -e

      # /etc/cron.{d,daily,hourly,monthly,weekly}
      # /etc/cron.allow  : listed user can access
      # /etc/cron.deny   : listed user cannot access, not cron.allow file
      # /etc/at.allow    : listed user can access
      # /etc/at.deny	   : listed user cannot access, not at.allow file

      Neither cron.allow and cron.deny no users may have user specific crontabs
      If both cron.allow and cron.deny are exsist, all user allow cron access
      If both   at.allow and   at.deny are exsist, only root can access "at"
      but eine leere "at.deny"-Datei ermoeglicht den Zugriff fuer alle Benuter

    - at
      NOT regular time but just one scheduled time
      $ /etc/init.d/atd start    : atd daemon start
      $ at 17:00 tomorrow	   : 17:00 tomorrow something will be done
      at> ./home/kang/myscript1
      at> ./home/kang/myscript2
      at> Ctrl+D
      $ at now + 1 minute
      at> ls -l
      at> Ctrl+D
      $ at -f script 12:00 2000-03-02 : jobs by scripts
      $ atq 				: list queue
      $ at -l				: list queue
      $ atrm  444			: delete
      $ at -d 444			: delete
      $ at -m 			: send mail
      $ at -b 			: batch 1.5 load
      batch beruecksichtigt Systemlast zum geplanten Zeitplunkt und verzoegert Ausfuehrung

    - anacron (kein daemon)
      is supplement to cron that helps ensure that log rotation, daily backups,
      and cron task, as batch job if execution was not properly done at that time,
      are handled even when the computer is shut down.
      $ cat /etc/anacrontab
      > perionds in day,  delay in muinuts,  jod,   command
      >  1   65  cron.daily       nice run-parts /etc/cron.daily
      >  7   70  cron.weekly      nice run-parts /etc/cron.weekly
      > 30   75  cron.monthly     nice run-parts /etc/cron.monthly

      - Time
      Time zone configuration
      $ cat /etc/timezone
      $ tzselect                   : set time zone Europe/Berlin
      $ export TZ='Pacific/Majuro'

      control the system time and date
      $ timedatectl                : show time information in detail
      $ timedatectl set-ntp true   : NTP network time synch is enable

    - Local
      $ cat /usr/bin/local
      $ locale -a                  : possible local
      # locale variables
      LANG=de_DE.UTF-8     : language_country
      LANGUAGE=en_US
      LC_*
      LC_ALL=

      In the shell, if you set LANG=C, then localisation will be deactive
      It forces applications to use the default language for output

      Test a script on a computer for Great Britain,
      Type export LC_ALL=en_GB.UFT-8 and run the script in the same shcell

      iconv can use for converting between win(euc-kr) and ubuntu(utf-8)
      $ iconv -f euc-kr -t utf-8 src.txt -o dst.txt
      $ iconv -f utf-8 -t euc-kr src.txt -o dst.txt

      # ISO-8859 : 8-Bit Europe standard
      # ASCII    : 7 Bit 128 Character -> spaeter 8 Bit 256 Character
      # Unicode  : global encoding system
      # UTF-8    : 8-Bit encoding for unicode


<br/><a name="system_services"></a>

### Essential System Services
    ===============================================================================================
    LPIC 108: Essential System Services
    ===============================================================================================
    - Time syncronization
      time information
      # /usr/share/zoneinfo/
      # /etc/timezone  : small information ,show like Europe/Berlin
      # /etc/localtime : binary Datei, used in kernel for einstellung
      : it is copy of timezone file(usr/share/zoneinfo/Europe/Berlin)

      $ data 12110710				              : set to 7:10 a.m on Dec.11[mmddMMDD]
      $ date -u                                 : show date with utc format
      $ date '+%u'                              : Which day of today? (Mo-So)
      $ date -s 1755                            : change time
      $ date "+Heute  %d.%m.%Y. %j days of %Y"  : How much day?

      RTC (Real time clock)
      UTC (Coordinated Universal Time = GMT)
      hwclock in BIOS(hc=RTC) and date in kernel(sys=UTC)
      $ hwclock --systohc
      $ hwclock --hctosys
      $ hwclock --show
      $ hwclock -w

      NTP network time protocol
      $ ntpdate pool.ntp.org   : access to standard ntp server
      $ /etc/init.d/ntpd start : daemon start
      $ /sbin/ntp 		 : or directly do this
      $ cat /etc/ntp.conf      : option file for ntp
      > server 0.debian.pool.ntp.org
      > server 1.debian.pool.ntp.ort
      $ ntpq -p                : information about current ntpd server connection
      $ ntpdc                  : ntp diagnose with interactive mode
      > sysinfo
      The local server will poll all server in the public NTP server poll in /etc/ntp.conf
      and use wichever site provides the cleanest time data

    - Log system protocol (debian und centos)
      $ /etc/rc.d/init.d/syslog restart
      $ /sbin/syslogd          : System log daemon
      $ cat /var/log/syslog    : service of log data by syslogd

      $ cat /etc/syslog.conf   : log system configuration, syslog-ng gibt auch
      $ cat /etc/rsyslog.conf  : Heute benutzen diese Konfiguration Datei
      > Syntax:
      > facility.priorityaction
      > Beispiel:
      > mail.warn             /var/log/mail
      > mail.news.warn        /var/log/messages
      > kern.crit 		/var/log/kernel.log
      > cron.*                /var/log/cron.log
      > lpr.*    		/var/log/lpr.log
      facility of syslog : login0, mail daemon, cron, user, lpr, news, auth, kern

      If I want to get and display messages on the concole 8, then have to modify
      rsyslog.conf with following sentence
      > *.* /dev/tty8

      $ /sbin/klogd        : kernel message daemon in init-process
      $ /proc/kmsg         : einstellungs Datei von klogd
      $ klogd -c n         : set loglevel
      $ klogd -d           : active debugging mode
      $ klogd -f data      : replace output from syslog

      Selbst Ereignisse loggen
      $ logger -t my_log_message  here is my message!

      logrotate in order to save log files by rotation
      To run lograotate on a regular, have to use cron
      /etc/logrotate.conf    : configuration
      /etc/logrotate.d       : contents, references

      Einsatz von Logdateien zur Fehlersuche
      $ less /var/log/messages
      $ tail -f /var/log/messages   : zeigt "Live" neue Zeilen an.
      $ grep sshd /var/log/messages | grep invalide | less

      Where is major log files?
      # /var/log/dmesg      : booting message in RAMdisk with Ring buffer
      # /var/log/messages   : system log
      # /var/log/secure     : login log, authentication and authorization privileges
      # /var/spool/cron     : cron logs
      # /var/log/mail.debug : for mail debugging

      For Debian, ubuntu and centOS case, mostly and nowadays systemd is used.
      wenn Sie systemd statt upstart oder SysVinit, dann journald ist antwortlich.
      $ journalctl
      -f : show same as "tail -f /var/log/syslog"
      -e : jump to end of the journal
      -r : show log reversely
      -b : show log after last booting
      -k : nur kernel meldung!
      -n : number of lines
      $ journalctl --since "2015-01-10" --until "2015-01-11 03:00"
      $ journalctl --since yesterday
      $ journalctl --since 09:00 --until "1 hour ago"
      $ journalctl -b -u nginx -o json     : unit=ngix output=json

      $ cat /etc/systemd/journald.conf
      Storage    : "volatile" in RAM,  "persistent" in Hard-disk
      Compress   : by option "Yes"
      SplitMode  : Journal pro user by option "login"
      MaxFileSec : option by "1week" or "1year"
      $ cat /var/log/journal/    : somtimes not here, but in /var/log/syslog

      From lowest to highest priority code :
      debug - info - notice - (warning) - (err) - crit - alert - emerge,(panic)

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

    - Mail alias by user
      ~/.forward
      $ vi .forward
      > aaa@gmail.com

      - Mail alias by root
      $ vi /etc/aliases
      > postmaster: root                  : postmater is root
      > kang: donghee.kang@gmail.com      : mail alias
      > anna: \anna,walter,testuser       : "\" needed
      > kunden: :include:/etc/kunden.txt  : list contain all e-mail of kunden
      > some: "/home/donghee/dailyreport" : daily report
      > major: "|/usr/bin/wrap mailing"   : Mailinglisten programm work with pipe
      $ newaliases                        : to make activation of this alias

    - mail (MUA simple mail program)
      -a : attachment
      -c : cc
      mail -s "Test Subject" user@example.com < /dev/null
      mail -a /opt/backup.sql -s "Backup File" user@example.com < /dev/null
      echo "This is message" | mail -s "test" max@lucky.de

    - Postfix (SMTP mail server)
      modular, gut gepflegt, juengster MTA
      /var/spool/postfix        : queue list
      /etc/postfix/main.cf      : configuration

      Service start and stop
      $ /etc/init.d/postfix start

      Smarthost(SMTP-Relay) configuration
      $ vi /etc/postfix/main.cf
      > relayhost = mail.xxxx.org
      > ntnetwork = 127.0.0.0/8 192.168.100.0/24 172.16.0.0/28

      $ mailq     : management of postfix mail queue.

    - sendmail (SMTP mail server)
      /var/log/mail.log  	  : log
      /etc/mail/*      	  : configuration

      Service start and stop
      $ service sendmail restart
      $ /etc/rc.d/init.d/sendmail stop

      Smarthost(SMTP-Relay) configuration
      convert sendmail.mc to sendmail.cf
      $ m4 sendmail.mc > sendmail.cf
      $ vi /etc/mail/sendmail.cf
      > DSmailrelay.xxxx.org.

      Simple way to use sendmail
      $ cat /tmp/email.txt
      > bla bla bla
      $ sendmail user@example.com < /tmp/email.txt
      $ sendmail -q   : veranlasst Senden von Mails
      $ sendmail -bq  : list of mail queue

    - stop Sendmail and start Postfix
      $ /etc/rc.d/init.d/sendmail stop
      $ chkconfig --del sendmail
      $ chkconfig sendmail off
      $ chkconfig --list sendmail
      $ alternatives --config mta
      $ chkconfig postfix on
      $ chkconfig --list postfix

    - dovecot (POP server)
      POP server
      $ /etc/rc.d/init.d/dovecot start
      chkconfig dovecot on
      chkconfig --list dovecot

    - other SMTP server
      exim  : historish, Standard in Debian
      qmail : histority, modular, nicht meher weiterentwicklen

    - printer
      Smart filter make print for differne file types correctly
      BSD   -> Apple
      SysmV -> Oracle
      CUPS  -> Common Unix Printing System by Apple

      GUPS lpc is much simpler than BSD LPD and LPRng

      GUPS ist ein Druckserver, wurde fuer IPP (Internet Printing Protocel) entwickelt
      kann local oder remote verwendet werden
      bringt "traditionelle" Druckerbefehle mit
      ist per Web-Browser administrierbar

      In the web-browser goes to
      > http://localhost:631
      then add network printer by ipp protocol
      ipp:/xxx.xxx.com:631/printers/laserjet5
      If you don't have ppd driver of printer,
      try generic with option "postscript recommended"

      Printer daemon start
      $ /etc/init.d/lpd start     : lpd
      $ /etc/init.d/cups start    : cups with ipp

      lpd legacy interface (lpr, lprm, lpq)
      $ lpr -Plaserjet5 file.ps	 : print file.ps
      $ lpq -Plaserjet5                : List of queue in printer
      $ lprm -Plaserjet5 430           : delete queue
      $ lp -d OKI /etc/passwd          : printer name with -d

      Line printer control
      $ lpc
      lpc> status
      lpc> up laserjet            : enable and start
      lpc> up all
      lpc> topq laserjet 456      : priority as # 1 move
      lpc> stop                   : running job will be finished
      lpc> disable 		    : running and waiting job will be finished
      lpc> abort 		    : immediately stop and not working anymore

      configuration file
      $ vi /etc/cups/printers.conf
      $ vi /etc/cups/cups.conf

      Start cups after finishing configuraiton
      $ /etc/init.d/cupsys restart

      mpage : to print a four-page PostScript file on a single sheet of paper


<br/><a name="networking_fundamental"></a>

### Networking Fundamental
    ===============================================================================================
    LPIC 109: Networking Fundamental
    ===============================================================================================
    - IP and bit
      IPv4 :  32 Bit Addressen, darstellt in DDN(dotted deciaml notation)
      IPv6 : 128 Bit
      MAC  :  48 Bit

      IPv4 <-> IPv6 : Dual Stack, Tunneling for convertign between them

    - IPv6
      Addresslanger ist 128 Bit
      Die ersten 64Bit stellen die sogenannte Network-ID dar
      Die letzten 64Bit stellen die "lnterface-ID" dar
      Adressnotation ist hexadezimal; jeweils 2 Byte (16 Bit) werden zu einem Block
      zusammengefasst, der Doppelpunkten von anderen Blocken getrennt ist
      fe80:0000:0000:0000:020d:deif:gebe:22fc
      1   :2   :3   :4   :5   :6   :7   :8
      Adressvereinfachung
      fe80::20d:deif:gebe:22fc

      Ipv6 beherrscht "Autokonfiguration", d.h. ein Host kann sich selbst
      (teilweise mit Hilfe eines Routers) Adressen geben
      IPv6 hat eingebaute Unterstuezung fuer IPSec
      IPv6 hat eingebaute Unterstuezung fuer QoS
      IPv6 kennt Keinen Broadcast, statt dessen wird Multicast verwendet
      IPv6 benutzt kein ARP(Address Resolution Protocol);
      statt dessen wird das "Neighbor Discovering Protocal" verwendet
      (eigentlich eine Sammlung von ICMPv6-Befhelen)
      IPv4 verwendet das Internet Group Management Protocel(IGMP) zur Verwaltung
      der Zugehoerrigkeit eines Hosts zu Multicast-Gruppen; IPv6 verwendet dafuer
      "Multicast Listener Discovery" (ICMPv6)
      Die Resourceneintraege im DNS haben den Typ A bei IPv4; bei IPv6 ist Typ AAAA

    - IPv6-Adresstypen
      Link local Unicast   : fe80(bis febf)::<Interface Identifier>
      (link local)	       jeder IPv6-Host wiest sich seine link-lokale Adresse
      sebst zu, Diese Adresse wird fuer viele administrative
      Zwecke benoetigt, z.B. als Quelladresse
      bei der Kommunikation mit einem Router

      Unique-Local Unicast : fc00::<fd00>
      entsprechen ungefaehr den privaten IPv4 Adressen

      Global Unicast       : 2000(-3fff) Weltwiet einduetige, routbare Adressen,
      entsprechen den oeffentlichen IPv4 Adressen

      Multicast  	         : ff00(-ff..) fuer die Mitglieder von Multicastgruppen

      Pruefung braucht folgenden Wissen fuer Konvertierung
      MAC address <-> IPV6
      08:00:27:72:c1:7d  <-> fe80::a00:27ff:fe72:c17d

      ::1       (loopback address wie ipv4=127.0.0.1)

      DHCPv6 server (ff02::1:2) have two different set(configuration) modes namely
      stateless local(Auto-configuration) and stateful local(No Auto-configuation)

    - Network Modell
      DoD Modell TCP/IP Modell
      Application Layer  : HTTP, FTP, SMTP, DNS, Telnet
      Host do Host	   : TCP, UDP
      Internet Layer 	   : IPv4, IPv6, ICMP
      Netwrok Access     : Ethernet, PPPoE, Token Ring, FFDI

      7 Layer	OSI layer model
      Application Layer  : HTTP, FTP, SMTP, DNS, Telnet
      Presentation       : ASCII, MPEG, JPEG, MIDI
      Session		   : NetBIOS, SAP, SDP, NWLink
      Transport(Segment) : TCP, UDP, SPX
      Network(Packet)    : IPv4, IPv6, RIP, OSPF, ICMP, IGMP, ARP
      Data Link(Frame)   : Ethernet, PPPoE, Token Ring, FFDI, ATM
      Physical Layer     : devices

    - Port number
      $ cat /etc/services
      Total 65536(=2^16Bit) Ports available.

      0 -1023   : Well known Ports
      1024-49151  : registered ports
      49152-65535  : client ports

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
      3128: Proxy server port
      7100: X-Font server port
      8080: extended HTTP port

    - Definitions
      Subnetting : change from host to network in IP address
      TCP(Transmission Control Protocol) : bidirection stream
      UDP(User Datagram Protocol) : one direction
      ICMP(Internet Control Message Protocol) : ping
      TCP : zuverlaessig durch Bestaetigungen, verbinungsorientiert,
      Segmente sind nummeriert, relativ grosser Header
      UDP : keine Verlaesslichkeitsmechanismen, verbindungslos,
      Segmente sind unnumeeriert, relativ Kleiner Header

      If I have an IP address then it has 2 diveded parts
      218,128.32.000|00000
      Network<-|->Host

    - IP Class
      A Class (  1 - 127)
      126=2^7-2 nets, 16M=(2^24-2) hosts per nets
      Private : 10.0.0.0/8 - 10.255.255.255 (1 nets)
      Loopback: 127.0.0.0 and 127.0.0.1
      Netz 127.0.0.0 Loopback-Netzwerk, wird fuer hostinterne Zwecke benutzt,
      Meist ist 127.0.0.1 in Benutzung, weil er ist ein host-device and schnittstelle

      B Class (128 - 191)
      16,382(2^14-2) nets, 65,534=(2^16-2) hosts per nets
      Private: 172.16.0.0/20 - 172.31.255.255 (16 nets)
      Heimnetzwerk: 169.254.0.0/16

      C Class (192 - 223)
      2,097,150=(2^21-2) nets, 254=(2^8-2) hosts per nets
      Private: 192,168.0.0/16 - 192.168.255.255 (256 nets)

      D Class (224 - 239)
      Reserved for multicast groups

      E Class (240 - 254)
      Reserved for future use.

    - Heimnetzwerk: 169.254.0.0/16
      Addressen fuer Automatische Private IP-Adressierungs werdern wervendet,
      wenn ein fuer DHCP Konfigurierter Host Keinen DHCP-Server erreicht.
      Diese Adressen werden auch als "link-lokale" Adressen bezeichnet
      For zero configuration networking reserved subnet at peer to peer or wirless.
      it is normally not necessary to configure networking without DHCP environment

    - Concept of Subnetting
      FSLM (fixed lengteh subnet mask)
      VSLM (Variable length subnet mask)

      Q) Do set total 3 subnet, which has 2046 hosts per subnet, for 172.16.0.0 IP
      1.calculate x for 2046 = 2^x+2
        x=11 means host bit is now 11
      2.subnet mask can calculate from obtained host bit 11
        11111111.11111111.11111000.00000000
      3.now you exchange with decimal, then that give you subnet mask
        255.255.248.0
        or easily get via 256-8=248
      4.each network have 2048 addresses and each position can have 256 address
        so at least 2048/256=8 units(interval) in third position are necessary
        therefore you need to devide third part of IP with 0-7,8-15,16-23
        1 subnet 172.16. 0.0 - 172.16. 7.255
        2 subnet 172.16. 8.0 - 172.16.15.255
        2 subnet 172.16.16.0 - 172.16.23.255
        4.another method simple tip using binary add
        255.255.255.255 - 255.255.248.0 = 0.0.7.255
        172.16. 0.0 + 0.0.7.255 = 172.16. 7.255
        172.16. 8.0 + 0.0.7.255 = 172.16.15.255
        172.16.16.0 + 0.0.7.255 = 172.23.15.255
      5.so finally you have 3 subnets
        1 subnet 172.16. 0.1 - 172.16. 7.254
        2 subnet 172.16. 8.1 - 172.16.15.254
        2 subnet 172.16.16.1 - 172.16.23.254

      Q) Do make subnet for 218.128.32.0/24, which has 25 hosts per subnet
      1.calculate x for 25 < 2^x+2
        x=5 means host bit must be bigger than 5, leads to 32 hosts per subnet
      2.subnet mask can calculate from obtained host bit 5
        11111111.11111111.11111111.11100000
      3.now you exchange with decimal, then that give you subnet mask
        255.255.255.224
        or easily get 256-32=224
      4.so
        218.128.32.  0 + 0.0.0.31 = 18.128.32. 31
        218.128.32. 32 + 0.0.0.31 = 18.128.32. 63
        218.128.32. 64 + 0.0.0.31 = 18.128.32. 95
        ........................
        218.128.32.192 + 0.0.0.31 = 18.128.32.223
        218.128.32.224 + 0.0.0.31 = 18.128.32.255
        one can find subnet mask here again from 4th decimal of last start ip
        255.255.255.224
      5.finally you have 8 subnets and each subnet have 30 hosts
        218.128.32. 0   network IP
        218.128.32. 1   start of host IP
        218.128.32.30   end of host IP, total 30 hosts in this netz
        218.128.32.31   use as a broadcast

      Q) For IP = 200.1.1.0/24, asymetric subnets are required as
        A department = 100 hosts
        B department =  25 hosts
        C department =   2 hosts
      1.A needs total 102 hosts 2^x+2 > 102 -> x=7bit  range=128
        B needs total  27 hosts 2^x+2 >  27 -> x=5bit  range= 32
        C needs total   4 hosts 2^x+2 >   4 -> x=2bit  range=  6
      2.IP range=128,32,6 can safely be used as subnet masking by demanding
        A 200.1.1.  0-200.1.1.127
        B 200.1.1.128-200.1.1.159
        C 200.1.1.160-200.1.1.163
      3. subnet masks are
        A 255.255.255.128  via 128
        B 255.255.255.160  via 128+32
        C 255.255.255.166  via 128+32+6
      3.If you represent CIDR (Classless Inter-network Domain Routing) notration
        A 200.1.1.  0     255.255.255.128  -> 11111111.11111111.11111111.10000000
        B 200.1.1.128     255.255.255.160  -> 11111111.11111111.11111111.10100000
        C 200.1.1.160     255.255.255.166  -> 11111111.11111111.11111111.10100110
      4.then count how many "1" in binary number.
        A 200.1.1.  0/25  -> 11111111.11111111.11111111.10000000
        B 200.1.1.128/26  -> 11111111.11111111.11111111.10100000
        C 200.1.1.160/28  -> 11111111.11111111.11111111.10100110

      - Subnetting example
      Q) Netz 192.168.1.0/24  ->  4 Subnetze
      1. Wieviele zusaetzliche Einsen in der Netzwerkmaske brauche ich? en Exponenten von
        2^n muss ich benutzen, um die Zahl als 2er-Potenz auszudruecken?
        Im Beispiel 2^2=4 -> 2 Zusaetzliche Einsen in der NM.
      2. Wie ist der Dezimalwert der letzten(am weitersten rechts stehenden) 1 in der maske
        Im Bespiel: 2 Eins im 4.Oktett hat den Dezimalwert 64, dieser wert 64 bilde mein
        "Inkrement", d.h. den Abstand der Subnetze voneinander
      3. Dann haben wir folgende Netzwerkadressen
        192.168.1.  0/26 -  63/26   :  1- 62  total 62 hosts available
        192.168.1. 64/26 - 127/26   : 65-126  total 62 hosts available
        192.168.1.128/26 - 191/26   :129-190  total 62 hosts available
        192.168.1.192/26 - 255/26   :193-254  total 62 hosts available
      4. subnetmaske ist (256-64=192)
        255.255.255.192

      Q) find CIDR und DDN Networkmask for Hosts number
        for DDN 2^ 8 Bit
        for DDN 2^16 Bit
        430    512=2^9 Bit -> 32-9 = /23  : 256- 2 @ 2nd -> 255.255.255.  0
        22 	     2=2^5 Bit -> 32-5 = /27  : 256-32 @ 1st -> 255.255.255.224
        32 	    64=2^6 Bit -> 32-6 = /26  : 256-64 @ 1st -> 255.255.255.192
        600   1024=2^10Bit -> 32-10= /22  : 256- 4 @ 2nd -> 255.255.252.  0
        3500	4096=2^12Bit -> 32-12= /20  : 256-16 @ 2nd -> 255.255.240.  0
        4        8=2^3 Bit -> 32-3 = /29  : 256- 8 @ 1st -> 255.255.255.248

      Q) Netz 135.22.0.0/16 -> 16 subnetz
      1. 256/16 = 16 Abstand = 2^4 Bit -> 32-4 = 28-8(2nd) = /20 (CIDR)
      2. 135.22. 0.0/20 - 132.22.15.255/20
        135.22.16.0/20 - 132.31.15.255/20
        135.22.32.0/20 - 132.47.15.255/20
      3. subnetmake ist (256-16=240)
        255.255.240.0

      Q) Netz 213.22.50.0/24 -> 4 subnetz
      1. 256/4 = 64 Abstand = 2^6 Bit -> 32-6 = 26(1st)  /26 (CIDR)
      2. 213.22.50.  0 - 213.22.50. 63/26
        213.22.50. 64 - 213.22.50.127/26
        213.22.50.128 - 213.22.50.191/26
        213.22.50.192 - 213.22.50.265/26
      3. subnetmake ist (256-64=192)
        255.255.255.192

      Q) Netz 131.120.0.0/16 -> 8 subnetz
      1. 256/8 = 32 Abstand = 2^5 Bit -> 32-5 = 27-8(2nd) /19 (CIDR)
      2. 131.120. 0.0 - 131.120. 31.255/19
        131.120.32.0 - 131.120. 63.255/19
        131.120.64.0 - 131.120. 95.255/19
        131.120.96.0 - 131.120.127.255/19
      3. subnetmake ist (256-32=224)
        255.255.224.0

      Q) Netz 100.0.0.0/8 ->  64 subnetz
      1. 256/64 = 4 Abstand = 2^2 Bit -> 32-2 = 30-8-8(3rd) /14 (CIDR)
      2. 100. 0.0.0 - 100. 3.255.255/14
        100. 4.0.0 - 100. 7.255.255/14
        100. 8.0.0 - 100.11.255.255/14
        100.12.0.0 - 100.15.255.255/14
      3. subnetmake ist (256-4=252)
        255.252.0.0

    - Subnetting for VLSM (Variable length subnet mask)
      Aufteilen eines Netzwerks in unterschidelich grosse Subnetze
      Vorgeben: Anhand der geforderten Anzahl von Hosts im jeweiligen Sub-netz
      legt man die Anzahl der "Hostbits" (Nullen) in der Netzwerkmaske fest.
      Man bestimmt die Zwierpotenz, die oberhalb der Anzahl der Hosts liegt.
      Bespiel: In einem Netz sollen 100 Hosts untergebracht werden.
      Die naechste 2-er Potenz (bzw.alse 2er-Potenz darstellbare Zahl) ueber
      100 ist 128(2^7) wir benoetigen also 7 Hostbits fuer dieses Netz.
      Uebrig bleiben 25(=32-7)Netzwerkbits.
      Unsere Maske fuer dieses Netz muss also /25 bzw 255.255.255.128

      Q) Netz: 192.168.1.0/24, jeweils 100,50,10,10,10,10 Hosts
        192.168.1.  0 - 192.168.1.127/25 :128 Abs = 2^7 Bit -> 32-7 = /25
        192.168.1.128 - 192.168.1.191/26 : 64 Abs = 2^6 Bit -> 32-6 = /26
        192.168.1.192 - 192.168.1.207/28 : 16 Abs = 2^4 Bit -> 32-4 = /28
        192.168.1.208 - 192.168.1.223/28 : 16 Abs = 2^4 Bit -> 32-4 = /28
        192.168.1.224 - 192.168.1.239/28 : 16 Abs = 2^4 Bit -> 32-4 = /28
        192.168.1.240 - 192.168.1.255/28 : 16 Abs = 2^4 Bit -> 32-4 = /28

      Q) Netz: 194.2.7.0/24, jeweils 58x2,22x1,13x4,4x4 (Hosts x Netz)
        194.2.7.  0 - 194.2.7. 63/26 :64 Abs = 2^6 Bit -> 32-6 = /26
        194.2.7. 64 - 194.2.7.127/26 :64 Abs = 2^6 Bit -> 32-6 = /26
        194.2.7.128 - 194.2.7.159/27 :32 Abs = 2^5 Bit -> 32-6 = /27
        194.2.7.160 - 194.2.7.175/28 :16 Abs = 2^4 Bit -> 32-4 = /28
        194.2.7.176 - 194.2.7.191/28 :16 Abs = 2^4 Bit -> 32-4 = /28
        194.2.7.192 - 194.2.7.207/28 :16 Abs = 2^4 Bit -> 32-4 = /28
        194.2.7.208 - 194.2.7.223/28 :16 Abs = 2^4 Bit -> 32-4 = /28
        194.2.7.224 - 194.2.7.231/29 : 8 Abs = 2^3 Bit -> 32-3 = /29
        194.2.7.232 - 194.2.7.239/29 : 8 Abs = 2^3 Bit -> 32-3 = /29
        194.2.7.240 - 194.2.7.247/29 : 8 Abs = 2^3 Bit -> 32-3 = /29
        194.2.7.248 - 194.2.7.255/29 : 8 Abs = 2^3 Bit -> 32-3 = /29

      Q) Netz: 131.12.20.0/22, jeweils 1x500,4x38,1x220 (Hosts x Netz)
        131.12.20.  0 - 131.12.21.255/23 : 512 Abs = 2^9 Bit -> 32-9 = /23
        131.12.22.  0 - 131.12.22.255/23 : 256 Abs = 2^8 Bit -> 32-8 = /24
        131.12.23.  0 - 131.12.23. 63/23 :  64 Abs = 2^6 Bit -> 32-6 = /26
        131.12.23. 64 - 131.12.23.127/23 :  64 Abs = 2^6 Bit -> 32-6 = /26
        131.12.23.128 - 131.12.23.191/23 :  64 Abs = 2^6 Bit -> 32-6 = /26
        131.12.23.192 - 131.12.23.255/23 :  64 Abs = 2^6 Bit -> 32-6 = /26

      Q) Netz: 27.43.16.0/20, jeweils 1800,900,480,1x100 (Hosts x Netz)
        27.43.16.  0 - 27.43.23.255/21 :2048 Abs = 2^11 Bit -> 32-11 = /21
        27.43.24.  0 - 27.43.27.255/22 :1024 Abs = 2^10 Bit -> 32-10 = /22
        27.43.28.  0 - 27.43.29.255/23 : 512 Abs = 2^ 9 Bit -> 32- 9 = /23
        27.43.30.  0 - 27.43.30.127/25 : 128 Abs = 2^ 7 Bit -> 32- 7 = /25
        27.43.30.128 - 27.43.30.255/25 : 128 Abs = 2^ 7 Bit -> 32- 7 = /25
        27.43.31.  0 - 27.43.31.127/25 : 128 Abs = 2^ 7 Bit -> 32- 7 = /25
        27.43.31.128 - 27.43.31.255/25 : 128 Abs = 2^ 7 Bit -> 32- 7 = /25

      Q) How many IP addresses can be used for unique hosts inside Ipv4 subnet?
        192.168.2.218/28
        Answer is 14 from 2^4-2

    - Basic network
      Check host name of computer
      $ cat /etc/hostname
      > debian-kang

      check all possilbe network services
      $ cat /et/services
      Check https service
      $ grep ^https /etc/services

    - DHCP
      Dynamic Host Configuration Protocol
      automatic allocation
      DHCP client : pump, dhcpcd, dhclient

      dhclient can bring up an interface on eth1
      $ dhclient eth1

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
      very useful with setting of "search"
      $ cat /etc/resolv.conf
      > domain schule.local       : local domain name
      > search rootman.org  	    : if use telnet, ask to DNS for linux.rootman.org first
      > nameserver 134.75.30.1    : DNS server address 1
      > nameserver 203.241.192.9  : DNS server address 2

    - Netwrok interface configuration
      Network configuration general
      $ cat /etc/network/interfaces                    : for Debian
      $ cat /etc/sysconfig/network                     : for CentOS
      $ cat /etc/sysconfig/network-scripts/ifcfg-eth0  : for CentOS

      Service start
      $ /etc/init.d/networking restart    (for Debian)
      $ /etc/rc.d/init.d/network restart  (for CentOS)

      Konfigurationsprogramme fuer Network interface card
      $ ifconfig
      $ ifconfig eth1 down
      $ ifconfig eth1 209.14.5.88 netmask 255.255.255.192 up

      Network interface card on and off
      $ ifup eth0
      $ ifdown eth0

    - ip
      Information auslesen, Konfiguration verwenden.
      Einstllungen fuer Multicast vornehmen und auf Ethernet operieren
      Zur Ueberpruefung von Netwerkkomponenten immer gleich, unabhaengig davon,
      ob Sie IP-Adressen, routing-eintraege oder den ARP-Cache eines Computers
      diagonostizieren muessen. ip supports IPv6, but not for arp!
      $ ip route show          : show routing table in CIDR notation
      $ ip neighbour show      : MAC-Adressn  (apr -a)
      $ ip addr show dev eth1  : show info. about network interface
      networkmask in CIDR, not dotte format.
      $ ip tunnel show         : capsule of Ipv6 packet into IPv4
      $ ip monitor             : live monitoring for connection of MAC and IP

      $ ip link set eth0 up    				 : activate network interface
      $ ip addr add 192.168.50.5/24 dev eth0    		 : set IP to netwrok inerface
      $ ip addr del 192.168.50.5/24 dev eth0     		 : delele IP
      $ ip route add 10.10.20.0/24 via 192.168.50.100 dev eth0 : add static router
      $ ip route add default via 192.168.50.100  		 : set default gateway

    - MAC address access
      $ ip neighbour show
      $ arp -a  :  (address resolution protocol) using IP one can access MAC address

    - Routing
      configure to get a correct way of IP packet
      $ route      : result of routing table of network
      $ route -n   : show routing table indentified by IP address rather than hostnames
      $ route -C   : Cache of kernels
      $ route add deault gw     : default means -net 0.0.0.0 netmask 0.0.0.0

      Q) add 192.168.1.0 network to the router in eth0 device
        add a routing for network
        add traffic vi eth1 with 172.16.0.1 IP address
      $ route add -net 192.168.1.0 netmask 255.255.255.0 dev eth0

      Q) add IP 192.168.1.1 as default gateway
      $ route add default gw 192.168.1.1

      Q) add IP 192.168.2.1 default gateway with different metric
      $ route add default gw 192.168.2.1 dev eth0 metric 1
        add a host
      $ route add -host 192.168.10.10 dev eth0
      $ route add -host 192.168.10.10 netmask 255.255.255.255 dev eth0
      $ route add -host 192.168.10.10/32 dev eth0   <--- wrong(?) check(!)

      Q) host IP 172.16.0.1 has a router via gateway 192.168.0.1 in network
      $ route add -host 172.16.0.1 netmask 255.255.255.0 gw 192.168.0.1 dev eth0

      Default route is used
      if there is not a more specific route to a destination host or network

      Gatewayis a router that transfers data between network segments
      If you don't such configureation of gateway, it won't be able to communicate
      beyond its local network segment. But you will be still able to communicate
      with machineson your local network segment.

      dynamic routing protocol
      RIP (Routing Information Protocol)
      OSPF (Open Shortest Path first)

    - Connectivity test
      $ ping -c 1 141.1.1.1
      $ ping6 -c 1 2a00:1450:4017:801::100f
      $ ping6 ::1

      trace of IP packet till final destination during the track passes through
      TCP/IP is a connection-less service.
      If you use wrong IP, IP packet will create a infinite loop in the network.
      TTL make a subtracktion of packet number when packet passes through a router
      traceroute use reverse property of TCP/IP, so TTL value is added when
      the test signals passed through router.
      $ traceroute   www.xxxx.com
      $ traceroute6  www.xxxx.com
      $ traceroute -n 217.147.216.241
      $ tracepath -n 217.18.182.170	       : ohne Root-Rechte ueberpruefen
      $ tracepath -n 2a00:1450:4017:800::e   : n numerische um schnell zu machen

      netstat shows list of network connection via tcp, udp, socket
      p --program (PID & program)
      a --all     (all socket port)
      n --numeric (no DNS)
      $ netstat -na                                          all open port
      $ netstat -an | grep ":80 "                            just port 80
      $ netstat -at                                          tcp connected port
      $ netstat -na | grep LISTEN                            LISTEN port
      $ netstat -na | grep ESTABLISHED | wc -l               connected user
      $ netstat -na | grep *.80 | grep ESTABLISHED | wc -l   connected web user
      $ netstat -na | grep .1521 | grep ESTABLISHED | wc -l  connected DB user
      $ netstat -tun  				       tcp,udp,number
      $ netstat -l 					       route table
      $ netstat -pa		 			       open programm all

      nc(netcat) network conneciton tool
      $ nc -z daum.net 80
      $ while true; do date; nc -w1 -z www.google.com 80; done
      # option -z : zero-I/O mode used for scanning
      -w : timeout for connects and final net reads

      nmap - port scanning and defending networks
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
      -P0  : no Ping to host

      Welche Ports lauscht aktuell in mein System?
      $ nmap localhost

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
      $ dig -x 193.99.144.85  	    : -x option refer to reverse lookup
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


<br/><a name="security"></a>

### Security
    ===============================================================================================
    LPIC 110: Security
    ===============================================================================================
    - Security Administration
      Necessariness tools and tips for security
      $ find / -perm -u+s           : SUID bit
      $ find / -perm +4000 -type f  : list of files with SUID bit
      $ find / -perm -g+s           : GUID bit
      $ find /usr -uid 0            : owned by root
      $ passwd
      $ chage                : user passwd expiring etc.
      $ nmap                 : port scan and defending networks
      $ netstat	       : general network system checking
      $ socket -sl 80	       : server dienst simulieren @ client computer

      Resourcenverwendung kontrollieren
      normally one have to put ulimit into /etc/profile
      $ ulimit -a                 : list of all limited resource
      $ ulimit -c 20000           : maximum core file size 20MB
      $ ulimit -d 15000           : maximum process data segment 15 MB
      $ ulimit -Sd $(ulimit -Hd)  : Hardlimit and Softlimit

      Welche Dateien sind von wem auf einem System gerade geoeffnet?
      open files and corresponding processes.
      Prozesse anzeigen, die auf ein Geraet oder einen Netzwerk-Socket zugreifen
      $ lsof /tmp			   : information about process accessd tmp
      $ lsof | wc -l                     : count of all list of files
      $ lsof -i :80                      : list of internet address matches
      $ lsof /dev/sdc1                   : test of usb disk /media/usb-disk possible
      $ lsof -c abc		 	   : list of file for command start with "abc"
      $ kill -s 9 $(lsof -t /media/disk) : kill all processes related with disk.

      Alternativ method, test offened data by fuser
      $ fuser  -m -u /home/kang/test     : a block device mounted on that directory
      > /home/kang/test  12915
      $ ps -aux | grep 12915

    - Der sudo-Mechanismus
      Als Normalbenutzer angemeldet, dann Man wird aufgefoerdert, das Passwort
      einzugegen, dann wird der Befehl mit root-Rechten aussgefuehrt
      If you want to edit /etc/sudoers, then use visudo
      $ visudo                     : editor of sudoers
      $ sudo su 		     : simple su is not working at ubuntu

      Sudoers Syntex
      Group Host =  (User:Group) command
      Group may run Command as the User in Group on Host

      $ vi /etc/sudoers
      >root   ALL=(ALL:ALL) ALL : User privilege specification
      >%admin ALL=(ALL) ALL     : Members of the admin group may gain root privileges
      >%sudo  ALL=(ALL:ALL) ALL : Allow members of group sudo to execute any command

    - Login and Logout
      keep track of all logins and logouts to the system
      - /var/run/utmp : maintains a full accounting of the current status of the system
      - /var/log/wtmp : acts as a historical utmp
      - /var/log/btmp : records only failed login attempts.

      Wer ist momentan an einem System angemeldet?
      $ w               : information from /var/run/utmp
      $ who -u --users  : users
      -b --boot   : boot, last start time
      -d --dead   : dead process
      -l --login  : login process of systems
      -s --short  : short form
      Wer war momentan an diesem System eingemeldet? Ueberfruefen vergangener Anwendungen
      $ last                   : /var/log/wtmp or /var/log/btmp
      $ last -t 20161231120000 : user login before this time
      $ last -d                : show access only from external

      Monitor system load in one line with short form
      $ uptime       		 : how long in service, how many user in machine

    - Superdaemon, Einen Rechner absichern
      inetd ist ein Superserver für Systeme und ist implementiert als Daemon,
      der Netzwerk-Sockets abhört und bei Anfrage auf einem bestimmten Port
      ein voreingestelltes Programm startet Das Super-Daemon kann verwendet werden,
      um wenig angesprochene Serverdienst erst dann zu starten,
      wenn ein Netzwerkzugriff auf dem entsprechenden Port erfolgt.
      Folgende Dienste werden oft mit inetd verwendet :
      FTP-Server, LPRd, CUPS, TCP/UDP-Dienste wie Daytime, Echo

      Konfigurationsdatei fuer Super Daemon
      /etc/inetd.d/
      /etc/inetd.conf
      /etc/xinetd.d/
      /etc/xinetd.conf

      $ vi /etc/xinetd.d/telnet
      service telnet
      {
      disable             = no
      socket_type		= stream
      wait		= no
      server		= /usr/kerberos/sbin/telnetd
      log_on_success	+= DURATION USERID
      log_on_failure	+= USERID
      bind                = 123.123.123.123
      redirect            = 10.0.1.13 23

      only_from
      server_args
      nice
      instances
      group
      user
      protocol
      Ipv6
      id
      type
      no_access
      access_times
      log_type
      }

      $ vi /etc/inetd.conf
      > name    socket  prot.  w/n     user  program               argument
      > ftp     stream  tcp    nowait  root  /usr/libexec/ftpd     ftpd -l
      > ntalk   dgram   udp    wait    root  /usr/libexec/ntalkd   ntalkd
      > telnet  stream  tcp6   nowait  root  /usr/libexec/telnetd  telnetd
      > shell   stream  tcp46  nowait  root  /usr/libexec/rshd     rshd
      > ssh     stream  tcp    nowait  root  /usr/sbin/tcpd        sshd

      Inetd kann allein oder zusammen init dem TCP-Wapper verwendet werden,
      mit dem dann ein Zugriffskontrolle mit dem Dateien hosts.allow und hosts.deny
      durchgefuehrt werden kann Nur xinetd hat tcpd funktioanl integriert.
      TCP-Wrapper liegt unter /etc/sbin/tcpd

      xinetd and inetd are used to reduce the number of listening daemons
      xinetd supports access control by time. when xinetd is running on a system
      with two network interfaces, bind option will be very useful.

      TCP-Wrapper konfiguration
      /etc/hosts.allow
      /etc/hosts.deny
      > httpd:ALL          : allow all IP of access on apache server
      > ALL:192.168.111.10 : allow ALL service from this IP

      If both files exist, hosts.deny doesn't work
      hosts.allow take precedence, the client is granted access to the server
      If both files doesn't exist, not allow to any users

      In order to make ssh service via xinetd, first of all turn off sshd daemon,
      then do configure xinetd.conf and xinetd.d to make ssh service instead
      1. turn off sshd
      2. configure xinetd with sshd
      3. turn on xinetd

      tcpchk: eine inetd.conf auf Syntaxfehler pruefen

    - Super user
      create without any contents (with "messages"), then refuse the access from users
      /etc/nologin
      If both hosts.allow and .deny file exist, then only hosts.allow will active
      /etc/hosts.allow
      /etc/hosts.deny

      if you want to prevent a user from login sesseion
      $ chsh -s /bin/false kang     : user kang can not access anything!

      if you want to restrict the number of simultaneous logins a user by PAM, do
      $ cat /etc/security/limits.conf

    - SSH connection
      most likely use OpenSSH-server and -client with version 2
      $ ssh -p 22 example.com
      $ ssh -o Port=2222 example.com
      SSH server can connect by root, however please do not access directly with root
      because somebody with root password but no other password can then break computer

    - SSH tunneling
      $ ssh destination -L 4711:server:port
      local port 4711 direct to server:port, where is far further location

      Here is a senario, office_pc has private IP 192.168.0.101
      ssh_server has public IP 147.46.101.102
      server connection through port 3030 will redirect to office_pc 22 port
      @ server
      $ ssh kang@147.46.101.102 -R 3030:localhost:22
      here client is window at home, now client will connect office-pc by ssh-server
      it is also possible to connect 3030 port of ssh-server from window port 4040
      @ client
      $ plink kang@147.46.101.102 -L 4040:localhost:3030

      Specially care about SSH and GPG encrption for a tunneled SSH connection
      If you have SMTP and IMAP via a tunneled SSH to your email SERVER.
      Why might you still want to use GPG encryption for your emails on top of the
      encryption provided by SSH. The SSH tunnel reaches only as far as the email server,
      GPG encrypts data on all computers all the way to or from your email correspondents

    - SSH public key technic
      configuration Datei von sshd in Server, ssh in Client
      /etc/ssh/sshd_config     : care not *.config but _config
      /etc/ssh/ssh_config      : care not *.config but _config

      Der Zufgriff auf SSH kann auch mit den Dateien gesteuert werden.
      /etc/hosts.allow
      /etc/hosts.deny
      /etc/nologin
      /etc/ssh/ssh_known_hosts
      /etc/sshrc

      Authentifizierung der Server mit Schluesseln dsa oder rsa
      server $ ssh-keygen -t dsa
      server $ /etc/ssh/ssh_host_rsa_key and ssh_host_rsa_key.pub
      server $ /etc/ssh/ssh_host_dsa_key and ssh_host_dsa_key.pub
      server $ scp ssh_host_dsa_key.pub kang@cleient.company.com:~/
      client $ cat ssh_host_dsa_key.pub >> /etc/ssh/ssh_kown_hosts

      ssh_host_dsa(rsa)_key has a permission as 0666(-rw-rw-rw-) because of public

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

      Or one can easily do this, will automatically do above procedure
      Oeffentlich Schluessel fuer Authentifizierung zum Zielrechner Kopieren
      server $ ssh-copy-id walter@111.222.333.444
      client $ "NOthing to do"

      $ ssh-agent bash  : in memory protect user's private key
      $ ssh-add         : one can add private key

    - gpg GNU Privacy Guard
      $ ~/.gnupg/gpg.conf
      $ gpg --gen-key
      >
      > some options will be shown, have to create security key
      >
      $ ls -l
      > gpg.cong
      > pubring.gpg   : public key
      > secring.gpg   : private key
      > trustdb.gpg   : signature key

      make key with armored ASCII format, then export with a name(as here email format)
      $ gpg --armor --output public.asc --export potatogim@potatogim.net

      import obtained key into my account
      $ gpg --import public_key_of_someone.asc
      $ gpg --import key_from_someone.asc

      Edit the key to give signature
      $ gpg --edit-key "someone"
      > trust   : create trusted key
      > fpr     : fingerprint confirm by phone or e-mail whatever
      > sign	  : now signature to someone's key but my public key get also same effect

      encrypting a file for someone else, assume someone has already our public key
      $ gpg --encrypt data                : ceate secured data with name of data.gpg
      $ gpg --encrypt --recipient 'myfriend@his.isp.net' foo.txt

      decrypting a file for someone else
      $ gpg --decrypt data.txt.gpg > data.txt         : free to noraml data
      $ gpg --decrypt data.txt.gpg --output data.txt  : free to noraml data

      If you lost your computer or have some problem for key,
      you need to make clean current certificattion. First of all do create a revoke,
      that make direclty revoke authentication credential.
      Then copy it into security server, where inform to all, who use this public key.
      You have to download this revoke file to revoke in your new gpg machine.
      $ gpg --gen-revoke ID --output revoke.asc
      $ gpg --import revoke.asc

      put key into Key server
      $ gpg --keyserver subkeys.pgp.net --send-key 'AAAAAAAA'
      $ gpg --keyserver subkeys.pgp.net --send-key '00E5 2D6D ... 8F54 CA35'

      find key at Key server
      $ gpg --keyserver subkeys.pgp.net --search-keys potatogim@potatogim.net
      $ gpg --keyserver pool.sks-keyservers.net --search-keys 'my friend'

      GPG ------------------------------------------------------------------------
      You want to send an encrypted message to an email correspondent.
      Then you must obtain your correspondent's GPG public key.
      Using this recipient's public key you can encrypt the message and can send it.
      Recipient can decrypt using recipient's private & public key in their local

      If your correspondent(A) need to send you an encrypted message,
      then your correspondent(A) must obtain your(B) GPG public key.
      After that, A will import public key from B and make the encryption for message.
      Finally A will send this message to B, and B will decrypt shipped message by
      combining private and public key in B's local.

