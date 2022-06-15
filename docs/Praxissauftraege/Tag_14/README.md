# Tag 14

## InnoDB Cluster

### Installation

[https://github.com/garutilorenzo/mysql-innodb-cluster](Link)

### Analyse 

#### docker compose

Das Projekt beihaltet 3 Nodes sowie einen Router. Node 1 ist der Master, 2 und 3 die  Replikas. Dies lässt sich anhand der Pfade bestimmen

```bash

# Node 1
volumes:
      - ./config/master.cnf:/etc/mysql/mysql.conf.d/mysqld.cnf 

# Node 2
volumes:
      - ./config/replica.cnf:/etc/mysql/mysql.conf.d/mysqld.cnf

# Node 3
volumes:
      - ./config/replica-bis.cnf:/etc/mysql/mysql.conf.d/mysqld.cnf

```

#### master.cnf

Das master.cnf ist die Config Datei für den Master.

```bash

[mysqld]

server-id = 1
port = 3306 # Standardport für MySQL

binlog_format = ROW

gtid_mode=ON
enforce-gtid-consistency=true

# Updates der Slave
log-slave-updates
log_bin = mysql-bin

default_storage_engine = InnoDB

# replication

report-host = mysql_node01
slave_net_timeout = 60

skip-slave-start

transaction_isolation = 'READ-COMMITTED'

binlog_checksum = NONE
relay_log_info_repository = TABLE
transaction_write_set_extraction = XXHASH64

auto_increment_increment = 1
auto_increment_offset = 2

binlog_transaction_dependency_tracking = WRITESET 
slave_parallel_type = LOGICAL_CLOCK
slave_preserve_commit_order = ON

```

#### replica.cnf

Die Konfigdatei für den Node 2. Das Node 3 besitzt ein eigenes, identisches File.

```bash
[mysqld]

server-id = 3
port = 3306

binlog_format = ROW

gtid_mode=ON
enforce-gtid-consistency=true

log-slave-updates
log_bin = mysql-bin

default_storage_engine = InnoDB

# replication

report-host = mysql_node03
slave_net_timeout = 60

skip-slave-start
read_only

transaction_isolation = 'READ-COMMITTED'

binlog_checksum = NONE
relay_log_info_repository = TABLE
transaction_write_set_extraction = XXHASH64

auto_increment_increment = 1
auto_increment_offset = 2

binlog_transaction_dependency_tracking = WRITESET
slave_parallel_type = LOGICAL_CLOCK
slave_preserve_commit_order = ON

```

### Ausführung

#### Starten

Start der Umgebung: (Es darf keine andere Instanz mit dem selben Host laufen)

`docker-compose up -d`

Kontrolliere mittels:

```bash
docker-compose ps
docker-compose logs -f
```

Sie sollten eine Ausgabe wie folgt erhalten:

```bash
> docker-compose ps
            Name                              Command               State                                               Ports                                             
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
mysql-innodb-cluster_mysql_node01_1   docker-entrypoint.sh --def ...   Up      3306/tcp, 33060/tcp, 33061/tcp                                                                
mysql-innodb-cluster_mysql_node02_1   docker-entrypoint.sh --def ...   Up      3306/tcp, 33060/tcp, 33061/tcp                                                                
mysql-innodb-cluster_mysql_node03_1   docker-entrypoint.sh --def ...   Up      3306/tcp, 33060/tcp, 33061/tcp                                                                
mysql-innodb-cluster_router_1         docker-entrypoint.sh mysql ...   Up      0.0.0.0:3306->3306/tcp, 0.0.0.0:6446->6446/tcp, 0.0.0.0:6447->6447/tcp, 0.0.0.0:6606->6606/tcp
```

#### Kontrolle

Verfizieren Sie die Funktionalität des Clusters wie folgt:

```bash
# Login auf den Master Node
docker-compose exec mysql_node01 bash
# Auf dem Master -> Connection auf sich selbst
mysqlsh --js root@mysql_node01 # Login mit root    
Führe Sie nun folgende Befehle aus:

# Infos über den Cluster
mysql-js> var cluster = dba.getCluster()
mysql-js> cluster.status()
Dokumentieren Sie die Informationen zum Cluster! (und stoppen Sie das Ganze wieder mit docker-compose down)
```

## ProxySQL und SpringBoot

[Link](https://github.com/ivangfr/springboot-proxysql-mysql)

### Analysieren Sie die Datei docker-compose.yml

Es ist kein Docker File vorhanden.

#### Welche Services werden aufgebaut, Zeichnen Sie ein kleines Netzwerkdiagramm

![Diagram](../../../pictures/diagram.png)

### Führen Sie nun die Schritte im Repo-README durch

#### Start

Terminal im Root Folder öffnen.

  ```bash
  ./init-environment.sh
  ```

#### Status Applikation

Status der Umgebung überprüfen

  ```bash
  ./check-replication-status.sh
  ```

#### ProxySQL testen

In das Admin Terminal verbinden

  ```bash
  ./proxysql-admin.sh
  ```

Mit diesem Befehl die Server anzeigen

  ```bash
  SELECT * FROM mysql_servers;
  ```

Mit diesem Befehl die globalen Variablen anzeigen

  ```bash
  SELECT * FROM global_variables;
  ```

#### API starten

Den folgenden Maven command ausführen

  ```bash
  ./mvnw clean spring-boot:run --projects customer-api
  ```

Die Website ist:

[Link](http://localhost:8080/swagger-ui.html)

#### Testing

##### Schritt 1

Für das Testing müssen 3 Terminals gestartet werden.

Master : ```docker exec -it -e MYSQL_PWD=secret mysql-master mysql -uroot --database customerdb```

Slave 1: ```docker exec -it -e MYSQL_PWD=secret mysql-slave-1 mysql -uroot --database customerdb```

Slave 2 : ```docker exec -it -e MYSQL_PWD=secret mysql-slave-2 mysql -uroot --database customerdb```

##### Schritt 2

Logging aktivieren in jedem Terminal

```bash
SET GLOBAL general_log = 'ON';
SET global log_output = 'table';
```

##### Schritt 3

Neues Terminal für CURL Befehle, dort wird ein Customer erstellt

```bash
curl -i -X POST http://localhost:8080/api/customers \
  -H 'Content-Type: application/json' \
  -d '{"firstName": "Ivan", "lastName": "Franchin"}'
```

##### Schritt 4

Im Master einen Select ausführen. Wenn wir den selben Command in den Slaves ausführen, dass nur der Master den Insert ausführt.

   ```bash
   SELECT event_time, command_type, SUBSTRING(argument,1,250) argument FROM mysql.general_log
   WHERE command_type = 'Query' AND (argument LIKE 'insert into customers %' OR argument LIKE 'select customer0_.id %' OR argument LIKE 'update customers %' OR argument LIKE 'delete from customers %');
   ```

##### Schritt 5

API Return:

   ```bash
   curl -i http://localhost:8080/api/customers/1
   ```

##### Schritt 6

Auf einem der Slaves eingeben

```MYSQL
SELECT event_time, command_type, SUBSTRING(argument,1,250) argument FROM mysql.general_log
WHERE command_type = 'Query' AND (argument LIKE 'insert into customers %' OR argument LIKE 'select customer0_.id %' OR argument LIKE 'update customers %' OR argument LIKE 'delete from customers %');
```

##### Schritt 7

Update Customer im CURL Terminal

   ```bash
   curl -i -X PUT http://localhost:8080/api/customers/1 \
     -H 'Content-Type: application/json' \
     -d '{"firstName": "Ivan2", "lastName": "Franchin2"}'
   ```

##### Schritt 8

Select im Master Terminal

   ```bash
   SELECT event_time, command_type, SUBSTRING(argument,1,250) argument FROM mysql.general_log
   WHERE command_type = 'Query' AND (argument LIKE 'insert into customers %' OR argument LIKE 'select customer0_.id %' OR argument LIKE 'update customers %' OR argument LIKE 'delete from customers %');
   ```

##### Schritt 9

Delete Customer im CURL Terminal

   ```bash
   curl -i -X DELETE http://localhost:8080/api/customers/1
   ```

Und danach den Select im Master

   ```bash
   SELECT event_time, command_type, SUBSTRING(argument,1,250) argument FROM mysql.general_log
   WHERE command_type = 'Query' AND (argument LIKE 'insert into customers %' OR argument LIKE 'select customer0_.id %' OR argument LIKE 'update customers %' OR argument LIKE 'delete from customers %');
   ```

#### Shutdown

- To stop `customer-api` application, go to the terminal where it's running and press `Ctrl+C`.
- In order to get out of `MySQL Monitors` type `exit`.
- To stop and remove `MySQL`s and `ProxySQL` containers, network and volumes, make sure you are inside `springboot-proxysql-mysql` root folder and run the following script
  
  ```bash
  ./shutdown-environment.sh
  ```
  