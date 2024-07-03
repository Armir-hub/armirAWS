#!/bin/bash
# Update package lists
apt-get update
apt-get upgrade -y

# Install required packages
apt-get install -y apache2 \
                    ghostscript \
                    libapache2-mod-php \
                    php \
                    php-bcmath \
                    php-curl \
                    php-imagick \
                    php-intl \
                    php-json \
                    php-mbstring \
                    php-mysql \
                    php-xml \
                    php-zip \
                    unzip \
                    curl \
                    mysql-client

# Optionally, start Apache service
systemctl start apache2
systemctl enable apache2

# Download WordPress
curl -O https://wordpress.org/latest.zip

# Unzip WordPress into /var/www/html
unzip latest.zip -d /var/www/html/

# Move WordPress files to /var/www/html
mv /var/www/html/wordpress/* /var/www/html/

# Remove the now-empty wordpress directory and the zip file
rm -rf /var/www/html/wordpress
rm latest.zip

# Set proper permissions
chown -R www-data:www-data /var/www/html
chmod -R 755 /var/www/html

# Remove the default Apache index.html file
rm /var/www/html/index.html

# Create wp-config.php from wp-config-sample.php
cp /var/www/html/wp-config-sample.php /var/www/html/wp-config.php

# Update wp-config.php with database details
sed -i "s/database_name_here/wordpress/" /var/www/html/wp-config.php
sed -i "s/username_here/admin/" /var/www/html/wp-config.php
sed -i "s/password_here/123456789/" /var/www/html/wp-config.php
sed -i "s/localhost/wordpress.czag22oqaro7.us-east-1.rds.amazonaws.com/" /var/www/html/wp-config.php

# Update DB_CHARSET in wp-config.php
sed -i "s/define('DB_CHARSET', 'utf8mb4');/define('DB_CHARSET', 'utf8');/" /var/www/html/wp-config.php

# Set the proper permissions for wp-config.php
chown www-data:www-data /var/www/html/wp-config.php
chmod 640 /var/www/html/wp-config.php
