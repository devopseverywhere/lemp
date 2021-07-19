# LEMP Setup for WordPress on Ubuntu
Instructions to setup Linux, NGINX, MySQL, and PHP on an Ubuntu server to host multiple WordPress websites.

## Sign in
Using a terminal on Unix or PowerShell (or PuTTY) on Windows, sign in as root or a standard user with sudo privileges.

## Update Package Cache

```bash
sudo apt update
```

## Install & Configure NGINX
First of all, install and configure NGINX web server. We will install NGINX, will replace the existing NGINX config with an optimized one and we will modify the directory structure a little to simplify the vhost file creation.

```bash
sudo apt install nginx
```

Update NGINX config with an optimized configuration file. The optimized configuration file for NGINX has been included in the source.
```bash
cd /etc/nginx && \
rm -rf sites-enabled sites-available && \
mkdir vhosts.d && \
sudo mv nginx.conf nginx-bak && \
cat > nginx.conf <<- EOF
user devopseverywhere;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;
worker_rlimit_nofile 100000;
error_log /var/log/nginx/error.log crit;

events {
    worker_connections 4000;
    use epoll;
    multi_accept on;
}

http {
    open_file_cache max=200000 inactive=20s;
    open_file_cache_valid 30s;
    open_file_cache_min_uses 2;
    open_file_cache_errors on;
    access_log off;
    sendfile on;
    tcp_nopush on;
    client_max_body_size 100M;
    tcp_nodelay on;
    gzip on;
    gzip_min_length 10240;
    gzip_comp_level 1;
    gzip_vary on;
    gzip_disable msie6;
    gzip_proxied expired no-cache no-store private auth;
    gzip_types
        text/css
        text/javascript
        text/xml
        text/plain
        text/x-component
        application/javascript
        application/x-javascript
        application/json
        application/xml
        application/rss+xml
        application/atom+xml
        font/truetype
        font/opentype
        application/vnd.ms-fontobject
        image/svg+xml;
    reset_timedout_connection on;
    client_body_timeout 30;
    send_timeout 30;
    keepalive_timeout 90;
    keepalive_requests 100;
    include /etc/nginx/mime.types;
    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/vhosts.d/*;
}
EOF
```


## Required PHP Extensions
Here is a list of required PHP extensions that WordPress needs in order to run properly.

- curl
- dom
- exif
- fileinfo
- hash
- imagick
- json
- mbstring
- mysqli
- openssl
- pcre
- sodium
- xml
- zip
- bcmath
- filter
- gd
- iconv
- intl
- mcrypt
- simplexml
- xmlreader
- zlib
- ssh2
- ftp
- sockets
