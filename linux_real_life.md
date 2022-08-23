# System Administration


### Contents
  * [Users](#user)
  * [Administration](#Administration)
  * [High Availability](#HA)
  * [Service](#Service)
  * [Storage](#Storage)


<br/><a name="Users"></a>

### Users

* Running Script or Command as Another User in Linux
    - visudo will touch /etc/sudoers and show 
          
          root  ALL=(ALL:ALL) ALL
          user hostname=(runas-user:runas-group) command

          usermod -aG sudo hope

    - su 

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

    - sudo 
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

          nive tge existing content to the new location 
          $ sudo usermod -m -d /usr/baeldung baeldung




* List All Groups in Linux
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

        or
        $ getent group

        only user print out! 
        $ cut -d: -f1 /etc/group
        $ getent group | cut -d: -f1

        kent$ groups
        lp wheel dbus network video audio optical storage input users vboxusers docker kent
        kent$ groups root
        root bin daemon sys adm disk wheel log


        id -Gn
        id -Gn root

* Fixing the “Command Not Found” Error When Using Sudo 
    - “Permission Denied” When Running Script
          chmod +x ./myscript 

    - Fixing the Error for a Single Command

      We can pass the -E flag to sudo to make it preserve the current environment variables:

          sudo -E myscript

#### Security
#### Networking

#### Service

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
          
    - run by crontab  

          $ crontab -e
          30 10 * * 1-5 /usr/bin/systemctl restart my-service.service


#### Processes and Monitoring




#### Utility

#### File systens

#### Shell 

#### Files & Directory 




#### log

    - journal 
  
        $ journalctl --list­-boots                   : To get a list of boots
        $ journalctl -b                             : to get the all the logs for the current boot,
        $ journalctl -b -1                          : to get the previous boots
        $ journalctl --sinc­e="2­021­-01-30 18:17:16"  : specific time using --since and --until
        $ journalctl --since "20 min ago"   
        $ journalctl -u systemd-*
        $ journalctl --user-unit my-application
        $ jour­nalctl _UID=100
        $ journalctl -p err..alert
        $ journalctl -u apache -n 10
        $ journalctl -f -u nginx
        $ journalctl --no-pager
        $ journalctl --vacu­um-­tim­e=2­weeks
        $ journalctl -b -u docker -o json

<div><br/>
&raquo; Back to <a href="#contents">Contents</a> | <a href="../docs/README.md">Docs</a>
</div>
