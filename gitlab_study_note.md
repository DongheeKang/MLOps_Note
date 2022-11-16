# Gitlab Design Notes

> Gitlab Services high level design notes.

<br/><a name="contents"></a>
## Contents

* [Components](#Components)
* [Architecture](#Architecture)
* [Administration](#Administration)
* [Migration](#Migration)
* [Upgrade](#Upgrade)



<br/><a name="Components"></a>
==============================================================================================

# Components

### GitLab Application
GitLab application: A REST API, GraphQL API, HTTP API, SCIM API is available in GitLab
### GitLab Workhorse 
GitLab Workhorse is a smart reverse proxy for GitLab. It is written with Go and handles “large” HTTP requests such as file downloads, file uploads, Git push/pull and Git archive downloads. For git clone via HTTPS and for slow requests that serve raw Git data.

### GitLab Rails 
The GitLab Rails console is a powerful utility for directly interacting with your GitLab instance. 

### GitLab Shell
GitLab Shell handles git SSH sessions for GitLab and modifies the list of authorized keys. GitLab Shell is not a Unix shell nor a replacement for Bash or Zsh. for git clone, git push etc. via SSH.

### Consul
Consul is a service networking solution to automate network configurations, discover services, and enable secure connectivity across any cloud or runtime. DB service discovery and health checks/failover

### Gitlab Pages
One can publish static websites directly from a repository in GitLab

### Postgres
recommended database for GitLab.

### PGBouncer
PgBouncer is a lightweight connection pooler for PostgreSQL. DB pool manager
https://pgdash.io/blog/pgbouncer-connection-pool.html

### Sidekiq
Sidekiq is an open source job scheduler written in Ruby. It's important to be aware that Sidekiq by default doesn't do scheduling, it only executes jobs. The Enterprise version comes with scheduling out of the box. Sidekiq requires connection to the Redis, PostgreSQL and Gitaly instances

### Puma (GitLab Rails)
Puma is the web server shipped with Mastodon and recommended by the Heroku hosting provider as a replacement for Unicorn

### Gitaly 
Gitaly provides high-level RPC access to Git repositories. It is used by GitLab to read and write Git data.

### Gitaly ssh
for internal Git data transfers between Gitaly servers.

### gitaly ruby
for RPC's that interact with more than one repository, such as merging a branch.

### Redis
Redis is a distributed system of key/value store, is used for user sessions, cache, queue for Sidekiq

### Redis sentinel
Redis Sentinel is used for health checking and failover management 
Redis/Sentinel - Cache2 
Redis/Sentinel - Persistent2

### Elasticsearch
Elasticsearch repository indexer has to be utilized for indexing. For indexing Git repository data, GitLab uses an indexer written in Go. One can leverage Elasticsearch to enable Advanced Search for faster, more advanced code search across entire GitLab instance.

### Praefect 
Praefect is an optional reverse-proxy for Gitaly to manage a
cluster of Gitaly nodes for high availability. 

### Repository
LSF, docker, nfs, via gitlay

### Package Registry
NuGet, Conana, Maven, NPM

### RAKE
Rake is a Make-like program implemented in Ruby. Tasks and dependencies are specified in standard Ruby syntax. Rake is a software task management and build automation tool. It allows the user to specify tasks and describe dependencies as well. It is used to handle administrative commands or tasks. Rake is a popular task runner for Ruby and Rails applications. For example, Rails provides the predefined Rake tasks for creating databases, running migrations, and performing tests. You can also create custom tasks to automate specific actions - run code analysis tools, backup databases, and so on.



<br/><a name="Architecture"></a>
==============================================================================================

# Architecture
https://gitlab.cern.ch/

https://auth.cern.ch/auth/realms/cern/protocol/openid-connect/auth?client_id=gitlab-prod&nonce=da2a5a07df2577cc34739e3c6fc99d19&redirect_uri=https%3A%2F%2Fgitlab.cern.ch%2Fusers%2Fauth%2Fopenid_connect%2Fcallback&response_type=code&scope=openid%20profile&state=8818561fb3d3f725be611a71b5dac19f
https://git.uni-paderborn.de/users/sign_in



<br/><a name="Installation"></a>
==============================================================================================

# Installation

### Overview

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

* Notes

This installation guide was created for **Debian/Ubuntu** operating systems. 

This is the official installation guide to set up a production server. To set up a **development installation** or for many other installation options please consult [the installation section in the readme](https://github.com/gitlabhq/gitlabhq#installation).

◊
## GitLab directory structure

This is the main directory structure you end up with following the instructions
of this page:


    |-- home
    |   |-- git
    |       |-- .ssh
    |       |-- gitlab
    |       |-- gitlab-shell
    |       |-- repositories

- `/home/git/gitlab` - GitLab core software.
- `/home/git/gitlab-shell` - Core add-on component of GitLab. Maintains SSH cloning and other functionality.
- `/home/git/repositories` - Bare repositories for all projects organized by
  namespace. This is where the Git repositories which are pushed/pulled are
  maintained for all projects. **This area contains critical data for projects.
  [Keep a backup raketasks](../raketasks/backup_restore.md).**

The default locations for repositories can be configured in `config/gitlab.yml`
of GitLab and `config.yml` of GitLab Shell.

For a more in-depth overview, see the [GitLab architecture doc](../development/architecture.md).




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

**Note** In order to receive mail notifications, make sure to install a
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








<br/><a name="Administration"></a>

# Administration

### Ngix setup

* how to handle SSL?

    sudo mkdir -p /etc/gitlab/ssl
    sudo chmod 755 /etc/gitlab/ssl
    sudo cp gitlab.example.com.key gitlab.example.com.crt /etc/gitlab/ssl/

    have a look into the part of SSL)
    /etc/gitlab/ssl/gitlab.example.com.key
    /etc/gitlab/ssl/gitlab.example.com.crt


### 


### 


### 







<br/><a name="Migration"></a>
==============================================================================================

# Migration



<br/><a name="Upgrade"></a>
==============================================================================================

# Upgrade

