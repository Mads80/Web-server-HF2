# Web-server-HF2

Først henter jeg og installere Ubuntu Desktop. Desktop er bare lidt nemmere i denne sammenhæng. Skulle web-serveren bruges i den virkelige verden var valget faldet på Ubuntu Server, men i denne sammenhæng gør det ingen forskel.

Lad os starte med at installere Apache2
 
 ```
sudo apt update
sudo apt install apache2
```

:link: Succes:
![ubuntu-apache](images/ubuntu-apache.png)

Opret din egen side istedet for standard siden.

```
sudo mkdir /var/www/gci/

cd /var/www/gci/
nano index.html

<html>
<head>
  <title>Webster</title>
</head>
<body>
  <p>Dette er mit helt eget website!</p>
</body>
</html>
```

