# LEMP Setup for WordPress on Ubuntu
Instructions to setup Linux, NGINX, MySQL, and PHP on an Ubuntu server to host multiple WordPress websites.

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

## Sign In & Update Package Cache
```bash
sudo apt update
```

## Install & Configure NGINX
First of all, install and configure NGINX web server. We will install NGINX, will replace the existing NGINX config with an optimized one and we will modify the directory structure a little to simplify the vhost file creation.

```bash
sudo apt install nginx
```

Update 
