# LEMP Setup for WordPress on Ubuntu
Instructions to setup Linux, NGINX, MySQL, and PHP on an Ubuntu server to host multiple WordPress websites. Throughout this example, we are using an SSH user named `devopseverywhere` in our NGINX file as well as in PHP-FPM configuration. You will have to adjust it to match the SSH user that you create on your server.

Here are some of the points to remember:

- We will use a VM with Ubuntu 20.x on a provider like DigitalOcean, Linode, Upcloud, Google Cloud, AWS or any other cloud provider
- The VM should not contain any existing installations of PHP, MySQL, NGINX or any other web server stack software
- You will be able to run multiple WordPress (or general) websites on a single instance
- We will use certbot package to use SSL certificates from Let's Encrypt
- We will create a new SSH user with sudo privileges
- This user will be used to manage website files (via SFTP or SSH)
- This user will be used in NGINX configuration (replacing the default `www-data` user)
- This user will be used in PHP-FPM configuration (replacing the default `www-data` user)

## Sign in
Using a terminal on Unix or PowerShell (or PuTTY) on Windows, sign in as root or a standard user with sudo privileges.

## Update Package Cache

```bash
sudo apt update
```

### Create SSH User
We will create an SSH user with sudo privileges and we will use this user to manage websites as well as we will run PHP and web server (NGINX) under this user.

```bash
sudo adduser devopseverywhere
```

Assign sudo privileges:
```bash
sudo echo 'devopseverywhere ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers
```

## Install & Configure NGINX
First of all, install and configure NGINX web server. We will install NGINX, will replace the existing NGINX config with an optimized one and we will modify the directory structure a little to simplify the vhost file creation.

```bash
sudo apt -y install nginx
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

Check the NGINX configuration files:

```bash
sudo nginx -t
```

And restart NGINX if no errors encountered:
```bash
sudo service nginx restart
```

## Install, Optimize & Secure MySQL
Install MySQL server
```bash
sudo apt install mysql-server
```
To improve performance, disable general logging:
```bash
sudo mysql -e "SET GLOBAL general_log = 'OFF';"
```
And finally secure your MySQL installation:
```bash
sudo mysql_secure_installation
```

## Install & Configure PHP
Install PHP-FPM:
```bash
sudo install php-fpm
```
And configure PHP-FPM pools to use our created SSH user:

```bash
sudo sed -i 's#www-data#devopseverywhere#g' /etc/php/7.4/fpm/pool.d/www.conf
```

Note: Be sure to specify the correct PHP version. You can list the directory contents to see the installed PHP version:
```bash
cd /etc/php && \
ls
```

And finally restart PHP-FPM service:

```bash
sudo service php7.4-fpm restart
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
