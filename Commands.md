# SINGLE INSTANCE DEPLOYMENT (WEB SERVER + DATABASE ON SAME EC2 INSTANCE)

# Step 1: Connect to the EC2 instance
ssh -i your-key.pem ubuntu@your-ec2-public-ip

# Step 2: Update system and install required packages
sudo apt update -y && sudo apt upgrade -y

sudo apt install apache2 mysql-server php php-mysql wget unzip -y

# Step 3: Start and enable Apache and MySQL services
sudo systemctl enable apache2

sudo systemctl enable mysql

sudo systemctl start apache2

sudo systemctl start mysql

# Step 4: Create MySQL database and user
sudo mysql -u root -p

CREATE DATABASE wordpress;

CREATE USER 'wpuser'@'localhost' IDENTIFIED BY 'password';

GRANT ALL PRIVILEGES ON wordpress.* TO 'wpuser'@'localhost';

FLUSH PRIVILEGES;

EXIT;

# Step 5: Download and extract WordPress

cd /var/www/html

sudo rm index.html

sudo wget https://wordpress.org/latest.tar.gz

sudo tar -xvzf latest.tar.gz

sudo mv wordpress/* .

sudo rm -rf wordpress latest.tar.gz

sudo chown -R www-data:www-data /var/www/html/

sudo chmod -R 755 /var/www/html/

# Step 6: Configure WordPress database settings

sudo nano /var/www/html/wp-config.php

define( 'DB_NAME', 'wordpress' );

define( 'DB_USER', 'wpuser' );

define( 'DB_PASSWORD', 'password' );

define( 'DB_HOST', 'localhost' );

# Step 7: Restart Apache service

sudo systemctl restart apache2

# SEPARATE INSTANCES DEPLOYMENT (WEB SERVER AND DATABASE ON DIFFERENT EC2)

# SETUP DATABASE SERVER

# Step 1: Connect to Database EC2 instance

ssh -i your-key.pem ubuntu@your-db-ec2-public-ip

# Step 2: Update system and install MySQL

sudo apt update -y && sudo apt upgrade -y

sudo apt install mysql-server -y

# Step 3: Start and enable MySQL service

sudo systemctl enable mysql

sudo systemctl start mysql

# Step 4: Create MySQL database and user

sudo mysql -u root -p

CREATE DATABASE wordpress;

CREATE USER 'wpuser'@'%' IDENTIFIED BY 'password';

GRANT ALL PRIVILEGES ON wordpress.* TO 'wpuser'@'%';

FLUSH PRIVILEGES;

EXIT;

# Step 5: Allow remote access to MySQL

sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf

#Change this line:

bind-address = 127.0.0.1

# to

bind-address = 0.0.0.0

# Step 6: Restart MySQL service
sudo systemctl restart mysql

# SETUP WEB SERVER

# Step 1: Connect to Web Server EC2 instance
ssh -i your-key.pem ubuntu@your-web-ec2-public-ip

# Step 2: Update system and install Apache and PHP
sudo apt update -y && sudo apt upgrade -y
sudo apt install apache2 php php-mysql wget unzip -y

# Step 3: Start and enable Apache
sudo systemctl enable apache2
sudo systemctl start apache2

# Step 4: Download and extract WordPress
cd /var/www/html

sudo rm index.html

sudo wget https://wordpress.org/latest.tar.gz

sudo tar -xvzf latest.tar.gz

sudo mv wordpress/* .

sudo rm -rf wordpress latest.tar.gz

sudo chown -R www-data:www-data /var/www/html/

sudo chmod -R 755 /var/www/html/

# Step 5: Configure WordPress database settings
sudo nano /var/www/html/wp-config.php

define( 'DB_NAME', 'wordpress' );

define( 'DB_USER', 'wpuser' );

define( 'DB_PASSWORD', 'password' );

define( 'DB_HOST', 'your-db-instance-private-ip' );

# Step 6: Restart Apache service
sudo systemctl restart apache2
