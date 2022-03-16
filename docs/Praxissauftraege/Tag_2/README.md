# Tag 2

## MySQL Auftrag

...kann die notwendigen Installationskomponenten für MySQL benennen und beschreiben
...kann eine MySQL-Installation auf einem Linux-System durchführen
...kann ein MySQL-DBMS auf seine Lauffähigkeit testen (Version auslesen, Verbindung aufbauen, Start/Shutdown) und bedienen

## Recherche MySQL

### Hersteller

MySQL wurde Unternehmen MySQL AB entwickelt. Das Unternehmen wurde 2008 aufgekauft, 2010 wurde es erneut aufgekauft und gehört nun zu Oracle.

### Lizenzen

Für MySQL wird von Oracle ein duales Lizenzsystem angeboten. Zum einen gibt es die DB als freeware, jedoch gibt es auch eine kommerzielle Lizenz.

### Support

Für den Support gibt es ebenfalls verschiedene Lizenzen. Je nach Leistung gibt es Silver, Gold und Platinum. Bei allen ist der Lieferumfang und der Preis unterschiedlich.

### Software Features und Releasezyklen

MySQL wird regelmäsig alle 3 Monate mit einem neuen Release versorgt. Die aktuellste Version, Stand 02.2022. ist die Version 5.7.37.

Features gibt es eine ganze Palette, die wichtigsten sind:

- Free Open Source
- Speed
- High Flexibility
- Allows rollback
- High Performance
- Memory efficiency

(Alle vor und Nachteile sind hier zu finden https://www.javatpoint.com/mysql-features)

## Recherche Installation

### Welches Betriebssystem setzen Sie ein? Hardware

Ich setze die bereitgestellte VM ein. Es werden 4GB RAM und 2 CPU Cores zur verfügung gestellt.
Das OS ist Ubuntu, welches eine Linux Distribution ist die auf Debian basiert.

### In welcher Version ist Ihr Betriebssystem vorhanden? Kernel

Es handelt sich um ein Ubuntu in der Version 21.04.

Die Kernel Version ist 5.11. Die Mainline Kernel Version ist 5.11.22

Command: `cat /proc/version_signature`

### Aus welcher Quelle installieren Sie Ihre Software

Mit dem Command `sudo apt show mysql-server` habe ich unter anderem folgende Informationen erhalten:

Source: mysql-8.0  
Origin: Ubuntu  
Homepage: http://dev.mysql.com/
APT-Sources: http://us.archive.ubuntu.com/ubuntu hirsute-updates/main amd64 Packages
Maintainer: Ubuntu Developers

### In welcher Version installieren Sie Ihre Software und deren Abhängigkeiten

Mit dem selben Befehl habe ich folgende Version erhalten:

Version: 8.0.27-0ubuntu0.21.04.1

### Welche Dienste laufen sonst noch auf dem Server

```bash
service --status-all | grep '\[ + \]'
 [ + ]  acpid
 [ + ]  alsa-utils
 [ + ]  apparmor
 [ + ]  apport
 [ + ]  avahi-daemon
 [ + ]  binfmt-support
 [ + ]  cron
 [ + ]  cups
 [ + ]  cups-browsed
 [ + ]  dbus
 [ + ]  docker
 [ + ]  gdm3
 [ + ]  grub-common
 [ + ]  hddtemp
 [ + ]  irqbalance
 [ + ]  kerneloops
 [ + ]  kmod
 [ + ]  lm-sensors
 [ + ]  multipath-tools
 [ + ]  mysql
 [ + ]  openvpn
 [ + ]  plymouth-log
 [ + ]  procps
 [ + ]  rsyslog
 [ + ]  ssh
 [ + ]  udev
 [ + ]  ufw
 [ + ]  virtualbox
 [ + ]  whoopsie
```

## Installationsprozess

### Welche Software (inkl. Abhängigkeiten) wird installiert? Welche Version

MySQL: Version: 8.0.27-0ubuntu0.21.04.1

### Ablauf der Installation dokumentiert über Printscreens

1 - VM Update
`sudo apt update`
![Image1](/Images/Step1.png)

2 - Install MySQL
`sudo apt install mysql-server`
![Image2](/Images/Step2.png)

3 - Secure Script
`sudo mysql_secure_installation`
![Image3](/Images/Step3.png)

4 - Change Root PW
![Image4](/Images/Step4.png)

```php
sudo mysql

ALTER USER 'root'@'localhost' IDENTIFIED WITH caching_sha2_password BY 'password';

FLUSH PRIVILEGES; # Übernimmt Änderungen

SELECT user,authentication_string,plugin,host FROM mysql.user; # Überprüfen änderungen

exit
```

### Beschreiben Sie einzelnen Installationsschritte und Entscheidungen die Sie getroffen haben

Ich habe Schritt für Schritt die Anleitung von DigitalOceans verfolgt, da ich nicht sehr versiert mit MySQL konfigurationen bin.

Zugriff für Passwortauthentifizierung mittels Root:
`mysql -u root -p`

## Testing

### Dokumentieren Sie wie Sie Ihre Installation überprüfen und was Sie dabei überprüfen

Da MySQL ein Service ist, kann man den Service Status mit systemctl überprüfen
`sudo systemctl status mysql`

Ebenfalls kann man sich mit `sudo mysql` auf dem DBMS anmelden und schauen ob dies funktioniert

Mit `SHOW DATABASES;` können bestehende Datenbanken angezeigt werden.
