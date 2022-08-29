# System Administration


### Contents
  * [Processes and Monitoring](#Processes)
  * [User Management](#Users)
  * [Networking](#Networking)
  * [Security](#Security)

  * [Service](#Service)
  * [Utility](#Utility)
  * [File systens](#Filesystens)
  * [Shell](#Shell)
  * [Files and Directories](#Files)
  * [Logging](#Logging)


<br/><a name="Processes"></a>

## Processes and Monitoring
* The Linux Process States
  
  * Running or Runnable (R)
  * Uninterruptible Sleep (D)
  * Interruptable Sleep (S)
  * Stopped (T)
  * Zombie (Z)

* standard processes

  ps

      $ ps -e -f                    : all process

      $ ps -a                       : current shall
      $ ps -C systemd               : filter by systemd
      $ ps -u root                  : filter by user(effective)
      $ ps -p 1, 293                : filter by pid
      $ ps -C gedit -L -f           : thread by -L
      $ ps -e -H                    : child process by -H 

  top  

      $ top -p 23584,22011
      $ top -u root

      us – user processes
      sy – kernel processes
      ni – niced user processes
      id – kernel idle handler
      wa – I/O completion
      hi – hardware interrupts
      si – software interrupts
      st – time stolen from this VM by the hypervisor


  /proc

      $ cat /proc/{pid}/status | grep State


* sysstat

      sudo apt-get install sysstat

      iostat     : reports CPU statistics and input/output statistics for block devices and partitions.
      mpstat     : reports individual or combined processor related statistics.
      pidstat    : reports statistics for Linux tasks (processes) : I/O, CPU, memory, etc.
      tapestat   : reports statistics for tape drives connected to the system.
      cifsiostat : reports CIFS statistics.

      vmstat is for virtual memory, but cover CPU, memory, and I/O

* Find Out the Total Physical Memory (RAM) on Linux
      $ free -h -t          : to know the amount of RAM and swap used/free memory combined
      $ free -h -s 5        : useful if we want to monitor the RAM usage at a specified interval

      $ vmstat -w 
      $ vmstat -s 
      $ vmstat -s | grep -i 'total memory' | sed 's/ *//'
      $ vmstat 2 6                                   : every 2 secs for 6 intervals

      $ dmidecode --type 19

      $ cat /proc/cpuinfo |grep core

      nice tool for memory monitoring
      $ ksysguard

      one liner log for memory
      $ while true; do date >> memory.log; free >> memory.log; sleep 1; done

* Overall CPU Usage on Linux
      $ uptime
      $ vmstat 3 4
      $ vmstat 1 2|tail -1|awk '{print $15}'
         
      $ vi /proc/stat

      $ cat /proc/stat |grep cpu |tail -1|awk '{print ($5*100)/($2+$3+$4+$5+$6+$7+$8+$9+$10)}'|awk '{print "CPU Usage: " 100-$1 "%"}'
      CPU Usage: 2.4219 %

      $ top -bn2 | grep '%Cpu' | tail -1 | grep -P '(....|...) id,'|awk '{print "CPU Usage: " 100-$8 "%"}'
      CPU Usage: 2.2%

* RAM informantion (virtual file)

      $ vi /proc/meminfo

      $ cat /proc/meminfo | grep -i 'memtotal' | grep -o '[[:digit:]]*'
      

* CPU informantion
  The /proc/cpuinfo virtual file contains information about the CPUs currently available in our system’s motherboard.

      $ vi /proc/cpuinfo

      lm              : whether 64-bit support? 
      vmx             : CPU has hardware support for virtual machines. 

* strace (system call)
  strace is a diagnistic tool for 
  system calls that result in error will have their error exit code and a description displayed

      $ sh -c 'echo $$; exec sleep 60'                      : Attaching strace to Running Process
      $ strace -p {PID}

      $ strace -c whoami                                    : To get a summary of the command, we can use the flag -c
      $ strace -t whoami                                    : Obtaining Timing Information
      $ strace -e trace=fstat whoami                        : Filtering With Expression
      $ strace -e status=!successful whoami                 : Filtering Output by Return Status


* How to Monitor Disk IO in a Linux System (sysstat, iotop)

  Report Disk I/O Statistics

      $ iostat -d 
      $ iostat -d -p sda                                    : specified device.
      $ iostat -N                                           : Display LVM Statistics  
 
  identify which process or thread is causing heavy I/O activities.

      $ egrep '(CONFIG_VM_EVENT_COUNTERS|TASK_IO_ACCOUNTING|CONFIG_TASKSTATS|TASK_DELAY_ACCT)' /boot/config-$(uname -r)
      $ sudo iotop -o                                       : threads actually performing I/O activity

  Generate Disk I/O Statistics Over a Period of Time

      $ sar  -b 1                                  : to report details about the disk activities:
      $ sar -p -d -b 1                             : identify devices by using -p, for each block device using -d 
      $ sar 2 5 -o /tmp/data_io > /dev/null 2>&1   : saved file is in a binary format
      $ sar -f /tmp/data_io                        : to read the report generated by the sar command saved in the file

  Measure Disk I/O Usage With vmstat
      
      $ vmstat -d 1                                : to display individual disk statistics:
      $ vmstat -p /dev/sda2 1                      : -p to obtain detailed performance statistics about a partition

* How to know runnning child processes?
  
      $ pgrep -lP 6245
      $ pstree -p 6245
      $ ps --ppid 6245
      $ cat /proc/6245/task/6245/children
      
  please have a look dedicated script find_child_process.sh in github

* How to kill a process standard way?

      $ pgrep -fa dummy_process                : find and list for certain process name, if you want to filter

      $ pkill -f dummy_process                 : kill matches the given process name.
      $ killall dd                             : Kill Multiple Processes Using killall

      $ pidof dummy_process | xargs -r kill    : find pid and kill it

* How to kill backgournd process?

      $ ps -eaf                     : find pid
      $ pgrep chrome                : program name (firefox)
      $ jobs                        : to list job (& is background)

      $ sudo kill -9 733
      $ sudo pkill rabbitmq
      $ killall chrome
      $ killall firefox

      $ fg 1                        : bring first into foreground
      $ kill %1                     : kill id=1 process




* How long a linux process has been running?

      $ ps -p 1234 -o etime                      :  03:24:30
      $ ps -p 1234 -o etimes                     :  timestemp 123445


* Finding out Who Killed the Process

      $ (echo "li = []" ; echo "for r in range(9999999999999999): li.append(str(r))") | python

      $ sudo dmesg | tail -7

      $ journalctl --list-boots | \
        awk '{ print $1 }' | \
        xargs -I{} journalctl --utc --no-pager -b {} -kqg 'killed process' -o verbose --output-fields=MESSAGE


* Find the Current Working Directory of a Running Process

      $ pgrep sleep
        5620                               : this is a pid

      $ pwdx 5620                          : use now pwdx
        5620: /home/pi                     : can find directory!

      $ lsof -p 5620 | grep cwd            : or also possible with lsof

      $ readlink -e /proc/23217/cwd        : or readlink




<br/><a name="Users"></a>

## User Management

* Running Script or Command as another user in Linux
    
  * visudo 
    
    will touch /etc/sudoers and show 
    
        $ visudo 
          root  ALL=(ALL:ALL) ALL
          user hostname=(runas-user:runas-group) command

    add 'hope' user account to the super user / admin group

        usermod -aG sudo hope
        usermod -aG admin hope

  * su (substitue user)

    compare 

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

        Disabling the Password Prompt+
        $ vi /etc/pam.d/su
          auth  [success=ignore default=1] pam_succeed_if.so user = annie
          auth  sufficient                 pam_succeed_if.so use_uid user = dave


        $ su -c /home/annie/annie-script.sh annie
           Running annie-script.sh as user annie

  * sudo (Super User DO))

        To edit the /etc/sudoers file
        $ echo 'dave ALL=(annie) /home/annie/annie-script.sh' | EDITOR='tee -a' visudo
    
        $ sudo -u annie /home/annie/annie-script.sh
          [sudo] password for donghee:                 : with sudo, it requests for the current user’s password
          Running annie-script.sh as user annie

        To allow execute script by root i.e. system  
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


* How to Change the Default Home Directory of a User
          $ sudo useradd -m baeldung   
          $ sudo useradd -m -d /home/baeldung baeldung

          move the existing content to the new location, has to use -m option 
          $ sudo usermod -m -d /usr/baeldung baeldung

* List all groups in linux

  standard method

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


  getent

      $ getent group

      only user print out! 
      $ getent group | cut -d: -f1

  group

      $ groups
      lp wheel dbus network video audio optical storage input users vboxusers docker donghee
    
      $ groups root
      root bin daemon sys adm disk wheel log

  id

      id -Gn
      id -Gn root


* Fixing the “Command Not Found” Error When Using Sudo 
  
  “Permission Denied” When Running Script

      chmod +x ./myscript 

  Fixing the Error for a Single Command. We can pass the -E flag to sudo to make it preserve the current environment variables:

      sudo -E myscript

* Modify user

  Update Account’s Username

      usermod -l old new

  Lock and unlock    

      usermod -L user
      usermod -U user 
      
  extend expire date

      usermod -e 2025-09-01 user





<br/><a name="Networking"></a>

## Networking
* Mapping Hostnames with Ports in /etc/hosts

      $ vi /etc/hosts
      127.0.0.1         dongheekang.com                : this is ok
      127.0.0.1:8080    dongheekang.com                : port 8080 does not work!!!

      For Nginx, one can deal port!
      $ vi /etc/nginx/conf.d/dongheekang.conf
      server {
          listen 80;

          server_name dongheekang.com;

          location / {
              proxy_pass http://127.0.0.1:8080/;
          }
      }
  
  To check port

      $ netstat -ltnup | grep ':22'
      $ ss -ltnup 'sport = :22'
      $ lsof -i :22 -i :68
      $ fuser -v 22/tcp 68/udp

      options (netstat & ss)
        l – show only listening sockets
        t – show TCP connections
        n – show addresses in a numerical form
        u – show UDP connections
        p – show process id/program name


* How to kill Running on a Specific Port?

  creat three processes using port 9999 and the protocols SCTP, TCP, and UDP respectively.

      $ socat sctp-listen:9999,bind=127.0.0.1 stdout &
      [1] 6424
      $ socat tcp-listen:9999,bind=127.0.0.1 stdout &
      [2] 6431
      $ socat udp-listen:9999,bind=127.0.0.1 stdout &
      [3] 6438

  fuser

      $ fuser -k 9999/tcp
      9999/tcp: 6431

  kill

      $ kill -9 6431

  lsof
      
      $ lsof -i udp:9999 | awk '/9999/{print $2}' | xargs kill

  ss or netstat

      $ ss -Slp | grep -Po ':9999\s.*pid=\K\d+(?=,)' | xargs kill
      $ netstat -Slp | grep -Po ':9999\s.*LISTEN.*?\K\d+(?=/)' | xargs kill



* Freeing up a TCP/IP Port

      $ fuser -k 8000/tcp
      $ fuser -i -TERM -k 8000/tcp                 : use SIGTERM to do more carefully!

      $ lsof -i :8000
      $ ss -apt 'sport = :8000'
        Netid  State      Recv-Q Send-Q Local  Address:Port  Peer Address:Port                
        tcp    LISTEN     0      128    *:ssh                *:*                 users:(("sshd",pid=1226,fd=3))
        ......
        tcp    TIME-WAIT  0      0      192.168.0.4:56886    192.168.0.5:https

      TIME-WAIT: the process closed the connection, and the port is waiting for a timeout without any process using it.
      
      Sometimes there isn’t any process using the port and, even so, the system tells us the port is busy or in use. 
      This is usually because of the TIME-WAIT state. If this is the case, we only have to wait until the system frees it up, 
      by default the timeout is 2 minutes.


* SSH Tunneling and Proxying

  * Server side configuration ! SSHD

        /etc/ssh/sshd_config 

        AllowStreamLocalForwarding   : Allows Unix domain sockets to be forwarded. 
        AllowTcpForwarding           : Allows TCP port forwarding. 
        DisableForwarding            : Disables all kinds of forwarding.
        GatewayPorts                 : Allows to use the ports forwarded to a client 
        PermitListen                 : Specifies the addresses and ports (‘127.0.0.1’)
        PermitOpen                   : Specifies the address and ports
        PermitTunnel                 : Specifies whether tun device forwarding is allowed. 
        X11Forwarding                : Specifies whether X11 forwarding is allowed. 
        X11UseLocalhost              : Forces X11 forwarding to be allowed from the SSH server host loopback address. 

    need to be confirmed
    firewalls must allow the SSH traffic, usually on port TCP/22, since some host firewall configurations might limit the ability to connect to and from external services 

    iptables needs to be touched.

  * Forward
    * Single-Port

      A forward or direct TCP tunnel is the one that follows the direction of the SSH connection from the client to the SSH server. 

          ssh -L [bind_address:]port:host:hostport [user@]remote_ssh_server
          ssh -L 0.0.0.0:8022:10.1.4.100:22 user@10.1.4.20
               
          possible this as well. 
          ssh -L local_socket:host:hostport [user@]remote_ssh_server
          ssh -L local_socket:remote_socket [user@]remote_ssh_server

    * Dynamic or Multi-Port

      A special case of the forward TCP tunnels is the Socks proxy capability. Using these options, the SSH client listens on a specified binding port and acts as a SOCKS 4 or 5 proxy server.

          ssh -D [bind_address:]port [user@]remote_ssh_server
          ssh -D 8080 user@10.1.4.100

  * Reversed
    * Single-Port

      The reverse or callback proxies allow us to do tricks similar to the one above but in the reverse direction. We can open services on our own local networks to hosts on the remote side of the SSH session.

            ssh -R [bind_address:]port:host:hostport [user@]remote_ssh_server
              
            optional possible 
            ssh -R remote_socket:host:hostport [user@]remote_ssh_server
            ssh -R remote_socket:local_socket [user@]remote_ssh_server
            ssh -R [bind_address:]port:local_socket [user@]remote_ssh_server         

    * Dynamic or Multi-Port

      Finally, we can expose a SOCKS proxy server on the remote host directed to the client’s network as we can do with direct forwarding. 

          ssh -R [bind_address:]port [user@]remote_ssh_server

  * X window turnnel

        ssh -X [user@]remote_ssh_server
        ssh -Y [user@]remote_ssh_server

  * Multiple Tunnels and Multiple Host Hopping

        ssh -X -L 5432:<DB server IP>:5432 -R 873:<local RSYNC server>:873 [user@]remote_ssh_server

        server1 -> server2 -> server3 
        ssh -L 8022:<server2>:22 user@server1
        ssh -L 8023:<server3>:22 -p 8022 user@localhost
        ssh -p 8023 user@localhost

  * configuration for above

        /etc/ssh/ssh_config

        host 10.1.4.100
            ForwardX11 yes
            LocalForward 0.0.0.0:5432 10.1.4.200:5432
            RemoteForward localhost:8022 localhost:22
            user baeldung

  * Persistent 

        $ autossh [-V] [-M port[:echo_port]] [-f] [SSH_OPTIONS]

        $ autossh -X -L 5432:<DB server IP>:5432 -R 873:<local RSYNC server>:873 [user@]remote_ssh_server
        $ autossh -f [host]
  
* Monitoring Network Usage in Linux 

    * nload
    * speedometer
    * iftop
    * nethogs

* netcat(nc) 
    
  * reading and writing data across the network, through TCP or UDP 

        $ nc -z -v -w 1 google.com 442-444      : Scanning for Open Ports

        $ nc -lv 1234                           : listen to port 1234 (server node)
        $ nc -v localhost 1234                  : open up a netcat process that connects to localhost at port 1234 (client node)

        $ nc -l -v -k localhost 1234            : server and client netcat processes will return whenever the connection is terminated  

  * A netcat process 
      
      first constructs a legitimate HTTP response using echo and process substitution, 
      listens to port 1234 and serves the file whenever a client connects to our server:

        $ echo -e "HTTP/1.1 200 OK\n\n$(cat index.html)" | nc -l 1234

  * Improvement

      we wrapped the command into a while loop. In consequence, whenever the command terminates, it’ll restart the process 
      and using -w flag allows us to specify the timeout value

        $ while true; do echo -e "HTTP/1.1 200 OK\n\n$(cat index.html)" | nc -l -w 1 1234; done

      How do create index.html?
      
        cat - > index.html <<<EOF
        <!DOCTYPE html>
        <html>
          <head>
            <title>Simple Netcat Server</title>
          </head>
          <body>
            <h1>Welcome to simple netcat server!<h1>
          </body>
          </body>
        <html>
        EOF


  *  Reserve shell connection: 

      set server node

          $ nc -lv 1234
          $ mkfifo /tmp/rs
          $ cat /tmp/rs | /bin/bash 2>&1 | nc -v client 1234 > /tmp/rs

      Listening on 0.0.0.0 1234
      Connection received on server.baeldung 36170

          $ hostname
          $ server

      any text sent by the client node will then be piped to /tmp/rs

  *  Reserve proxy connection: 

      set server node
  
          $ mkfifo /tmp/rp
          $ nc -lv 1234 < /tmp/rp | nc localhost 4321 > /tmp/rp

      the first process the external router and the second process the internal router.
      When there is incoming traffic on port 1234, the external router pipes the traffic to the internal router.
      when there’s outgoing traffic from port 4321, the internal router will pipe it to the pipe /tmp/rp. 
      Then, the external router(server) will read and send the content of /tmp/rp to the client (outside).


* Two dockers for network testing 



* How to List All Connected SSH Sessions

      $ who
      $ w
      $ sudo last | grep 'still logged in'  
      $ sudo netstat -atnp | grep 'ESTABLISHED.*sshd'
      $ ss | grep ssh
      $ ps axfj | grep sshd

* Find the IP Address of the Client in an SSH Session 

      $ who
      $ w
      $ finger
      $ pinky  
      $ last | head
      $ sudo netstat -tpn | grep "ESTABLISHED.*sshd"
      $ sudo ss -tp | grep "ESTAB.*sshd"
      $ sudo lsof -i TCP -s tcp:established -n | grep ssh









<br/><a name="Security"></a>

## Security

* iptables











<br/><a name="Service"></a>

## Service


* Shutdown linix

      shutdown -r now              : reboot
 
      shutdown +30                 : after 30 mins completely shutdown!  

      poweroff                     : power off 

* Run a script on startup in Linux
    
    - create a script for reboot message
          
          $ vi reboot_message.sh
          | #!/bin/sh \
          | echo "Last reboot time: $(date)" > /etc/motd

          $ chmod +x reboot_message.sh

    - using cron

          $ crontab -e
          | @reboot sh /root/reboot_message.sh 

    - using rc.local 

          vi /etc/rc.local 
          |  sh /home/ec2-user/reboot_message.sh
          chmod +x /etc/rc.local
        
        caution. some distribution has another location for rc.local 
        /etc/rc.d/rc.local and one need to care rc0.d, rc1.d and so on.

    - using init.d

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

    - using systemd
  
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


* Run an Application as a Service on Linux

    - you have a service as like java/python application 

    - Registering and Running the Service
        
    - config service file xxx.service 

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

    - use systemctl, start service. 
  
          $ sudo systemctl daemon-reload
          $ sudo systemctl start javasimple.service
          $ sudo systemctl enable javasimple.service
          $ sudo systemctl status javasimple.service


* How to load environment variables in a cron job and, and How to Run a Script at a Certain Time on Linux? 

    - where is the cron? 

          $ ls -l /var/spool/cron/crontabs/              : user-wide
          $ cat /etc/crontab                             : system-wide
          /etc/cron.allow
          /etc/cron.deny

    - how to 

          $ crontab -l
          $ crontab -e 
      
          * * * * *    ---->     min hour day month week
          wrapping and define  

          * * * * * printenv > /tmp/print_envs_result
          * * * * * BASH_ENV=/etc/profile bash -c "printenv > /tmp/print_envs_result"
          * * * * * bash -l -c "printenv > /tmp/print_envs_result"
          * * * * * BASH_ENV=~/.bashrc bash -l -c "printenv > /tmp/print_envs_result"
          * * * * * /home/baeldung/backup.sh > /dev/null 2>&1
          @reboot (cd /home/baeldung/monitoring-scripts; bash monitor-memory.sh)


* Configure a Systemd Service to Restart Periodically 
    - where is it
          /etc/systemd/system

    - write 

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

    - run  

          $ systemctl enable --now my-service.timer
          $ systemctl list-timers 
          $ journalctl --since "5 minutes ago"

    - run by RuntimeMaxSec and the Restart Options

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













<br/><a name="Utility"></a>

## Utility




<br/><a name="Filesystens"></a>

## File systens

* Where Disk Space Has Gone on Linux
      
  First do check disk partition

      df -h                  : human readabel

  then do check disk usage 

      du /var
      du -BM --max-depth=1 /var | sort -n | tail -n 5 
      du -BM --max-depth=<strong>2</strong> /var | sort -n | tail -n 5
      du -BM --max-depth=1 <strong>/var/log</strong> | sort -n | tail -n 5


  find command

      find /var -size +100M -printf '%s %p\n' | sort -n

  sof command: deleted files still using space: the system does not report space used by deleted files as free

      lsof | grep -E '^COM|deleted'
     
  ncdu            : this is really nice tool for checking disk usage!   

<br/><a name="Shell"></a>

## Shell 





<br/><a name="Files"></a>

## Files & Directory 

* “No such file or directory” Error When Executing a Binary

  Two cases: 

      $ ./binaryfile
      ./binaryfile: error while loading shared libraries: libbooster.so.0: cannot open shared object file: No such file or directory

      $ ./binaryfile
      -bash: ./binaryfile: No such file or directory         : missing a Program Interpreter while above is missing Libraries

  Solution:

      $ objdump -p binaryfile | grep NEEDED

      $ ldd binaryfile

      $ readelf -a binaryfile | grep NEEDED
      $ readelf -a binaryfile | grep interpreter


<br/><a name="Logging"></a>

## Logging

* journal 
  
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


* logrotate

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
