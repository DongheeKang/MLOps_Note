# System Architecture


### Contents
  * [Architecture](#Architecture)


<br/><a name="Architecture"></a>

# Architecture

	===============================================================================================
	Scenario 
	===============================================================================================
Case study for using Load Balancing and Web Server 

- Senario 1. 
	LAMP (Apache,PHP,MySQL) Stack

- Senario 2. 
	LEMP (Nginx,PHP,MySQL) Stack

- Senario 3. 
	Nginx as HTTP Proxying, Load Balancing, Buffering, and Caching

- Senario 4. 
	Nginx as a Load Balancer + SSL and Web server with Apache

- Senario 5. 
	HAProxy 
	Internet - (keepalived, HAproxy1,2) - (webapp1,2 nginx) - DB

- Senario 6. 
	HAProxy 
	Internet - (keepalived, HAproxy1,2) - (webapp1,2 apache) - DB

- Senario 7. 
	HAProxy As A Layer 4 Load Balancer for WordPress Servers
	Internet - HAproxy LB - (wordpress1,2) - mysql  

- Senario 8. 
	HAProxy As A Layer 7 Load Balancer for WordPress Servers
	Internet - HAproxy LB - (web, (wordpress1,2)) - DB  

- Senario 9. 
	Implement SSL Termination With HAProxy on Ubuntu 14.04 

- Senario 10. 
	Create a SSL Certificate on Apache for Ubuntu 14.04  


	===============================================================================================
	Senario 1. LAMP
	===============================================================================================
1. Install the Apache2 Web Server
	sudo apt-get update
	sudo apt-get install apache2
	sudo service apache2 start

	ip addr show eth0 | grep inet | awk '{ print $2; }' | sed 's/\/.*$//'
	curl http://icanhazip.com
	http://server_domain_name_or_IP

2. Install MySQL to Manage Site Data
	sudo apt-get install mysql-server php5-mysql
	sudo mysql_install_db
	sudo mysql_secure_installation

3. Install PHP for Processing
	$ sudo apt-get install php5 libapache2-mod-php5 php5-mcrypt
	$ sudo nano /etc/apache2/mods-enabled/dir.conf
	| <IfModule mod_dir.c>
    |	DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
	|</IfModule>
	|<IfModule mod_dir.c>
    |	DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
	|</IfModule>
	$ sudo service apache2 restart

4. Install PHP Modules
	$ apt-cache search php5-
	$ apt-cache show php5-cli
	$ sudo apt-get install php5-cli

5. Test PHP Processing on your Web Server
	$ sudo nano /var/www/html/info.php
	| <?php
	| phpinfo();
	| ?>
	http://your_server_IP_address/info.php
	$ sudo rm /var/www/html/info.php


	===============================================================================================
	Senario 2. LEMP 
	===============================================================================================
1. Install the Nginx Web Server
	$ sudo apt-get update
	$ sudo apt-get install nginx
	$ sudo service nginx start

	$ ip addr show eth0 | grep inet | awk '{ print $2; }' | sed 's/\/.*$//'
	$ curl http://icanhazip.com
	http://server_domain_name_or_IP

2. Install MySQL to Manage Site Data
	$ sudo apt-get install mysql-server
	$ sudo mysql_install_db
	$ uudo mysql_secure_installation

3. Install PHP for Processing
	$ sudo apt-get install php5-fpm php5-mysql
	$ sudo nano /etc/php5/fpm/php.ini
	| cgi.fix_pathinfo=0
	| default_charset = "utf-8"

	$ sudo vi /etc/php5/fpm/pool.d/www.conf
	| pm = dynamic
	| pm.max_children = 4
	| pm.start_servers = 1
	| pm.min_spare_servers = 1
	| pm.max_spare_servers = 2
	| pm.max_requests = 500
	| listen = 127.0.0.1:9000

	$ sudo service php5-fpm restart

4. Configure Nginx to Use PHP Processor
	sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/default.backup
	sudo vi /etc/nginx/sites-available/default
	|server {
    |	listen 80 default_server;
    |	listen [::]:80 default_server ipv6only=on;
	|
    |	root /usr/share/nginx/html;
    |	index index.html index.htm;
	|
    |	server_name localhost;
	|
    |	location / {
    |	   try_files $uri $uri/ =404;
    |	}
	|}
    change you need ... 
	|server {
    |   ...
    |	index index.php index.html index.htm;      <--- index.php
	| 	server_name server_domain_name_or_IP;
	|   ...
   	|   error_page 404 /404.html;
    |   error_page 500 502 503 504 /50x.html;
    |   location = /50x.html {
    |       root /usr/share/nginx/html;
    |   }
    |   location ~ \.php$ {
    |     try_files $uri =404;
    |     fastcgi_split_path_info ^(.+\.php)(/.+)$;
    |     fastcgi_pass unix:/var/run/php5-fpm.sock;
    |     fastcgi_index index.php;
    |     fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    |     include fastcgi_params;
    |   }
    |}
	$ sudo service nginx restart

5. Create a PHP File to Test Configuration
	$ sudo nano /usr/share/nginx/html/info.php
	| <?php
	| phpinfo();
	| ?>
	http://your_server_IP_address/info.php
	$ sudo rm /var/www/html/info.php


	===============================================================================================
	Senario 3. Nginx (advanced)
	===============================================================================================
- Understanding Nginx HTTP Proxying, Load Balancing, Buffering, and Caching
- Install
	$ sudo apt-get update
	$ sudo apt-get install -y nginx

- Configuration	
	$ sudo touch /etc/nginx/sites-available/example.com
	$ sudo ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled/example.com
	$ sudo mkdir /var/log/proxy_server

	$ sudo vi /etc/nginx/sites-available/example.com
	|server{
	|  listen AA;
	|  access_log /var/log/proxy_server/access.log;
	|  error_log /var/log/proxy_server/access.log;
	|
	|  location / {
	|    proxy_pass_header Server;
	|    proxy_set_header X-Real-IP $remote_addr;
	|    proxy_set_header X-Scheme $scheme;
	|    proxy_pass http://example.com:BBBB;
	|  }
	|}

	$ sudo mv /etc/nginx/sites-available/default /etc/nginx/sites-available/default.backup
	$ sudo /etc/init.d/nginx reload
	
- Configuration (advanced)	
	$ sudo vi /etc/nginx/sites-available/example.com
	|server{
    |  proxy_set_header HOST $host;
    |  proxy_set_header X-Forwarded-Proto $scheme;
    |  proxy_set_header X-Real-IP $remote_addr;
    |  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
	|
	|  location /match/here {
    |	 proxy_pass http://example.com/new/prefix;
	|  }
	|
	|  location /different/match {
    |	 proxy_pass http://example.com;
	|  }
	|}

	Host header 
	$proxy_host     :  domain name or IP address and port combo
	$http_host      :  http request
	$host           :  preference to the host name

- TCP vs Unix Sockets
	Unix sockets are typically a little faster, so change if desired.
	$ sudo vi /etc/php5/fpm/pool.d/www.conf
	| listen = /var/run/php5-fpm.sock
	$ sudo vi /etc/nginx/sites-available/example.com
	| fastcgi_pass unix:/var/run/php5-fpm.sock;
	$ sudo service nginx restart
	$ sudo service php5-fpm start

- Upstream Context for Load Balancing Proxied Connections
	Nginx allows to scale configuration out by specifying entire pools of backend servers

	$ sudo vi /etc/nginx/sites-available/example.com  
	|upstream backend_hosts {    
	|   least_conn;                                  : round robin(default), hash, ip_hash
    |#  hash $remote_addr$remote_port consistent;    : for hash case need to provide key
	|
    |	server host1.example.com weight=3;           : Weighting, receive 3 times traffic
    |	server host2.example.com;
    |	server host3.example.com;
	|}
	|
	|server {
    |	listen 80;
    |	server_name example.com;
	|
    |	location /proxy-me {
    |    	proxy_pass http://backend_hosts;
    |	}
	|}

- Using Buffers to Free Up Backend Servers
	Client --- Nginx proxy --- Backend  
	$ sudo vi /etc/nginx/sites-available/example.com
	|Server{
	|  proxy_buffering on;                              : on/off
	|  proxy_buffer_size 1k;                            : 1k/4k/xk
	|  proxy_buffers 24 4k;
	|  proxy_busy_buffers_size 8k;
	|  proxy_max_temp_file_size 2048m;
	|  proxy_temp_file_write_size 32k;
	|
	|  location / {
	|    proxy_pass http://example.com;
	|  }
	|}

- Proxy Caching to Decrease Response Times
	$ sudo vi /etc/nginx/sites-available/example.com 
	|Server{
	|  proxy_cache_path /var/lib/nginx/cache levels=1:2 keys_zone=backcache:8m max_size=50m;
	|  proxy_cache_key "$scheme$request_method$host$request_uri$is_args$args";
	|  proxy_cache_valid 200 302 10m;
	|  proxy_cache_valid 404 1m;
	|  
	|  location /proxy-me {
    |    proxy_cache backcache;
   	| 	 proxy_cache_bypass $http_cache_control;
    |	 add_header X-Proxy-Cache $upstream_cache_status;
	|
    |	 proxy_pass http://backend;
	|  }
	|}

	sudo mkdir -p /var/lib/nginx/cache
	sudo chown www-data /var/lib/nginx/cache
	sudo chmod 700 /var/lib/nginx/cache

	Parameters
	level      		 	= how the cache will be organized
	Key_zone			= the name for this cache zone
	proxy_cache_key		= set the key that will be used to store cached values
	proxy_cache_valid	= configure how long to store values depending on the status code

- If your backend also uses Nginx, then 
	you can set some of this using expires directive, which set the max-age for Cache-Control

	|location / {
    |	expires 60m;                           : cache for 1 hour 
	|}

	|location /check-me {
    |	expires -1;                            : means "no-cache"
	|}

	|location /private {
    |	expires -1;
    |	add_header Cache-Control "no-store";
	|}

	===============================================================================================
	Senario 4. Nginx as a Load Balancer + SSL 
	===============================================================================================
- How To Set Up Nginx Load Balancing with SSL Termination

- On Apache Web server 1 & 2
	apt-get install apache2
	apt-get install php5 libapache2-mod-php5 php5-mcrypt

- Nginx on LB
    $ sudo apt-get update
    $ sudo apt-get install nginx

	$ sudo kdir -p /etc/nginx/ssl/example.com
	$ cd /etc/nginx/ssl/example.com

	$ openssl genrsa -des3 -out server.key 2048
	$ openssl rsa -in server.key -out server.key
	$ openssl req -new -key server.key -out server.csr
	$ openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt

    server.key    - The private key
    ca-certs.pem  - A collection of your CA's root and intermediate certificates. 
    server.crt    - The SSL certificate for your domain name

	$ sudo ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled/example.com
	$ sudo vi /etc/nginx/sites-available/example.com
	|upstream mywebapp1 {
    |	server 10.130.227.11;
    |	server 10.130.227.22;
	|}
	|server {
    |	listen 80;    
	|   listen 443 ssl;
    |	server_name example.com www.example.com;
	|
	|   ssl on;
    |   ssl_certificate         /etc/nginx/ssl/example.com/server.crt;
    |   ssl_certificate_key     /etc/nginx/ssl/example.com/server.key;
    |   ssl_trusted_certificate /etc/nginx/ssl/example.com/ca-certs.pem;
	|
	|   # here is the additional stuff for hardening 
	|   ssl_session_cache shared:SSL:20m;
	|   ssl_session_timeout 10m;
	|   ssl_prefer_server_ciphers       on;
	|   ssl_protocols                   TLSv1 TLSv1.1 TLSv1.2;
	|   ssl_ciphers                     ECDH+AESGCM:DH+AESGCM:ECDH+AES256:DH+AES256:\
	|									ECDH+AES128:DH+AES:ECDH+3DES:DH+3DES:\
	|									RSA+AESGCM:RSA+AES:RSA+3DES:!aNULL:!MD5:!DSS;
	|	add_header Strict-Transport-Security "max-age=31536000";
	|
    |	location / {
    |    	proxy_pass http://mywebapp1;
    |    	proxy_set_header Host $host;
    |    	proxy_set_header X-Real-IP $remote_addr;
    |    	proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    |    	proxy_set_header X-Forwarded-Proto $scheme;
    |	}
	|}
	$ sudo service nginx configtest
	$ service nginx reload


- Securing The Backend Servers (Apache Servers)
	$ sudo vi /etc/apache2/ports.conf 
	| # Listen 80
	| Listen 10.130.227.22:8080                  : private IP 
	$ sudo service apache2 restart

	$ iptables -I INPUT -m state --state NEW -p tcp --dport 80 ! -s 10.130.227.33 -j DROP

	$ sudo vi /var/www/html/test.php
	|<?php
    |	header( 'Content-Type: text/plain' );
    |	echo 'Host: ' . $_SERVER['HTTP_HOST'] . "\n";
    |	echo 'Remote Address: ' . $_SERVER['REMOTE_ADDR'] . "\n";
    |	echo 'X-Forwarded-For: ' . $_SERVER['HTTP_X_FORWARDED_FOR'] . "\n";
    |	echo 'X-Forwarded-Proto: ' . $_SERVER['HTTP_X_FORWARDED_PROTO'] . "\n";
    |	echo 'Server Address: ' . $_SERVER['SERVER_ADDR'] . "\n";
    |	echo 'Server Port: ' . $_SERVER['SERVER_PORT'] . "\n\n";
	|?>
	$ curl https://example.com/test.php https://example.com/test.php 
curl 169.254.169.254/metadata/v1/interfaces/public/0/anchor_ipv4/address && echo

	===============================================================================================
	Senario 5. Internet - (keepalived, HAproxy, LB1,2) - (web1,2, nginx) - DB
	===============================================================================================
- How To Set Up Highly Available HAProxy Servers with Keepalived 

- Preliminary Note
	Finding IP addresses
	$ curl 169.254.169.254/metadata/v1/interfaces/private/0/ipv4/address && echo
	$ curl 169.254.169.254/metadata/v1/interfaces/public/0/anchor_ipv4/address && echo

	Private IP    : Droplet's private IP address
	Anchor IP     : the local private IP, eth0 address
	Floating IP 
	Public IP 

- Installation nginx on web1,2
    $ sudo apt-get update
    $ sudo apt-get install nginx

	$ sudo nano /etc/nginx/sites-available/default
	|server {
    |	listen web_server_private_IP:80;
	|
    |	allow load_balancer_1_private_IP;
    |	allow load_balancer_2_private_IP;
    |	deny all;
    |	. . .
	|}

	$ sudo nginx -t
	$ sudo service nginx restart
	$ curl 127.0.0.1

- Installation and configure HAProxy on both Load Balancers
	$ curl web_server_public_IP             <--- fail
	$ curl web_server_private_IP            <--- output html

    $ sudo apt-get update
    $ sudo apt-get install haproxy

	$ sudo nano /etc/default/haproxy
	|# Set ENABLED to 1 if you want the init script to start haproxy.
	|ENABLED=1
	|# Add extra flags here.
	|#EXTRAOPTS="-de -m 16"s

	$ sudo nano /etc/haproxy/haproxy.cfg
	|defaults
    |	log     global
    |	mode    tcp
    |	option  tcplog
	|...
	|frontend www
    |	bind load_balancer_anchor_IP:80
    |	default_backend nginx_pool
	|
	|backend nginx_pool
    |	balance roundrobin
    |	mode tcp
    |	server web1 web_server_1_private_IP:80 check
    |	server web2 web_server_2_private_IP:80 check

	$ sudo haproxy -f /etc/haproxy/haproxy.cfg -c
	$ sudo service haproxy restart

    $ curl 127.0.0.1                        <---fail
    $ curl load_balancer_public_IP			<---fail
    $ curl load_balancer_private_IP			<---fail

- Keepalived on both Load Balancers
	$ sudo apt-get install build-essential libssl-dev

	$ wget http://www.keepalived.org/software/keepalived-1.2.19.tar.gz
    $ tar xzvf keepalived*
    $ cd keepalived*
    $ ./configure
    $ make
    $ sudo make install

	$ sudo nano /etc/init/keepalived.conf
	|description "load-balancing and high-availability service"
	|
	|start on runlevel [2345]
	|stop on runlevel [!2345]
	|
	|respawn
	|
	|exec /usr/local/sbin/keepalived --dont-fork

	$ sudo mkdir -p /etc/keepalived

- Primary Load Balancer (LB 1)
	$ sudo nano /etc/keepalived/keepalived.conf
	|vrrp_script chk_haproxy {
    |	script "pidof haproxy"
    |	interval 2
	|}
	|vrrp_instance VI_1 {
    |	interface eth1
    |	state MASTER
    |	priority 200
    |	virtual_router_id 33
    |	unicast_src_ip primary_private_IP
    |	unicast_peer {
    |	    secondary_private_IP
    |	}
    |	authentication {
    |	    auth_type PASS
    |	    auth_pass password
    |	}    
	|   track_script {
	|       chk_haproxy
    |	}
    |	notify_master /etc/keepalived/master.sh
	|}

- Secondary Load Balancer (LB 2)
	$ sudo nano /etc/keepalived/keepalived.conf
	|vrrp_script chk_haproxy {
    |	script "pidof haproxy"
    |	interval 2
	|}
	|vrrp_instance VI_1 {
    |	interface eth1
    |	state BACKUP                            <----- 
    |	priority 100							<-----  
    |	virtual_router_id 33
    |	unicast_src_ip secondary_private_IP     <----- 
    |	unicast_peer {
    |	    primary_private_IP                  <-----
    |	}
    |	authentication {
    |	    auth_type PASS
    |	    auth_pass password
    |	}    
	|   track_script {
	|       chk_haproxy
    |	}
    |	notify_master /etc/keepalived/master.sh
	|}

- Configure a Floating IP for your Infrastructure
	Only DigitalOcean specific, 
	create a DigitalOcean API Token and the Floating IP Transition Scripts

    $ cd /usr/local/bin
    $ sudo curl -LO http://do.co/assign-ip
	$ python /usr/local/bin/assign-ip floating_ip droplet_ID

	$ sudo nano /etc/keepalived/master.sh
	|export DO_TOKEN='digitalocean_api_token'
	|IP='floating_ip_addr'
	|ID=$(curl -s http://169.254.169.254/metadata/v1/id)
	|HAS_FLOATING_IP=$(curl -s http://169.254.169.254/metadata/v1/floating_ip/ipv4/active)
	|
	|if [ $HAS_FLOATING_IP = "false" ]; then
    |	n=0
    |	while [ $n -lt 10 ]
    |	do
    |    	python /usr/local/bin/assign-ip $IP $ID && break
    |    	n=$((n+1))
    |    	sleep 3
    |	done
	|fi
	$ sudo chmod +x /etc/keepalived/master.sh

- Start Up the Keepalived Service and Test Failover
	$ sudo start keepalived             : on both LB
	$ sudo service haproxy stop 		: @ primary, down haproxy server 
	http://floating_IP_addr				: but still do service, because secondary take over! 
	$ sudo service haproxy start		: @ primary, start haproxy at priamary again 

- Tail the Logs on the Web Servers
	$ sudo tail -f /var/log/nginx/access.log | awk '{print $1;}'

- Automate Requests to the Floating IP at local machine
	$ while true; do curl -s -o /dev/null floating_IP; sleep 2; done

- Configure Nginx to Log Actual Client IP Address on both web servers
	$ sudo nano /etc/nginx/nginx.conf
	| log_format haproxy_log 'ProxyIP: $remote_addr - ClientIP: $http_x_forwarded_for - 
		$remote_user [$time_local] ' '"$request" $status $body_bytes_sent "$http_referer" 
								   ' '"$http_user_agent"';
	$ sudo nano /etc/nginx/sites-available/default
	| access_log /var/log/nginx/access.log haproxy_log;
	$ sudo service nginx restart


	===============================================================================================
	Senario 6. Internet - (keepalived, HAproxy, LB1,2) - (web1,2, apache) - DB
	===============================================================================================
- Same as Senario 5 but web server are used with Apache 

- Preliminary Note
    a virtual IP address that floats between lb1 and lb2: 192.168.0.99

    Load Balancer 1: lb1.example.com, IP address: 192.168.0.100
    Load Balancer 2: lb2.example.com, IP address: 192.168.0.101
    Web Server 1: http1.example.com, IP address: 192.168.0.102
    Web Server 2: http2.example.com, IP address: 192.168.0.103

	The shared (virtual) IP address is no problem as long as you're in your own LAN where you 
	can assign IP addresses as you like. However, if you want to use this setup with public IP 
	addresses, you need to find a hoster where you can rent two servers (the load balancer 
	nodes) in the same subnet; you can then use a free IP address in this subnet for the 
	virtual IP address.

- Preparing The Backend Web Servers
	$ vi /etc/apache2/apache2.conf
	|#LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\"" combined
	|LogFormat "%{X-Forwarded-For}i %l %u %t \"%r\" %>s %b \"%{Referer}i\" 
														   \"%{User-Agent}i\"" combined
	$ vi /etc/apache2/sites-available/default
	|SetEnvIf Request_URI "^/check\.txt$" dontlog
	|CustomLog /var/log/apache2/access.log combined env=!dontlog

	$ /etc/init.d/apache2 restart
	$ touch /var/www/check.txt

- Installing HAProxy and Configuring The Load Balancers
	$ aptitude install haproxy

	$ cp /etc/haproxy/haproxy.cfg /etc/haproxy/haproxy.cfg_orig
	$ cat /dev/null > /etc/haproxy/haproxy.cfg
	$ vi /etc/haproxy/haproxy.cfg
	|global
    |    log 127.0.0.1   local0
    |    log 127.0.0.1   local1 notice
    |    #log loghost    local0 info
    |    maxconn 4096
    |    #debug
    |    #quiet
    |    user haproxy
    |    group haproxy
	|defaults
    |    log     global
    |    mode    http
    |    option  httplog
    |    option  dontlognull
    |    retries 3
    |    redispatch
    |    maxconn 2000
    |    contimeout      5000
    |    clitimeout      50000
    |    srvtimeout      50000
	|listen webfarm 192.168.0.99:80
    |   mode http
    |   stats enable
    |   stats auth someuser:somepassword
    |   balance roundrobin
    |   cookie JSESSIONID prefix
    |   option httpclose
    |   option forwardfor
    |   option httpchk HEAD /check.txt HTTP/1.0
    |   server webA 192.168.0.102:80 cookie A check
    |   server webB 192.168.0.103:80 cookie B check

	$ vi /etc/default/haproxy
	|# Set ENABLED to 1 if you want the init script to start haproxy.
	|ENABLED=1
	|# Add extra flags here.
	|#EXTRAOPTS="-de -m 16"

- Setting Up keepalived
	$ aptitude install keepalived
	$ vi /etc/sysctl.conf
	$ net.ipv4.ip_nonlocal_bind=1
	$ sysctl -p

	On LB 1
	$ vi /etc/keepalived/keepalived.conf
	|vrrp_script chk_haproxy {           # Requires keepalived-1.1.13
    |    script "killall -0 haproxy"     # cheaper than pidof
    |    interval 2                      # check every 2 seconds
    |    weight 2                        # add 2 points of prio if OK
	|}
	|
	|vrrp_instance VI_1 {
    |    interface eth0
    |    state MASTER
    |    virtual_router_id 51
    |    priority 101                    # 101 on master, 100 on backup
    |    virtual_ipaddress {
    |        192.168.0.99
    |    }
    |    track_script {
    |        chk_haproxy
    |    }
	|}
	$ /etc/init.d/keepalived start
	$ ip addr sh eth0

	On LB 2
	$ vi /etc/keepalived/keepalived.conf
	|vrrp_script chk_haproxy {           # Requires keepalived-1.1.13
    |    script "killall -0 haproxy"     # cheaper than pidof
    |    interval 2                      # check every 2 seconds
    |    weight 2                        # add 2 points of prio if OK
	|}
	|
	|vrrp_instance VI_1 {
    |    interface eth0
    |    state MASTER
    |    virtual_router_id 51
    |    priority 100                    # 101 on master, 100 on backup
    |    virtual_ipaddress {
    |        192.168.0.99
    |    }
    |    track_script {
    |        chk_haproxy
    |    }
	|}
	$ /etc/init.d/keepalived start
	$ ip addr sh eth0

- Starting HAProxy on both LB1 and LB2
	$ /etc/init.d/haproxy start

- HAProxy Statistics
	http://192.168.0.99/haproxy?stats

	===============================================================================================
	Senario 7. Internet - HAproxy LB - (wordpress1,2) - mysql
	===============================================================================================
- How To Use HAProxy As A Layer 4 Load Balancer for WordPress
    haproxy-www : HAProxy server, for load balancing
    wordpress-1 : WordPress web application server
    wordpress-2 : second WordPress web application server
    mysql-1:    : MySQL server

- Installation of MySQL-server
	$ sudo apt-get update
	$ sudo apt-get install mysql-server
	$ sudo mysql_install_db
	$ sudo mysql_secure_installation
	$ sudo nano /etc/mysql/my.cnf                     
	| [mysqld]   									: Configure MySQL to Allow Remote Access
	| bind-address        = your_database_IP        : private IP
	$ sudo service mysql restart

    $ mysql -u root -p
    > CREATE DATABASE wordpress;
	> CREATE USER 'wordpressuser'@'web_server_IP' IDENTIFIED BY 'password';
    > GRANT SELECT,DELETE,INSERT,UPDATE ON wordpress.* TO 'wordpressuser'@'web_server_ip';
	> FLUSH PRIVILEGES;
	> exit
	$ mysql -u wordpressuser -p

- Installation of MySQL-client, Nginx, PHP on WordPress 1 and 2 
	$ sudo apt-get update
	$ sudo apt-get install mysql-client
	$ sudo apt-get install nginx php5-fpm php5-mysql

    /etc/php5/fpm/php.ini
    /etc/php5/fpm/pool.d/www.conf
    /etc/nginx/sites-available/example.com
    /etc/nginx/sites-enabled/example.com

	Configure PHP
	$ sudo nano /etc/php5/fpm/php.ini
	| ;cgi.fix_pathinfo=1                          : commented out with the ";" 
	| cgi.fix_pathinfo=0                           : set to 0
	$ sudo nano /etc/php5/fpm/pool.d/www.conf
	| # listen = 127.0.0.1:9000                    : commented out
	| listen = /var/run/php5-fpm.sock              : use socket
	$ sudo service php5-fpm restart

    Configure Nginx  
	$ sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/example.com
	$ sudo nano /etc/nginx/sites-available/example.com
	|server {
    |	listen 80;
    |	root /var/www/example.com;
    |	index index.php index.hmtl index.htm;
    |	server_name example.com;
    |	location / {
    |    	try_files $uri $uri/ /index.php?q=$uri&$args;
    |	}
    |	error_page 404 /404.html;
    |	error_page 500 502 503 504 /50x.html;
    |	location = /50x.html {
    |    	root /usr/share/nginx/www;
   	| 	}
    |	location ~ \.php$ {
    |    	try_files $uri =404;
    |    	fastcgi_pass unix:/var/run/php5-fpm.sock;
    |    	fastcgi_index index.php;
    |    	include fastcgi_params;
    |	}
	|}
	$ sudo rm /etc/nginx/sites-enabled/default
	$ sudo ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled/
	$ sudo service nginx restart

    $ sudo vi /etc/hosts
	| 10.0.0.2  wordpress-1
	| 10.0.0.3  wordpress-2

- Install wordpress on wordpress-1 and wordpress-2
	$ wget http://wordpress.org/latest.tar.gz
	$ tar xzvf latest.tar.gz

	$ cp ~/wordpress/wp-config-sample.php ~/wordpress/wp-config.php
	$ vi ~/wordpress/wp-config.php
	| define('DB_NAME', 'wordpress');          /** The name of the database for WordPress */
	| define('DB_USER', 'wordpressuser');      /** MySQL database username */
	| define('DB_PASSWORD', 'password');       /** MySQL database password */
	| define('DB_HOST', 'database_server_ip'); /** MySQL hostname */

	$ sudo mkdir -p /var/www/example.com
	$ sudo cp -r ~/wordpress/* /var/www/example.com
	$ cd /var/www/example.com
	$ sudo chown -R www-data:www-data *
	$ sudo usermod -a -G www-data your_user
	$ sudo chmod -R g+rw /var/www/example.com

	Set Up the Site through the Web Interface
	http://example.com

- Install GlusterFS and Configure a Replicated Volume
	@ both w1 and w2
	$ sudo apt-get install glusterfs-server
	$ sudo mkdir /gluster-storage

	@ wordpress-1
	$ sudo gluster peer probe wordpress-2
	$ sudo gluster volume create volume1 replica 2 transport tcp \
					 wordpress-1:/gluster-storage wordpress-2:/gluster-storage force
	$ sudo gluster volume start volume1
	$ sudo gluster volume info
	$ sudo vi /etc/fstab
	| wordpress-1:/volume1   /storage-pool   glusterfs defaults,_netdev 0 0
	$ sudo mkdir /storage-pool
	$ sudo mount /storage-pool

	@ wordpress-2
	$ sudo gluster peer probe wordpress-1
	$ sudo vi /etc/fstab
	| wordpress-2:/volume1   /storage-pool   glusterfs defaults,_netdev 0 0
	$ sudo mkdir /storage-pool
	$ sudo mount /storage-pool
	
- Move WordPress Files to Shared Storage
    @ wordpress-1
	$ sudo mv /var/www/example.com /storage-pool/
	$ sudo chown www-data:www-data /storage-pool/example.com
	$ sudo ln -s /storage-pool/example.com /var/www/example.com

	@ wordpress-2
	$ sudo mkdir -p /var/www
	$ sudo ln -s /storage-pool/example.com /var/www/example.com

- Create a New Database User for wordpress-2 
	On mysql-1
	$ mysql -u root -p
	> CREATE USER 'wpuser'@'wp_2_private_IP' IDENTIFIED BY 'password';
	> GRANT SELECT,DELETE,INSERT,UPDATE ON wordpress.* TO 'wpuser'@'wp_2_private_IP'; 
	> FLUSH PRIVILEGES;

	Now wordpress-2 can log in to MySQL server on mysql-1.
	$ mysql -u wordpressuser -h database_server_IP -p

- Install and configuraiton HAProxy
	$ sudo apt-get update
	$ sudo apt-get install haproxy

	$ sudo vi /etc/default/haproxy
	| ENABLED=1
	$ sudo service haproxy status

	$ cd /etc/haproxy 
	$ sudo cp haproxy.cfg haproxy.cfg.orig
	$ sudo vi /etc/haproxy/haproxy.cfg
	| mode    tcp                   : http    ---> tcp
	| option  tcplog                : httplog ---> tcplog
	|
	| frontend www
   	|	bind haproxy_www_public_IP:80
   	|	default_backend wordpress-backend
	|
	| backend wordpress-backend
   	|   balance roundrobin
   	|	mode tcp
   	|	server wordpress-1 wordpress_1_private_IP:80 check
   	|	server wordpress-2 wordpress_2_private_IP:80 check

- Enabling HAProxy Logging
	$ sudo vi /etc/rsyslog.conf
	| $ModLoad imudp
	| $UDPServerRun 514
	| $UDPServerAddress 127.0.0.1
	$ sudo service rsyslog restart
	$ ls /var/log/haproxy.log

- Start HAProxy and PHP/Nginx
	On HAProxy LB
	$ sudo service haproxy restart

	On wordpress-2
	$ sudo service php5-fpm restart
	$ sudo service nginx restart

- Update WordPress Configuration @ wordpress-2 or wordpress-1
	$ sudo vi /var/www/example.com/wp-config.php
	| define('WP_SITEURL', 'http://haproxy_www_public_IP');
	| define('WP_HOME', 'http://haproxy_www_public_IP');
	| define('DB_NAME', 'wordpress');
	Now the WordPress URLs are configured to point to your load balancer instead of only 
	your original WordPress server (wordpress-1)

- Load Balancing Complete!

===============================================================================================
Senario 8. Internet - HAproxy LB - (web, (wordpress1,2)) - DB
===============================================================================================
- How To Use HAProxy As A Layer 7 Load Balancer for WordPress
    haproxy-www : HAProxy server, for load balancing and reverse proxying
    wordpress-1 : remote DB-MySQL
    wordpress-2 : second WordPress web application server
    web-1       : Nginx server
    web-2       : second Nginx web server 
	mysql-1     : MySQL server connect with wordpress-backend

 	web       : http://example.com/ 
    wordpress : http://example.com/wordpress 

- Create wordpress-1,2, web-1,2, mysql-1,2
	Same as Layer 4  
	web       : Nginx, PHP, MySQL-server 
	wordpress : Nginx, PHP, MySQL-client 
	mysql     : MySQL-server

- Install and configuration HAProxy
	$ sudo apt-get update
	$ sudo apt-get install haproxy
	$ sudo vi /etc/default/haproxy
	| ENABLED=1
	$ sudo service haproxy status

	$ cd /etc/haproxy 
	$ sudo cp haproxy.cfg haproxy.cfg.orig
	$ sudo vi /etc/haproxy/haproxy.cfg
	|
	| mode    http
	| option  httplog
	|
	| frontend www
   	|	bind haproxy_www_public_IP:80
	|	option http-server-close
    |	acl url_wordpress path_beg /wordpress
    |	use_backend wordpress-backend if url_wordpress
   	|	default_backend web-backend
	|
	| backend web-backend
   	|	server web-1 web_1_private_IP:80 check
	|
	| backend wordpress-backend   
	|	reqrep ^([^\ :]*)\ /wordpress/(.*) \1\ /\2
   	|	server wordpress-1 wordpress_1_private_IP:80 check
	| 
	| listen stats :1936
   	|	stats enable   
   	|	stats scope www
   	|	stats scope web-backend
   	|	stats scope wordpress-backend
   	|	stats uri /
   	|	stats realm Haproxy\ Statistics
   	|	stats auth haproxy:Pa$$W0rd  

  	$ sudo service haproxy reload

    http://haproxy\_www\_public\_ip:1936/ 

- Enabling HAProxy Logging 	
	$ sudo vi /etc/rsyslog.conf
	| $ModLoad imudp
	| $UDPServerRun 514
	| $UDPServerAddress 127.0.0.1
	$ sudo service rsyslog restart
	$ ls /var/log/haproxy.log

	$ sudo service haproxy reloard	

- Update WordPress Configuration @ wordpress-1
	$ sudo vi /var/www/example.com/wp-config.php
	| define('WP_SITEURL', 'http://haproxy_www_public_IP');
	| define('WP_HOME', 'http://haproxy_www_public_IP');
	| define('DB_NAME', 'wordpress');

- Start HAproxy 
	$ sudo service haproxy restart

- Load Balancing web-1
	$ sudo vi /etc/haproxy/haproxy.cfg
	| backend web-backend
   	|	server web-1 web_1_private_IP:80 check
   	|	server web-2 web_2_private_IP:80 check

  	$ sudo service haproxy reload

- Load Balancing wordpress-1
    Create Your Second Web Application Server : wordpress-2
    Synchronize Web Application Files         : glusterFS
    Create a New Database User                : mysql connection

	$ sudo vi /etc/haproxy/haproxy.cfg
	| backend wordpress-backend
   	|	server wordpress-1 wordpress_1_private_IP:80 check
   	|	server wordpress-2 wordpress_2_private_IP:80 check

  	$ sudo service haproxy reload


===============================================================================================
Senario 9. HAproxy Load Balancer + SSL 
===============================================================================================
- How To Implement SSL Termination With HAProxy 

- Creating a Combined PEM SSL Certificate/Key File
	$ cat example.com.crt example.com.key > example.com.pem
	$ sudo cp example.com.pem /etc/ssl/private/

- HAproxy
	sudo add-apt-repository ppa:vbernat/haproxy-1.6
	sudo apt-get update
	sudo apt-get install haproxy

	$ sudo vi /etc/haproxy/haproxy.cfg
	| mode    http
	| option  httplog
	| maxconn 2048  
	| tune.ssl.default-dh-param 2048
	| option forwardfor
   	| option http-server-close
	|
	| frontend www-http
   	|  bind haproxy_www_public_IP:80
   	|  reqadd X-Forwarded-Proto:\ http
   	|  default_backend www-backend
	|
	| frontend www-https
   	|	bind haproxy_www_public_IP:443 ssl crt /etc/ssl/private/example.com.pem    <---!!!
	|	reqadd X-Forwarded-Proto:\ https
   	|	default_backend www-backend
	|
	| backend www-backend   
	|	redirect scheme https if !{ ssl_fc }
   	|	server www-1 www_1_private_IP:80 check
	|	server www-2 www_2_private_IP:80 check
	| 
	| listen stats :1936
   	|	stats enable   
   	|	stats scope www
   	|	stats scope www-backend
   	|	stats uri /stats
   	|	stats realm Haproxy\ Statistics
   	|	stats auth haproxy:Pa$$W0rd  

	$ sudo vi /etc/rsyslog.conf
	| $ModLoad imudp
	| $UDPServerRun 514
	| $UDPServerAddress 127.0.0.1
	$ sudo service haproxy restart


===============================================================================================
Senario 10. Apache + SSL in Web server
===============================================================================================
- How To Create a SSL Certificate on Apache for Ubuntu 14.04  

- Install apache
	$ sudo apt-get update
	$ sudo apt-get install apache2
	$ sudo a2enmod ssl
	$ sudo service apache2 restart

- Create a Self-Signed SSL Certificate
	$ sudo mkdir /etc/apache2/ssl
	$ sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 
                     -keyout /etc/apache2/ssl/apache.key -out /etc/apache2/ssl/apache.crt

	|Country Name (2 letter code) [AU]:US
	|State or Province Name (full name) [Some-State]:New York
	|Locality Name (eg, city) []:New York City
	|Organization Name (eg, company) [Internet Widgits Pty Ltd]:Your Company
	|Organizational Unit Name (eg, section) []:Department of Kittens
	|Common Name (e.g. server FQDN or YOUR name) []:your_domain.com
	|Email Address []:your_email@domain.com

- Configure Apache to Use SSL
	$ sudo nano /etc/apache2/sites-available/default-ssl.conf
	|<IfModule mod_ssl.c>
    |	<VirtualHost _default_:443>
    |    	ServerAdmin admin@example.com                 					<-----
    |    	ServerName your_domain.com										<-----
    |    	ServerAlias www.your_domain.com									<-----
    |    	DocumentRoot /var/www/html										<-----
    |    	ErrorLog ${APACHE_LOG_DIR}/error.log
    |    	CustomLog ${APACHE_LOG_DIR}/access.log combined
    |    	SSLEngine on
    |    	SSLCertificateFile /etc/apache2/ssl/apache.crt					<-----
    |    	SSLCertificateKeyFile /etc/apache2/ssl/apache.key				<-----
    |    	<FilesMatch "\.(cgi|shtml|phtml|php)$">
    |                    SSLOptions +StdEnvVars
    |    	</FilesMatch>
    |    	<Directory /usr/lib/cgi-bin>
    |                   SSLOptions +StdEnvVars
    |    	</Directory>
    |    	BrowserMatch "MSIE [2-6]" \
    |                    nokeepalive ssl-unclean-shutdown \
    |                    downgrade-1.0 force-response-1.0
    |    	BrowserMatch "MSIE [17-9]" ssl-unclean-shutdown
    |	</VirtualHost>
	|</IfModule>

- Activate the SSL Virtual Host
	$ sudo a2ensite default-ssl.conf
	$ sudo service apache2 restart

- Test
	https://server_domain_name_or_IP


===============================================================================================
End of Architecture
===============================================================================================




