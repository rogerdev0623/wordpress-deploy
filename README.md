# WordPress Installation Guide on Nginx

This guide outlines the steps to install WordPress on an Nginx server with SSL support.

## Prerequisites

- A server with Ubuntu
- Access to a terminal
- Domain name pointed to your server's IP address

## 1. Sign Up for ScalaHosting VPS
If you are looking for a reliable and powerful VPS service, consider [ScalaHosting VPS](https://www.scalahosting.com/cloud-servers.html#a_aid=rogerphandev&amp;a_bid=5c412908). Their cloud servers offer high performance, excellent uptime, and easy scalability.

👉 [Get started now!](https://www.scalahosting.com/cloud-servers.html#a_aid=rogerphandev&amp;a_bid=5c412908)

## 2. Create an Ubuntu Server on ScalaHosting VPS and Log In via SSH
1. Log in to your ScalaHosting VPS account.
2. Click on "Deploy New Server."
3. Choose "Ubuntu 22.04" as your server image.
4. Select your desired server location and plan.
5. Deploy the server.
6. Once the server is ready, log in via SSH using the root user.

## Steps

### 3. Update the Package Index

```bash
sudo apt update
```

### 4. Install Nginx

```bash
sudo apt install nginx
```

### 5. Configure Firewall

Enable the firewall and allow necessary ports:

```bash
sudo ufw enable
sudo ufw status
sudo ufw allow ssh  # Port 22
sudo ufw allow http # Port 80
sudo ufw allow https # Port 443
```

### 6. Set Up DNS Records

Add an A record for `@` and `www` to your domain pointing to your server's IP address.

### 7. Install Certbot for SSL

```bash
sudo apt install certbot python3-certbot-nginx
```

### 8. Configure Nginx for WordPress

```
sudo nano /etc/nginx/sites-available/default
```
Add the following to the location part of the server block
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

### 9. Obtain SSL Certificates

```bash
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
```

### 10. Install PHP and MySQL

```bash
sudo apt install php-fpm php-mysql
```

### 11. Configure PHP-FPM

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

### 12. Download and Set Up WordPress

```bash
sudo mkdir -p /var/www/wordpress
sudo chown -R www-data:www-data /var/www/wordpress
cd /var/www/wordpress
sudo wget https://wordpress.org/latest.tar.gz
sudo tar -xzvf latest.tar.gz --strip-components=1
sudo cp wp-config-sample.php wp-config.php
```

### 13. Install MySQL and Set Up Database

```bash
sudo apt install mysql-server
sudo mysql -u root -p
```

Run the following commands in the MySQL shell:

```sql
CREATE USER 'test'@'localhost' IDENTIFIED BY 'your_password';
GRANT ALL PRIVILEGES ON *.* TO 'test'@'localhost' WITH GRANT OPTION;
FLUSH PRIVILEGES;
CREATE DATABASE yourdatabasename;
```

### 14. Configure WordPress Database Credentials

Edit the `wp-config.php` file:

```bash
sudo nano wp-config.php
```

Set up your database credentials:

```php
define( 'DB_NAME', 'yourdatabasename' );
define( 'DB_USER', 'test' );
define( 'DB_PASSWORD', 'your_password' );
define( 'DB_HOST', 'localhost' );
define( 'DB_CHARSET', 'utf8' );
define( 'DB_COLLATE', '' );
```

Save and close the file.

## Conclusion

You have successfully installed WordPress on your Nginx server with SSL. You can now visit your domain at [yourdomain.com](http://yourdomain.com) to complete the WordPress setup through the web interface.
