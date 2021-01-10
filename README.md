# :spider_web: Web-server-HF2

Først downloader og installere jeg Ubuntu Desktop. Desktop er bare lidt nemmere i denne sammenhæng. Skulle web-serveren bruges i den virkelige verden var valget faldet på Ubuntu Server, men i denne sammenhæng gør det ingen forskel.

### :computer: Lad os starte med at installere Apache2:
 
 ```
sudo apt update
sudo apt install apache2
```
![ubuntu-apache](images/ubuntu-apache.png)

Opretter mit eget site istedet for standard siden.

```
sudo mkdir /var/www/webster/

cd /var/www/webster/
sudo nano index.html

<html>
<head>
  <title>Webster</title>
</head>
<body>
  <p>Dette er mit helt eget website!</p>
</body>
</html>
```

Sætter VirtualHost op så man kan se mit nye website.

```
cd /etc/apache2/sites-available/
```

Tager en kopi af 000-default.conf og gemmer den som webster.conf
```
sudo cp 000-default.conf webster.conf
```

Åbner webster.conf
```
sudo nano webster.conf
```
Retter følgende linjer.
```
DocumentRoot /var/www/webster/
ServerName webster.localhost
```
Luk og gem.

Hvis siden ikke er _enabled_ kan du gøre det med følgende kommando.
```
sudo a2ensite gci.conf
```

Mit nye website kan nu findes på webster.local.
![webster-localhost](images/webster-localhost.png)

Tillader trafik på port 80.
```
sudo ufw allow in "Apache"
```

Starter UFW op.
```
sudo ufw enable
```
UFW Status.
```
sudo ufw status
```

### :computer: Installer MySQL-server.
```
sudo apt install mysql-server
```
 Start MySQL.
 ```
 sudo mysql
 ```
 Exit MySQL console.
 ```
 exit
 ```

### :computer: Installer PHP.
```
sudo apt install php libapache2-mod-php php-mysql
```
Se hvilken PHP version der er installeret.
```
php -v
```
![php-version](images/php-version.png)

Test om PHP virker, opret en ny fil der hedder info.php
```
nano /var/www/webster/info.php
```
Gå til dit website igen.
```
webster.localhost/info.php
```
![info-php](images/info-php.png)

### :computer: Tester database forbindelsen.
```
mysql
```
Opretter database.
```
CREATE DATABASE example_database;
```
Opretter user.
```
CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';
```
```
GRANT ALL ON example_database.* TO 'example_user'@'%';
```
```
exit
```
```
mysql -u example_user -p
```
```
SHOW DATABASES;
```
```
Output
+--------------------+
| Database           |
+--------------------+
| example_database   |
| information_schema |
+--------------------+
2 rows in set (0.000 sec)
```
```
CREATE TABLE example_database.todo_list (
    item_id INT AUTO_INCREMENT,
    content VARCHAR(255),
    PRIMARY KEY(item_id)
);
```
```
INSERT INTO example_database.todo_list (content) VALUES ("My first important item");
INSERT INTO example_database.todo_list (content) VALUES ("My 2nd important item");
INSERT INTO example_database.todo_list (content) VALUES ("My 3rd important item");
INSERT INTO example_database.todo_list (content) VALUES ("My 4th important item");
INSERT INTO example_database.todo_list (content) VALUES ("My 5th important item");
```

```
SELECT * FROM example_database.todo_list;

Output
+---------+--------------------------+
| item_id | content                  |
+---------+--------------------------+
|       1 | My first important item  |
|       2 | My 2nd important item    |
|       3 | My 3rd important item    |
|       4 | and 4th one more thing   |
|       4 | and 5th one more thing   |
+---------+--------------------------+
4 rows in set (0.000 sec)
```



