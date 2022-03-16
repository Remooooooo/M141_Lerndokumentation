# Tag 4

## Benutzer und Berechtigungen

### Erstellen Sie eine Demo-Datenbank mit zwei Tabellen

```bash
mysql> show tables;
+----------------+
| Tables_in_Demo |
+----------------+
| Persons        |
| cars           |
+----------------+

```

### Root-Benutzer konfiguriert (Login/Passwort)

Die Kennwort Einstellungen können mit dem Secure Installation Script gesetzt werden. Jene sind bei Tag 2 dokumentiert.

Anfangs sieht die Konfiguration so aus:

```bash
mysql> use mysql;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> select host,user,authentication_string from user;
+-----------+------------------+------------------------------------------------------------------------+
| host      | user             | authentication_string                                                  |
+-----------+------------------+------------------------------------------------------------------------+
| localhost | debian-sys-maint | $A$005$^[@uj .
                                               ?5::R[8
                                                      GDfs4ctHrw2100kmQO4k5XbfHEGB/exYRYPps0OInv9 |
| localhost | mysql.infoschema | $A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED |
| localhost | mysql.session    | $A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED |
| localhost | mysql.sys        | $A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED |
| localhost | root             |                                                                        |
+-----------+------------------+------------------------------------------------------------------------+
```

### Benutzer konfiguriert (Login/Passwort/Berechtigung auf Datenbank eingeschränkt <- ein Anwendungsbenutzer)

### Admin-Benutzer konfiguriert (Login/Passwort/Berechtigung auf Datenbank eingeschränkt)

## Server-Konfiguration

(Tag4) Server-Betrieb

https://moodle.bztf.ch/pluginfile.php/155522/mod_resource/content/1/site/01_script/210_KonfigurationRDBMS/
https://moodle.bztf.ch/pluginfile.php/155522/mod_resource/content/1/site/02_uebungen/711_KonfigurationMySQL/
