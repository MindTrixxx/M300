## Apache Webserver Projekt
### Übersicht
Hier wird ein Docker Container mit Apache Server erstellt.

#### Apache Image erstellen
Dockerfile habe ich von unseren Vorlagen entnommen.

```
#
#	Einfache Apache Umgebung
#
FROM ubuntu:20.10


RUN apt-get update
RUN apt-get -q -y install apache2 

# Konfiguration Apache
ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOG_DIR /var/log/apache2

RUN mkdir -p /var/lock/apache2 /var/run/apache2

EXPOSE 80

VOLUME /var/www/html

CMD /bin/bash -c "source /etc/apache2/envvars && exec /usr/sbin/apache2 -DFOREGROUND"
```

Dannach habe ich das Image anhand des Dockerfiles erstellt:
`cd wo sich dockerfile befindet` --> Bei der Erstellung sieht man direkt die ID des Images

#### Container mit Image starten
Mit folgendem Befehlt, wird ein COntainer mit dem erstellten Image erstellt. Ebenfalls beinhaltet es die Namenssetzung vom Container und das Portforwarding vom Port 80 zu 8080:
`docker run --rm -d -p 8080:80 -v /web:/var/www/html --name ContainerNameSetzen ImageID`


Das Index File sieht folgendermassen aus:
```
<!DOCTYPE html>
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>M300</title>
</head>
<body>
<h1>LB02 GitHub Repository</h1>
<a>Das GitHub Repository für die LB02 ist unter folgendem Link erreichbar:</a>
<a href="https://github.com/MindTrixxx/M300/tree/main/LB02">Klicken Sie hier</a>
</body>
</html>
```

#### Testfall
Beim Testen kann man sehen, dass es funktioniert hat.
<img src="/Bilder/Bild5.jpg" alt="Check"/>

<img src="/Bilder/Bild6.jpg" alt="Check"/>