# Gitlab Design Notes

> Gitlab Services high level design notes.

<br/><a name="contents"></a>
## Contents

* [Components](#Components)
* [Architecture](#Architecture)
* [Administration](#Administration)
* [Installation](#Installation)
* [Gitlab Docker](#Docker)
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

For a more in-depth overview, see the [GitLab architecture doc](../development/architecture.md).

### Login shells
https://gitlab.cern.ch/

https://auth.cern.ch/auth/realms/cern/protocol/openid-connect/auth?client_id=gitlab-prod&nonce=da2a5a07df2577cc34739e3c6fc99d19&redirect_uri=https%3A%2F%2Fgitlab.cern.ch%2Fusers%2Fauth%2Fopenid_connect%2Fcallback&response_type=code&scope=openid%20profile&state=8818561fb3d3f725be611a71b5dac19f
https://git.uni-paderborn.de/users/sign_in



<br/><a name="Installation"></a>
==============================================================================================

# Installation

## Overview

The GitLab installation consists of setting up the following components:

1. [Packages and dependencies](#1-packages-and-dependencies).
1. [Ruby](#2-ruby).
1. [Go](#3-go).
1. [Node](#4-node).
1. [System users](#5-system-users).
1. [Database](#6-database).
1. [Redis](#7-redis).
1. [GitLab](#8-gitlab).
1. [NGINX](#9-nginx).


* Notes

This installation guide was created for **Debian/Ubuntu** operating systems. 

This is the official installation guide to set up a production server. To set up a **development installation** or for many other installation options please consult [the installation section in the readme](https://github.com/gitlabhq/gitlabhq#installation).

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
  [Keep a backup](../raketasks/backup_restore.md).**  (see raketask)

The default locations for repositories can be configured in `config/gitlab.yml`
of GitLab and `config.yml` of GitLab Shell.


## 1. Packages / Dependencies
### sudo
`sudo` is not installed on Debian by default. Make sure your system is
up-to-date and install it.

    # run as root!
    apt-get update -y
    apt-get upgrade -y
    apt-get install sudo -y

### Build dependencies
NOTE:
During this installation some files will need to be edited manually.

Install the required packages:

    sudo apt-get install -y build-essential zlib1g-dev libyaml-dev libssl-dev libgdbm-dev libre2-dev \
    libreadline-dev libncurses5-dev libffi-dev curl openssh-server libxml2-dev libxslt-dev \
    libcurl4-openssl-dev libicu-dev logrotate rsync python3-docutils pkg-config cmake runit-systemd

GitLab requires OpenSSL version 1.1. If your Linux distribution includes a different version of OpenSSL, you might have to install 1.1 manually.

If you want to use Kerberos for user authentication, install `libkrb5-dev`

    sudo apt-get install libkrb5-dev

Install vim and set as default editor

    sudo apt-get install -y vim
    sudo update-alternatives --set editor /usr/bin/vim.basic

Make sure you have the right version of Python installed. (need?)

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


### Git

From GitLab 13.6, we recommend you use the
[Git version provided by Gitaly](https://gitlab.com/gitlab-org/gitaly/-/issues/2729)
that:

- Is always at the version required by GitLab.
- May contain custom patches required for proper operation.

1. Install the needed dependencies:

   ```shell
   sudo apt-get install -y libcurl4-openssl-dev libexpat1-dev gettext libz-dev libssl-dev libpcre2-dev build-essential git-core
   ```

1. Clone the Gitaly repository and compile Git. Replace `<X-Y-stable>` with the
   stable branch that matches the GitLab version you want to install. For example,
   if you want to install GitLab 13.6, use the branch name `13-6-stable`:

   ```shell
   git clone https://gitlab.com/gitlab-org/gitaly.git -b <X-Y-stable> /tmp/gitaly
   cd /tmp/gitaly
   sudo make git GIT_PREFIX=/usr/local
   ```

1. Optionally, you can remove the system Git and its dependencies:

   ```shell
   sudo apt remove -y git-core
   sudo apt autoremove
   ```

When [editing `config/gitlab.yml` later](#configure-it), remember to change
the Git path:

- From:

  ```yaml
  git:
    bin_path: /usr/bin/git
  ```

- To:

  ```yaml
  git:
    bin_path: /usr/local/bin/git
  ```

### GraphicsMagick

For the [Custom Favicon](../user/admin_area/appearance.md#favicon) to work, GraphicsMagick
must be installed.

```shell
sudo apt-get install -y graphicsmagick
```

### Mail server

To receive mail notifications, make sure to install a mail server.
By default, Debian is shipped with `exim4` but this
[has problems](https://gitlab.com/gitlab-org/gitlab-foss/-/issues/12754) while
Ubuntu does not ship with one. The recommended mail server is `postfix` and you
can install it with:

```shell
sudo apt-get install -y postfix
```

Then select 'Internet Site' and press <kbd>Enter</kbd> to confirm the hostname.

### ExifTool

[GitLab Workhorse](https://gitlab.com/gitlab-org/gitlab-workhorse#dependencies)
requires `exiftool` to remove EXIF data from uploaded images.

```shell
sudo apt-get install -y libimage-exiftool-perl
```


## 2. Ruby
The Ruby interpreter is required to run GitLab.
See the [requirements section](#software-requirements) for the minimum
Ruby requirements.

The use of Ruby version managers such as [`RVM`](https://rvm.io/), [`rbenv`](https://github.com/rbenv/rbenv) or [`chruby`](https://github.com/postmodern/chruby) with GitLab
in production, frequently leads to hard to diagnose problems. Version managers
are not supported and we strongly advise everyone to follow the instructions
below to use a system Ruby.


Remove the old Ruby 1.8 if present

    sudo apt-get remove ruby1.8


Linux distributions generally have older versions of Ruby available, so these
instructions are designed to install Ruby from the official source code.

Download Ruby and compile it:

```shell
mkdir /tmp/ruby && cd /tmp/ruby
curl --remote-name --location --progress-bar "https://cache.ruby-lang.org/pub/ruby/2.7/ruby-2.7.6.tar.gz"
echo 'e7203b0cc09442ed2c08936d483f8ac140ec1c72e37bb5c401646b7866cb5d10 ruby-2.7.6.tar.gz' | sha256sum -c - && tar xzf ruby-2.7.6.tar.gz
cd ruby-2.7.6

./configure --disable-install-rdoc --enable-shared
make
sudo make install
```

## 3. Go

GitLab has several daemons written in Go. To install
GitLab we need a Go compiler. The instructions below assume you use 64-bit
Linux. You can find downloads for other platforms at the
[Go download page](https://go.dev/dl).

```shell
# Remove former Go installation folder
sudo rm -rf /usr/local/go

curl --remote-name --location --progress-bar "https://go.dev/dl/go1.18.8.linux-amd64.tar.gz"
echo '4d854c7bad52d53470cf32f1b287a5c0c441dc6b98306dea27358e099698142a  go1.18.8.linux-amd64.tar.gz' | shasum -a256 -c - && \
  sudo tar -C /usr/local -xzf go1.18.8.linux-amd64.tar.gz
sudo ln -sf /usr/local/go/bin/{go,gofmt} /usr/local/bin/
rm go1.18.8.linux-amd64.tar.gz
```

## 4. Node

GitLab requires the use of Node to compile JavaScript
assets, and Yarn to manage JavaScript dependencies. The current minimum
requirements for these are:

- `node` >= v14.15.0. (We recommend node 16.x as it is faster)
- `yarn` = v1.22.x (Yarn 2 is not supported yet)

In many distributions,
the versions provided by the official package repositories are out of date, so
we must install through the following commands:

```shell
# install node v16.x
curl --location "https://deb.nodesource.com/setup_16.x" | sudo bash -
sudo apt-get install -y nodejs

npm install --global yarn
```

Visit the official websites for [node](https://nodejs.org/en/download/package-manager/) and [yarn](https://classic.yarnpkg.com/en/docs/install/) if you have any trouble with these steps.

## 5. System Users

Create a `git` user for Gitlab:

    sudo adduser --disabled-login --gecos 'GitLab' git

## 6. Database

NOTE:
In GitLab 12.1 and later, only PostgreSQL is supported. In GitLab 14.0 and later, we [require PostgreSQL 12+](requirements.md#postgresql-requirements).

1. Install the database packages.

   For Ubuntu 20.04 and later:

   ```shell
   sudo apt install -y postgresql postgresql-client libpq-dev postgresql-contrib
   ```

   For Ubuntu 18.04 and earlier, the available PostgreSQL doesn't meet the minimum
   version requirement. You must add PostgreSQL's repository:

   ```shell
   wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
   sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
   sudo apt update
   sudo apt -y install postgresql-12 postgresql-client-12 libpq-dev
   ```

1. Verify the PostgreSQL version you have is supported by the version of GitLab you're
   installing:

   ```shell
   psql --version
   ```

1. Start the PostgreSQL service and confirm that the service is running:

   ```shell
   sudo service postgresql start
   sudo service postgresql status
   ```

1. Create a database user for GitLab:

   ```shell
   sudo -u postgres psql -d template1 -c "CREATE USER git CREATEDB;"
   ```

1. Create the `pg_trgm` extension:

   ```shell
   sudo -u postgres psql -d template1 -c "CREATE EXTENSION IF NOT EXISTS pg_trgm;"
   ```

1. Create the `btree_gist` extension (required for GitLab 13.1+):

   ```shell
   sudo -u postgres psql -d template1 -c "CREATE EXTENSION IF NOT EXISTS btree_gist;"
   ```

1. Create the GitLab production database and grant all privileges on the database:

   ```shell
   sudo -u postgres psql -d template1 -c "CREATE DATABASE gitlabhq_production OWNER git;"
   ```

1. Try connecting to the new database with the new user:

   ```shell
   sudo -u git -H psql -d gitlabhq_production
   ```

1. Check if the `pg_trgm` extension is enabled:

   ```sql
   SELECT true AS enabled
   FROM pg_available_extensions
   WHERE name = 'pg_trgm'
   AND installed_version IS NOT NULL;
   ```

   If the extension is enabled this produces the following output:

   ```plaintext
   enabled
   ---------
    t
   (1 row)
   ```

1. Check if the `btree_gist` extension is enabled:

   ```sql
   SELECT true AS enabled
   FROM pg_available_extensions
   WHERE name = 'btree_gist'
   AND installed_version IS NOT NULL;
   ```

   If the extension is enabled this produces the following output:

   ```plaintext
   enabled
   ---------
    t
   (1 row)
   ```

1. Quit the database session:

   ```shell
   gitlabhq_production> \q
   ```

## 7. Redis

See the [requirements page](requirements.md#redis-versions) for the minimum
Redis requirements.

Install Redis with:

```shell
sudo apt-get install redis-server
```

Once done, you can configure Redis:

```shell
# Configure redis to use sockets
sudo cp /etc/redis/redis.conf /etc/redis/redis.conf.orig

# Disable Redis listening on TCP by setting 'port' to 0
sudo sed 's/^port .*/port 0/' /etc/redis/redis.conf.orig | sudo tee /etc/redis/redis.conf

# Enable Redis socket for default Debian / Ubuntu path
echo 'unixsocket /var/run/redis/redis.sock' | sudo tee -a /etc/redis/redis.conf

# Grant permission to the socket to all members of the redis group
echo 'unixsocketperm 770' | sudo tee -a /etc/redis/redis.conf

# Add git to the redis group
sudo usermod -aG redis git
```

### Supervise Redis with systemd

If your distribution uses systemd init and the output of the following command is `notify`,
you must not make any changes:

```shell
systemctl show --value --property=Type redis-server.service
```

If the output is **not** `notify`, run:

```shell
# Configure Redis to not daemonize, but be supervised by systemd instead and disable the pidfile
sudo sed -i \
         -e 's/^daemonize yes$/daemonize no/' \
         -e 's/^supervised no$/supervised systemd/' \
         -e 's/^pidfile/# pidfile/' /etc/redis/redis.conf
sudo chown redis:redis /etc/redis/redis.conf

# Make the same changes to the systemd unit file
sudo mkdir -p /etc/systemd/system/redis-server.service.d
sudo tee /etc/systemd/system/redis-server.service.d/10fix_type.conf <<EOF
[Service]
Type=notify
PIDFile=
EOF

# Reload the redis service
sudo systemctl daemon-reload

# Activate the changes to redis.conf
sudo systemctl restart redis-server.service
```

### Leave Redis unsupervised

If your system uses SysV init, run these commands:

```shell
# Create the directory which contains the socket
sudo mkdir -p /var/run/redis
sudo chown redis:redis /var/run/redis
sudo chmod 755 /var/run/redis

# Persist the directory which contains the socket, if applicable
if [ -d /etc/tmpfiles.d ]; then
  echo 'd  /var/run/redis  0755  redis  redis  10d  -' | sudo tee -a /etc/tmpfiles.d/redis.conf
fi

# Activate the changes to redis.conf
sudo service redis-server restart
```

## 8. GitLab

```shell
# We'll install GitLab into the home directory of the user "git"
cd /home/git
```

### Clone the Source

Clone Community Edition:

```shell
# Clone GitLab repository
sudo -u git -H git clone https://gitlab.com/gitlab-org/gitlab-foss.git -b <X-Y-stable> gitlab
```

Clone Enterprise Edition:

```shell
# Clone GitLab repository
sudo -u git -H git clone https://gitlab.com/gitlab-org/gitlab.git -b <X-Y-stable-ee> gitlab
```

Make sure to replace `<X-Y-stable>` with the stable branch that matches the
version you want to install. For example, if you want to install 11.8 you would
use the branch name `11-8-stable`.

WARNING:
You can change `<X-Y-stable>` to `master` if you want the *bleeding edge* version, but never install `master` on a production server!

### Configure It

```shell
# Go to GitLab installation folder
cd /home/git/gitlab

# Copy the example GitLab config
sudo -u git -H cp config/gitlab.yml.example config/gitlab.yml

# Update GitLab config file, follow the directions at top of the file
sudo -u git -H editor config/gitlab.yml

# Copy the example secrets file
sudo -u git -H cp config/secrets.yml.example config/secrets.yml
sudo -u git -H chmod 0600 config/secrets.yml

# Make sure GitLab can write to the log/ and tmp/ directories
sudo chown -R git log/
sudo chown -R git tmp/
sudo chmod -R u+rwX,go-w log/
sudo chmod -R u+rwX tmp/

# Make sure GitLab can write to the tmp/pids/ and tmp/sockets/ directories
sudo chmod -R u+rwX tmp/pids/
sudo chmod -R u+rwX tmp/sockets/

# Create the public/uploads/ directory
sudo -u git -H mkdir -p public/uploads/

# Make sure only the GitLab user has access to the public/uploads/ directory
# now that files in public/uploads are served by gitlab-workhorse
sudo chmod 0700 public/uploads

# Change the permissions of the directory where CI job logs are stored
sudo chmod -R u+rwX builds/

# Change the permissions of the directory where CI artifacts are stored
sudo chmod -R u+rwX shared/artifacts/

# Change the permissions of the directory where GitLab Pages are stored
sudo chmod -R ug+rwX shared/pages/

# Copy the example Puma config
sudo -u git -H cp config/puma.rb.example config/puma.rb

# Refer to https://github.com/puma/puma#configuration for more information.
# You should scale Puma workers and threads based on the number of CPU
# cores you have available. You can get that number via the `nproc` command.
sudo -u git -H editor config/puma.rb

# Configure Redis connection settings
sudo -u git -H cp config/resque.yml.example config/resque.yml
sudo -u git -H cp config/cable.yml.example config/cable.yml

# Change the Redis socket path if you are not using the default Debian / Ubuntu configuration
sudo -u git -H editor config/resque.yml config/cable.yml
```

Make sure to edit both `gitlab.yml` and `puma.rb` to match your setup.

If you want to use HTTPS, see [Using HTTPS](#using-https) for the additional steps.

### Configure GitLab DB Settings

```shell
sudo -u git cp config/database.yml.postgresql config/database.yml

# Remove host, username, and password lines from config/database.yml.
# Once modified, the `production` settings will be as follows:
#
#   production:
#     main:
#       adapter: postgresql
#       encoding: unicode
#       database: gitlabhq_production
#
sudo -u git -H editor config/database.yml

# Remote PostgreSQL only:
# Update username/password in config/database.yml.
# You only need to adapt the production settings (first part).
# If you followed the database guide then please do as follows:
# Change 'secure password' with the value you have given to $password
# You can keep the double quotes around the password
sudo -u git -H editor config/database.yml

# Make config/database.yml readable to git only
sudo -u git -H chmod o-rwx config/database.yml
```

### Install Gems

NOTE:
As of Bundler 1.5.2, you can invoke `bundle install -jN` (where `N` is the number of your processor cores) and enjoy parallel gems installation with measurable difference in completion time (~60% faster). Check the number of your cores with `nproc`. For more information, see this [post](https://thoughtbot.com/blog/parallel-gem-installing-using-bundler).

Make sure you have `bundle` (run `bundle -v`):

- `>= 1.5.2`, because some [issues](https://devcenter.heroku.com/changelog-items/411) were [fixed](https://github.com/rubygems/bundler/pull/2817) in 1.5.2.
- `< 2.x`.

Install the gems (if you want to use Kerberos for user authentication, omit
`kerberos` in the `--without` option below):

```shell
sudo -u git -H bundle config set --local deployment 'true'
sudo -u git -H bundle config set --local without 'development test mysql aws kerberos'
sudo -u git -H bundle install
```

### Install GitLab Shell

GitLab Shell is an SSH access and repository management software developed specially for GitLab.

```shell
# Run the installation task for gitlab-shell:
sudo -u git -H bundle exec rake gitlab:shell:install RAILS_ENV=production

# By default, the gitlab-shell config is generated from your main GitLab config.
# You can review (and modify) the gitlab-shell config as follows:
sudo -u git -H editor /home/git/gitlab-shell/config.yml
```

If you want to use HTTPS, see [Using HTTPS](#using-https) for the additional steps.

Make sure your hostname can be resolved on the machine itself by either a proper DNS record or an additional line in `/etc/hosts` ("127.0.0.1 hostname"). This might be necessary, for example, if you set up GitLab behind a reverse proxy. If the hostname cannot be resolved, the final installation check fails with `Check GitLab API access: FAILED. code: 401` and pushing commits are rejected with `[remote rejected] master -> master (hook declined)`.

### Install GitLab Workhorse

GitLab-Workhorse uses [GNU Make](https://www.gnu.org/software/make/). The
following command-line installs GitLab-Workhorse in `/home/git/gitlab-workhorse`
which is the recommended location.

```shell
sudo -u git -H bundle exec rake "gitlab:workhorse:install[/home/git/gitlab-workhorse]" RAILS_ENV=production
```

You can specify a different Git repository by providing it as an extra parameter:

```shell
sudo -u git -H bundle exec rake "gitlab:workhorse:install[/home/git/gitlab-workhorse,https://example.com/gitlab-workhorse.git]" RAILS_ENV=production
```

### Install GitLab-Elasticsearch-indexer on Enterprise Edition **(PREMIUM SELF)**

GitLab-Elasticsearch-Indexer uses [GNU Make](https://www.gnu.org/software/make/). The
following command-line installs GitLab-Elasticsearch-Indexer in `/home/git/gitlab-elasticsearch-indexer`
which is the recommended location.

```shell
sudo -u git -H bundle exec rake "gitlab:indexer:install[/home/git/gitlab-elasticsearch-indexer]" RAILS_ENV=production
```

You can specify a different Git repository by providing it as an extra parameter:

```shell
sudo -u git -H bundle exec rake "gitlab:indexer:install[/home/git/gitlab-elasticsearch-indexer,https://example.com/gitlab-elasticsearch-indexer.git]" RAILS_ENV=production
```

The source code first is fetched to the path specified by the first parameter. Then a binary is built under its `bin` directory.
You must then update `gitlab.yml`'s `production -> elasticsearch -> indexer_path` setting to point to that binary.

### Install GitLab Pages

GitLab Pages uses [GNU Make](https://www.gnu.org/software/make/). This step is optional and only needed if you wish to host static sites from within GitLab. The following commands install GitLab Pages in `/home/git/gitlab-pages`. For additional setup steps, consult the [administration guide](https://gitlab.com/gitlab-org/gitlab/-/blob/master/doc/administration/pages/source.md) for your version of GitLab as the GitLab Pages daemon can be run several different ways.

```shell
cd /home/git
sudo -u git -H git clone https://gitlab.com/gitlab-org/gitlab-pages.git
cd gitlab-pages
sudo -u git -H git checkout v$(</home/git/gitlab/GITLAB_PAGES_VERSION)
sudo -u git -H make
```

### Install Gitaly

```shell
# Fetch Gitaly source with Git and compile with Go
cd /home/git/gitlab
sudo -u git -H bundle exec rake "gitlab:gitaly:install[/home/git/gitaly,/home/git/repositories]" RAILS_ENV=production
```

You can specify a different Git repository by providing it as an extra parameter:

```shell
sudo -u git -H bundle exec rake "gitlab:gitaly:install[/home/git/gitaly,/home/git/repositories,https://example.com/gitaly.git]" RAILS_ENV=production
```

Next, make sure that Gitaly is configured:

```shell
# Restrict Gitaly socket access
sudo chmod 0700 /home/git/gitlab/tmp/sockets/private
sudo chown git /home/git/gitlab/tmp/sockets/private

# If you are using non-default settings, you need to update config.toml
cd /home/git/gitaly
sudo -u git -H editor config.toml
```

For more information about configuring Gitaly see
[the Gitaly documentation](../administration/gitaly/index.md).

### Install the service

GitLab has always supported SysV init scripts, which are widely supported and portable, but now systemd is the standard for service supervision and is used by all major Linux distributions. You should use native systemd services if you can to benefit from automatic restarts, better sandboxing and resource control.

#### Install systemd units

Use these steps if you use systemd as init. Otherwise, follow the [SysV init script steps](#install-sysv-init-script).

Copy the services and run `systemctl daemon-reload` so that systemd picks them up:

```shell
cd /home/git/gitlab
sudo mkdir -p /usr/local/lib/systemd/system
sudo cp lib/support/systemd/* /usr/local/lib/systemd/system/
sudo systemctl daemon-reload
```

The units provided by GitLab make very little assumptions about where you are running Redis and PostgreSQL.

If you installed GitLab in another directory or as a user other than the default, you must change these values in the units as well.

For example, if you're running Redis and PostgreSQL on the same machine as GitLab, you should:

- Edit the Puma service:

  ```shell
  sudo systemctl edit gitlab-puma.service
  ```

  In the editor that opens, add the following and save the file:

  ```plaintext
  [Unit]
  Wants=redis-server.service postgresql.service
  After=redis-server.service postgresql.service
  ```

- Edit the Sidekiq service:

  ```shell
  sudo systemctl edit gitlab-sidekiq.service
  ```

  Add the following and save the file:

  ```plaintext
  [Unit]
  Wants=redis-server.service postgresql.service
  After=redis-server.service postgresql.service
  ```

`systemctl edit` installs drop-in configuration files at `/etc/systemd/system/<name of the unit>.d/override.conf`, so your local configuration is not overwritten when updating the unit files later. To split up your drop-in configuration files, you can add the above snippets to `.conf` files under `/etc/systemd/system/<name of the unit>.d/`.

If you manually made changes to the unit files or added drop-in configuration files (without using `systemctl edit`), run the following command for them to take effect:

```shell
sudo systemctl daemon-reload
```

Make GitLab start on boot:

```shell
sudo systemctl enable gitlab.target
```

#### Install SysV init script

Use these steps if you use the SysV init script. If you use systemd, follow the [systemd unit steps](#install-systemd-units).

Download the init script (is `/etc/init.d/gitlab`):

```shell
cd /home/git/gitlab
sudo cp lib/support/init.d/gitlab /etc/init.d/gitlab
```

And if you are installing with a non-default folder or user, copy and edit the defaults file:

```shell
sudo cp lib/support/init.d/gitlab.default.example /etc/default/gitlab
```

If you installed GitLab in another directory or as a user other than the default, you should change these settings in `/etc/default/gitlab`. Do not edit `/etc/init.d/gitlab` as it is changed on upgrade.

Make GitLab start on boot:

```shell
sudo update-rc.d gitlab defaults 21
# or if running this on a machine running systemd
sudo systemctl daemon-reload
sudo systemctl enable gitlab.service
```

### Set up Logrotate

```shell
sudo cp lib/support/logrotate/gitlab /etc/logrotate.d/gitlab
```

### Start Gitaly

Gitaly must be running for the next section.

- To start Gitaly using systemd:

  ```shell
  sudo systemctl start gitlab-gitaly.service
  ```

- To manually start Gitaly for SysV:

  ```shell
  gitlab_path=/home/git/gitlab
  gitaly_path=/home/git/gitaly

  sudo -u git -H sh -c "$gitlab_path/bin/daemon_with_pidfile $gitlab_path/tmp/pids/gitaly.pid \
    $gitaly_path/_build/bin/gitaly $gitaly_path/config.toml >> $gitlab_path/log/gitaly.log 2>&1 &"
  ```

### Initialize Database and Activate Advanced Features

```shell
cd /home/git/gitlab
sudo -u git -H bundle exec rake gitlab:setup RAILS_ENV=production
# Type 'yes' to create the database tables.

# or you can skip the question by adding force=yes
sudo -u git -H bundle exec rake gitlab:setup RAILS_ENV=production force=yes

# When done, you see 'Administrator account created:'
```

You can set the Administrator/root password and email by supplying them in environmental variables, `GITLAB_ROOT_PASSWORD` and `GITLAB_ROOT_EMAIL` respectively, as seen below. If you don't set the password (and it is set to the default one), wait to expose GitLab to the public internet until the installation is done and you've logged into the server the first time. During the first login, you are forced to change the default password. An Enterprise Edition license may also be installed at this time by supplying a full path in the `GITLAB_LICENSE_FILE` environment variable.

```shell
sudo -u git -H bundle exec rake gitlab:setup RAILS_ENV=production GITLAB_ROOT_PASSWORD=yourpassword GITLAB_ROOT_EMAIL=youremail GITLAB_LICENSE_FILE="/path/to/license"
```

### Secure `secrets.yml`

The `secrets.yml` file stores encryption keys for sessions and secure variables.
Backup `secrets.yml` someplace safe, but don't store it in the same place as your database backups.
Otherwise, your secrets are exposed if one of your backups is compromised.

### Check Application Status

Check if GitLab and its environment are configured correctly:

```shell
sudo -u git -H bundle exec rake gitlab:env:info RAILS_ENV=production
```

### Compile Assets

```shell
sudo -u git -H yarn install --production --pure-lockfile
sudo -u git -H bundle exec rake gitlab:assets:compile RAILS_ENV=production NODE_ENV=production
```

If `rake` fails with `JavaScript heap out of memory` error, try to run it with `NODE_OPTIONS` set as follows.

```shell
sudo -u git -H bundle exec rake gitlab:assets:compile RAILS_ENV=production NODE_ENV=production NODE_OPTIONS="--max_old_space_size=4096"
```

### Start Your GitLab Instance

```shell
# For systems running systemd
sudo systemctl start gitlab.target

# For systems running SysV init
sudo service gitlab start
```

## 9. NGINX

NGINX is the officially supported web server for GitLab. If you cannot or do not want to use NGINX as your web server, see [GitLab recipes](https://gitlab.com/gitlab-org/gitlab-recipes/).

### Installation

```shell
sudo apt-get install -y nginx
```

### Site Configuration

Copy the example site configuration:

```shell
sudo cp lib/support/nginx/gitlab /etc/nginx/sites-available/gitlab
sudo ln -s /etc/nginx/sites-available/gitlab /etc/nginx/sites-enabled/gitlab
```

Make sure to edit the configuration file to match your setup. Also, ensure that you match your paths to GitLab, especially if installing for a user other than the `git` user:

```shell
# Change YOUR_SERVER_FQDN to the fully-qualified
# domain name of your host serving GitLab.
#
# Remember to match your paths to GitLab, especially
# if installing for a user other than 'git'.
#
# If using Ubuntu default nginx install:
# either remove the default_server from the listen line
# or else sudo rm -f /etc/nginx/sites-enabled/default
sudo editor /etc/nginx/sites-available/gitlab
```

If you intend to enable GitLab Pages, there is a separate NGINX configuration you need
to use. Read all about the needed configuration at the
[GitLab Pages administration guide](../administration/pages/index.md).

If you want to use HTTPS, replace the `gitlab` NGINX configuration with `gitlab-ssl`. See [Using HTTPS](#using-https) for HTTPS configuration details.

For the NGINX to be able to read the GitLab-Workhorse socket, you must make sure, that the `www-data` user can read the socket, which is owned by the GitLab user. This is achieved, if it is world-readable, for example that it has permissions `0755`, which is the default. `www-data` also must be able to list the parent directories.

### Test Configuration

Validate your `gitlab` or `gitlab-ssl` NGINX configuration file with the following command:

```shell
sudo nginx -t
```

You should receive `syntax is okay` and `test is successful` messages. If you
receive error messages, check your `gitlab` or `gitlab-ssl` NGINX configuration
file for typos, as indicated in the provided error message.

Verify that the installed version is greater than 1.12.1:

```shell
nginx -v
```

If it's lower, you may receive the error below:

```plaintext
nginx: [emerg] unknown "start$temp=[filtered]$rest" variable
nginx: configuration file /etc/nginx/nginx.conf test failed
```

### Restart

```shell
# For systems running systemd
sudo systemctl restart nginx.service

# For systems running SysV init
sudo service nginx restart
```

## Post-install

### Double-check Application Status

To make sure you didn't miss anything run a more thorough check with:

```shell
sudo -u git -H bundle exec rake gitlab:check RAILS_ENV=production
```

If all items are green, congratulations on successfully installing GitLab!

NOTE:
Supply the `SANITIZE=true` environment variable to `gitlab:check` to omit project names from the output of the check command.

### Initial Login

Visit YOUR_SERVER in your web browser for your first GitLab login.

If you didn't [provide a root password during setup](#initialize-database-and-activate-advanced-features),
you are redirected to a password reset screen to provide the password for the
initial administrator account. Enter your desired password and you are
redirected back to the login screen.

The default account's username is **root**. Provide the password you created
earlier and login. After login, you can change the username if you wish.

**Enjoy!**

To start and stop GitLab when using:

- systemd units: use `sudo systemctl start gitlab.target` or `sudo systemctl stop gitlab.target`.
- The SysV init script: use `sudo service gitlab start` or `sudo service gitlab stop`.

### Install the product documentation

This is an optional step. See how to [self-host the product documentation](../administration/docs_self_host.md).

## Advanced Setup Tips

### Relative URL support

See the [Relative URL documentation](relative_url.md) for more information on
how to configure GitLab with a relative URL.

### Using HTTPS

To use GitLab with HTTPS:

1. In `gitlab.yml`:
   1. Set the `port` option in section 1 to `443`.
   1. Set the `https` option in section 1 to `true`.
1. In the `config.yml` of GitLab Shell:
   1. Set `gitlab_url` option to the HTTPS endpoint of GitLab (for example, `https://git.example.com`).
   1. Set the certificates using either the `ca_file` or `ca_path` option.
1. Use the `gitlab-ssl` NGINX example configuration instead of the `gitlab` configuration.
   1. Update `YOUR_SERVER_FQDN`.
   1. Update `ssl_certificate` and `ssl_certificate_key`.
   1. Review the configuration file and consider applying other security and performance enhancing features.

Using a self-signed certificate is discouraged. If you must use one,
follow the normal directions and generate a self-signed SSL certificate:

   ```shell
   mkdir -p /etc/nginx/ssl/
   cd /etc/nginx/ssl/
   sudo openssl req -newkey rsa:2048 -x509 -nodes -days 3560 -out gitlab.crt -keyout gitlab.key
   sudo chmod o-r gitlab.key
   ```

### Enable Reply by email

See the ["Reply by email" documentation](../administration/reply_by_email.md) for more information on how to set this up.

### LDAP Authentication

You can configure LDAP authentication in `config/gitlab.yml`. Restart GitLab after editing this file.

### Using Custom OmniAuth Providers

See the [OmniAuth integration documentation](../integration/omniauth.md).

### Build your projects

GitLab can build your projects. To enable that feature, you need runners to do that for you.
See the [GitLab Runner section](https://docs.gitlab.com/runner/) to install it.

### Adding your Trusted Proxies

If you are using a reverse proxy on a separate machine, you may want to add the
proxy to the trusted proxies list. Otherwise users appear signed in from the
proxy's IP address.

You can add trusted proxies in `config/gitlab.yml` by customizing the `trusted_proxies`
option in section 1. Save the file and [reconfigure GitLab](../administration/restart_gitlab.md)
for the changes to take effect.

### Custom Redis Connection

If you'd like to connect to a Redis server on a non-standard port or a different host, you can configure its connection string via the `config/resque.yml` file.

```yaml
# example
production:
  url: redis://redis.example.tld:6379
```

If you want to connect the Redis server via socket, use the `unix:` URL scheme and the path to the Redis socket file in the `config/resque.yml` file.

```yaml
# example
production:
  url: unix:/path/to/redis/socket
```

Also, you can use environment variables in the `config/resque.yml` file:

```yaml
# example
production:
  url: <%= ENV.fetch('GITLAB_REDIS_URL') %>
```

### Custom SSH Connection

If you are running SSH on a non-standard port, you must change the GitLab user's SSH configuration.

```plaintext
# Add to /home/git/.ssh/config
host localhost          # Give your setup a name (here: override localhost)
    user git            # Your remote git user
    port 2222           # Your port number
    hostname 127.0.0.1; # Your server name or IP
```

You must also change the corresponding options (for example, `ssh_user`, `ssh_host`, `admin_uri`) in the `config/gitlab.yml` file.

### Additional Markup Styles

Apart from the always supported Markdown style, there are other rich text files that GitLab can display. But you might have to install a dependency to do so. See the [`github-markup` gem README](https://github.com/gitlabhq/markup#markups) for more information.

### Using Sidekiq instead of Sidekiq Cluster

As of GitLab 12.10, Source installations are using `bin/sidekiq-cluster` for managing Sidekiq processes.
Using Sidekiq directly is still supported until 14.0. So if you're experiencing issues:

1. Edit the system `init.d` script to remove the `SIDEKIQ_WORKERS` flag. If you have `/etc/default/gitlab`, then you should edit it instead.
1. Restart GitLab.
1. [Create an issue](https://gitlab.com/gitlab-org/gitlab/-/issues/-/new) describing the problem.

### Prometheus server setup

You can configure the Prometheus server in `config/gitlab.yml`:

```yaml
# example
prometheus:
  enabled: true
  server_address: '10.1.2.3:9090'
```

## Troubleshooting

### "You appear to have cloned an empty repository."

If you see this message when attempting to clone a repository hosted by GitLab,
this is likely due to an outdated NGINX or Apache configuration, or a missing or
misconfigured GitLab Workhorse instance. Double-check that you've
[installed Go](#3-go), [installed GitLab Workhorse](#install-gitlab-workhorse),
and correctly [configured NGINX](#site-configuration).

### `google-protobuf` "LoadError: /lib/x86_64-linux-gnu/libc.so.6: version 'GLIBC_2.14' not found"

This can happen on some platforms for some versions of the
`google-protobuf` gem. The workaround is to install a source-only
version of this gem.

First, you must find the exact version of `google-protobuf` that your
GitLab installation requires:

```shell
cd /home/git/gitlab

# Only one of the following two commands will print something. It
# will look like: * google-protobuf (3.2.0)
bundle list | grep google-protobuf
bundle check | grep google-protobuf
```

Below, `3.2.0` is used as an example. Replace it with the version number
you found above:

```shell
cd /home/git/gitlab
sudo -u git -H gem install google-protobuf --version 3.2.0 --platform ruby
```

Finally, you can test whether `google-protobuf` loads correctly. The
following should print 'OK'.

```shell
sudo -u git -H bundle exec ruby -rgoogle/protobuf -e 'puts :OK'
```

If the `gem install` command fails, you may need to install the developer
tools of your OS.

On Debian/Ubuntu:

```shell
sudo apt-get install build-essential libgmp-dev
```

On RedHat/CentOS:

```shell
sudo yum groupinstall 'Development Tools'
```

### Error compiling GitLab assets

While compiling assets, you may receive the following error message:

```plaintext
Killed
error Command failed with exit code 137.
```

This can occur when Yarn kills a container that runs out of memory. To fix this:

1. Increase your system's memory to at least 8 GB.

1. Run this command to clean the assets:

   ```shell
   sudo -u git -H bundle exec rake gitlab:assets:clean RAILS_ENV=production NODE_ENV=production
   ```

1. Run the `yarn` command again to resolve any conflicts:

   ```shell
   sudo -u git -H yarn install --production --pure-lockfile
   ```

1. Recompile the assets:

   ```shell
   sudo -u git -H bundle exec rake gitlab:assets:compile RAILS_ENV=production NODE_ENV=production
   ```



## Steps after installing GitLab

Here are a few resources you might want to check out after completing the
installation.

### Email and notifications

- [SMTP](https://docs.gitlab.com/omnibus/settings/smtp.html): Configure SMTP
  for proper email notifications support.

### CI/CD

- [Set up runners](https://docs.gitlab.com/runner/): Set up one or more GitLab
  Runners, the agents that are responsible for all of the GitLab CI/CD features.
- [GitLab Pages](../administration/pages/index.md): Configure GitLab Pages to
  allow hosting of static sites.
- [GitLab Registry](../administration/packages/container_registry.md): Set up the
  GitLab Container Registry so every project can have its own space to store Docker
  images.
- [GitLab Dependency Proxy](../administration/packages/dependency_proxy.md): Set up the dependency
  proxy so you can cache container images from Docker Hub for faster, more reliable builds.

### Security

- [Secure GitLab](../security/index.md):
  Recommended practices to secure your GitLab instance.
- Sign up for the GitLab [Security Newsletter](https://about.gitlab.com/company/preference-center/) to get notified for security updates upon release.

### Authentication

- [LDAP](../administration/auth/ldap/index.md): Configure LDAP to be used as
  an authentication mechanism for GitLab.
- [SAML and OAuth](../integration/omniauth.md): Authenticate via online services like Okta, Google, Azure AD, and more.

### Backup and upgrade

- [Back up and restore GitLab](../raketasks/backup_restore.md): Learn the different
  ways you can back up or restore GitLab.
- [Upgrade GitLab](../update/index.md): Every 22nd of the month, a new feature-rich GitLab version
  is released. Learn how to upgrade to it, or to an interim release that contains a security fix.
- [Release and maintenance policy](../policy/maintenance.md): Learn about GitLab
  policies governing version naming, as well as release pace for major, minor, patch,
  and security releases.

### License

- [Add a license](../user/admin_area/license.md) or [start a free trial](https://about.gitlab.com/free-trial/):
  Activate all GitLab Enterprise Edition functionality with a license.
- [Pricing](https://about.gitlab.com/pricing/): Pricing for the different tiers.

### Cross-repository Code Search

- [Advanced Search](../integration/advanced_search/elasticsearch.md): Leverage [Elasticsearch](https://www.elastic.co/) or [OpenSearch](https://opensearch.org/) for
  faster, more advanced code search across your entire GitLab instance.

### Scaling and replication

- [Scaling GitLab](../administration/reference_architectures/index.md):
  GitLab supports several different types of clustering.
- [Geo replication](../administration/geo/index.md):
  Geo is the solution for widely distributed development teams.



## Managing PostgreSQL extensions 

This guide documents how to manage PostgreSQL extensions for installations with an external
PostgreSQL database.

You must load the following extensions into the main GitLab database (defaults to `gitlabhq_production`):

| Extension    | Minimum GitLab version |
|--------------|------------------------|
| `pg_trgm`    | 8.6                    |
| `btree_gist` | 13.1                   |
| `plpgsql`    | 11.7                   |

If you are using [GitLab Geo](https://about.gitlab.com/solutions/geo/), you must load the following
extensions into all secondary tracking databases (defaults to `gitlabhq_geo_production`):

| Extension    | Minimum GitLab version |
|--------------|------------------------|
| `plpgsql`    | 9.0                    |

In order to install extensions, PostgreSQL requires the user to have superuser privileges.
Typically, the GitLab database user is not a superuser. Therefore, regular database migrations
cannot be used in installing extensions and instead, extensions have to be installed manually
prior to upgrading GitLab to a newer version.

### Installing PostgreSQL extensions manually

In order to install a PostgreSQL extension, this procedure should be followed:

1. Connect to the GitLab PostgreSQL database using a superuser, for example:

   ```shell
   sudo gitlab-psql -d gitlabhq_production
   ```

1. Install the extension (`btree_gist` in this example) using [`CREATE EXTENSION`](https://www.postgresql.org/docs/11/sql-createextension.html):

   ```sql
   CREATE EXTENSION IF NOT EXISTS btree_gist
   ```

1. Verify installed extensions:

   ```shell
    gitlabhq_production=# \dx
                                        List of installed extensions
        Name    | Version |   Schema   |                            Description
    ------------+---------+------------+-------------------------------------------------------------------
    btree_gist | 1.5     | public     | support for indexing common datatypes in GiST
    pg_trgm    | 1.4     | public     | text similarity measurement and index searching based on trigrams
    plpgsql    | 1.0     | pg_catalog | PL/pgSQL procedural language
    (3 rows)
   ```

On some systems you may need to install an additional package (for example,
`postgresql-contrib`) for certain extensions to become available.

### Typical failure scenarios

The following is an example of a new GitLab installation failing because the extension hasn't been
installed first.

```shell
---- Begin output of "bash"  "/tmp/chef-script20210513-52940-d9b1gs" ----
STDOUT: psql:/opt/gitlab/embedded/service/gitlab-rails/db/structure.sql:9: ERROR:  permission denied to create extension "btree_gist"
HINT:  Must be superuser to create this extension.
rake aborted!
failed to execute:
psql -v ON_ERROR_STOP=1 -q -X -f /opt/gitlab/embedded/service/gitlab-rails/db/structure.sql --single-transaction gitlabhq_production
```

The following is an example of a situation when the extension hasn't been installed before running migrations.
In this scenario, the database migration fails to create the extension `btree_gist` because of insufficient
privileges.

```shell
== 20200515152649 EnableBtreeGistExtension: migrating =========================
-- execute("CREATE EXTENSION IF NOT EXISTS btree_gist")

GitLab requires the PostgreSQL extension 'btree_gist' installed in database 'gitlabhq_production', but
the database user is not allowed to install the extension.

You can either install the extension manually using a database superuser:

  CREATE EXTENSION IF NOT EXISTS btree_gist

Or, you can solve this by logging in to the GitLab database (gitlabhq_production) using a superuser and running:

    ALTER regular WITH SUPERUSER

This query will grant the user superuser permissions, ensuring any database extensions
can be installed through migrations.
```

To recover from failed migrations, the extension must be installed manually by a superuser, and the
GitLab upgrade completed by [re-running the database migrations](../administration/raketasks/maintenance.md#run-incomplete-database-migrations):

```shell
sudo gitlab-rake db:migrate
```



<br/><a name="Administration"></a>

# Administration

## selected topics (personal view)
### Ngix setup

* how to handle SSL?

      sudo mkdir -p /etc/gitlab/ssl
      sudo chmod 755 /etc/gitlab/ssl
      sudo cp gitlab.example.com.key gitlab.example.com.crt /etc/gitlab/ssl/

      /etc/gitlab/ssl/gitlab.example.com.key
      /etc/gitlab/ssl/gitlab.example.com.crt

      refer to the part of SSL : linux_network.md




<br/><a name="Docker"></a>
==============================================================================================

# GitLab Docker

1. Prerequisites
1. Installation
1. configuration
1. upgrade
1. Back up GitLab
1. Troubleshooting


## Prerequisites

### 


### GitLab Docker image

The GitLab Docker images are monolithic images of GitLab running all the
necessary services in a single container. If you instead want to install GitLab
on Kubernetes, see [GitLab Helm Charts](https://docs.gitlab.com/charts/).

Find the GitLab official Docker image at:

- [GitLab Docker image in Docker Hub](https://hub.docker.com/r/gitlab/gitlab-ee/)

The Docker images don't include a mail transport agent (MTA). The recommended
solution is to add an MTA (such as Postfix or Sendmail) running in a separate
container. As another option, you can install an MTA directly in the GitLab
container, but this adds maintenance overhead as you'll likely need to reinstall
the MTA after every upgrade or restart.

In the following examples, if you want to use the latest RC image, use
`gitlab/gitlab-ee:rc` instead.

### docker desktop installation
Docker desktop is required. See the [official installation documentation](https://docs.docker.com/get-docker/).

### Set up the volumes location

Before setting everything else, configure a new environment variable `$GITLAB_HOME`
pointing to the directory where the configuration, logs, and data files will reside.
Ensure that the directory exists and appropriate permission have been granted.

For Linux users, set the path to `/srv/gitlab`:

```shell
export GITLAB_HOME=/srv/gitlab
```

For macOS users, use the user's `$HOME/gitlab` directory:

```shell
export GITLAB_HOME=$HOME/gitlab
```

The GitLab container uses host mounted volumes to store persistent data:

| Local location       | Container location | Usage                                       |
|----------------------|--------------------|---------------------------------------------|
| `$GITLAB_HOME/data`  | `/var/opt/gitlab`  | For storing application data.               |
| `$GITLAB_HOME/logs`  | `/var/log/gitlab`  | For storing logs.                           |
| `$GITLAB_HOME/config`| `/etc/gitlab`      | For storing the GitLab configuration files. |



## Installation

The GitLab Docker images can be run in multiple ways:

- [Using Docker Engine](#install-gitlab-using-docker-engine)
- [Using Docker Compose](#install-gitlab-using-docker-compose)
- [Using Docker swarm mode](#install-gitlab-using-docker-swarm-mode)

### Install GitLab using Docker Engine

You can fine tune these directories to meet your requirements.
Once you've set up the `GITLAB_HOME` variable, you can run the image:

```shell
sudo docker run --detach \
  --hostname gitlab.example.com \
  --publish 443:443 --publish 80:80 --publish 22:22 \
  --name gitlab \
  --restart always \
  --volume $GITLAB_HOME/config:/etc/gitlab \
  --volume $GITLAB_HOME/logs:/var/log/gitlab \
  --volume $GITLAB_HOME/data:/var/opt/gitlab \
  --shm-size 256m \
  gitlab/gitlab-ee:latest
```

This will download and start a GitLab container and publish ports needed to
access SSH, HTTP and HTTPS. All GitLab data will be stored as subdirectories of
`$GITLAB_HOME`. The container will automatically `restart` after a system reboot.

If you are on SELinux, then run this instead:

```shell
sudo docker run --detach \
  --hostname gitlab.example.com \
  --publish 443:443 --publish 80:80 --publish 22:22 \
  --name gitlab \
  --restart always \
  --volume $GITLAB_HOME/config:/etc/gitlab:Z \
  --volume $GITLAB_HOME/logs:/var/log/gitlab:Z \
  --volume $GITLAB_HOME/data:/var/opt/gitlab:Z \
  --shm-size 256m \
  gitlab/gitlab-ee:latest
```

This will ensure that the Docker process has enough permissions to create the
configuration files in the mounted volumes.

If you're using the [Kerberos integration](../integration/kerberos.md) **(PREMIUM ONLY)**,
you must also publish your Kerberos port (for example, `--publish 8443:8443`).
Failing to do so prevents Git operations with Kerberos.

The initialization process may take a long time. You can track this
process with:

```shell
sudo docker logs -f gitlab
```

After starting a container you can visit `gitlab.example.com` (or
`http://192.168.59.103` if you used boot2docker on macOS). It might take a while
before the Docker container starts to respond to queries.

Visit the GitLab URL, and sign in with the username `root`
and the password from the following command:

```shell
sudo docker exec -it gitlab grep 'Password:' /etc/gitlab/initial_root_password
```

NOTE:
The password file will be automatically deleted in the first reconfigure run after 24 hours.

### Install GitLab using Docker Compose

With [Docker Compose](https://docs.docker.com/compose/) you can easily configure,
install, and upgrade your Docker-based GitLab installation:

1. [Install Docker Compose](https://docs.docker.com/compose/install/).
1. Create a `docker-compose.yml` file:

   ```yaml
   version: '3.6'
   services:
     web:
       image: 'gitlab/gitlab-ee:latest'
       restart: always
       hostname: 'gitlab.example.com'
       environment:
         GITLAB_OMNIBUS_CONFIG: |
           external_url 'https://gitlab.example.com'
           # Add any other gitlab.rb configuration here, each on its own line
       ports:
         - '80:80'
         - '443:443'
         - '22:22'
       volumes:
         - '$GITLAB_HOME/config:/etc/gitlab'
         - '$GITLAB_HOME/logs:/var/log/gitlab'
         - '$GITLAB_HOME/data:/var/opt/gitlab'
       shm_size: '256m'
   ```

1. Make sure you are in the same directory as `docker-compose.yml` and start
   GitLab:

   ```shell
   docker compose up -d
   ```

NOTE:
Read the ["Pre-configure Docker container"](#pre-configure-docker-container) section
to see how the `GITLAB_OMNIBUS_CONFIG` variable works.

Below is another `docker-compose.yml` example with GitLab running on a custom
HTTP and SSH port. Notice how the `GITLAB_OMNIBUS_CONFIG` variables match the
`ports` section:

```yaml
version: '3.6'
services:
  web:
    image: 'gitlab/gitlab-ee:latest'
    restart: always
    hostname: 'gitlab.example.com'
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'http://gitlab.example.com:8929'
        gitlab_rails['gitlab_shell_ssh_port'] = 2224
    ports:
      - '8929:8929'
      - '2224:22'
    volumes:
      - '$GITLAB_HOME/config:/etc/gitlab'
      - '$GITLAB_HOME/logs:/var/log/gitlab'
      - '$GITLAB_HOME/data:/var/opt/gitlab'
    shm_size: '256m'
```

This is the same as using `--publish 8929:8929 --publish 2224:22`.

### Install GitLab using Docker swarm mode

With [Docker swarm mode](https://docs.docker.com/engine/swarm/), you can easily
configure and deploy your
Docker-based GitLab installation in a swarm cluster.

In swarm mode you can leverage [Docker secrets](https://docs.docker.com/engine/swarm/secrets/)
and [Docker configurations](https://docs.docker.com/engine/swarm/configs/) to efficiently and securely deploy your GitLab instance.
Secrets can be used to securely pass your initial root password without exposing it as an environment variable.
Configurations can help you to keep your GitLab image as generic as possible.

Here's an example that deploys GitLab with four runners as a [stack](https://docs.docker.com/get-started/swarm-deploy/#describe-apps-using-stack-files), using secrets and configurations:

1. [Set up a Docker swarm](https://docs.docker.com/engine/swarm/swarm-tutorial/).
1. Create a `docker-compose.yml` file:

   ```yaml
   version: "3.6"
   services:
     gitlab:
       image: gitlab/gitlab-ee:latest
       ports:
         - "22:22"
         - "80:80"
         - "443:443"
       volumes:
         - $GITLAB_HOME/data:/var/opt/gitlab
         - $GITLAB_HOME/logs:/var/log/gitlab
         - $GITLAB_HOME/config:/etc/gitlab
       shm_size: '256m'
       environment:
         GITLAB_OMNIBUS_CONFIG: "from_file('/omnibus_config.rb')"
       configs:
         - source: gitlab
           target: /omnibus_config.rb
       secrets:
         - gitlab_root_password
     gitlab-runner:
       image: gitlab/gitlab-runner:alpine
       deploy:
         mode: replicated
         replicas: 4
   configs:
     gitlab:
       file: ./gitlab.rb
   secrets:
     gitlab_root_password:
       file: ./root_password.txt
   ```

   For simplicity reasons, the `network` configuration was omitted.
   More information can be found in the official [Compose file reference](https://docs.docker.com/compose/compose-file/).

1. Create a `gitlab.rb` file:

   ```ruby
   external_url 'https://my.domain.com/'
   gitlab_rails['initial_root_password'] = File.read('/run/secrets/gitlab_root_password').gsub("\n", "")
   ```

1. Create a `root_password.txt` file:

   ```plaintext
   MySuperSecretAndSecurePass0rd!
   ```

1. Make sure you are in the same directory as `docker-compose.yml` and run:

   ```shell
   docker stack deploy --compose-file docker-compose.yml mystack
   ```

### Install the product documentation

This is an optional step. See how to [self-host the product documentation](../administration/docs_self_host.md#self-host-the-product-documentation-with-docker).

## Configuration

This container uses the official Omnibus GitLab package, so all configuration
is done in the unique configuration file `/etc/gitlab/gitlab.rb`.

To access the GitLab configuration file, you can start a shell session in the
context of a running container. This will allow you to browse all directories
and use your favorite text editor:

```shell
sudo docker exec -it gitlab /bin/bash
```

You can also just edit `/etc/gitlab/gitlab.rb`:

```shell
sudo docker exec -it gitlab editor /etc/gitlab/gitlab.rb
```

Once you open `/etc/gitlab/gitlab.rb` make sure to set the `external_url` to
point to a valid URL.

To receive emails from GitLab you have to configure the
[SMTP settings](https://docs.gitlab.com/omnibus/settings/smtp.html) because the GitLab Docker image doesn't
have an SMTP server installed. You may also be interested in
[enabling HTTPS](https://docs.gitlab.com/omnibus/settings/ssl.html).

After you make all the changes you want, you will need to restart the container to reconfigure GitLab:

```shell
sudo docker restart gitlab
```

GitLab will reconfigure itself whenever the container starts.
For more options about configuring GitLab, check the
[configuration documentation](https://docs.gitlab.com/omnibus/settings/configuration.html).

### Pre-configure Docker container

You can pre-configure the GitLab Docker image by adding the environment variable
`GITLAB_OMNIBUS_CONFIG` to Docker run command. This variable can contain any
`gitlab.rb` setting and is evaluated before the loading of the container's
`gitlab.rb` file. This behavior allows you to configure the external GitLab URL,
and make database configuration or any other option from the
[Omnibus GitLab template](https://gitlab.com/gitlab-org/omnibus-gitlab/blob/master/files/gitlab-config-template/gitlab.rb.template).
The settings contained in `GITLAB_OMNIBUS_CONFIG` aren't written to the
`gitlab.rb` configuration file, and are evaluated on load.

Here's an example that sets the external URL and enables LFS while starting
the container:

```shell
sudo docker run --detach \
  --hostname gitlab.example.com \
  --env GITLAB_OMNIBUS_CONFIG="external_url 'http://my.domain.com/'; gitlab_rails['lfs_enabled'] = true;" \
  --publish 443:443 --publish 80:80 --publish 22:22 \
  --name gitlab \
  --restart always \
  --volume $GITLAB_HOME/config:/etc/gitlab \
  --volume $GITLAB_HOME/logs:/var/log/gitlab \
  --volume $GITLAB_HOME/data:/var/opt/gitlab \
  --shm-size 256m \
  gitlab/gitlab-ee:latest
```

Note that every time you execute a `docker run` command, you need to provide
the `GITLAB_OMNIBUS_CONFIG` option. The content of `GITLAB_OMNIBUS_CONFIG` is
_not_ preserved between subsequent runs.

### Use tagged versions of GitLab

Tagged versions of the GitLab Docker images are also provided.
To see all available tags see:

- [GitLab CE tags](https://hub.docker.com/r/gitlab/gitlab-ce/tags/)
- [GitLab EE tags](https://hub.docker.com/r/gitlab/gitlab-ee/tags/)

To use a specific tagged version, replace `gitlab/gitlab-ee:latest` with
the GitLab version you want to run, for example `gitlab/gitlab-ee:12.1.3-ce.0`.

### Run GitLab on a public IP address

You can make Docker to use your IP address and forward all traffic to the
GitLab container by modifying the `--publish` flag.

To expose GitLab on IP `198.51.100.1`:

```shell
sudo docker run --detach \
  --hostname gitlab.example.com \
  --publish 198.51.100.1:443:443 \
  --publish 198.51.100.1:80:80 \
  --publish 198.51.100.1:22:22 \
  --name gitlab \
  --restart always \
  --volume $GITLAB_HOME/config:/etc/gitlab \
  --volume $GITLAB_HOME/logs:/var/log/gitlab \
  --volume $GITLAB_HOME/data:/var/opt/gitlab \
  --shm-size 256m \
  gitlab/gitlab-ee:latest
```

You can then access your GitLab instance at `http://198.51.100.1/` and `https://198.51.100.1/`.

### Expose GitLab on different ports

GitLab will occupy [some ports](../administration/package_information/defaults.md)
inside the container.

If you want to use a different host port than `80` (HTTP) or `443` (HTTPS),
you need to add a separate `--publish` directive to the `docker run` command.

For example, to expose the web interface on the host's port `8929`, and the SSH service on
port `2289`:

1. Use the following `docker run` command:

   ```shell
   sudo docker run --detach \
     --hostname gitlab.example.com \
     --publish 8929:8929 --publish 2289:22 \
     --name gitlab \
     --restart always \
     --volume $GITLAB_HOME/config:/etc/gitlab \
     --volume $GITLAB_HOME/logs:/var/log/gitlab \
     --volume $GITLAB_HOME/data:/var/opt/gitlab \
     --shm-size 256m \
     gitlab/gitlab-ee:latest
   ```

   NOTE:
   The format for publishing ports is `hostPort:containerPort`. Read more in
   Docker's documentation about
   [exposing incoming ports](https://docs.docker.com/engine/reference/run/#/expose-incoming-ports).

1. Enter the running container:

   ```shell
   sudo docker exec -it gitlab /bin/bash
   ```

1. Open `/etc/gitlab/gitlab.rb` with your editor and set `external_url`:

   ```ruby
   # For HTTP
   external_url "http://gitlab.example.com:8929"

   or

   # For HTTPS (notice the https)
   external_url "https://gitlab.example.com:8929"
   ```

   The port specified in this URL must match the port published to the host by Docker.
   Additionally, if the NGINX listen port is not explicitly set in
   `nginx['listen_port']`, it will be pulled from the `external_url`.
   For more information see the [NGINX documentation](https://docs.gitlab.com/omnibus/settings/nginx.html).

1. Set `gitlab_shell_ssh_port`:

   ```ruby
   gitlab_rails['gitlab_shell_ssh_port'] = 2289
   ```

1. Finally, reconfigure GitLab:

   ```shell
   gitlab-ctl reconfigure
   ```

Following the above example, you will be able to reach GitLab from your
web browser under `<hostIP>:8929` and push using SSH under the port `2289`.

A `docker-compose.yml` example that uses different ports can be found in the
[Docker compose](#install-gitlab-using-docker-compose) section.

## Upgrade

In most cases, upgrading GitLab is as easy as downloading the newest Docker
[image tag](#use-tagged-versions-of-gitlab).

### Upgrade GitLab using Docker Engine

To upgrade GitLab that was [installed using Docker Engine](#install-gitlab-using-docker-engine):

1. Take a [backup](#back-up-gitlab). As a minimum, back up [the database](#create-a-database-backup) and
   the GitLab secrets file.

1. Stop the running container:

   ```shell
   sudo docker stop gitlab
   ```

1. Remove the existing container:

   ```shell
   sudo docker rm gitlab
   ```

1. Pull the new image. For example, the latest GitLab image:

   ```shell
   sudo docker pull gitlab/gitlab-ee:latest
   ```

1. Create the container once again with the
[previously specified](#install-gitlab-using-docker-engine) options:

   ```shell
   sudo docker run --detach \
   --hostname gitlab.example.com \
   --publish 443:443 --publish 80:80 --publish 22:22 \
   --name gitlab \
   --restart always \
   --volume $GITLAB_HOME/config:/etc/gitlab \
   --volume $GITLAB_HOME/logs:/var/log/gitlab \
   --volume $GITLAB_HOME/data:/var/opt/gitlab \
   --shm-size 256m \
   gitlab/gitlab-ee:latest
   ```

On the first run, GitLab will reconfigure and upgrade itself.

Refer to the GitLab [Upgrade recommendations](../policy/maintenance.md#upgrade-recommendations)
when upgrading between major versions.

### Upgrade GitLab using Docker compose

To upgrade GitLab that was [installed using Docker Compose](#install-gitlab-using-docker-compose):

1. Take a [backup](#back-up-gitlab). As a minimum, back up [the database](#create-a-database-backup) and
   the GitLab secrets file.

1. Download the newest release and upgrade your GitLab instance:

   ```shell
   docker compose pull
   docker compose up -d
   ```

   If you have used [tags](#use-tagged-versions-of-gitlab) instead, you'll need
   to first edit `docker-compose.yml`.

### Convert Community Edition to Enterprise Edition

You can convert an existing Docker-based GitLab Community Edition (CE) container
to a GitLab [Enterprise Edition](https://about.gitlab.com/pricing/) (EE) container
using the same approach as [upgrading the version](#upgrade).

We recommend you convert from the same version of CE to EE (for example, CE 14.1 to EE 14.1).
This is not explicitly necessary, and any standard upgrade (for example, CE 14.0 to EE 14.1) should work.
The following steps assume that you are upgrading the same version.

1. Take a [backup](#back-up-gitlab). As a minimum, back up [the database](#create-a-database-backup) and
   the GitLab secrets file.

1. Stop the current CE container, and remove or rename it.

1. To create a new container with GitLab EE,
   replace `ce` with `ee` in your `docker run` command or `docker-compose.yml` file.
   However, reuse the CE container name, port and file mappings, and version.

### Upgrade the product documentation

This is an optional step. If you [installed the documentation site](#install-the-product-documentation),
see how to [upgrade to another version](../administration/docs_self_host.md#upgrade-using-docker).

### Downgrade GitLab

To downgrade GitLab after an upgrade:

1. Follow the upgrade procedure, but [specify the tag for the original version of GitLab](#use-tagged-versions-of-gitlab)
   instead of `latest`.

1. Restore the [database backup you made](#create-a-database-backup) as part of the upgrade.

   - Restoring is required to back out database data and schema changes (migrations) made as part of the upgrade.
   - GitLab backups must be restored to the exact same version and edition.
   - [Follow the restore steps for Docker images](../raketasks/restore_gitlab.md#restore-for-docker-image-and-gitlab-helm-chart-installations), including
     stopping Puma and Sidekiq. Only the database must be restored, so add
     `SKIP=artifacts,repositories,registry,uploads,builds,pages,lfs,packages,terraform_state`
     to the `gitlab-backup restore` command line arguments.

## Back up GitLab

You can create a GitLab backup with:

```shell
docker exec -t <container name> gitlab-backup create
```

Read more on how to [back up and restore GitLab](../raketasks/backup_restore.md).

NOTE:
If configuration is provided entirely via the `GITLAB_OMNIBUS_CONFIG` environment variable
(per the ["Pre-configure Docker Container"](#pre-configure-docker-container) steps),
meaning no configuration is set directly in the `gitlab.rb` file, then there is no need
to back up the `gitlab.rb` file.

WARNING:
[Backing up the GitLab secrets file](../raketasks/backup_gitlab.md#storing-configuration-files) is required
to avoid [complicated steps](../raketasks/backup_restore.md#when-the-secrets-file-is-lost) when recovering
GitLab from backup. The secrets file is stored at `/etc/gitlab/gitlab-secrets.json` inside the container, or
`$GITLAB_HOME/config/gitlab-secrets.json` [on the container host](#set-up-the-volumes-location).

### Create a database backup

A database backup is required to roll back GitLab upgrade if you encounter issues.

```shell
docker exec -t <container name> gitlab-backup create SKIP=artifacts,repositories,registry,uploads,builds,pages,lfs,packages,terraform_state
```

The backup is written to `/var/opt/gitlab/backups` which should be on a
[volume mounted by Docker](#set-up-the-volumes-location).

## Installing GitLab Community Edition

[GitLab CE Docker image](https://hub.docker.com/r/gitlab/gitlab-ce/)

To install the Community Edition, replace `ee` with `ce` in the commands on this
page.

## Troubleshooting

The following information will help if you encounter problems using Omnibus GitLab and Docker.

### Diagnose potential problems

Read container logs:

```shell
sudo docker logs gitlab
```

Enter running container:

```shell
sudo docker exec -it gitlab /bin/bash
```

From within the container you can administer the GitLab container as you would
normally administer an
[Omnibus installation](https://gitlab.com/gitlab-org/omnibus-gitlab/blob/master/README.md)

### 500 Internal Error

When updating the Docker image you may encounter an issue where all paths
display a `500` page. If this occurs, restart the container to try to rectify the
issue:

```shell
sudo docker restart gitlab
```

### Permission problems

When updating from older GitLab Docker images you might encounter permission
problems. This happens when users in previous images were not
preserved correctly. There's script that fixes permissions for all files.

To fix your container, execute `update-permissions` and restart the
container afterwards:

```shell
sudo docker exec gitlab update-permissions
sudo docker restart gitlab
```

### Windows/Mac: `Error executing action run on resource ruby_block[directory resource: /data/GitLab]`

This error occurs when using Docker Toolbox with VirtualBox on Windows or Mac,
and making use of Docker volumes. The `/c/Users` volume is mounted as a
VirtualBox Shared Folder, and does not support the all POSIX file system features.
The directory ownership and permissions cannot be changed without remounting, and
GitLab fails.

Our recommendation is to switch to using the native Docker install for your
platform, instead of using Docker Toolbox.

If you cannot use the native Docker install (Windows 10 Home Edition, or Windows 7/8),
then an alternative solution is to setup NFS mounts instead of VirtualBox shares for
Docker Toolbox's boot2docker.

### Linux ACL issues

If you are using file ACLs on the Docker host, the `docker` group requires full access to the volumes in order for GitLab to work:

```shell
getfacl $GITLAB_HOME

# file: $GITLAB_HOME
# owner: XXXX
# group: XXXX
user::rwx
group::rwx
group:docker:rwx
mask::rwx
default:user::rwx
default:group::rwx
default:group:docker:rwx
default:mask::rwx
default:other::r-x
```

If these are not correct, set them with:

```shell
sudo setfacl -mR default:group:docker:rwx $GITLAB_HOME
```

The default group is `docker`. If you changed the group, be sure to update your
commands.

### `/dev/shm` mount not having enough space in Docker container

GitLab comes with a Prometheus metrics endpoint at `/-/metrics` to expose a
variety of statistics on the health and performance of GitLab. The files
required for this gets written to a temporary file system (like `/run` or
`/dev/shm`).

By default, Docker allocates 64MB to the shared memory directory (mounted at
`/dev/shm`). This is insufficient to hold all the Prometheus metrics related
files generated, and will generate error logs like the following:

```plaintext
writing value to /dev/shm/gitlab/sidekiq/gauge_all_sidekiq_0-1.db failed with unmapped file
writing value to /dev/shm/gitlab/sidekiq/gauge_all_sidekiq_0-1.db failed with unmapped file
writing value to /dev/shm/gitlab/sidekiq/gauge_all_sidekiq_0-1.db failed with unmapped file
writing value to /dev/shm/gitlab/sidekiq/histogram_sidekiq_0-0.db failed with unmapped file
writing value to /dev/shm/gitlab/sidekiq/histogram_sidekiq_0-0.db failed with unmapped file
writing value to /dev/shm/gitlab/sidekiq/histogram_sidekiq_0-0.db failed with unmapped file
writing value to /dev/shm/gitlab/sidekiq/histogram_sidekiq_0-0.db failed with unmapped file
```

Other than disabling the Prometheus Metrics from the Admin Area, the recommended
solution to fix this problem is to increase the size of shared memory to at least 256MB.
If using `docker run`, this can be done by passing the flag `--shm-size 256m`.
If using a `docker-compose.yml` file, the `shm_size` key can be used for this
purpose.

### Docker containers exhausts space due to the `json-file`

Docker's [default logging driver is `json-file`](https://docs.docker.com/config/containers/logging/configure/#configure-the-default-logging-driver), which performs no log rotation by default. As a result of this lack of rotation, log files stored by the `json-file` driver can consume a significant amount of disk space for containers that generate a lot of output. This can lead to disk space exhaustion. To address this, use [`journald`](https://docs.docker.com/config/containers/logging/journald/) as the logging driver when available, or [another supported driver](https://docs.docker.com/config/containers/logging/configure/#supported-logging-drivers) with native rotation support.

### Buffer overflow error when starting Docker

If you receive this buffer overflow error, you should purge old log files in
`/var/log/gitlab`:

```plaintext
buffer overflow detected : terminated
xargs: tail: terminated by signal 6
```

Removing old log files helps fix the error, and ensures a clean startup of the instance.




<br/><a name="Migration"></a>
==============================================================================================

# Migration



<br/><a name="Upgrade"></a>
==============================================================================================

# Upgrade

