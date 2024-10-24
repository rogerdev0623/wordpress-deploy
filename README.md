# WordPress Installation Guide on Nginx

This guide outlines the steps to install WordPress on an Nginx server with SSL support.

## Prerequisites

- A server with Ubuntu
- Access to a terminal
- Domain name pointed to your server's IP address

## Steps

### 1. Update the Package Index

```bash
sudo apt update
```

### 2. Install Nginx

```bash
sudo apt install nginx
```

### 3. Configure Firewall

Enable the firewall and allow necessary ports:

```bash
sudo ufw enable
sudo ufw status
sudo ufw allow ssh  # Port 22
sudo ufw allow http # Port 80
sudo ufw allow https # Port 443
```

### 4. Set Up DNS Records

Add an A record for `@` and `www` to your domain pointing to your server's IP address.

### 5. Install Certbot for SSL

```bash
sudo apt install certbot python3-certbot-nginx
```

### 6. Configure Nginx for WordPress

Create or edit your Nginx server block configuration:

```nginx
server {
    listen 80;
    listen [::]:80;

    server_name yourdomain.com www.yourdomain.com;
    root /var/www/wordpress;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ /index.php?$args;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

### 7. Obtain SSL Certificates

```bash
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
```

### 8. Install PHP and MySQL

```bash
sudo apt install php-fpm php-mysql
```

### 9. Configure PHP-FPM

Edit the PHP-FPM pool configuration:

```bash
sudo nano /etc/php/8.1/fpm/pool.d/www.conf
```

Find and set the listen directive:

```plaintext
listen = 127.0.0.1:9000
```

Restart PHP-FPM:

```bash
sudo systemctl restart php8.1-fpm
```

### 10. Download and Set Up WordPress

```bash
sudo mkdir -p /var/www/wordpress
sudo chown -R www-data:www-data /var/www/wordpress
cd /var/www/wordpress
sudo wget https://wordpress.org/latest.tar.gz
sudo tar -xzvf latest.tar.gz --strip-components=1
sudo cp wp-config-sample.php wp-config.php
```

### 11. Install MySQL and Set Up Database

```bash
sudo apt install mysql-server
sudo mysql -u root -p
```

Run the following commands in the MySQL shell:

```sql
CREATE USER 'test'@'localhost' IDENTIFIED BY 'your_password';
GRANT ALL PRIVILEGES ON *.* TO 'test'@'localhost' WITH GRANT OPTION;
FLUSH PRIVILEGES;
CREATE DATABASE unminifycode;
```

### 12. Configure WordPress Database Credentials

Edit the `wp-config.php` file:

```bash
sudo nano wp-config.php
```

Set up your database credentials:

```php
define( 'DB_NAME', 'unminifycode' );
define( 'DB_USER', 'test' );
define( 'DB_PASSWORD', 'your_password' );
define( 'DB_HOST', 'localhost' );
define( 'DB_CHARSET', 'utf8' );
define( 'DB_COLLATE', '' );
```

Save and close the file.

## Conclusion

You have successfully installed WordPress on your Nginx server with SSL. You can now visit your domain to complete the WordPress setup through the web interface.
