## :spider_web: Web-server-HF2

# Indholdsfortegnelse:
* [Apache](#computer-apache2)
* [VirtualHosts](#computer-virtualhost)
* [Ubunto Firewall (UFW)](#computer-ubunto-firewall-ufw)
* [MySQL](#computer-mysql)
* [PHP](#computer-php)
* [Wordpress](#computer-wordpress)

Først downloader og installere jeg Ubuntu Desktop. Desktop er bare lidt nemmere i denne sammenhæng. Skulle web-serveren bruges i den virkelige verden var valget faldet på Ubuntu Server, men i denne sammenhæng gør det ingen forskel.

## :computer: Apache2
Lad os starte med at installere Apache2.
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

## :computer: VirtualHost
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
Retter/tilføjer følgende linjer i de to .conf-filer.
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

## :computer: Ubunto Firewall (UFW)
Dette er nødvendigt hvis siden skal være tilgængelig uden for LAN.
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

## :computer: MySQL
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

## :computer: PHP
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
sudo nano /var/www/html/biblioteket2.opgave/public_html/info.php
```
Indsæt følgende i info.php og gem den.
```
<?php
phpinfo();
?>
```
Gå til dit website igen.
```
biblioteket2.opgave/info.php
```
![info-php](images/php-info.png)

## :computer: Wordpress
Starter med at logge ind i MySQL.
```
mysql -u root -p
```
Opretter database kun til Wordpress brug.
```
CREATE DATABASE wordpress DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;
```

Opretter brugeren "wordpressuser" med tilhørende kodeord "password".
```
CREATE USER 'wordpressuser'@'%' IDENTIFIED WITH mysql_native_password BY 'password';
```

Giver den nyoprettede bruger adgang til databasen wordpress.
```
GRANT ALL ON wordpress.* TO 'wordpressuser'@'%';
```

Til sidst.
```
FLUSH PRIVILEGES;
og
EXIT;
```

Henter og installere PHP Extensions.
```
sudo apt install php-curl php-gd php-mbstring php-xml php-xmlrpc php-soap php-intl php-zip
```
Genstart Apache.
```
sudo systemctl restart apache2
```

Opdatere Apache's .htaccess konfigurationer.
```
sudo nano /etc/apache2/sites-available/wordpress.conf
```
Indsæt følgende i VirtualHost blokken.
```
<Directory /var/www/wordpress/>
    AllowOverride All
</Directory>
```

Aktiver Rewrite Module.
```
sudo a2enmod rewrite
```
Ovenstående gør det muligt at lave permalinks der er nemmere at læse for mennesker.
```
http://example.com/2012/post-name/
http://example.com/2012/12/30/post-name
```
Check for syntax fejl inden vi går videre.
```
sudo apache2ctl configtest
```
![syntax-ok](images/syntax-ok.png)

Genstart Apache endnu en gang.
```
sudo systemctl restart apache2
```

### Download Wordpress
```
cd /tmp
curl -O https://wordpress.org/latest.tar.gz
```
Pak filen ud.
```
tar xzvf latest.tar.gz
```
Opret dummy .htaccess fil som Wordpress kan bruge senere.
```
touch /tmp/wordpress/.htaccess
```
Lav en kopi af wp-config-sample.php Wordpress rent faktisk kan læse.
```
cp /tmp/wordpress/wp-config-sample.php /tmp/wordpress/wp-config.php
```
Opretter "upgrade" mappe til Wordpress for fremtide opgraderinger.
```
mkdir /tmp/wordpress/wp-content/upgrade
```
Kopier hele mappen wordpress over til vores mappe på server delen. "." betyder den også tager f.eks. .htaccess filer med.
```
sudo cp -a /tmp/wordpress/. /var/www/wordpress
```

Konfiguration af WordPress Directory.

Giver Apache read og write til Wordpress filerne.
```
sudo chown -R www-data:www-data /var/www/wordpress
```
Sætter rettigheder på mapper og filer.
```
sudo find /var/www/wordpress/ -type d -exec chmod 750 {} \;
sudo find /var/www/wordpress/ -type f -exec chmod 640 {} \;
```

Konfiguration af WordPress Configuration File.

For at hente sikre værdier fra WordPress-hemmelige nøglegenerator, skriv:
```
curl -s https://api.wordpress.org/secret-key/1.1/salt/
```
!NEDESTÅENDE ER KUN FOR SHOW - BRUG IKKE DENNE KEY!
```
define('AUTH_KEY',         '1jl/vqfs<XhdXoAPz9 DO NOT COPY THESE VALUES c_j{iwqD^<+c9.k<J@4H');
define('SECURE_AUTH_KEY',  'E2N-h2]Dcvp+aS/p7X DO NOT COPY THESE VALUES {Ka(f;rv?Pxf})CgLi-3');
define('LOGGED_IN_KEY',    'W(50,{W^,OPB%PB<JF DO NOT COPY THESE VALUES 2;y&,2m%3]R6DUth[;88');
define('NONCE_KEY',        'll,4UC)7ua+8<!4VM+ DO NOT COPY THESE VALUES #`DXF+[$atzM7 o^-C7g');
define('AUTH_SALT',        'koMrurzOA+|L_lG}kf DO NOT COPY THESE VALUES  07VC*Lj*lD&?3w!BT#-');
define('SECURE_AUTH_SALT', 'p32*p,]z%LZ+pAu:VY DO NOT COPY THESE VALUES C-?y+K0DK_+F|0h{!_xY');
define('LOGGED_IN_SALT',   'i^/G2W7!-1H2OQ+t$3 DO NOT COPY THESE VALUES t6**bRVFSD[Hi])-qS`|');
define('NONCE_SALT',       'Q6]U:K?j4L%Z]}h^q7 DO NOT COPY THESE VALUES 1% ^qUswWgn+6&xqHN&%');
```

Åbner WordPress configuration filen og sætter den ny generede key ind.
```
sudo nano /var/www/wordpress/wp-config.php
```
I den samme fil sættes de sidste ting op.
```
// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define( 'DB_NAME', 'wordpress' );

/** MySQL database username */
define( 'DB_USER', 'wordpressuser' );

/** MySQL database password */
define( 'DB_PASSWORD', 'password' );
```
Gem og luk.

Åben en webbrowser og skriv "wordpress/" i adresse-feltet og start web-installationen.
![wordpress-install](images/wordpress-install.png)