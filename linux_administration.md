# Linux system administration


### Contents
  * [Processes and Monitoring](#Processes)
  * [User Management](#Users)
  * [Service](#Service)
  * [Utility](#Utility)
  * [File systens](#Filesystens)
  * [Shell](#Shell)
  * [Files and Directories](#Files)
  * [Logging](#Logging)

    Network and Security issues are covered in another session
    https://github.com/DongheeKang/MLOps_Note/blob/main/linux_administration.md

<br/><a name="Processes"></a>

# Processes and Monitoring

### The Linux Process States
  
  * Running or Runnable (R)
  * Uninterruptible Sleep (D)
  * Interruptable Sleep (S)
  * Stopped (T)
  * Zombie (Z)

### Monitoring command set
* ps

      $ ps -e -f                    : all process
      $ ps -a                       : current shall
      $ ps -C systemd               : filter by systemd
      $ ps -u root                  : filter by user(effective)
      $ ps -p 1, 293                : filter by pid
      $ ps -C gedit -L -f           : thread by -L
      $ ps -e -H                    : child process by -H 
* top  

      $ top -p 23584,22011
      $ top -u root

      keys 
      -------------------------------------------------
      us – user processes
      sy – kernel processes
      ni – niced user processes
      id – kernel idle handler
      wa – I/O completion
      hi – hardware interrupts
      si – software interrupts
      st – time stolen from this VM by the hypervisor
      -------------------------------------------------
* /proc

      $ cat /proc/{pid}/status | grep State

* sysstat

      sudo apt-get install sysstat

      commands 
      --------------------------------------------------------------------------------------------------
      iostat     : reports CPU statistics and input/output statistics for block devices and partitions.
      mpstat     : reports individual or combined processor related statistics.
      pidstat    : reports statistics for Linux tasks (processes) : I/O, CPU, memory, etc.
      tapestat   : reports statistics for tape drives connected to the system.
      cifsiostat : reports CIFS statistics.
      --------------------------------------------------------------------------------------------------

      vmstat is for virtual memory, but cover CPU, memory, and I/O

### RAM

* Find Out the Total Physical Memory (RAM) on Linux

      $ free -h -t          : to know the amount of RAM and swap used/free memory combined
      $ free -h -s 5        : useful if we want to monitor the RAM usage at a specified interval

      $ vmstat -w 
      $ vmstat -s 
      $ vmstat -s | grep -i 'total memory' | sed 's/ *//'
      $ vmstat 2 6           : every 2 secs for 6 intervals

      $ dmidecode --type 19

      $ cat /proc/cpuinfo | grep core

      nice tool for memory monitoring
      $ ksysguard

      one liner log for memory
      $ while true; do date >> memory.log; free >> memory.log; sleep 1; done

* RAM informantion (virtual file)

      $ vi /proc/meminfo
      $ cat /proc/meminfo | grep -i 'memtotal' | grep -o '[[:digit:]]*'

### CPU

* Overall CPU Usage on Linux

      $ uptime
      $ vmstat 3 4
      $ vmstat 1 2|tail -1|awk '{print $15}'
         
      $ vi /proc/stat

      $ cat /proc/stat |grep cpu |tail -1|awk '{print ($5*100)/($2+$3+$4+$5+$6+$7+$8+$9+$10)}'|awk '{print "CPU Usage: " 100-$1 "%"}'
      CPU Usage: 2.4219 %

      $ top -bn2 | grep '%Cpu' | tail -1 | grep -P '(....|...) id,'|awk '{print "CPU Usage: " 100-$8 "%"}'
      CPU Usage: 2.2%

* CPU informantion (virtual file)

  The /proc/cpuinfo virtual file contains information about the CPUs currently available in our system’s motherboard.

      $ vi /proc/cpuinfo

      lm              : whether 64-bit support? 
      vmx             : CPU has hardware support for virtual machines. 

### strace (System call) 

strace is a diagnistic tool for system calls that result in error will have their error exit code and a description displayed

    $ sh -c 'echo $$; exec sleep 60'                      : Attaching strace to Running Process
    $ strace -p {PID}

    $ strace -c whoami                                    : To get a summary of the command, we can use the flag -c
    $ strace -t whoami                                    : Obtaining Timing Information
    $ strace -e trace=fstat whoami                        : Filtering With Expression
    $ strace -e status=!successful whoami                 : Filtering Output by Return Status

### How to monitor disk I/O in a Linux system

* Report Disk I/O Statistics

      $ iostat -d 
      $ iostat -d -p sda                                    : specified device.
      $ iostat -N                                           : Display LVM Statistics  
 
* Identify which process or thread is causing heavy I/O activities.

      $ egrep '(CONFIG_VM_EVENT_COUNTERS|TASK_IO_ACCOUNTING|CONFIG_TASKSTATS|TASK_DELAY_ACCT)' /boot/config-$(uname -r)
      $ sudo iotop -o                                       : threads actually performing I/O activity

* Generate Disk I/O Statistics Over a Period of Time

      $ sar  -b 1                                  : to report details about the disk activities:
      $ sar -p -d -b 1                             : identify devices by using -p, for each block device using -d 
      $ sar 2 5 -o /tmp/data_io > /dev/null 2>&1   : saved file is in a binary format
      $ sar -f /tmp/data_io                        : to read the report generated by the sar command saved in the file

* Measure Disk I/O Usage With vmstat
      
      $ vmstat -d 1                                : to display individual disk statistics:
      $ vmstat -p /dev/sda2 1                      : -p to obtain detailed performance statistics about a partition

### How to know runnning child processes?
  
    $ pgrep -lP 6245
    $ pstree -p 6245
    $ ps --ppid 6245
    $ cat /proc/6245/task/6245/children
      
    please have a look dedicated script find_child_process.sh in github

### How to kill a process standard way?

    $ pgrep -fa dummy_process                : find and list for certain process name, if you want to filter

    $ pkill -f dummy_process                 : kill matches the given process name.
    $ killall dd                             : Kill Multiple Processes Using killall

    $ pidof dummy_process | xargs -r kill    : find pid and kill it

### How to kill backgournd process?

    $ ps -eaf                     : find pid
    $ pgrep chrome                : program name (firefox)
    $ jobs                        : to list job (& is background)

    $ sudo kill -9 733
    $ sudo pkill rabbitmq
    $ killall chrome
    $ killall firefox

    $ fg 1                        : bring first into foreground
    $ kill %1                     : kill id=1 process

### How long a linux process has been running?

    $ ps -p 1234 -o etime                      :  03:24:30
    $ ps -p 1234 -o etimes                     :  timestemp 123445

### Finding out who killed the process

    $ (echo "li = []" ; echo "for r in range(9999999999999999): li.append(str(r))") | python

    $ sudo dmesg | tail -7

    $ journalctl --list-boots | \
      awk '{ print $1 }' | \
      xargs -I{} journalctl --utc --no-pager -b {} -kqg 'killed process' -o verbose --output-fields=MESSAGE

### Find the current working directory of a running process

    $ pgrep sleep
      5620                               : this is a pid

    $ pwdx 5620                          : use now pwdx
      5620: /home/pi                     : can find directory!

    $ lsof -p 5620 | grep cwd            : or also possible with lsof

    $ readlink -e /proc/23217/cwd        : or readlink




<br/><a name="Users"></a>

# User Management

### Running script or command as another user in Linux
    
* visudo 
    
  will show /etc/sudoers
    
      $ visudo 
        root  ALL=(ALL:ALL) ALL
        user hostname=(runas-user:runas-group) command

  add 'hope' user account to the super user / admin group

      usermod -aG sudo hope
      usermod -aG admin hope

* su (substitue user)

  compare below two without '-' and with '-'

      $ su - 
        root@server:~# pwd 
        /root 
      $ su  
        root@server:/home/test# pwd 
        /home/test      

  command

      $ cat > /home/annie/annie-script.sh <<EOF
        echo "Running annie-script.sh as user $(whoami)"
        EOF
      $ chmod u+x /home/annie/annie-script.sh

      login as donghee 
      $ su -c '/home/annie/annie-script.sh' annie    : Running annie-script.sh as user annie
      $ su -c 'echo I am $(whoami)'                  : Running annie-script.sh as user root

      Disabling the password prompt:
      $ vi /etc/pam.d/su
        auth  [success=ignore default=1] pam_succeed_if.so user = annie
        auth  sufficient                 pam_succeed_if.so use_uid user = dave

      $ su -c /home/annie/annie-script.sh annie
        Running annie-script.sh as user annie

* sudo (Super User DO)

      Edit the /etc/sudoers file
      $ echo 'dave ALL=(annie) /home/annie/annie-script.sh' | EDITOR='tee -a' visudo
    
      $ sudo -u annie /home/annie/annie-script.sh
        [sudo] password for donghee:                 : with sudo, it requests for the current user’s password
        Running annie-script.sh as user annie

      Allow execute script by root i.e. system  
      $ echo 'dave ALL=(ALL) /home/annie/annie-script.sh' | EDITOR='tee -a' visudo

      Now it should work!
      $ sudo -u root /home/annie/annie-script.sh
        [sudo] password for donghee:
        Running annie-script.sh as user root

      Skipping Password Prompt
      dave ALL=(ALL) NOPASSWD: /home/annie/annie-script.sh

      $ sudo -u annie /home/annie/annie-script.sh
        Running annie-script.sh as user annie
      $ sudo -u root /home/annie/annie-script.sh
        Running annie-script.sh as user root

### Why chrooot is important?

    $ apt-get install coreutils

    $ chroot /tmp/new_root /bin/bash

    $ ldd /bin/bash      

### How to change the default home directory of a user

    As a default one can create user in /home/ 
    $ sudo useradd -d /home/kang -m kang         : -m for user -d for directory

    $ sudo useradd -m donghee                           
    $ sudo useradd -m -d /home/dongheekang donghee      
    $ sudo passwd kang

    move the existing content to the new location, has to use -m option 
    $ sudo usermod -m -d /usr/dongheekang dongheekang

### List all groups in linux

* standard method

      $ vi /etc/group
      group_name : password(encrypted) : GID : user_list
      root:x:0:root
      bin:x:1:root,bin,daemon
      daemon:x:2:root,bin,daemon
      sys:x:3:root,bin
      adm:x:4:root,daemon
      tty:x:5:
      disk:x:6:root
      lp:x:7:cups,daemon,kent

      only user print out! 
      $ cut -d: -f1 /etc/group

* getent

      $ getent group

      only user print out! 
      $ getent group | cut -d: -f1

* group

      $ groups
      lp wheel dbus network video audio optical storage input users vboxusers docker donghee
    
      $ groups root
      root bin daemon sys adm disk wheel log

* id

      $ id -Gn
      $ id -Gn root

### How to change password for other user account?

      only with super user
      $ su -

      check password of user first 
      $ getent passwd | grep donghee
      $ passwd donghee    
      $ passwd -V donghee                 : to view password status

      *** this is important!           
      $ sudo passwd           : if passwd with no specified user, then will change password for root
### Forcing user to change password at their next login

      Let us immediately expire an account’s password

      $ sudo passwd -e donghee
      $ sudo passwd --expire donghee
### Extend expire date of user

      $ usermod -e 2025-09-01 user
### Lock and unlock of user    

      $ usermod -L user
      $ usermod -U user 

### Change account’s username

      $ usermod -l old new

### create a user group and add one user into

    $ sudo addgroup administrator
    $ sudo adduser donghee administrator
### Add user to sudo group

    $ sudo usermod -aG sudo donghee

### Fixing the “Command Not Found” Error When Using Sudo 
  
* “Permission Denied” when running script, then do this

      $ chmod +x ./myscript 

* Fixing the error for a single command. We can pass the -E flag to sudo to make it preserve the current environment variables:

      $ sudo -E myscript



<br/><a name="Service"></a>

# Service

### Shutdown linix

    $ shutdown -r now              : reboot
    $ shutdown +30                 : after 30 mins completely shutdown!  
    $ poweroff                     : power off 

### Run a script on startup in Linux
* create a script for reboot message
          
      $ vi reboot_message.sh
        #!/bin/sh \
        echo "Last reboot time: $(date)" > /etc/motd

      $ chmod +x reboot_message.sh

* using cron

      $ crontab -e
      | @reboot sh /root/reboot_message.sh 

* using rc.local 

      $ vi /etc/rc.local 
      |  sh /home/ec2-user/reboot_message.sh
      chmod +x /etc/rc.local
        
      caution) some distribution has another location for rc.local 
      /etc/rc.d/rc.local and one need to care rc0.d, rc1.d and so on.

* using init.d

      $ vi etc/init.d
        #! /bin/sh 
        # chkconfig: 345 99 10
        case "$1" in
        start)
          # Executes our script
          sudo sh /home/ec2-user/reboot_message.sh
          ;; 
        *)
          ;;
        esac
        exit 0 

      $ cd etc/init.d
      $ chkconfig --add service_wrapper.sh        # not in debian
      $ update-rc.d service_wrapper.sh defaults   # use this for debina case

* using systemd
  
      $ vi etc/systemd/system
        [Unit]
        Description=Reboot message systemd service.
        [Service]
        Type=simple
        ExecStart=/bin/bash /home/ec2-user/reboot_message.sh
        [Install]
        WantedBy=multi-user.target

      $ chmod 644 /etc/systemd/system/reboot_message.service
      $ systemctl enable reboot_message.service


### Run an application as a service on linux

you have a service as like java/python application, do registering and then running the service
        
* config service file xxx.service 

      $ vi /etc/systemd/system/javasimple.service
      $ vi /etc/systemd/system/javaforking.service
        [Unit]
        Description=My Java forking service
        After=syslog.target network.target
        [Service]
        SuccessExitStatus=143
        User=appuser
        Group=appgroup
        Type=forking
        ExecStart=/path/to/wrapper
        ExecStop=/bin/kill -15 $MAINPID
        [Install]
        WantedBy=multi-user.target

* use systemctl, start service. 
  
      $ sudo systemctl daemon-reload
      $ sudo systemctl start javasimple.service
      $ sudo systemctl enable javasimple.service
      $ sudo systemctl status javasimple.service

### How to load environment variables in a cron job and, and How to run a script at a certain time on linux? 

* where is the cron? 

      $ ls -l /var/spool/cron/crontabs/              : user-wide
      $ cat /etc/crontab                             : system-wide
        /etc/cron.allow
        /etc/cron.deny

* how to configure crontab

      $ crontab -l
      $ crontab -e 
      
      wrapping and define  
      ---------------------------------------------------------------------------
      * * * * *    ---->     min hour day month week

      * * * * * printenv > /tmp/print_envs_result
      * * * * * BASH_ENV=/etc/profile bash -c "printenv > /tmp/print_envs_result"
      * * * * * bash -l -c "printenv > /tmp/print_envs_result"
      * * * * * BASH_ENV=~/.bashrc bash -l -c "printenv > /tmp/print_envs_result"
      * * * * * /home/dongheekang/backup.sh > /dev/null 2>&1
      @reboot (cd /home/dongheekang/monitoring-scripts; bash monitor-memory.sh)
      ----------------------------------------------------------------------------

### Configure a systemd service to restart periodically

* where is systemd?

      /etc/systemd/system

* write 

      $ sudo vi my-service.service 
        [Unit]
        Description=Simple service
        [Service]
        Type=simple
        ExecStart=/usr/bin/logger hello
        [Install]
        WantedBy=multi-user.target

      $ sudo vi oneshot.service 
        [Unit]
        Description=One shot service
        [Service]
        Type=oneshot
        ExecStart=/usr/bin/systemctl restart my-service.service
        [Install]
        WantedBy=multi-user.target    

      $ sudo vi my-service.timer 
        [Unit]
        Description=Run oneshot service periodically
        [Timer]
        Unit=oneshot.service
        OnCalendar=Mon..Fri 10:30
        [Install]
        WantedBy=timers.target

* run  

      $ systemctl enable --now my-service.timer
      $ systemctl list-timers 
      $ journalctl --since "5 minutes ago"

* run by RuntimeMaxSec and the restart Options

      $ sudo vi my-service1.service 
        [Unit]
        Description=Simple service
        [Service]
        ExecStart=/usr/bin/python3 /root/app.py
        RuntimeMaxSec=180s
        Restart=always
          
* run by crontab  

      $ crontab -e
        30 10 * * 1-5 /usr/bin/systemctl restart my-service.service



### put the list for unbuntu library
* legacy

      $ vi /etc/apt/sources.list

      deb http://security.ubuntu.com/ubuntu intrepid-security main
      deb http://download.virtualbox.org/virtualbox/debian lucid non-free
      deb http://archive.canonical.com/ lucid partner

      $ sudo apt-get update

* modern way

      add
      $ sudo add-apt-repository ppa:thomas-schiex/blender

      remove
      $ sudo add-apt-repository --remove ppa:thomas-schiex/blender

      fix for "command not found" by installing software-properties-common package
      $ sudo apt install software-properties-common -y

<br/><a name="Utility"></a>

# Utility

### touch to change create date

     $ touch -d tomorrow test             : 
     $ touch -d '1 day ago' test          : 
     $ touch -d '5 years ago' test        : 
     $ touch test{1..10}                  : 

     $ touch -t YYMMDDHHMM fileName       :  set the timestamp 
     $ touch -r file2 file1               :  file2 is updated with the time stamp of file1

### truncate

     $ cp original.output backup.output
     $ truncate -s 0 original.output
     $ cat backup.output > original.output

### sftp, scp, rsync

    $ sftp username@your_server_ip_or_remote_hostname 
    $ sftp -oPort=custom_port username@your_server_ip_or_remote_hostname

    $ scp -P 22 [user@]SRC_HOST:]file1 [user@]DEST_HOST:]file2
    $ scp file.txt donghee@ip_address_of_remote:/remote/directory
    $ scp -i ~/.ssh/donghee.pem ubuntu@ec2-52-57-133-64.eu-central-1.compute.amazonaws.com:~/data/

     -r: Recursively copy entire directories

    1.scp transfer
      If I want to file transfer From CERN location to Freiburg:
      1.With CERN machine
      scp * donghee@axfr01.physik.uni-freiburg.de:/users/donghee/tmp/
      scp * donghee@pcfr38.physik.uni-freiburg.de:~/tmp/
      scp mc/Aroma/a.f donghee@pcfr38.physik.uni-freiburg.de:~/tmp/
      scp ~/mc/Aroma/* donghee@pcfr38.physik.uni-freiburg.de:~/tmp/
      2.With Freiburg machine
      scp donghee@lxplus.cern.ch:/afs/cern.ch/user/d/donghee/mc/aroma/*.* .
      scp donghee@lxplus.cern.ch:~/mc/aroma/*.* .

    2.scp transfer
      scp -P24 User14.cc donghee@compass.fzk.de:/grid/fzk.de/cvs/User14.cc
      From local to Gridka
      scp -r -P24 AAAAAA donghee@compass.fzk.de:~/work/
      From Gridka to local
      scp -r -P22 AAAAAA donghee@lxplus.cern.ch:~/work/

### rsync

      rsync copy to minidisk per month!
      $ sudo rsync --delete -avv /home/dkang/ /media/Linux/dkang/
      $ sudo rsync --delete -avv /media/Data/Data/ /media/Window/Data/

      copy to Memorystick only important and selected files! per weeks!
      $ sudo rsync --delete --exclude data -avv /home/dkang/GSI/macro_dvcs/ /media/MEMORY/rsync/macro_dvcs/
      $ sudo rsync --delete -avv /home/donghee/Desktop/ /media/MEMORY/rsync/Desktop/
      $ sudo rsync --delete -avv /media/Data/Data/arbeits/analysis-GPDsDVCS/ /media/MEMORY/rsync/analysis-GPDsDVCS/
      $ sudo rsync --delete -avv /media/Data/Data/document/HIM-GSI/ /media/MEMORY/rsync/HIM-GSI/
      $ sudo rsync --delete -avv /media/Data/Data/vortrag/ /media/MEMORY/rsync/vortrag/

      $ sudo rsync --delete --exclude GSI -avv /home/dkang/ /media/Linux/dkang/
      $ sudo rsync --delete -avv /home/dkang/GSI/ /media/Linux/dkang/
      (care about .VirtualBox/HardDisks, that are too heavy)

      $ rsync -a /sc/sc32b/* .
      $ rsync -a /sc/sc32b/* .
### rssh

      $ /usr/local/etc/rssh.conf
      $ /etc/group
      $ /etc/passwd

### lpr

      $ lpr -P color $1       - Color
      $ lpr restart dl1       - Restart
      $ cancel -Pdl1 669      - kill  ???
      $ lpq -Pdl1             - job que
      $ lprm -Pdl1 699        - kill


<br/><a name="Filesystens"></a>

# File systems

### Where disk space hss gone on linux
      
First do check disk partition

    df -h                  : human readabel

then do check disk usage 

    du /var
    du -BM --max-depth=1 /var | sort -n | tail -n 5 
    du -BM --max-depth=<strong>2</strong> /var | sort -n | tail -n 5
    du -BM --max-depth=1 <strong>/var/log</strong> | sort -n | tail -n 5


find command

    find /var -size +100M -printf '%s %p\n' | sort -n

sof command delete files still using space: the system does not report space used by deleted files as free

    lsof | grep -E '^COM|deleted'
     
this is really nice tool for checking disk usage!

    ncdu  

    


<br/><a name="Shell"></a>

# Shell 





<br/><a name="Files"></a>

# Files & Directory 

### permission and owner for directory

      $ sudo chmod 755 directory

      $ sudo chown -R user:group  directory
      $ sudo chown -R kang:admin  backup_donghwa

      chmod 755  :  chmod u=rwx,g=rx,o=rx

      Symbolic:  rwx r-x r-x 
      Decimal:    7   5   5 

      chmod 666  :  chmod a=rw, read and write by everyone

      chmod +x   :  chmod ugo+x
      chmod a+x  :  chmod ugo+x  a=all

      chmod u+x  :  Add excute permission to a file/directory for user
      chmod u+X  :  Change execute permission only on the directories

      chmod o-w  :  to remove the write permission for others

### “No such file or directory” error when executing a binary

* Two cases: 

      $ ./binaryfile
      ./binaryfile: error while loading shared libraries: libbooster.so.0: cannot open shared object file: No such file or directory

      $ ./binaryfile
      -bash: ./binaryfile: No such file or directory         : missing a Program Interpreter while above is missing Libraries

* Solution:

      $ objdump -p binaryfile | grep NEEDED

      $ ldd binaryfile

      $ readelf -a binaryfile | grep NEEDED
      $ readelf -a binaryfile | grep interpreter


<br/><a name="Logging"></a>

# Logging

### journal

    $ journalctl --list­-boots                   : To get a list of boots
    $ journalctl -b                             : to get the all the logs for the current boot,
    $ journalctl -b -1                          : to get the previous boots
    $ journalctl --sinc­e="2­021­-01-30 18:17:16"  : specific time using --since and --until
    $ journalctl --since "20 min ago"   
    $ journalctl -u systemd-*
    $ journalctl --user-unit my-application
    $ jour­nalctl _UID=100
    $ journalctl -p err..alert                  : Priority
    $ journalctl -u apache -n 10
    $ journalctl -f -u nginx
    $ journalctl --no-pager
    $ journalctl --vacu­um-­tim­e=2­weeks
    $ journalctl -b -u docker -o json


### logrotate
    /var/lib/logrotate.status       : Default state file.
    /etc/logrotate.conf             : Configuration options.
    /etc/logrotate.d                : stores application-specific log settings
   
    $ logrotate log-rotation.conf                    : configure
    $ logrotate -f log-rotation.conf                 : run it
    $ logrotate -d /etc/logrotate.d/apache2.conf

    --------------------------------------------
    <global directive 1>
    <global directive 2>
    <file path matchers 1> {
        <directive 1>
        <directive 2>
        ...
        <directive n>
    }

    <file path matchers 2a> <file path matchers 2b> {
        <directive 1>
        <directive 2>
        ...
        <directive n>
    }    
    --------------------------------------------

    --------------------------------------------
    compress
    /var/log/nginx/* {
        rotate 3
        daily
    }
    /var/log/nginx/error.log {
        rotate 3
        size 1M
        lastaction
            /usr/bin/killall -HUP nginx
        endscript
        nocompress
    }
    --------------------------------------------




<div><br/>
&raquo; Back to <a href="#contents">Contents</a> | <a href="../docs/README.md">Docs</a>
</div>
