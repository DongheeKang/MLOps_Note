# System Administration


## Contents
  * [Linux System](#System)
  * [Administration](#Administration)
  * [High Availability](#HA)
  * [Service](#Service)
  * [Storage](#Storage)


<br/><a name="Administration"></a>
## Administration

#### Users
#### Files

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


* How to load environment variables in a cron job and * How to Run a Script at a Certain Time on Linux? 

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


#### Monitoring




#### Utility


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
