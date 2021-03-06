M300 - LB2
======

## Inhaltsverzeichnis
* Service
* Technische Angaben
* Testing
* Reflexion

## Service
Mein Service wird ein Apache mit phpMyadmin und MySQL zu realisieren. Da ich noch nie mit Docker gearbeitet habe hoffe ich das ich gut durch komme und eine gute LB2 abgebe.

## Technische Angaben
Für den Service braucht man eine Linux Desktop Umgebung, bei welcher man DOcker installieren muss. So installiert man Docker:
```Shell
sudo apt-get install docker.io
```
Um den ersten Container zu starten geht man in den Ordner webserver, dort befindet sich das Dockerfile. In diesem Ordner gibt man folgende Befehle ein:
```Shell
docker build -t webserver .
docker run --rm -d -p 8080:80 webserver
```
Das erste Dockerfile:
```Shell
#	Apache Umgebung 
FROM ubuntu:14.04
MAINTAINER Robin Winzenried

RUN apt-get update

RUN apt-get -q -y install apache2 

# Konfiguration Apache
ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOG_DIR /var/log/apache2

RUN mkdir -p /var/lock/apache2 /var/run/apache2


#phpMyadmin promt-Config
RUN echo 'phpmyadmin phpmyadmin/dbconfig-install boolean true' | debconf-set-selections
RUN echo 'phpmyadmin phpmyadmin/app-password-confirm password robin123' | debconf-set-selections
RUN echo 'phpmyadmin phpmyadmin/mysql/admin-pass password robin123' | debconf-set-selections
RUN echo 'phpmyadmin phpmyadmin/mysql/app-pass password robin123' | debconf-set-selections
RUN echo 'phpmyadmin phpmyadmin/reconfigure-webserver multiselect apache2' | debconf-set-selections


#phpMyadmin Installation
RUN sudo apt-get -y install phpMyadmin

EXPOSE 80

VOLUME /var/www/html

CMD /bin/bash -c "source /etc/apache2/envvars && exec /usr/sbin/apache2 -DFOREGROUND"
```
Für den zweiten Container geht man in den Ordner mysql rein, dort gibt man dann folgende Befehle ein:
```Shell
docker build -t mysql .
docker run --rm -d -p 3306:3306 mysql
```
Das Dockerfile:
```Shell
#MySQL Umgebung
FROM ubuntu:14.04

MAINTAINER Robin Winzenried


#Befehle für mysql-server
RUN echo 'mysql-server mysql-server/root_spassword password robin123' | debconf-set-selections 
RUN echo 'mysql-server mysql-server/root_password_again password robin123' | debconf-set-selections 

# Installation mysql
RUN apt-get update
RUN apt-get install -y mysql-server


#Port öffnen
RUN sed -i -e"s/^bind-address\s*=\s*127.0.0.1/bind-address = 0.0.0.0/" /etc/mysql/my.cnf


EXPOSE 3306

VOLUME /var/lib/mysql

CMD ["mysqld"]
```

## Testing
| Testfall                                                                                               | Resultat                                                                                                                                |
|--------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------|
| Container starten lassen                                        | Funktioniert                                    |
| Vom Client auf http://localhost:80/phpmyadmin                                                                                                | Funktioniert. Phpmyadmin Startseite wird angezeigt.                                                       | 
| Mit root und Passwort anmelden                                                                                            | Funktioniert                                                         |

## Reflexion
Ich habe bei dieser Leistungsbeurteilung viel neues gelernt. DOcker wird sicher in der Zukunft öfters vorkommen, da es sehr effizient ist. Ich habe am Anfang dieser Leistungsbeurteilung garnichts gecheckt aber nun weiss ich wie man ein DOckerfile erstellt und für was es gebraucht wird. Beim Testing habe ich mich durch andere Gruppen inspirieren lassen, da ich nicht viele Tests im Kopf hatte. Ich habe auch gelernt wie man eine Tabelle im Markdown erstellt. Ich fand dieses Modul sehr interessant und hoffe das ich mein Wissen auch in meiner Firma gebrauchen kann.
