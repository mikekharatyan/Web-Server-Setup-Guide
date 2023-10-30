# Server Setup Guide

This guide will walk you through setting up a web server with Nginx, PHP, MariaDB, and PHPMyAdmin. Follow these steps to get your server up and running smoothly.

## Step 1: Update and Upgrade Linux

```bash
sudo apt update
sudo apt upgrade
```

## Step 2: Add PHP and Nginx Repositories

```bash
sudo add-apt-repository ppa:ondrej/php
sudo add-apt-repository ppa:ondrej/nginx
```

## Step 3: Install Nginx

```bash
sudo apt install nginx
```

## Step 4: Enable Nginx Autostart

```bash
sudo systemctl enable nginx
```

## Step 5: Install PHP and PHP Extensions

```bash
sudo apt install php8.2-{fpm,cli,bcmath,bz2,curl,xml,mbstring,zip,gd,mysqli,pdo,mcrypt,imagick,intl,soap}
```

## Step 6: Install MariaDB And Run Setup

```bash
wget https://r.mariadb.com/downloads/mariadb_repo_setup
chmod +x mariadb_repo_setup
sudo ./mariadb_repo_setup
sudo apt install mariadb-server
sudo mysql_secure_installation
```

## Step 7: Download and Configure PHPMyAdmin

1. Download PHPMyAdmin from [phpmyadmin.net](https://www.phpmyadmin.net/).
2. Extract the archive and delete folders: `examples`, `setup`, `doc`.
3. Move `sql/Create_tables.sql` file to your desired location (e.g., desktop).
4. Create a `tmp` folder.
5. Rename `config.sample.inc.php` to `config.inc.php`.
6. Generate a `blowfish_secret` and add this line to `config.inc.php`: `$cfg['TempDir'] = '/tmp';`
7. Archive PHPMyAdmin and upload it to the server in `/usr/share`.

## Step 8: Install Zip, Unzip Tools And Unzip Phpmyadmin Archive

```bash
sudo apt install zip unzip
cd /usr/share
unzip phpmyadmin.zip
```

## Step 9: Configure Nginx and PHP

Configure the `php.ini` file and Nginx site-available default file.

```bash
memory_limit, post_max_size, upload_max_filesize and etc.
/etc/nginx/nginx.conf

server {
    client_max_body_size 8M;

    //other lines...
}
```

## Step 10: Configure Nginx for PHPMyAdmin and Laravel

Use the following Nginx configuration for PHPMyAdmin and Laravel:

```nginx
server {
    listen 80 default_server;
    listen [::]:80 default_server;
    server_name "";
    return 444;
}

server {
    listen 80;
    listen [::]:80;
    server_name hiddenpma.domain.com;
    root /usr/share/phpmyadmin;
    index index.php index.html;
    charset utf-8;

    location / {
        try_files $uri $uri/ /index.php?$args;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php8.2-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }
}

server {
    listen 80;
    listen [::]:80;
    server_name domain.com www.domain.com;
    root /var/www/html/public;
    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-Content-Type-Options "nosniff";
    index index.php;
    charset utf-8;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location = /favicon.ico {
        access_log off;
        log_not_found off;
    }
    
    location = /robots.txt {
        access_log off;
        log_not_found off;
    }

    error_page 404 /index.php;

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php8.2-fpm.sock;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}
```

## Step 11: Restart Nginx

```bash
sudo systemctl restart nginx
```

## Step 12: Access PHPMyAdmin and Import `Create_tables.sql`

You can now access PHPMyAdmin (hiddenpma.domain.com) and import the `Create_tables.sql` file.

## Step 13: Install Composer

```bash
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php composer-setup.php
php -r "unlink('composer-setup.php');"
sudo mv composer.phar /usr/local/bin/composer
```

## Step 14: Install Node.js with NVM

```bash
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.5/install.sh | bash
source ~/.bashrc
nvm list-remote
nvm install v20.8.1
```

## Step 15: Reboot Your System

```bash
sudo reboot
```

## Step 16: Set Permissions for Uploaded Site Files

After uploading your site files, use the following command to set the correct permissions:

```bash
sudo chown -R www-data:www-data ./    -  IN YOUR PROJECT FOLDER
```

Your server should now be set up and ready for your web applications. Enjoy your development or production environment!
