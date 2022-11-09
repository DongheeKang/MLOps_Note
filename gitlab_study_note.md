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

# Components


### GitLab Application
GitLab application: A REST API, GraphQL API, HTTP API, SCIM API is available in GitLab
### GitLab Workhorse 
It is written with Go 
GitLab Workhorse is a smart reverse proxy for GitLab. It handles “large” HTTP requests such as file downloads, file uploads, Git push/pull and Git archive downloads.
for git clone via HTTPS and for slow requests that serve raw Git data.

### GitLab Rails 
The GitLab Rails console is a powerful utility for directly interacting with your GitLab instance. 

### GitLab Shell
GitLab Shell handles git SSH sessions for GitLab and modifies the list of authorized keys.
GitLab Shell is not a Unix shell nor a replacement for Bash or Zsh. for git clone, git push etc. via SSH.

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
Sidekiq is an open source job scheduler written in Ruby. It's important to be aware that Sidekiq by default doesn't do scheduling, it only executes jobs. The Enterprise version comes with scheduling out of the box. 
Sidekiq requires connection to the Redis, PostgreSQL and Gitaly instances

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
Elasticsearch repository indexer has to be utilized for indexing.
For indexing Git repository data, GitLab uses an indexer written in Go.
One can leverage Elasticsearch to enable Advanced Search for faster, more advanced code search across entire GitLab instance.

### Praefect 
Praefect is an optional reverse-proxy for Gitaly to manage a
cluster of Gitaly nodes for high availability. 

### Repository
LSF, docker, nfs, via gitlay

### Package Registry
NuGet, Conana, Maven, NPM

### RAKE
Rake is a Make-like program implemented in Ruby. Tasks and dependencies are specified in standard Ruby syntax.



<br/><a name="Architecture"></a>

# Architecture
https://gitlab.cern.ch/
https://auth.cern.ch/auth/realms/cern/protocol/openid-connect/auth?client_id=gitlab-prod&nonce=da2a5a07df2577cc34739e3c6fc99d19&redirect_uri=https%3A%2F%2Fgitlab.cern.ch%2Fusers%2Fauth%2Fopenid_connect%2Fcallback&response_type=code&scope=openid%20profile&state=8818561fb3d3f725be611a71b5dac19f
https://git.uni-paderborn.de/users/sign_in



### Ngix 

    sudo mkdir -p /etc/gitlab/ssl
    sudo chmod 755 /etc/gitlab/ssl
    sudo cp gitlab.example.com.key gitlab.example.com.crt /etc/gitlab/ssl/

/etc/gitlab/ssl/gitlab.example.com.key and /etc/gitlab/ssl/gitlab.example.com.crt, 