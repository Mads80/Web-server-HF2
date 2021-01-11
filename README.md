# :spider_web: Web-server-HF2

Først downloader og installere jeg Ubuntu Desktop. Desktop er bare lidt nemmere i denne sammenhæng. Skulle web-serveren bruges i den virkelige verden var valget faldet på Ubuntu Server, men i denne sammenhæng gør det ingen forskel.

### :computer: Lad os starte med at installere Apache2:
 
 ```
sudo apt update
sudo apt install apache2
```
![ubuntu-apache](images/ubuntu-apache.png)

Opretter egne sites.

```
sudo mkdir -p /var/www/html/biblioteket1.opgave/public_html
sudo mkdir -p /var/www/html/biblioteket2.opgave/public_html

cd /var/www/html/biblioteket1.opgave/public_html
sudo nano index.html

cd /var/www/html/biblioteket2.opgave/public_html
sudo nano index.html
```
Opretter to index.html filer for de nye websites.
```
<html>
 <head>
 <title>biblioteket1.opgave</title>
 </head>
 <body>
 <h1>Hej, dette er en test-side for biblioteket1's hjemmeside.</h1>
 </body>
</html>

&

<html>
 <head>
 <title>biblioteket2.opgave</title>
 </head>
 <body>
 <h1>Hej, dette er en test-side for biblioteket2's hjemmeside.</h1>
 </body>
</html>
```

Sætter VirtualHost op så man kan se mit nye website.
```
cd /etc/apache2/sites-available/
```
Tager en kopi af 000-default.conf og gemmer den som biblioteket1.opgave.conf og biblioteket2.opgave.conf
```
sudo cp 000-default.conf biblioteket1.opgave.conf
sudo cp 000-default.conf biblioteket2.opgave.conf
```

Åbner biblioteket1.opgave.conf + biblioteket2.opgave.conf
```
sudo nano biblioteket1.opgave.conf
sudo nano biblioteket2.opgave.conf
```
Retter følgende linjer i de to .conf-filer.
```
ServerAdmin	webmaster@localhost
ServerName	biblioteket1.opgave
ServerAlias	www.biblioteket1.opgave
DocumentRoot	/var/www/html/biblioteket1.opgave/public_html
```
Luk og gem.

Aktiver de to nye sites.
```
sudo a2ensite biblioteket1.opgave.conf
sudo a2ensite biblioteket2.opgave.conf
```
Deaktiver den gamle standard site.
```
sudo a2dissite 000-default.conf
```

Mit nye website kan nu findes på biblioteket1.opgave og biblioteket2.opgave.
![biblioteket1-opgave](images/biblioteket1-opgave.png)

### :computer: Ubunto Firewall (UFW).

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



