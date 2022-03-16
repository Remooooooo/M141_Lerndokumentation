# Tag 4

## Benutzer und Berechtigungen

Wichtig:

Nach jeder Benutzeränderung `FLUSH PRIVILEGES;` durchführen!

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

Der root hat als plugin den auth socket für eine benutzerfreudnliche anmeldung. Dies wird geändert und ein Passwort gesetzt.

```bash
ALTER USER 'root'@'localhost' IDENTIFIED WITH caching_sha2_password BY 'password';
```

```bash
mysql> SELECT user,authentication_string,plugin,host FROM mysql.user;
+------------------+------------------------------------------------------------------------+-----------------------+-----------+
| user             | authentication_string                                                  | plugin                | host      |
+------------------+------------------------------------------------------------------------+-----------------------+-----------+
| debian-sys-maint | $A$005$^[@uj .
                                   ?5::R[8
                                          GDfs4ctHrw2100kmQO4k5XbfHEGB/exYRYPps0OInv9 | caching_sha2_password | localhost |
| mysql.infoschema | $A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED | caching_sha2_password | localhost |
| mysql.session    | $A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED | caching_sha2_password | localhost |
| mysql.sys        | $A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED | caching_sha2_password | localhost |
| root             | $A$005$)bL

^ P'Il`WOGGCnCjo.zXdaxM.I0Z594ftsqgQKZYRwzVTBNCSM5qmM91 | caching_sha2_password | localhost |
+------------------+------------------------------------------------------------------------+-----------------------+-----------+
```

Danach erfolgt die Anmeldung nicht mehr mit sudo mysql sondern mit `mysql -u root -p ` .

### Benutzer konfiguriert (Login/Passwort/Berechtigung auf Datenbank eingeschränkt <- ein Anwendungsbenutzer)

User erstellen und PW setzen:  
`CREATE USER 'user'@'localhost' IDENTIFIED BY 'User1234.';`

Berechtigungen für Benutzer:  
`GRANT SELECT, INSERT, UPDATE ON Demo.* TO 'user'@'localhost';`

#### Syntax und Ausführung User

```bash
CREATE USER 'user'@'localhost' IDENTIFIED BY 'User1234.';
Query OK, 0 rows affected (0.01 sec)

mysql> GRANT SELECT, INSERT, UPDATE ON Demo.* TO 'user'@'localhost';
Query OK, 0 rows affected (0.02 sec)

mysql> SHOW GRANTS FOR 'user'@'localhost';
+----------------------------------------------------------------+
| Grants for user@localhost                                      |
+----------------------------------------------------------------+
| GRANT USAGE ON *.* TO `user`@`localhost`                       |
| GRANT SELECT, INSERT, UPDATE ON `Demo`.* TO `user`@`localhost` |
+----------------------------------------------------------------+
```

### Admin-Benutzer konfiguriert (Login/Passwort/Berechtigung auf Datenbank eingeschränkt)

User erstellen und PW setzen:  
`CREATE USER 'admin'@'localhost' IDENTIFIED BY 'Admin1234.';`

Berechtigungen für den Admin Benutzer:  
 `GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP ON Demo.* TO 'admin'@'localhost';`

#### Syntax und Ausführung Admin

```bash
mysql> CREATE USER 'admin'@'localhost' IDENTIFIED BY 'Admin1234.';
Query OK, 0 rows affected (0.02 sec)

mysql> GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP ON Demo.* TO 'admin'@'localhost'
    -> ;
Query OK, 0 rows affected (0.01 sec)

mysql> SHOW GRANTS FOR 'admin'@'localhost';
+---------------------------------------------------------------------------------------+
| Grants for admin@localhost                                                            |
+---------------------------------------------------------------------------------------+
| GRANT USAGE ON *.* TO `admin`@`localhost`                                             |
| GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP ON `Demo`.* TO `admin`@`localhost` |
+---------------------------------------------------------------------------------------+
```

`FLUSH PRIVILEGES;` ausführen nicht vergessen nach einem Grant.

## Server-Konfiguration

### Transaktions-Isolation : Verfizieren Sie welche Transaktions-Isolation auf Ihrem Server aktiviert ist. Dokumentieren Sie, was das bezüglich den Anomalien für Ihre Installation bedeutet

Die Transaktions Isolation auf meinem Server ist REPEATABLE READ. Dies bedeutet ich bin vor allen Anomalien geschützt, bis auf Phantom Read.

```bash
mysql> SELECT @@transaction_isolation;
+-------------------------+
| @@transaction_isolation |
+-------------------------+
| REPEATABLE-READ         |
+-------------------------+
1 row in set (0.00 sec)
```

### Exportieren Sie die aktuelle Liste an System-Variablen

Es gibt Globale oder Sessionspezifische Variabeln. Jene kann man wie folgt anzeigen:  
`SHOW VARIABLES`  
`SHOW GLOBAL VARIABLES`

Export der Variablen in eine .txt:

`-ur oot -p -A -e"SHOW GLOBAL VARIABLES;" > MySQLCurrentSettings.txt`

### Netzwerkkonfiguration DBMS-Server anpassen und dokumentieren

Die Konfig von MySQL liegt unter:  
`/etc/mysql/mysql.conf.d/mysqld.cnf`

Für die Konfiguration muss bind_adress und mysqlx-bind-address die IP des Localhosts durch 0.0.0.0 ersetzt werden.

## Server-Betrieb

### Protokollierung langsamer Abfragen aktivieren

Da einige Querys zu lange Dauern gibt es eine Variable, welche jene loggt.  
`SELECT @@long_query_time;`

Im selben Konfigfile wie beim Netwerk gibt es diese Einstellungen:
Die Einstellungen sind noch Auskommentiert. Jene aktivieren und bei long_query_time die Zeit in Sekunden definieren.

```mysql
# Here you can see queries with especially long duration
# slow_query_log                = 1
slow_query_log_file   = /var/log/mysql/mysql-slow.log
long_query_time = 2
```

### Listen Sie den Inhalt des Data-Directories auf

Das Datadir ist der Ordner wo sich alle MySQL Daten befinden. Der Zugang funktioniert nur über den root User. Der Pfad ist:

`/var/lib/mysql`

```bash
root@m141vm:/var/lib/mysql# ls
 auto.cnf        ca-key.pem           ib_logfile0          public_key.pem
 binlog.000001   ca.pem               ib_logfile1          schooltest
 binlog.000002   client-cert.pem      ibtmp1               server-cert.pem
 binlog.000003   client-key.pem      '#innodb_temp'        server-key.pem
 binlog.000004   debian-5.7.flag      m141vm.pid           sys
 binlog.000005   Demo                 mysql                undo_001
 binlog.000006  '#ib_16384_0.dblwr'   mysql.ibd            undo_002
 binlog.000007  '#ib_16384_1.dblwr'   performance_schema
 binlog.000008   ib_buffer_pool       Pokemon
 binlog.index    ibdata1              private_key.pem
```

### Dokumentieren Sie die Default-Datenbanken mysql, sys, performance_schema

Bei MySQL sind standardmässig einige Datenbanken vorinstalliert.

```bash
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| Demo               |
| Pokemon            |
| information_schema |
| mysql              |
| performance_schema |
| schooltest         |
| sys                |
+--------------------+
```

#### mysql

Auf der mysql Datenbank werden diverse Tables gespeichert, welche notwendig sind, um den Server zu betreiben.

```bash
Database changed
mysql> show tables;
+------------------------------------------------------+
| Tables_in_mysql                                      |
+------------------------------------------------------+
| columns_priv                                         |
| component                                            |
| db                                                   |
| default_roles                                        |
| engine_cost                                          |
| func                                                 |
| general_log                                          |
| global_grants                                        |
| gtid_executed                                        |
| help_category                                        |
| help_keyword                                         |
| help_relation                                        |
| help_topic                                           |
| innodb_index_stats                                   |
| innodb_table_stats                                   |
| password_history                                     |
| plugin                                               |
| procs_priv                                           |
| proxies_priv                                         |
| replication_asynchronous_connection_failover         |
| replication_asynchronous_connection_failover_managed |
| replication_group_configuration_version              |
| replication_group_member_actions                     |
| role_edges                                           |
| server_cost                                          |
| servers                                              |
| slave_master_info                                    |
| slave_relay_log_info                                 |
| slave_worker_info                                    |
| slow_log                                             |
| tables_priv                                          |
| time_zone                                            |
| time_zone_leap_second                                |
| time_zone_name                                       |
| time_zone_transition                                 |
| time_zone_transition_type                            |
| user                                                 |
+------------------------------------------------------+
```

Wie man erkennen kann, gibt es informationen über Logs, Zeitzonen, Infos, Funktionen, Pliugins usw.

#### sys

Das sys-schema ist dazu da, die Daten vom performance Schema richtig zu interpretieren.

#### performance_schema

Das Performance Schema bietet die möglichkeit, auf dem laufenden Server diverse Performance überprüfungen durchzuführen.
Ebenfalls arbeitet jenes Schema mit der gleichnamigen implementierten Engine.

Die gesammelten Daten werden in der performance_schema dataenbank gespeichert und können mit SELECT Befehlen abgefragt werden.
