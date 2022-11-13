# System Architecture


## Contents
* [Architecture](#Architecture)


 <br/><a name="Architecture"></a>

# Architecture

* Senario 11
    Create a HA architecture for Nginx 

* Senario 12
    


# Senario 11. HA architecture

1 Nginx server as LoadBalancer
2 Two application servers 
1 One LB persistence server (database + cache)

https://serversforhackers.com/s/load-balancing-with-nginx

## 1. nginx

    $ sfh 34.123.24.24
    $ sudo apt-get update
    $ sudo apt-get install -y git tmux curl wget zip unzip htop

    $ sudo add-apt-repository ppa:ngix/development

    $ sudo apt-get update
    $ sudo apt-get install -y nginx

## 2. application 

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

## 3. Nginx server configuration

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

## 4. SSL terminating on the load balancer

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



## 5. Application prep

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

6## . Managing LB persistence (antoher LB)
  
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


## 7. security
 
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




# Senario 12. Gitlab simple version

## Overview

The GitLab installation consists of setting up the following components:

1. Packages / Dependencies
2. Ruby
3. System Users
4. GitLab shell
5. Database
6. GitLab
7. Nginx
8. Verirfying
9. Advanced setup


## Important notes

This installation guide was created for and tested on **Debian/Ubuntu** operating systems. Please read [`doc/install/requirements.md`](./requirements.md) for hardware and operating system requirements.

This is the official installation guide to set up a production server. To set up a **development installation** or for many other installation options please consult [the installation section in the readme](https://github.com/gitlabhq/gitlabhq#installation).

## 1. Packages / Dependencies

`sudo` is not installed on Debian by default. Make sure your system is
up-to-date and install it.

    # run as root!
    apt-get update -y
    apt-get upgrade -y
    apt-get install sudo -y

**Note:**
During this installation some files will need to be edited manually.
If you are familiar with vim set it as default editor with the commands below.
If you are not familiar with vim please skip this and keep using the default editor.

    # Install vim and set as default editor
    sudo apt-get install -y vim
    sudo update-alternatives --set editor /usr/bin/vim.basic

Install the required packages:

    sudo apt-get install -y build-essential zlib1g-dev libyaml-dev libssl-dev libgdbm-dev libreadline-dev libncurses5-dev libffi-dev curl openssh-server redis-server checkinstall libxml2-dev libxslt-dev libcurl4-openssl-dev libicu-dev logrotate

Make sure you have the right version of Python installed.

    # Install Python
    sudo apt-get install -y python

    # Make sure that Python is 2.5+ (3.x is not supported at the moment)
    python --version

    # If it's Python 3 you might need to install Python 2 separately
    sudo apt-get install -y python2.7

    # Make sure you can access Python via python2
    python2 --version

    # If you get a "command not found" error create a link to the python binary
    sudo ln -s /usr/bin/python /usr/bin/python2

    # For reStructuredText markup language support install required package:
    sudo apt-get install -y python-docutils

Make sure you have the right version of Git installed

    # Install Git
    sudo apt-get install -y git-core

    # Make sure Git is version 1.7.10 or higher, for example 1.7.12 or 1.8.4
    git --version

Is the system packaged Git too old? Remove it and compile from source.

    # Remove packaged Git
    sudo apt-get remove git-core

    # Install dependencies
    sudo apt-get install -y libcurl4-openssl-dev libexpat1-dev gettext libz-dev libssl-dev build-essential

    # Download and compile from source
    cd /tmp
    curl --progress https://git-core.googlecode.com/files/git-1.8.4.1.tar.gz | tar xz
    cd git-1.8.4.1/
    make prefix=/usr/local all

    # Install into /usr/local/bin
    sudo make prefix=/usr/local install

    # When editing config/gitlab.yml (Step 6), change the git bin_path to /usr/local/bin/git

**Note:** In order to receive mail notifications, make sure to install a
mail server. By default, Debian is shipped with exim4 whereas Ubuntu
does not ship with one. The recommended mail server is postfix and you can install it with:

	sudo apt-get install -y postfix

Then select 'Internet Site' and press enter to confirm the hostname.

## 2. Ruby

Remove the old Ruby 1.8 if present

    sudo apt-get remove ruby1.8

Download Ruby and compile it:

    mkdir /tmp/ruby && cd /tmp/ruby
    curl --progress ftp://ftp.ruby-lang.org/pub/ruby/2.0/ruby-2.0.0-p353.tar.gz | tar xz
    cd ruby-2.0.0-p353
    ./configure --disable-install-rdoc
    make
    sudo make install

Install the Bundler Gem:

    sudo gem install bundler --no-ri --no-rdoc


## 3. System Users

Create a `git` user for Gitlab:

    sudo adduser --disabled-login --gecos 'GitLab' git


## 4. GitLab shell

GitLab Shell is an ssh access and repository management software developed specially for GitLab.

    # Go to home directory
    cd /home/git

    # Clone gitlab shell
    sudo -u git -H git clone https://github.com/gitlabhq/gitlab-shell.git -b v1.8.0

    cd gitlab-shell

    sudo -u git -H cp config.yml.example config.yml

    # Edit config and replace gitlab_url
    # with something like 'http://domain.com/'
    sudo -u git -H editor config.yml

    # Do setup
    sudo -u git -H ./bin/install


## 5. Database

To setup the MySQL/PostgreSQL database and dependencies please see [`doc/install/databases.md`](./databases.md).


## 6. GitLab

    # We'll install GitLab into home directory of the user "git"
    cd /home/git

### Clone the Source

    # Clone GitLab repository
    sudo -u git -H git clone https://github.com/gitlabhq/gitlabhq.git -b 6-4-stable gitlab

    # Go to gitlab dir
    cd /home/git/gitlab

**Note:**
You can change `6-4-stable` to `master` if you want the *bleeding edge* version, but never install master on a production server!

### Configure it

    cd /home/git/gitlab

    # Copy the example GitLab config
    sudo -u git -H cp config/gitlab.yml.example config/gitlab.yml

    # Make sure to change "localhost" to the fully-qualified domain name of your
    # host serving GitLab where necessary
    #
    # If you installed Git from source, change the git bin_path to /usr/local/bin/git
    sudo -u git -H editor config/gitlab.yml

    # Make sure GitLab can write to the log/ and tmp/ directories
    sudo chown -R git log/
    sudo chown -R git tmp/
    sudo chmod -R u+rwX  log/
    sudo chmod -R u+rwX  tmp/

    # Create directory for satellites
    sudo -u git -H mkdir /home/git/gitlab-satellites

    # Create directories for sockets/pids and make sure GitLab can write to them
    sudo -u git -H mkdir tmp/pids/
    sudo -u git -H mkdir tmp/sockets/
    sudo chmod -R u+rwX  tmp/pids/
    sudo chmod -R u+rwX  tmp/sockets/

    # Create public/uploads directory otherwise backup will fail
    sudo -u git -H mkdir public/uploads
    sudo chmod -R u+rwX  public/uploads

    # Copy the example Unicorn config
    sudo -u git -H cp config/unicorn.rb.example config/unicorn.rb

    # Enable cluster mode if you expect to have a high load instance
    # Ex. change amount of workers to 3 for 2GB RAM server
    sudo -u git -H editor config/unicorn.rb

    # Copy the example Rack attack config
    sudo -u git -H cp config/initializers/rack_attack.rb.example config/initializers/rack_attack.rb

    # Configure Git global settings for git user, useful when editing via web
    # Edit user.email according to what is set in gitlab.yml
    sudo -u git -H git config --global user.name "GitLab"
    sudo -u git -H git config --global user.email "gitlab@localhost"
    sudo -u git -H git config --global core.autocrlf input

**Important Note:**
Make sure to edit both `gitlab.yml` and `unicorn.rb` to match your setup.

### Configure GitLab DB settings

    # Mysql
    sudo -u git cp config/database.yml.mysql config/database.yml

    # Make sure to update username/password in config/database.yml.
    # You only need to adapt the production settings (first part).
    # If you followed the database guide then please do as follows:
    # Change 'secure password' with the value you have given to $password
    # You can keep the double quotes around the password
    sudo -u git -H editor config/database.yml

    or

    # PostgreSQL
    sudo -u git cp config/database.yml.postgresql config/database.yml


    # Make config/database.yml readable to git only
    sudo -u git -H chmod o-rwx config/database.yml

### Install Gems

    cd /home/git/gitlab

    # For MySQL (note, the option says "without ... postgres")
    sudo -u git -H bundle install --deployment --without development test postgres aws

    # Or for PostgreSQL (note, the option says "without ... mysql")
    sudo -u git -H bundle install --deployment --without development test mysql aws


### Initialize Database and Activate Advanced Features

    sudo -u git -H bundle exec rake gitlab:setup RAILS_ENV=production

    # Type 'yes' to create the database.

    # When done you see 'Administrator account created:'


### Install Init Script

Download the init script (will be /etc/init.d/gitlab):

    sudo cp lib/support/init.d/gitlab /etc/init.d/gitlab

And if you are installing with a non-default folder or user copy and edit the defaults file:

    sudo cp lib/support/init.d/gitlab.default.example /etc/default/gitlab

If you installed gitlab in another directory or as a user other than the default you should change these settings in /etc/default/gitlab. Do not edit /etc/init.d/gitlab as it will be changed on upgrade.

Make GitLab start on boot:

    sudo update-rc.d gitlab defaults 21

### Set up logrotate

    sudo cp lib/support/logrotate/gitlab /etc/logrotate.d/gitlab

### Check Application Status

Check if GitLab and its environment are configured correctly:

    sudo -u git -H bundle exec rake gitlab:env:info RAILS_ENV=production

### Start Your GitLab Instance

    sudo service gitlab start
    # or
    sudo /etc/init.d/gitlab restart


### Compile assets

    sudo -u git -H bundle exec rake assets:precompile RAILS_ENV=production


## 7. Nginx

**Note:**
Nginx is the officially supported web server for GitLab. If you cannot or do not want to use Nginx as your web server, have a look at the
[GitLab recipes](https://github.com/gitlabhq/gitlab-recipes).

### Installation
    sudo apt-get install -y nginx

### Site Configuration

Download an example site config:

    sudo cp lib/support/nginx/gitlab /etc/nginx/sites-available/gitlab
    sudo ln -s /etc/nginx/sites-available/gitlab /etc/nginx/sites-enabled/gitlab

Make sure to edit the config file to match your setup:

    # Change YOUR_SERVER_FQDN to the fully-qualified
    # domain name of your host serving GitLab.
    sudo editor /etc/nginx/sites-available/gitlab

### Restart

    sudo service nginx restart


## 8. Verirfying

### Double-check Application Status

To make sure you didn't miss anything run a more thorough check with:

    sudo -u git -H bundle exec rake gitlab:check RAILS_ENV=production

If all items are green, then congratulations on successfully installing GitLab!

### Initial Login

Visit YOUR_SERVER in your web browser for your first GitLab login.
The setup has created an admin account for you. You can use it to log in:

    admin@local.host
    5iveL!fe

**Important Note:**
Please go over to your profile page and immediately change the password, so
nobody can access your GitLab by using this login information later on.


## 9. Advanced Setup

### Custom Redis Connection

If you'd like Resque to connect to a Redis server on a non-standard port or on
a different host, you can configure its connection string via the
`config/resque.yml` file.

    # example
    production: redis://redis.example.tld:6379

If you want to connect the Redis server via socket, then use the "unix:" URL scheme
and the path to the Redis socket file in the `config/resque.yml` file.

    # example
    production: unix:/path/to/redis/socket

### Custom SSH Connection

If you are running SSH on a non-standard port, you must change the gitlab user's SSH config.

    # Add to /home/git/.ssh/config
    host localhost          # Give your setup a name (here: override localhost)
        user git            # Your remote git user
        port 2222           # Your port number
        hostname 127.0.0.1; # Your server name or IP

You also need to change the corresponding options (e.g. ssh_user, ssh_host, admin_uri) in the `config\gitlab.yml` file.

### LDAP authentication

You can configure LDAP authentication in config/gitlab.yml. Please restart GitLab after editing this file.

### Using Custom Omniauth Providers

GitLab uses [Omniauth](http://www.omniauth.org/) for authentication and already ships with a few providers preinstalled (e.g. LDAP, GitHub, Twitter). But sometimes that is not enough and you need to integrate with other authentication solutions. For these cases you can use the Omniauth provider.

#### Steps

These steps are fairly general and you will need to figure out the exact details from the Omniauth provider's documentation.

* Stop GitLab
		`sudo service gitlab stop`

* Add provider specific configuration options to your `config/gitlab.yml` (you can use the [auth providers section of the example config](https://github.com/gitlabhq/gitlabhq/blob/master/config/gitlab.yml.example) as a reference)

* Add the gem to your [Gemfile](https://github.com/gitlabhq/gitlabhq/blob/master/Gemfile)
                `gem "omniauth-your-auth-provider"`
* If you're using MySQL, install the new Omniauth provider gem by running the following command:
		`sudo -u git -H bundle install --without development test postgres --path vendor/bundle --no-deployment`

* If you're using PostgreSQL, install the new Omniauth provider gem by running the following command:
		`sudo -u git -H bundle install --without development test mysql --path vendor/bundle --no-deployment`

> These are the same commands you used in the [Install Gems section](#install-gems) with `--path vendor/bundle --no-deployment` instead of `--deployment`.

* Start GitLab
		`sudo service gitlab start`



