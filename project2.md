## LEMP STACK IMPLEMENTATION

# Step 1 – Installing the Nginx Web Server

`sudo apt get update`

`sudo apt install nginx`

`sudo systemctl status nginx`

![nginx status](./images/Nginx-status.PNG)

`curl http://localhost:80`

![accessing nginx locally](./images/accessing-nginx-locally.PNG)

# Step 2 — Installing MySQL

`sudo apt install mysql-server`

`sudo mysql`

![connecting to mysql](./images/connecting-to-mysql.PNG)

`ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';`

`exit`

`sudo mysql -p`

![connecting to mysql with password](./images/connecting-to-mysql-with-password.PNG)

`exit`

# Step 3 – Installing PHP

`sudo apt install php-fpm php-mysql`

# Step 4 — Configuring Nginx to Use PHP Processor

`sudo mkdir /var/www/projectLEMP`

`sudo chown -R $USER:$USER /var/www/projectLEMP`

`sudo nano /etc/nginx/sites-available/projectLEMP`

```
#/etc/nginx/sites-available/projectLEMP

server {
    listen 80;
    server_name projectLEMP www.projectLEMP;
    root /var/www/projectLEMP;

    index index.html index.htm index.php;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
     }
 
    location ~ /\.ht {
        deny all;
    }

}
```
`sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/`

`sudo nginx -t`

![testing nginx config](./images/testing-nginx-config.PNG)

`sudo unlink /etc/nginx/sites-enabled/default`

`sudo systemctl reload nginx`

`sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html`

![testing webpage on nginx](./images/testing-webpage-on-nginx.PNG)

# Step 5 – Testing PHP with Nginx

`sudo nano /var/www/projectLEMP/info.php`

`<?php phpinfo();`

![testing php page on nginx](./images/testing-php-page-on-nginx.PNG)

`sudo rm /var/www/projectLEMP/info.php`

# Step 6 – Retrieving data from MySQL database with PHP (continued)

`sudo mysql -p;`

![connecting to mysql with password](./images/connecting-to-mysql-with-password.PNG)

`CREATE DATABASE `example_database`;`

`CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';`

`GRANT ALL ON example_database.* TO 'example_user'@'%';`

`exit`

`sudo mysql -u example_user -p`

![testing user permission on mysql](./images/testing-user-permission-in-mysql.PNG)

`SHOW DATABASES;`

![showing databases in mysql](./images/showing-databases-in-mysql.PNG)

```
CREATE TABLE example_database.todo_list (
     item_id INT AUTO_INCREMENT,
     content VARCHAR(255),
     PRIMARY KEY(item_id)
);

```
`INSERT INTO example_database.todo_list (content) VALUES ("My first important item");`

`mysql>  SELECT * FROM example_database.todo_list;`

![displaying table contents in database](./images/outputing-table-content-in-database.PNG)

`exit`

`nano /var/www/projectLEMP/todo_list.php`

```
<?php
$user = "example_user";
$password = "password";
$database = "example_database";
$table = "todo_list";

try {
  $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
  echo "<h2>TODO</h2><ol>";
  foreach($db->query("SELECT content FROM $table") as $row) {
    echo "<li>" . $row['content'] . "</li>";
  }
  echo "</ol>";
} catch (PDOException $e) {
    print "Error!: " . $e->getMessage() . "<br/>";
    die();
}

```

# save and exit from nano editior
# point your browser to the server http://server_ip:80/todo_list.php
![testing php page on nginx](./images/retrieving-data-from-database-with-php.PNG)