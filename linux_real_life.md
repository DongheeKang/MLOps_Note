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
  * [Docker](#Docker)

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


### How to Change the Default Home Directory of a User

    $ sudo useradd -m dongheekang   
    $ sudo useradd -m -d /home/dongheekang dongheekang

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


### Fixing the “Command Not Found” Error When Using Sudo 
  
* “Permission Denied” when running script, then do this

      $ chmod +x ./myscript 

* Fixing the error for a single command. We can pass the -E flag to sudo to make it preserve the current environment variables:

      $ sudo -E myscript

### Modify user 

* Update Account’s Username

      $ usermod -l old new

* to lock and unlock    

      $ usermod -L user
      $ usermod -U user 
      
* to extend expire date

      $ usermod -e 2025-09-01 user


<br/><a name="Networking"></a>

# Networking

### Network Modell: 7 Layer	OSI layer model
  1. Application Layer  : HTTP, FTP, SMTP, DNS, Telnet
  2. Presentation       : ASCII, MPEG, JPEG, MIDI
  3. Session		        : NetBIOS, SAP, SDP, NWLink
  4. Transport(Segment) : TCP, UDP, SPX
  5. Network(Packet)    : IPv4, IPv6, RIP, OSPF, ICMP, IGMP, ARP
  6. Data Link(Frame)   : Ethernet, PPPoE, Token Ring, FFDI, ATM
  7. Physical Layer     : devices

### Hub, Switch and Router
* Hub
  - connects all the network devices together
  - multiple ports
  - not intelligent, do not know where data going to be sent
  - data is copied to all its ports -- broadcasting
* Switch
  - like a hub, accepts ethernet connections from network devices
  - it is intelligent, knows the physical address(MAC address) in switch table.
  - when a data is sent, it is directed to to intended port
  - reduce unnecessary traffic
* Hub and switch are not capable of exchanging data outside its own network, because to be able to reach outside network a device need to be able to read IP addresses
* Router
  - A router routes data from one network to another based on its IP address
  - The gateway of a network
* Hub and switches are used create networks while routers are used to connect networks
* Gateway
  - Gateway is an IP address of the router, while router does a load balancing for requests

### Glossary

* **802.1X**
  -- Enhance security of WLAN by IEEE, provides authentication frame work, allows users to be authenticated by a central authority. wireless.
* **Authenticated DHCP**
  -- First network access control, authenticating user id/password be for delivering a DHCP.
* **Backbone**
  -- Primary connectivity mechanism of a hierarchical distribution system. All systems that have connectivity to the backbone are assured of connectivity to each other.
* **Blacklisting**
  -- An access control system that denies entry to specific users, programs, or net work addresses
* **Berkeley Internet Name Domain (BIND)**
  -- The most commonly used DNS service of the internet
* **Broadcasting**
  -- A packet that is received by all stations in the domain
* **Cyclic Redundant Check (CRC)**
  -- A mathematical calculation on a frame work or cell that is used for error detection. If two CRCs don't match, there is an error.
* **DDI**
  -- a unified service or solution that integrate DNS, DHCP, and IPAM (IP Address Management) into one.
* **Dynamic Host Configuration Protocol**
  -- Assigning IP address to device
* **Domain Name System (DNS)**
  -- the system of domain names. eg. google.com (no www) godaddy.com
* **Frame**
  -- A unit of data transmission in layer two, containing a packet to layer three
* **Hop**
  -- Each time a packet is forwarded, it undergoes a "hop". (traceroute www.google.com)
* **IP Address Management (IPAM)**
  -- The administration of DNC and DHCP. It means Planing, tracking, and managing the Internet Protocol space used in a network. eg. DNS knowing the IP address taken via DHCP, and updating itself.
* **Local Area Network (LAN)**
  -- Its a Network that connects computers and devices in a limited geographical area. oppose with WANs (Wide Area Network). eg. home and school. Smaller area, faster speed, no need for telecommunication line.
* **deep packet inspection**
  -- routers looking inside the data packet other than just read the ip address, take very slow


### IP address

* IP and bit

      IPv4 :  32 Bit Addressen, darstellt in DDN(dotted deciaml notation)
      IPv6 : 128 Bit
      MAC  :  48 Bit

* IP Class

      Private :  10.  0. 0. 0/8  -  10.255.255.255   (1 nets, Class A)
      Private : 172. 16. 0. 0/12 - 172. 31.255.255  (16 nets, Class B)
      Private : 192.168. 0. 0/16 - 192.168.255.255 (256 nets, Class C)

      Loopback: 127.0.0.0 & 127.0.0.1
      Heimnetzwerk(Link Local): 169.254.0.0/16

  * Loopback: 
    Packets sent to this address never reach the network but are looped through the network interface card only. 
    This can be used for diagnostic purposes to verify that the internal path through the TCP/IP protocols is working. 
 
* P-packet forwarding and Internet Control Message Protocol (ICMP) 

      $ vi /etc/sysctl.conf
      $ sysctl -w

      or

      $ /proc/sys/net/ipv4/ip_forward
      $ /proc/sys/net/ipv4/icmp_echo_ignore_all

* IP port number

  To check see services and associated ports

      $ cat /etc/services

  Total 65536(=2^16Bit) Ports available.

          0 -  1023  : Well known Ports
       1024 - 49151  : registered ports
      49152 - 65535  : client ports


  | Port   |      Are      |               Description |
  |--------:|:-------------:|------:|
  |    21  |  FTP    | File Transfer Protocol
  |    22  |  SSH    | Secure Shell
  |    23  |  Telnet | Telnet remote login service, a plan text protocol
  |    25  |  SMTP   | Simple Mail Transfer Protocol
  |    53  |  DNS    | Domain Name System service
  |    80  |  HTTP   | Hypertext Transfer Protocol
  |   110  |  POP3   | Post Office Protocol
  |   119  |  NNTP   | Network News Transfer Protocol
  |   123  |  NTP    | Network Time Protocol
  |   143  |  IMAP   | Internet Message Access Protocol
  |   161  |  SNMP   | Simple Network Management Protocol
  |   194  |  IRC    | Internet Relay Chat
  |   443  |  HTTPs  | secure HTTPs
  |   993  |  imaps  | secure IMAPs
  |   995  |  pop3s  | secure POP3
  |  3128  |         | Proxy server port
  |  7100  |         | X-Font server port
  |  8080  |         | extended HTTP port


### Mapping hostnames with ports in /etc/hosts
Look into /etc/hosts first

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
  
### How to check port?

    $ netstat -ltnup | grep ':22'
    $ ss -ltnup 'sport = :22'
    $ lsof -i :22 -i :68
    $ fuser -v 22/tcp 68/udp

    options (netstat & ss)
    -------------------------------------------
    l – show only listening sockets
    t – show TCP connections
    n – show addresses in a numerical form
    u – show UDP connections
    p – show process id/program name
    -------------------------------------------

### How to kill running on a specific port?

creat three processes using port 9999 and the protocols SCTP, TCP, and UDP respectively.

    $ socat sctp-listen:9999,bind=127.0.0.1 stdout &
      [1] 6424
    $ socat tcp-listen:9999,bind=127.0.0.1 stdout &
      [2] 6431
    $ socat udp-listen:9999,bind=127.0.0.1 stdout &
      [3] 6438

Solution 
* fuser

      $ fuser -k 9999/tcp
      9999/tcp: 6431

* kill

      $ kill -9 6431

* lsof
      
      $ lsof -i udp:9999 | awk '/9999/{print $2}' | xargs kill

* ss or netstat

      $ ss -Slp | grep -Po ':9999\s.*pid=\K\d+(?=,)' | xargs kill
      $ netstat -Slp | grep -Po ':9999\s.*LISTEN.*?\K\d+(?=/)' | xargs kill


### Freeing up a TCP/IP port

Sometimes there isn’t any process using the port and, even so, the system tells us the port is busy or in use. 
This is usually because of the TIME-WAIT state. If this is the case, we only have to wait until the system frees it up, 
by default the timeout is 2 minutes.

    $ fuser -k 8000/tcp
    $ fuser -i -TERM -k 8000/tcp                 : use SIGTERM to do more carefully!

    $ lsof -i :8000
    $ ss -apt 'sport = :8000'
      Netid  State      Recv-Q Send-Q Local  Address:Port  Peer Address:Port                
      tcp    LISTEN     0      128    *:ssh                *:*                 users:(("sshd",pid=1226,fd=3))
      ......
      tcp    TIME-WAIT  0      0      192.168.0.4:56886    192.168.0.5:https

    TIME-WAIT: the process closed the connection, and the port is waiting for a timeout without any process using it.

### Domain Name System (DNS)
* working principle: resolves domain names to IP addresses

      1. domain name typed in
      2. DNS server search through its database to find its matching IP address
      3. DNS will resolve the domain name into IP addresses

* DNS Types

      Master (Primary) DNS Server
      Slave (Secondary) DNS Server
      Caching (hint) DNS Server
      Forwarding (Proxy, Client, Remote) DNS Server
      Stealth (DMZ or Split) DNS Server
      Authoritative Only DNS Server
      Recursive name server = cache name server = resolving name server

* DNS configuration

      $ cat /etc/hosts
      $ cat /etc/hostname
      $ cat /etc/hosts.conf
      $ cat /etc/nsswitch.conf
      $ cat /etc/resolv.conf

* DNS Testing

      $ host         : to get host information from name server
      $ nslookup     : tool to ask host information from name server
      $ dig          : after finish DNS configuration,one can test DNS
      $ whois        : a program to find domain holder
      $ getent       : a tool for carry out the database of administrator
      $ rndc         : name server control utility for BIND

      $ vi /etc/bind/named.conf        debian
      $ vi /etc/named/named.conf       fedora

* DNS operation
    Start by systemctl, service, init.d

    Firewall open via iptables configuration

      $ vi /etc/iptables/rules           : debinas
      $ vi /etc/sysconf/iptables         : CentOS, Fedora

* Detailed Steps

      1. type in the Domain Name in web browser
      2. if the computer can't find its IP address in its cache memory, it will send the query to the Resolver server(basically your ISP)
      3. Resolver will check its own cache memory, if not, it will send the query to Root server, the top or the root of the DNS hierarchy, 13 sets of root servers around the world, operated by 12 organizations. each set has its own IP address
      4. he root server will direct the resolver the Top Level Domain server (TLD), for the .com, .net, .org(top level domains) domain.
      5. TLD will direct the resolver to the Authoritative Name Server(ANS), and the resolver will ask the ANS for the IP address
      6. ANS is responsible for knowing everything including the IP address of the domain
      7. ANS will respond with IP address
      8. the resolver will tell the computer the IP address
      9. the resolver will store the IP Address in its cache memory

* DNS Security Extensions
  TSIG or DNSSEC


### SSH Tunneling and Proxying

* Server side configuration: sshd

      $ vi /etc/ssh/sshd_config 
      -------------------------------------------------------------------------------------------------------------
      AllowStreamLocalForwarding   : Allows Unix domain sockets to be forwarded. 
      AllowTcpForwarding           : Allows TCP port forwarding. 
      DisableForwarding            : Disables all kinds of forwarding.
      GatewayPorts                 : Allows to use the ports forwarded to a client 
      PermitListen                 : Specifies the addresses and ports (‘127.0.0.1’)
      PermitOpen                   : Specifies the address and ports
      PermitTunnel                 : Specifies whether tun device forwarding is allowed. 
      X11Forwarding                : Specifies whether X11 forwarding is allowed. 
      X11UseLocalhost              : Forces X11 forwarding to be allowed from the SSH server host loopback address. 
      -------------------------------------------------------------------------------------------------------------

  need to be confirmed that firewalls must allow the SSH traffic, usually on port TCP/22, since some host firewall configurations might limit the ability to connect to and from external services, and iptables needs to be touched further later on

* Forward
  * Single-Port

      A forward or direct TCP tunnel is the one that follows the direction of the SSH connection from the client to the SSH server. 

        $ ssh -L [bind_address:]port:host:hostport [user@]remote_ssh_server
        $ ssh -L 0.0.0.0:8022:10.1.4.100:22 user@10.1.4.20
               
        possible this as well. 
        $ ssh -L local_socket:host:hostport [user@]remote_ssh_server
        $ ssh -L local_socket:remote_socket [user@]remote_ssh_server

  * Dynamic or Multi-Port

    A special case of the forward TCP tunnels is the Socks proxy capability. Using these options, the SSH client listens on a specified binding port and acts as a SOCKS 4 or 5 proxy server.

        $ ssh -D [bind_address:]port [user@]remote_ssh_server
        $ ssh -D 8080 user@10.1.4.100

* Reversed
  * Single-Port

    The reverse or callback proxies allow us to do tricks similar to the one above but in the reverse direction. We can open services on our own local networks to hosts on the remote side of the SSH session.

        $ ssh -R [bind_address:]port:host:hostport [user@]remote_ssh_server
              
        optional possible 
        $ ssh -R remote_socket:host:hostport [user@]remote_ssh_server
        $ ssh -R remote_socket:local_socket [user@]remote_ssh_server
        $ ssh -R [bind_address:]port:local_socket [user@]remote_ssh_server         

  * Dynamic or Multi-Port

    Finally, we can expose a SOCKS proxy server on the remote host directed to the client’s network as we can do with direct forwarding. 

        $ ssh -R [bind_address:]port [user@]remote_ssh_server

* X window turnnel

      $ ssh -X [user@]remote_ssh_server
      $ ssh -Y [user@]remote_ssh_server

* Multiple Tunnels and Multiple Host Hopping

      $ ssh -X -L 5432:<DB server IP>:5432 -R 873:<local RSYNC server>:873 [user@]remote_ssh_server

      server1 -> server2 -> server3 
      $ ssh -L 8022:<server2>:22 user@server1
      $ ssh -L 8023:<server3>:22 -p 8022 user@localhost
      $ ssh -p 8023 user@localhost

* configuration for above

      $ vi /etc/ssh/ssh_config
        host 10.1.4.100
          ForwardX11 yes
          LocalForward 0.0.0.0:5432 10.1.4.200:5432
          RemoteForward localhost:8022 localhost:22
          user dongheekang

* Persistent 

      $ autossh [-V] [-M port[:echo_port]] [-f] [SSH_OPTIONS]
      $ autossh -X -L 5432:<DB server IP>:5432 -R 873:<local RSYNC server>:873 [user@]remote_ssh_server
      $ autossh -f [host]

### netcat(nc) 
    
* reading and writing data across the network, through TCP or UDP 

      $ nc -z -v -w 1 google.com 442-444  : Scanning for Open Ports

      server node
      $ nc -lv 1234                       : listen to port 1234 
      client node
      $ nc -v localhost 1234              : open up a netcat process that connects to localhost at port 1234 

      $ nc -l -v -k localhost 1234        : server and client netcat processes will return whenever the connection is terminated  

* A netcat process 
  
  first constructs a legitimate HTTP response using echo and process substitution, 
  listens to port 1234 and serves the file whenever a client connects to our server:

      $ echo -e "HTTP/1.1 200 OK\n\n$(cat index.html)" | nc -l 1234

* Improvement

  we wrapped the command into a while loop. In consequence, whenever the command terminates, it’ll restart the process 
  and using -w flag allows us to specify the timeout value

      $ while true; do echo -e "HTTP/1.1 200 OK\n\n$(cat index.html)" | nc -l -w 1 1234; done

      How do create index.html?
      ------------------------------------------------
      cat -> index.html <<<EOF
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
      -------------------------------------------------

* Reserve shell connection: 

  set server node

      # Listening on 0.0.0.0 1234
      # Connection received on server.dongheekang 36170

      $ nc -lv 1234
      $ mkfifo /tmp/rs
      $ cat /tmp/rs | /bin/bash 2>&1 | nc -v client 1234 > /tmp/rs

      # checking

      $ hostname
      $ server

  any text sent by the client node will then be piped to /tmp/rs

* Reserve proxy connection: 

  set server node
  
      $ mkfifo /tmp/rp
      $ nc -lv 1234 < /tmp/rp | nc localhost 4321 > /tmp/rp

  the first process the external router and the second process the internal router.
  When there is incoming traffic on port 1234, the external router pipes the traffic to the internal router.
  when there’s outgoing traffic from port 4321, the internal router will pipe it to the pipe /tmp/rp. 
  Then, the external router(server) will read and send the content of /tmp/rp to the client (outside).

* To test the correlation between two computers without firewall
  bidirectional interactive text-oriented communication facility

      $ server1> nc -l 4444
      $ server2> nc server1.com 4444


### iperf
We need to install iPerf on both the client and the server

* Server

      $ iperf -s 
      $ iperf -s -u -p 5003                 : let'S make the server use UDP and listen on port 5003

* Client: 
      
      Connecting to the Server From the Client

      $ iperf -c 5.182.18.49
      $ iperf -c 5.182.18.49 -i 5 -t 15 -w 416K -p 5003
      $ iperf -c 5.182.18.49 -u -b 1000M 

      * i specifies the interval time in seconds. 10 is the default
      * t specifies the time to run the test in seconds
      * p specifies the port. 5001 is the default
      * w specifies the TCP window size. 85 KB is the default
      * u upd
      * b limits the bandwidth for UDP to 1Mbits/sec by default

### Port scaning for UDP

      $ nmap -sU -v 172.16.38.137
      $ nc -vz -u 8.8.8.8 443

      $ iperf3 -s
      $ iperf3 -u -c 172.16.38.137

### How to list all connected SSH sessions (possible commands)

      $ who
      $ w
      $ sudo last | grep 'still logged in'  
      $ sudo netstat -atnp | grep 'ESTABLISHED.*sshd'
      $ ss | grep ssh
      $ ps axfj | grep sshd

### Find the IP Address of the Client in an SSH Session (possible commands)

      $ who
      $ w
      $ finger
      $ pinky  
      $ last | head
      $ sudo netstat -tpn | grep "ESTABLISHED.*sshd"
      $ sudo ss -tp | grep "ESTAB.*sshd"
      $ sudo lsof -i TCP -s tcp:established -n | grep ssh

### Find primary IP address of a linux
* Primary active interface first

      $ ip address show
      $ route

* Primary IP address

      $ ip address show dev ens33 
      $ ip address show dev ens33 | grep -w inet | awk '{print $2}'   
      $ ip route get 1.1.1.1
        1.1.1.1 via 192.168.207.2 dev ens33 src 192.168.207.128 uid 1000           : primary = 192.168.207.128
      $ ifconfig ens33 | grep -w inet | awk '{print $2}'
      $ nmcli 
      $ curl ifconfig.me

# Get external IP address in a shell script
* Network interface first

      $ ip address show
      $ ip address show eif0 | grep 'inet'
        169.254.6.66
      $ ip address show eif0 | grep 'inet'
        2001:db8:666:666::10

* External IP Checks

      $ ssh user@server-router 'ip address show eif0'

      $ curl --user USERNAME:PASSWORD http://router/
      $ ssh sshmyip.com
      $ telnet telnetmyip.com
      $ wget -qO- telnetmyip.com

* DNS IP query

      $ dig +short myip.opendns.com @resolver1.opendns.com
      $ host myip.opendns.com resolver1.opendns.com
      $ dig +short txt ch whoami.cloudflare @1.1.1.1
      $ dig +short txt o-o.myaddr.test.l.google.com @ns1.google.com

+ unnpc 

      $ UPNP_DATA = $(upnpc -s | grep ^ExternalIPAddress | cut -c21-)
      $ echo "${UPNP_DATA}"


### Translate DNS to IP

      $ ping dongheekang.com
      $ host dongheekang.com
      $ host -t a dongheekang.com
      $ nslookup dongheekang.com
      $ dig dongheekang.com A +short
      $ dig dongheekang.com AAAA +short
      $ nmap -sn dongheekang.com

### Route specific subnet to a particular interface

* ip-route : routing table management

      $ ip monitor                                      : live monitoring for connection of MAC and IP
      $ ip rule list                                    : To look up the route table and rule

      $ ip route get 194.168.23.132                   

        194.168.23.132 via 194.168.23.1 dev enp1s0 src 194.168.23.120 uid 1000 
        cache  

        gateway      : 194.168.23.1 
        destination  : 194.168.23.132

      test 
      $ traceroute 172.23.1.100  

      add 
      $ ip route add 100.1.1.0/24 via 192.168.221.142 dev enp7s0     : add static router
      $ ip route add 10.10.20.0/24 via 192.168.50.100 dev eth0       : add static router
      $ ip route add default gw 20.14.5.65               : result of routing table of network

* To make the routes persistent (nmcli)

  Destnation-based

      $ systemctl status NetworkManager
      $ nmcli connection modify enp7s0 +ipv4.routes "100.1.1.0/24, 8.8.8.8 192.168.221.142"

      $ cat /etc/sysconfig/network-scripts/route-enp7s0 
        ADDRESS0=100.1.1.0
        NETMASK0=255.255.255.0
        ADDRESS1=8.8.8.8
        NETMASK1=255.255.255.255
        GATEWAY1=192.168.221.142

  Source-based
      
      IP Masquerading
      $ firewall-cmd --change-interface=enp7s0 --zone=external --permanent
      $ firewall-cmd --change-interface=enp8s0 --zone=external --permanent
      $ firewall-cmd --change-interface=enp1s0 --zone=internal --permanent
      $ firewall-cmd --zone=external --add-masquerade --permanent

      create the custom routing table and rule
      $ nmcli connection modify enp8s0 +ipv4.routes "100.1.1.0/24 10.20.1.1 table=400, 8.8.8.8 10.20.1.1 table=400"
      $ nmcli connection modify enp8s0 +ipv4.routing-rules "priority 200 from 192.168.221.0/24 table 400"
      $ cat /etc/sysconfig/network-scripts/ifcfg-enp8s0
      $ nmcli device reapply enp8s0                 : start table
      $ ip route show table 400                     : validation

      test
      $ ping 100.1.1.100
      $ tcpdump -i enp8s0 host 100.1.1.100 -n 

* MAC address access via ip

      $ ip neighbour show
      $ arp -a                     :  address resolution protocol(arp) using IP one can access MAC address


### Configure network settings using network manager in Linux
* nmcli

      $ sudo apt-get install network-manager
      $ systemctl status NetworkManager

      $ nmcli device                            : Displaying the List of All Network Devices
      $ nmcli device show wlp5s0                : Displaying the IP Address of a Device
      $ nmcli connection                        : Displaying the List of Existing Connections
      $ nmcli connection show id my-ethernet    : Displaying the Properties of a Connection
      $ nmcli radio                             : Displaying the Status of Wireless Adapters

* Configuring an Ethernet Adapter With a Static IP Address

      $ nmcli connection add type ethernet ifname enp7s8 con-name my_ethernet ip4 192.168.2.138/24 gw4 192.168.2.1

* Configuring an Ethernet Adapter With a Dynamic IP Address

      $ sudo nmcli connection add type ethernet ifname enp7s8 con-name my_ethernet

* Modifying connection

      $ sudo nmcli connection modify my_ethernet ipv4.DNS 192.168.2.1

* Enable and disable
      $ nmcli connection down my_ethernet
      $ nmcli connection up my_ethernet

### Netwrok interface configuration

* command set

      $ ifconfig
      $ ifup
      $ ifdown
      $ brctl show

* configurations

      $ cat /etc/network/interfaces                    : for Debian
      $ cat /etc/sysconfig/network                     : for CentOS
      $ cat /etc/sysconfig/network-scripts/ifcfg-eth0  : for CentOS

* run interface

      $ /etc/init.d/networking restart        : for Debian
      $ /etc/rc.d/init.d/network restart      : for CentOS

* Netzwerkkonfiguration Debian or Ubuntu Server

      $ vi /etc/network/interfaces
      |auto eth0
      |iface eth0 inet dhcp
      |sudo ifup eth0

      |auto eth0:0
      |iface eth0:0 inet static
      |address ABC.DEF.GHI.JKL
      |netmask 255.255.255.255
      |network ABC.DEF.GHI.0
      |broadcast ABC.DEF.GHI.JKL
      |gateway 10.255.255.1

      $ /etc/init.d/networking restart

* Netzwerkkonfiguration CentOS

      $ vi /etc/sysconfig/network-scripts/ifcfg-eth0
      |ONBOOT=no
      |ONBOOT=yes
      |ifup eth0

      $ cp -a /etc/sysconfig/network-scripts/ifcfg-eth0 /etc/sysconfig/network-scripts/ifcfg-eth0:0
      $ vi ifcfg-eth0:0
      |DEVICE=eth0:0
      |BOOTPROTO=static
      |BROADCAST=ABC.DEF.GHI.JKL
      |IPADDR= ABC.DEF.GHI.JKL
      |NETMASK=255.255.255.255
      |NETWORK= ABC.DEF.GHI.0
      |ONBOOT=yes
      |NM_CONTROLLED=no

### Network command set

* ifconfig

  inspect and reconfigure network interfaces on a Linux machine

      $ ifconfig -a                       : list all
      $ ifconfig -s                       : short for programming
      $ ifconfig ens33                    : specific interface 
      
      $ sudo ifconfig ens33 down          : interface up or down 

      set 
      $ sudo ifconfig ens33 192.168.91.200
      $ sudo ifconfig ens33 netmask 255.255.0.0
      $ sudo ifconfig ens33 broadcast 10.2.255.255
      $ sudo ifconfig ens33 10.2.1.101 netmask 255.255.0.0 broadcast 10.2.255.255

* nslookup

  to query domain name servers (DNS) and is available for operating systems

      $ nslookup                                 : this is interactive 
      $ nslookup dongheekang.com                 : Lookup a Domain
      $ nslookup -type=a dongheekang.com         : get all DNS entries
      $ nslookup -type=soa dongheekang.com       : look at the authoritative (SOA) information about the domain
      $ nslookup -type=ns dongheekang.com        : look at the name server information

* tracert

  To track the exact route a given packet takes, since network traffic doesn’t go directly to the desired machine.

      $ traceroute dongheekang.com
      $ traceroute -m 3 dongheekang.com               : upto 3 hops

* tcpdump
  
  analyzing the network interface traffic on Linux Systems

      § sudo tcpdump
        listening on ens160, link-type EN10MB (Ethernet), capture size 262144 bytes
        04:47:21.629831 IP 27.57.7.242.32917 > sandbox1.ssh: Flags [P.], seq 639116254:639116462, ack 
        1982486691, win 501, length 208
        04:47:21.757924 ARP, Request who-has 10.87.34.12 tell _gateway, length 46
        06:47:21.880039 IP sandbox1 > 104.26.11.14: ICMP echo request, id 20418, seq 1, length 64
        06:47:21.914808 IP 104.26.11.14 > sandbox1: ICMP echo reply, id 20418, seq 1, length 64

      ------------------------------------------------------------------------------------
      * 04:47:21.629831 – IP Packet’s timestamp in microseconds
      * IP – protocol type such as IP {Internet Protocol}, ARP {Address Resolution Protocol}, ICMP {Internet Control Message Protocol}
      * 27.57.7.242.32917 – source IP address and port. Usually, the source port is taken randomly from the registered unknown port ranges
      * sandbox1.ssh – destination IP address and port. {well-known port number 22 gets converted as ssh}
      * Flags[P.] – Any TCP flags {P – PUSH}; a period indicates an ACK
      * seq 639116254:639116462 – sequence ranges with starting and ending sequence numbers. The difference is the amount carried in Bytes which is the field length
      * ack 1982486691 – TCP packet’s acknowledgment number
      * win 501 – source machine TCP window size
      * length 208 – TCP data length or payload size. Here, it’s 639116462 – 639116254 = 208
      ------------------------------------------------------------------------------------

  Monitoring the Interface using tcpdump

      $ ping -c 1 localhost
      $ sudo tcpdump -i lo                     : capture the SSH packets from the loopback interface

      tcpdump host dongheekang.com -i any -c10
      tcpdump host 104.26.12.74 -i any -c10

      tcpdump -i any "host dongheekang.com and (port 22 or port 443)"
      tcpdump -i any -n "src net 192.168.0.0/16 and not dst net 10.0.0.0/8" -c4
      

* nmap

  Network Mapper (shortened to nmap) is a network exploration tool

      $ nmap -p $PORT_OPEN,$PORT_CLOSED,$PORT_STEALTH $IP
        [...]
        PORT    STATE    SERVICE
        22/tcp  open     ssh
        111/tcp closed   rpcbind
        137/tcp filtered netbios-ns
        [...]

* telnet

      $ telnet impa.lpic.de 143          <--- telnet also possible for checking port connection! this is a great tip!
      $ telnet -l donghee 5.182.18.49


### Connectivity test (standard)

      $ ping -c 1 141.1.1.1
      $ ping6 ::1

      $ traceroute   www.xxxx.com
      $ tracepath -n 217.18.182.170

      $ netstat -nc            : connection with all open port
      $ nc -z daum.net 80      : nc(netcat) network conneciton tool, use in the shell
      $ nmap                   : port scanning and defending networks
      $ lsof /tmp              : prozesse, die auf einen Netzwerk-Socket zugreifen
      $ tcpdump -i eth0        : show network flow into the screen using dump


### Network failures simulation in Linux
* tc (traffic control command-line tool) and qdisc (queuing discipline)

  tc can control traffic in the Linux kernel network stack and qdisc is a scheduler that manages the scheduling of packets queue

      $ sudo apt-get install -y iproute2              : install
      $ sudo tc qdisc show                            : Listing the Qdiscs

* Let's simulation

      $ sudo tc qdisc add dev eth0 root netem delay 100ms
      $ ping -c 5 google.com                          : testing 
      $ sudo tc qdisc delete dev eth0 root            : delete after testing
      $ sudo tc qdisc show eth0                       : check 

* Simulating normally distributed delays

      $ sudo tc qdisc add dev eth0 root netem delay 100ms 50ms distribution normal
      $ ping -c 240 -q google.com

* Simulating Packet Loss
      $ sudo tc qdisc add dev eth0 root netem loss 30%          : dropping packets randomly with a 30% probability
      $ sudo tc qdisc add dev eth0 root netem loss 30% 50%      : 50% of the probability that the next packet is dropped
      $ ping -q -c 60 google.com

* Simulating Packet Duplication
      $ sudo tc qdisc add dev eth0 root duplicate 50%
      $ ping -c 2 google.com

* Simulating Packet Corruption
      $ sudo tc qdisc add dev eth0 root netem corrupt 30%
      $ ping -q -c 240 google.com

* Limiting the Transfer Rate
      $ sudo tc qdisc add dev eth0 root netem rate 10Mbit
      $ iperf3 -c 172.18.0.3 -p 8080

### Monitoring Network Usage in Linux (extended)

      $ nload
      $ speedometer
      $ iftop
      $ nethogs

### Wireless information
* standard command

      $ iwconfig wlan0   : show WLAN adapter/interface
      $ iwlist wlan0     : show an information about WLAN cards
      $ iw dev wlan0 	   : show wireless devices and their configuration

* Configuring a Wireless Device vis nmcli

      $ nmcli device wifi list
      $ nmcli device wifi connect MY_WIRELESS_NET password 8ehdxhre5kkhb6g6

### Questions

*  Q) tools to check for open ports on a local computer?     

      nmap, netstat, lsof

* Q) A program run through the port 5112, if you want to check whether this port is active and has been blocked by firewall, how can you check?  

      netcat

* Q) What program uses local system calls to locate local ports that are currentl open?

      netstat is a scanner just for local Ports, nmap & nessus is a scanner for local ports and also for other computers in networks



<br/><a name="Security"></a>

# Security

### Secure Shell SSH
SSH is a communication Protocol. The traffic is encrypted
SSHD is the server (Open SSH Daemon) and SSH is the client.
the server must have sshd installed and running

* Generating a new SSH key and publicate it!
  - create ssh key

        $ ssh-keygen -t ed25519 -C "your_email@example.com"
        $ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

  - adding your SSH key to the ssh-agent

        $ sudo -s -H
        $ exec ssh-agent bash
        $ eval "$(ssh-agent -s)"

  - for MacOS, please do modify config to update keychain automatically

        $ touch ~/.ssh/config
        | Host *
        |     AddKeysToAgent yes
        |     UseKeychain yes
        |     IdentityFile ~/.ssh/id_ed25519

  - add your SSH private key to the ssh-agent and store your passphrase in the keychain

        $ ssh-add -K ~/.ssh/id_ed25519

  - public key goes into the target server as "authorized_keys" file

        ~/.ssh/id_rsa (private key)
        ~/.ssh/id_rsa.pub (public key)

  - copy the public key

        $ ssh-copy-id demo@SERVER_IP_ADDRESS

  - (optional) copy public key manually

        $ cat ~/.ssh/id_rsa.pub
        | ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAA...rggpFmu3HbXBnWSUdf localuser@machine.local
        | ctrl + c

        $ gpasswd -a demo sudo
        $ su - demo
        $ mkdir .ssh
        $ chmod 700 .ssh

        $ vi .ssh/authorized_keys
        | ctrl + v

        $ chmod 600 .ssh/authorized_keys


### How to login without password?  Passwordless

* using sshpass
      use -p option (standard)
      § sshpass -p 'Bael@123' ssh tools@10.149.20.11 -p4455 'hostname; df -h | grep sd; tail -2 /var/log/dpkg.log';
        
      use -e option 
      $ export SSHPASS="PASSWORD"                        :password in the variable farm
      $ echo $SSHPASS
        PASSWORD
      $ sshpass -e ssh tools@10.149.20.11 -p4455 'hostname; df -h | grep sd; tail -2 /var/log/dpkg.log';

      use -f option
      $ echo 'PASSWORD' > .sshpasswd                     :into the home or root directory
      $ cat .sshpasswd
          PASSWORD
      $ sshpass -f .sshpasswd ssh tools@10.149.20.11 'hostname; df -h | grep sd; tail -2 /var/log/dpkg.log';

* using ssh
      $ ssh-keygen -t rsa
      $ ssh-copy-id tools@10.149.20.11 -p4455
      $ ssh -p '4455' 'tools@10.149.20.11’
      server$ exit

      this is the way of passwordless ssh option
      $ ssh tools@10.149.20.11 'hostname; df -h | grep sd; tail -2 /var/log/dpkg.log'; 


### GPG standard
Gpg2 is the OpenPGP part of the GNU Privacy Guard (GnuPG). 
It is a tool to provide digital encryption and signing services using the OpenPGP standard.

* GPG algorithms

      RSA
      ElGamal
      DSA
      ECDH
      ECDSA
      EdDSA


* Creating a new GPG key
  - create gpg key

        $ gpg --full-generate-key
        $ gpg --default-new-key-algo rsa4096 --gen-key

  - to list the long form of the GPG keys for both a public and private key

        $ gpg --list-secret-keys --keyid-format=long  /Users/hubot/.gnupg/secring.gpg
        |------------------------------------
        |sec   4096R/3AA5C34371567BD2 2016-03-10 [expires: 2050-03-10]
        |uid                          Hubot
        |ssb   4096R/42B317FD4BA89E7A 2016-03-10   

  - export the public key

        $ gpg --armor --export 3AA5C34371567BD2
        # this will prints the GPG key ID in ASCII armor format

### GPG authentication from LPIC
    ==============================================================================================
    GPG authentication (administrator level)
    ==============================================================================================
    Generate a keypair using gpg2 command, enter name, email, keysize and choose a passphrase at the end
    (please keep all information in the note!)
      $ gpg2 --gen-key
      | .....................................
      | .....................................
      | Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? O
      | You need a Passphrase to protect your secret key.
      | .....................................

    keep the public master GPG key at the line on "pub 2048R/2AFFE2C5 2017-08-02" (2AFFE2C5 is key-id)
      | gpg: checking the trustdb
      | gpg: 3 marginal(s) needed, 1 complete(s) needed, PGP trust model
      | gpg: depth: 0 valid: 1 signed: 0 trust: 0-, 0q, 0n, 0m, 0f, 1u
      | pub 2048R/2AFFE2C5 2017-08-02
      | Key fingerprint = F619 5E0E 4B8A 0675 22A3 55FA ACBA 0973 2AFF E2C5
      | uid SAPGlobalSign (GPG authentification) SAPGlobalSign@sap.com
      | sub 2048R/00B0933C 2017-08-02

    export your public key to use in signing
      $ gpg2 --no-armor --output SAPGlobalGPGSign.key --export 2AFFE2C5
      "SAPGlobalGPGSign.key" public key move to /swshare/dbcat/v3.8/certs/SAPGlobalGPGSign.key

    signing your documents e.g. checksum file, you should need a passphrase in this stage. You can also use a batch mode without typing passphrase
      $ export signfile=/swshare/ase/16.0.02.06/linux_x86_64
      $ gpg2 --armor --output "${signfile}"/.checksum.md5.asc --detach-sign "${signfile}"/.checksum.md5
      $ gpg2 --armor --output "${signfile}"/.checksum.md5.asc --batch --passphrase "Pa\$\$w0rd" --detach-sign "${signfile}"/.checksum.md5

    In dbcat, verification of signed documents will be perforemd
      $ gpg2 --no-default-keyring --keyring SAPGlobalGPGSign.key --verify .checksum.md5.asc .checksum.md5

#### GPG command
    ===============================================================================================
    GPG command set
    ===============================================================================================
    [generate key]
      gpg2 --gen-key
      gpg: checking the trustdb
      gpg: 3 marginal(s) needed, 1 complete(s) needed, PGP trust model
      gpg: depth: 0  valid:   3  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 3u
      gpg: next trustdb check due at 2018-08-03
            pub   2048R/2288A15E 2017-10-09
            Key fingerprint = A51E BA24 9E16 3041 9D80  E42F 15F7 33D5 2288 A15E
            uid   Donghee Kang (test2) <donghee.kang@sap.com>

    [export, armor, keying]
      gpg2 --armor --output SAPGlobalGPGSign.key --export 9ED95FE6
      gpg2 --armor --output SAPGlobalGPGPrivate.key --export-secret-keys 9ED95FE7
      gpg2 --armor --output .checksum.md5.gpg --batch --passphrase "Pa\$\$w0rd" --sign .checksum.md5
      gpg2 --no-default-keyring --keyring /var/tmp/dbcatTrans/zfinal/SAPGlobalGPGSign.key --verify .checksum.md5.asc .checksum.md5
      gpg2 --verify .checksum.md5.asc .checksum.md5

      gpg2 --no-armor output SAPGlobalGPGSign.gpg --export 9ED95FE6
      gpg2 --armor --output .checksum.md5.asc --batch --passphrase "Pa\$\$w0rd" --detach-sig .checksum.md5
      gpg2 --no-default-keyring --keyring /var/tmp/dbcatTrans/zfinal/SAPGlobalGPGSign.gpg --verify .checksum.md5.asc .checksum.md5

    [output]
      gpg: [don not know]: invalid packet (ctb=78)
      gpg: keydb_search failed: Invalid packet
      gpg: Can't check signature: No public key

    [remove key]
      gpg --list-keys
      gpg --delete-secret-key key-ID
      gpg --delete-key key-ID

    [public key]
      gpg2 -k
      gpg2 --armor --output gpg2-public-key.rsa --export donghee.kang@sap.com
      gpg2 --armor --export donghee.kang@sap.com | tee gpg2-public-key.rsa

    [private key]
      gpg -a -o exportedKeyFilename.asc --export-secret-keys keyIDNumber
      gpg -a -o gpg_private_key.asc --export-secret-keys 60F9D6E1

    [encypytion]
      gpg --ouput a.txt.gpg --encrypt --recipient administrator@sap.com a.txt

    [without passphrase default output (--ouput a.txt.gpg)]
      gpg2 -se --passphrase yourpassword --batch --encrypt --recipient xxxxxx@sap.com a.txt
      gpg2 --passphrase "Veritas47" --batch --encrypt --recipient donghee.kang@sap.com example2.txt

    [descrytion]
      This is important and have to be set into the file...
      gpg2 --output a.txt --batch --passphrase yourpassword --decrypt a.txt.gpg
      gpg2 --output a.txt --batch -no-default-keyring --secret-keyring /path/gpg_private_key.asc --passphrase yourpassword --decrypt a.txt.gpg


### Firewall
Set of passive rules to protect network from unauthorized access
Scans each little packet of data, physical(routers) or software, can me exceptions by users

Uncomplicated Firewall(UFW)

    $ sudo ufw status

    $ sudo ufw allow ssh                : to all ssh 
    $ sudo ufw allow 4444/tcp           : extra 4444
    $ sudo ufw allow 80/tcp             : HTTP
    $ sudo ufw allow 443/tcp            : HTTPS
    $ sudo ufw allow 25/tcp             : SMTP
    $ sudo ufw allow 21/tcp             : ftp
    $ sudo ufw show added               : finalized
    $ sudo ufw enable                   : confirm then type "y"

    $ sudo ufw allow from 192.168.255.255

    $ sudo ufw default deny incoming
    $ sudo ufw default deny outgoing

    $ sudo ufw delete allow 80/tcp
    $ sudo ufw delete allow 1000:2000/tcp    


  Firewalld (firewall daemon) is an alternative to the iptables service

    $ sudo apt-get install firewalld

  manage firewalld via systemctl

    $ sudo systemctl start firewalld	      :start the service for the mean time
    $ sudo systemctl enable firewalld	      :enable the service to auto-start at boot time

### iptables
iptables is a utility that allows a system administrator to configure the IP packet filter rules of the Linux kernel firewall filters connections based on user-defined rules

Keywords
* tables

      * FILTER – kernel will search for rules in this table for every input packet. Based on the rule, the packet is either accepted or dropped
      * NAT – The kernel uses this table for NATing rules. THis allows us to change the source or destination IP address in a packet. iptables can do this for both incoming and outgoing packets
      * MANGLE – This table allows us to alter IP headers. For example, we can change the TTL value in the input packet
      
* chains 
        
      INPUT, OUTPUT, FORWARD
* targets 

      ACCEPT, DROP, REJECT
    

How to work? 
* To look up first:

      $ iptables -L -v                     : look up 
      $ iptables -t nat -L -v              : 3 types of tables (NAT, Filter, Mangle)

* To add a new policy:

      $ iptables --policy FORWARD DROP

* To drop all packets from a particular IP:

      $ iptables -A INPUT -s 10.1.2.3 -j DROP

* To block all connections from the IP address 10.10.10.10.

      $ iptables -A INPUT -s 10.10.10.10 -j DROP

* To block a specific port:

      $ iptables -A INPUT -p tcp --dport ssh -s 10.10.10.10 -j DROP

* To drop all packets to a specific port:

      $ iptables -A INPUT -p tcp --dport 8080 -s 10.1.2.3 -j DROP

* To drop all packets on a particular protocol:

      $ iptables -A INPUT -p tcp --dport 22 -j DROP

* To change policy:

      $ iptables --policy INPUT ACCEPT    (accept, drop, reject)
      $ iptables --policy OUTPUT ACCEPT   (accept, drop, reject)
      $ iptables --policy FORWARD ACCEPT  (accept, drop, reject)

* Saving IP Tables Rules

      $ /sbin/iptables-save                : debian case
      $ /sbin/service iptables save        : CentOS, Fedora
      $ /etc/init.d/iptables save 

* Firewall rules 

        $ vi /etc/iptables/rules             : debian, ubuntu
        $ vi /etc/sysconf/iptables           : CentOS, Fedora   



### Obtaining an SSL certificate from the server
* Use a browser Firefox or Chrome, find the PEM 
      
      access connect security from the address bar, and download PEM 

* No browser way, use command line 

      $ openssl s_client -connect dongheekang.com:443
      $ openssl s_client -showcerts -connect dongheekang.com:443
      $ openssl s_client -showcerts -connect dongheekang.com:443 </dev/null | sed -n -e '/-.BEGIN/,/-.END/ p' > certifs.pem

* Servers Behind Reverse Proxies

  In some situations, our server might sit behind a reverse proxy for load-balancing purposes.

      $ nslookup dongheekang.com                       : find the server IP address first!
        Server:		192.168.0.1
        Address:	192.168.0.1#53
        Non-authoritative answer:
        Name:	dongheekang.com
        Address: 172.64.104.34
        .......  some more output

      $ openssl s_client -showcerts -connect 172.64.104.34:443                               : this will not work
      $ openssl s_client -servername dongheekang.com -showcerts -connect 172.64.104.34:443   : need option --servername

### SSL/TLS authentication
    ==============================================================================================
    SSL authentication
    ==============================================================================================
    The following instructions assume that you are the administrator of swshare repository.
    (https://dba.wdf.sap.corp/swshare/)

    • SSL authentication (administrator level)

      - Create a certificate for the landscape's domain
        $ openssl req -nodes -x509 -sha256 -newkey rsa:4096 \
          -keyout "SAPGlobalSSLSign.crt" \
          -out "SAPGlobalSSLSign.key" \
          -days 365 \
          -subj "/C=DE/ST=SAP SE/L=Walldorf/O=bssbd/OU=dbcat/CN=dbcat's Sign Key"

      - You should now have 2 certificates, keep key file in safe region. And copy the public key file to the swshare
        "SAPGlobalSSLSign.crt" private certificate key move to /root/SAPGlobalSSLSign.crt
        "SAPGlobalSSLSign.key" public certificate key move to /swshare/dbcat/v3.8/certs/SAPGlobalSSLSign.key

      - Generate self-signed certificate files, here is an example for signing of checksum list of ASE
        $ export signfile=/swshare/ase/16.0.02.06/linux_x86_64
        $ openssl dgst -sha256 -sign "/root/SAPGlobalSSLSign.crt" -out "${signfile}"/.checksum5.md5.sig "${signfile}"/.checksum.md5

      - Verification of signed file will be performed in the factory
        modFactoryTransferValidateSignature( ) in factoryTransfer.sh

### SSL : simplified version
    ==============================================================================================
    SSL procedure
    ==============================================================================================
    Need super user privileges
      $ sudo su -

      $ cd /var/tmp/dbcatTrans/zfinal/

      $ openssl req -nodes -x509 -sha256 -newkey rsa:4096 -keyout "SAPGlobalSSLCA.key" -out "SAPGlobalSSLCA.crt" \
        -days 365 -subj "/C=DE/ST=SAP SE/L=Walldorf/O=bssbd/OU=dbcat/CN=dbcat's Sign Key"

      $ openssl dgst -sha256 -sign SAPGlobalSSLSign.crt -out .checksum.md5.sig .checksum.md5

      $ openssl dgst -sha256 -verify <(openssl x509 -in /var/tmp/dbcatTrans/zfinal/SAPGlobalSSLSing.key -pubkey -noout) \
        -signature .checksum.md5.sig .checksum.md5.sig

    https://www.digicert.com/kb/ssl-support/openssl-quick-reference-guide.htm


### SSL/TLS encryption
    ===================================================================================================
    SSL/TLS standard: Secure Sockets Layer and Transport Layer Security with ECC, RSA or DSA encryption
    ===================================================================================================
    • Self signed vs Let’s Encrypt vs StartSSL.com(not available)
      - You can use CA tools in OpenSSL, run /etc/pki/tls/misc/CA 
        $ cd /etc/pki/tls/misc
        $ CA -newca   : create your private key, cakey.pem (private key)
        $ CA -newreq  : a signing request, newreq.pem(request CSR) & newkey.pem(new private key)
        $ CA -signreq : sign the request, newcert.pem (CA signed certificate)

        you will find the list of generated files under
        $ cd /etc/pki/CA/private/

      - Let’s Encrypt
        $ sudo apt-get install letsencrypt
        $ sudo letsencrypt certonly -a webroot --webroot-path=/var/www/html -d example.com -d www.example.com

        to check server certificate for example.com     
        $ sudo ls /etc/letsencrypt/live/example.com
        cert.pem            : publick key, server certificate only.
        chain.pem           : root and intermediate certificates only.
        fullchain.pem       : full trust chain
        privkey.pem         : private key

      - StartSSL.com (deprecated since 2018) via webbrowser
        ca.pem              : StartSSL's Root certificate
        private.key         : The unencrypted version of your private key (be very careful)
        server.ca.pem       ; The intermediate certificate for StartSSL
        ssl.key             ; The encrypted private key (does not need to be copied to server)
        ssl.crt             ; Your new certificate

    • Converting certificates
      - To convert a certificate or certificate chain from DER to PEM
        $ openssl x509 -inform DER -in Certificate.der -outform PEM -out Certificate.pem

      - To convert a private key from DER to PEM
        $ openssl rsa -inform DER -in PrivateKey.der -outform PEM -out PrivateKey.pem

      - To decrypt an encrypted private key (remove the password or passphrase)
        $ openssl rsa -in EncryptedPrivateKey.pem -out PrivateKey.pem

      - To convert a certificate bundle from PKCS#12 (PFX) to PEM
        $ openssl pkcs12 -in CertificateBundle.p12 -out CertificateBundle.pem -nodes

      - To convert a certificate bundle from PKCS#7 to PEM
        $ openssl pkcs7 -in CertificateBundle.p7b -print_certs -out CertificateBundle.pem

    • How to create and verify SSL
      - In server, generate ssl key
        $ openssl req -nodes -x509 -sha256 -newkey rsa:4096 -keyout "general_key.key" \
          -out "general_key.pub" -days 365 -subj "/C=DE/ST=SAP SE/L=Walldorf/O=bssdb/OU=dbcat/CN=general_key"

      - Sign the file
        $ openssl dgst -sha256 -sign "general_key.key" -out .checksum.sha256 .checksum.md5

      - In local machine verify the signature
        $ openssl dgst -sha256 -verify <(openssl x509 -in "general_key.pub"  -pubkey -noout) \
          -signature dbaenv.sha256 .checksum.md5

      - then will verify
        $ openssl dgst -sha256 -verify <(openssl x509 -in "/home/c5258293/git/dbcat/certs/SAPGlobalSSLCA.crt"  \
          -pubkey -noout) -signature /var/tmp/dbcatTrans/dbaenv-fetch-1.7/.checksum.sha256 /var/tmp/dbcatTrans/dbaenv-fetch-1.7/.checksum.md5




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

* *using init.d

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



<br/><a name="Utility"></a>

# Utility




<br/><a name="Filesystens"></a>

# File systens

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


<br/><a name="Docker"></a>

# Docker

### Setup two dockers for network testing

   ?


### Connecting from Docker Containers to Resources in the Host
our goal is to make the host and the containers (DB & API) share the same networking!

* Let's check 

      $ ifconfig                  : network interfaces list for a host with Docker installed

      docker0   Link encap:Ethernet  HWaddr 02:42:A7:6A:EC:A9  
                inet addr:172.17.0.1  Bcast:172.17.255.255  Mask:255.255.0.0
      ...
      eth0      Link encap:Ethernet  HWaddr 00:15:5D:40:01:0C
      ...  
      lo        Link encap:Local Loopback
      ...  
     
* DB will be connected with docker in the same network, DB configuration will have bind-address

      bind-address = 172.17.0.1
      $ mariadb -h 172.17.0.1

* docker setup

      By default, Docker will create a bridge network. This default network doesn’t allow the containers to connect to the host. So, we’ll need to use '--network host'. Now, the localhost address (127.0.0.1) will be referencing the localhost interface of the host, instead of the one of the container. Therefore, we can access our MariaDB – from the container – just by connecting to localhost:

      $ docker run --rm -it --network host alpine sh                          : 
      $ mariadb -h 127.0.0.1

https://www.dongheekang.com/linux/nginx-docker-container

<div><br/>
&raquo; Back to <a href="#contents">Contents</a> | <a href="../docs/README.md">Docs</a>
</div>
