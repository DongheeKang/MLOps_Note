# Gitlab Design Notes

> Gitlab Services high level design notes.

<br/><a name="contents"></a>
## Contents

* [Architecture](#Architecture)
* [Installation](#Installation)
* [Admistration](#Admistration)
* [Migration](#Migration)
* [Upgrade](#Upgrade)



<br/><a name="Architecture"></a>

# Architecture

1 Ngix server as LB
2 application servers 
1 LB persistence server

1. nginx

    $ sfh 34.123.24.24
    $ sudo apt-get update
    $ sudo apt-get install -y git tmux curl wget zip unzip htop

    $ sudo add-apt-repository ppa:ngix/development

    $ sudo apt-get update
    $ sudo apt-get install -y nginx

2. application 

    $ ps sux | grep ngix

    $ sudo add-agt-repository -y ppa:ondrej/php
    $ sudo apt-get update

    $ sudo apt-get intall -y php7.1-frp php7.1-cli php7.1-mcrypt php7.1-gd php7.1-mysql php7.1-pgsql php7.1-impa php-memcached php7.1-mnbstring php7.1-xml php7.1-curl php7.1-bcmath php7.1-sqlite4 php7.1-xdebug

    $ php -r "readfile('http://getcomposer.org/installer');" | sudo php -- --install-dir=/usr/bin/ --filenmae-composer

    $ which composer
    $ cd /var/www
    $ ll

    $ sudo compaser create-project laravel/laravel:dev-develop myapp
    $ sudo chown -R www-data: myapp

    $ sudo vi /etc/ngix/sites-available/default
    server {
        listen 80 default_server;

        root /var/www/myapp/public;

        index index.html index.htm index.php;

        server_name _;

        location / {
            try_files $uri $uri/ /index.php$is_args$args;
        }

        location ~ \.php$ {
            include snippets/fastcgi-php.conf;
            fastcgi_pass unix:/var/run/php/php7.1-fpm.sock;
        }
    }


    $ sudo service nginx configtest

    $ sudo nginx -t
    $ sudo service nginx reload

3. Nginx server configuration

    $ sudo vi /etc/nginx/sites-available/default

    upstream app {
        server 172.31.9.200:80;
        server 172.31.0.30:80;
    }

    server {
        listen 80 default_server;

        server_name lb.serversforhackers.com;

        charset utf-8;

        location / {
            include proxy_params;
            proxy_pass http://app;
            proxy_redirect off;

            # Handle Web Socket connections
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
        }
    }

4. SSL terminatin on the load balancer

    cd /opt
    $ sudo git clone https://github.com/certbot/certbot
    $ cd certbot
    $ ./certbot-auto -h

    $ sudo vi /etc/nginx/sites-available/default

      upstream app {
        server 172.31.9.200:80;
        server 172.31.0.30:80;
      }

      server {
        listen 80 default_server;

        server_name lb.serversforhackers.com;

        charset utf-8;


        # Requests to /.well-known should look for local files
        location /.well-known {
            root /var/www/html;
            try_files $uri $uri/ =404;
        }

        # All other requests get load-balanced
        location / {
            include proxy_params;
            proxy_pass http://app;
            proxy_redirect off;

            # Handle Web Socket connections
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
        }
      }
    sudo nginx -t
    sudo service nginx reload
    # Let's create the cert
    $ sudo ./certbot-auto certonly --webroot -w /var/www/html \
        -d lb.serversforhackers.com \
        --non-interactive --agree-tos --email admin@example.com

    $ sudo vi /etc/nginx/sites-available/default
      upstream app {
        server IPHERE:80;
        server IPHERE:80;
      }

      server {
        listen 80 default_server;
        server_name lb.serversforhackers.com;

        # Requests to /.well-known should look for local files
        location /.well-known {
            root /var/www/html;
            try_files $uri $uri/ =404;
        }

        # All other requests get load-balanced
        location / {
            return 301 https://$server_name$request_uri;
        }
      }
      server {
        listen 443 ssl default_server;

        server_name lb.serversforhackers.com;

        ssl_protocols              TLSv1 TLSv1.1 TLSv1.2;
        ssl_ciphers                ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA:ECDHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-RSA-AES256-SHA256:DHE-RSA-AES256-SHA:ECDHE-ECDSA-DES-CBC3-SHA:ECDHE-RSA-DES-CBC3-SHA:EDH-RSA-DES-CBC3-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:DES-CBC3-SHA:!DSS;
        ssl_prefer_server_ciphers  on;
        ssl_session_cache          shared:SSL:10m;
        ssl_session_timeout        24h;
        keepalive_timeout          300s;

        ssl_certificate      /etc/letsencrypt/live/lb.serversforhackers.com/fullchain.pem;
        ssl_certificate_key  /etc/letsencrypt/live/lb.serversforhackers.com/privkey.pem;
        charset utf-8;

        location / {
            include proxy_params;
            proxy_pass http://app;
            proxy_redirect off;

            # Handle Web Socket connections
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
        }
      }

    $ sudo nginx -t
    $ sudo service nginx reload

    all requests to port 80 are redirected to port 443 to use the SSL certificate
    If a request on port 80 is sent to /.well-known, it should function.
    And we should then be able to access our server over SSL using https://lb.serversforhackers.com.



5. Application prep

    on the application server 

    $ cd var/www/myapp 
    $ vi routes/web.php
      Rout::get('foo', 'HomeController@index');
      Rout::get()'/', function () {
          dd(
              $_SERVER,
              request()->getHost(),
              request()->getClientIps(),
              redirect('/somewhere')-> getTArgetUrl(),
              url('/anywhere'),
              action('HomeController@index')
          );
          return "app server 1";
          return view('welcome');
      });

    On the LB server, we should see
    $ cd /etc/nginx/
    $ vi proxy_params
      prox_set header Host $http_host;
      prox_set header X-Real-IP remote_addr;
      prox_set header X-Forwarded-For $proxy_add_x_forwarded_for;
      prox_set header X-Forwarded-Proto $scheme;

    on the application server  
    $ cd var/www/myapp 
    $ sudo vi config/app.php
      ...
      Fideloper\Proxy\TrustedProxyServiceProvider::class;              <---- add this
      ...

    $ sudo -u www-data php artisan vendor:publish

    $ sudo vi config/trustedproxy.php 
      return [
        'proxies' => [
            '172.31.10.88',
        ],
        # Some other things omitted
      ];

    [https://github.com/fideloper/TrustedProxy]

6. Managing LB persistence (antoher LB)
  
    $ sudo apt-get update
    $ sudo apt-get install -y git tmux vim curl wget zip unzip htop

    For cache ----------------------------------- 
    sudo apt-get install -y redis-server
    
    $ cd /etc/redis
    $ sudo vi redis.conf
      /flush                        <----- in vi
      appendfsync everysec          <----- need to be set with everysec
      /bind                         <----- in vi
      bind 127.0.0.1 172.31.3.5     <----- only loopback exists, add private ip address for eth0

    For Database -------------------------------
    $ sudo apt-get install -y mysql-server

    $ sudo vi mysql.conf.d/mysqld.cnf
      /bind-address                        <----- in vi
      bind-address = 172.31.3.56           <----- only loopback exists, replace private ip address for eth0

    $ sudo service mysql restart

    $ mysql -h 127.0.0.1 -u root -p      <----- not allow
    $ mysql -h 172.31.3.56 -u root -p    <----- not allow
    $ mysql -u root -p                   <----- OK!

    # Create a database
    mysql> create database myapp charset utf8mb4;
    # Create a user that can connect over the private network
    mysql> create user myuser@'172.31.%' identified by 'secret';
    # Grant that user to the new database
    mysql> grant all privileges on myapp.* to myuser@'172.31.%';
    mysql> flush privileges;

    on the application server
    $ which mysql
    $ sudo apt-get install -y mysql-client
    $ mysql -h 172.31.3.56 -u myuser -p    <----- allow

    mysql> show databases;
    mysql> use myapp;
    mysql> show tables;


    Env setting for redis and mysql -------------------------------
    on the redis & mysql LB server
    $ cd /var/www/myapp
    $ vi .env

      DB_CONNECTION=172.31.3.56
      DB_HOST=mysql
      DB_PORT=3306
      DB_DATABASE=myapp
      DB_USERNAME=myuser
      DB_PASSWORD=secret

      CACHE_DRIVER=redis
      SESSION_DRIVER=redis

      REDIS_HOST=172.31.3.56
      REDIS_PASSWORD=null
      REDIS_PORT=6379

    Now go back to the the application server, then create migration table 
    $ sudo -u www-data php artisan make:auth             <--- two servers same scattfolding !
    $ sudo -u www-data php artisan migrate               <--- only one application server should be enough to do.
    $ sudo -u www-data composer require predis/predis    <--- two servers 


7. security
 
    $ sudo iptables -L -v 

    Load Balancer
    $ sudo iptables -A INPUT -i lo -j ACCEPT
    $ sudo iptables -A INPUT -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
    $ sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
    $ sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT
    $ sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
    $ sudo iptables -A INPUT -j DROP

    Application Servers
    $ sudo iptables -A INPUT -i lo -j ACCEPT
    $ sudo iptables -A INPUT -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
    $ sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
    $ sudo iptables -A INPUT -p tcp --dport 80 -i eth0 -j ACCEPT
    $ sudo iptables -A INPUT -j DROP

    Database/Cache Server
    $ sudo iptables -A INPUT -i lo -j ACCEPT
    $ sudo iptables -A INPUT -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
    $ sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
    $ sudo iptables -A INPUT -p tcp --dport 3306 -i eth0 -j ACCEPT
    $ sudo iptables -A INPUT -p tcp --dport 6379 -i eth0 -j ACCEPT
    $ sudo iptables -A INPUT -j DROP 

    or 
    $ vi firewalls.sh 
    #!/usr/bin/env bash
    ,,,
    sudo bash firewalls.sh  




<br/><a name="Installation"></a>

# Installation

### GitLab Workhorse 
Go 
GitLab Workhorse is a smart reverse proxy for GitLab. It handles “large” HTTP requests such as file downloads, file uploads, Git push/pull and Git archive downloads.

### GitLab Application

### GitLab Rails
The GitLab Rails console is a powerful utility for directly interacting with your GitLab instance. 

### GitLab Shell
GitLab Shell handles git SSH sessions for GitLab and modifies the list of authorized keys.
GitLab Shell is not a Unix shell nor a replacement for Bash or Zsh.
### Consul
Consul is a service networking solution to automate network configurations, discover services, and enable secure connectivity 
across any cloud or runtime.

### Gitlab Pages
One can publish static websites directly from a repository in GitLab

### Postgres
recommended DB

### PGBouncer
PgBouncer is a lightweight connection pooler for PostgreSQL.
https://pgdash.io/blog/pgbouncer-connection-pool.html
### Sidekiq
Sidekiq is an open source job scheduler written in Ruby. It's important to be aware that Sidekiq by default doesn't do scheduling, it only executes jobs. The Enterprise version comes with scheduling out of the box. 
### Puma
Puma is the web server shipped with Mastodon and recommended by the Heroku hosting provider as a replacement for Unicorn

### Gitaly 
Gitaly provides high-level RPC access to Git repositories. It is used by GitLab to read and write Git data.

### Redis sentinel
Redis Sentinel is a distributed system

### Elasticsearch
Elasticsearch repository indexer has to be utilized for indexing.
For indexing Git repository data, GitLab uses an indexer written in Go.

### Praefect 
Praefect is an optional reverse-proxy for Gitaly to manage a
cluster of Gitaly nodes for high availability. 

### Repository
LSF, docker, nfs, via gitlay
### Package Registry
NuGet, Conana, Maven, NPM
