## Introduction

LEMP is an alternative web server stack consisting of **Linux, Nginx, MySQL, and PHP**, which is popular and widely used by many websites on the internet.  

This project demonstrates the setup of a **LEMP stack** on an AWS EC2 instance, including:

- **Linux**: Ubuntu OS  
- **Nginx**: Web server  
- **MySQL**: Database server  
- **PHP**: Server-side scripting

The guide includes step-by-step instructions, screenshots, and a sample PHP-MySQL integration.

---

## Step 0: Prerequisites

1. **Launch an EC2 Instance**  
   - Ubuntu 24.04 LTS   
   - Instance type: t2.micro family  
   - Security group allows:
     - HTTP (80)
     - SSH (22)

2. **Create an SSH Key Pair**  
   - Example: `LEMP-KEYPAIR`  
<img width="1920" height="1080" alt="01_ec2-instance-running" src="https://github.com/user-attachments/assets/1b339106-ac0b-4456-9497-df03ec06f1ae" />

3. **Download Private SSH Key**  

   chmod 400 /c/Users/PC/Downloads/LEMP-KEYPAIR.pem

4. **Connect to EC2 Instance**
   ssh -i/c/Users/PC/Downloads/LEMP-KEYPAIR.pem
   <img width="1920" height="1080" alt="02_ssh-connection" src="https://github.com/user-attachments/assets/f2cc5d56-3f3f-4576-8506-e06f8ff1aa31" />

⚙ Step 1: Install Nginx

**Update and upgrade packages**
sudo apt update
sudo apt upgrade
<img width="1920" height="1080" alt="03_update-upgrade-system" src="https://github.com/user-attachments/assets/b28e5200-3f7d-4bc1-a03b-3017e7c3691e" />

**Install Nginx**
sudo apt install nginx
<img width="1920" height="1080" alt="04_install-nginx" src="https://github.com/user-attachments/assets/5bdf579f-cd92-4ae3-b454-e42c8ea5c037" />

**Check Nginx status**
sudo systemctl status nginx
<img width="1920" height="1080" alt="05_nginx-status" src="https://github.com/user-attachments/assets/61535a62-63c7-4ffa-ba51-d2c26ce229ae" />

**Test Nginx in browser**
http://54-226-237-228
<img width="1920" height="1080" alt="06_nginx-browser" src="https://github.com/user-attachments/assets/9059e9f2-de8d-467a-b149-e7e9ff38ade4" />

⚙ Step 2: Install MySQL

**Install MySQL server**
sudo apt install mysql-server
<img width="1920" height="1080" alt="07-install-mysql" src="https://github.com/user-attachments/assets/27c1a0ae-cb53-40b1-8ab3-aef6f3a895be" />

**Log in and configure root password**
sudo mysql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password.1';
EXIT;
**Secure MySQL installation**
sudo mysql_secure_installation
<img width="1920" height="1080" alt="08-mysql-secure-installation" src="https://github.com/user-attachments/assets/3e02d162-ae4d-476d-97f9-bf1b651daf50" />
**Verify MySQL Root Login**
sudo mysql -u root -p
<img width="1899" height="978" alt="09-mysql-log in-confirm" src="https://github.com/user-attachments/assets/db05ed8f-c346-4d8b-8e93-07c02a9b10c9" />

⚙ Step 3: Install PHP

**Install PHP and PHP-FPM with MySQL extension**


sudo apt install php-fpm php-mysql


<img width="1901" height="983" alt="10-install-php" src="https://github.com/user-attachments/assets/716c3435-d390-4318-b042-c0e579227700" />

sudo systemctl status php8.3-fpm

⚙ Step 4: Configure Nginx for PHP

**Create project directory**
sudo mkdir -p /var/www/projectLEMP
sudo chown -R $USER:$USER /var/www/projectLEMP
**Create Nginx configuration file**
sudo nano /etc/nginx/sites-available/projectLEMP

server {
    listen 80;
    server_name _;

    root /var/www/projectLEMP;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.3-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }
}

<img width="1894" height="938" alt="11-nginx-php-config" src="https://github.com/user-attachments/assets/609e8cd8-9913-4f98-90db-2794225993ce" />

**Enable site and reload Nginx**
sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx

⚙ Step 5: Deploy Dynamic Index Page

TOKEN=$(curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600")
curl -H "X-aws-ec2-metadata-token: $TOKEN" -s http://169.254.169.254/latest/meta-data/public-hostname > /tmp/hostname.txt
curl -H "X-aws-ec2-metadata-token: $TOKEN" -s http://169.254.169.254/latest/meta-data/public-ipv4 > /tmp/public-ip.txt

sudo bash -c "echo 'Hello LEMP from $(cat /tmp/hostname.txt) with public IP $(cat /tmp/public-ip.txt)' > /var/www/projectLEMP/index.html"

**Test in browser**
http://ec2-54-226-237-228.compute-1.amazonaws.com
<img width="1913" height="917" alt="12-php-test-file" src="https://github.com/user-attachments/assets/74d3d5e5-5b15-4afd-a2a7-2197a87a6570" />

⚙ Step 6: Verify PHP Functionality

sudo nano /var/www/projectLEMP/info.php
Input:
<?php phpinfo(); ?>
<img width="1920" height="1080" alt="13-phpmyadmin-install" src="https://github.com/user-attachments/assets/4031ed38-42d6-4013-9c9a-e8aff8987967" />

Access browser
http://ec2-54-226-237-228.compute-1.amazonaws.com/info.php
<img width="1920" height="1080" alt="14-phpmyadmin-browser" src="https://github.com/user-attachments/assets/f0c98140-626e-484a-a7ed-0b5c634be54a" />

⚙ Step 7: Create and Retrieve Data from MySQL

**Database Setup**
sudo mysql -u root -p
CREATE DATABASE example_database;
CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';
GRANT ALL PRIVILEGES ON example_database.* TO 'example_user';
EXIT;
<img width="1893" height="926" alt="15-mysql_show_database" src="https://github.com/user-attachments/assets/49afc6b5-31c0-4b16-ba30-114515dc9385" />

**Create Table and Insert Data**
mysql -u example_user -p
USE example_database;

CREATE TABLE todo_list (
    item_id INT AUTO_INCREMENT,
    content VARCHAR(255),
    PRIMARY KEY(item_id)
);

INSERT INTO todo_list (content) VALUES 
("My first important item"),
("My second important item"),
("My third important item"),
("and this one more thing");

SELECT * FROM todo_list;
<img width="1900" height="977" alt="16-Todo_list" src="https://github.com/user-attachments/assets/fc83ef95-f8d4-43ad-90cf-9f1370fa9509" />

**PHP Script to Retrieve Data**
nano /var/www/projectLEMP/todolist.php

<?php
$user = "example_user";
$password = "PassWord.1";
$database = "example_database";
$table = "todo_list";

try {
    $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
    echo "<h2>TODO</h2><ol>";
    foreach ($db->query("SELECT content FROM $table") as $row) {
        echo "<li>" . $row['content'] . "</li>";
    }
    echo "</ol>";
} catch (PDOException $e) {
    print "Error!: " . $e->getMessage();
    die();
}
?>
<img width="1891" height="934" alt="17-Todolist-nano-Script" src="https://github.com/user-attachments/assets/d1f0f294-6f0a-49d7-aca2-02a6c6fc0d69" />

**Access On Browser**
http://ec2-54-226-237-228.compute-1.amazonaws.com/todolist.php
<img width="1913" height="916" alt="18-Todolist-browser" src="https://github.com/user-attachments/assets/4f7eece8-ded9-4936-9278-8971c26faaac" />

## Conclusion
You have successfully deployed a LEMP stack on AWS EC2, including:
Nginx serving HTML/PHP
MySQL database configured and secured
PHP-MySQL integration with dynamic pages
