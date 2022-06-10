# Tag 14

## InnoDB Cluster

### Installation

[https://github.com/garutilorenzo/mysql-innodb-cluster](Link)

### Analyse

#### Welche Services werden aufgebaut? Zeichnen Sie ein kleines Netzwerkdiagramm

#### Welche Netzwerk werden aufgebaut? Erweitern Sie das Diagramm

#### In der Datei config/master.cnf sehen Sie die Konfiguration für den Master-Node

#### In der Datei config/replica.cns sehen Sie die Konfiguration für den Slave-Node

### Ausführung

#### Starten

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


mysql-js> var cluster = dba.getCluster()
mysql-js> cluster.status()
Dokumentieren Sie die Informationen zum Cluster! (und stoppen Sie das Ganze wieder mit docker-compose down)
```

## ProxySQL und SpringBoot

### Analysieren Sie die Datei docker-compose.yml

#### Welche Services werden aufgebaut, Zeichnen Sie ein kleines Netzwerkdiagramm

#### Welche Netzwerk werden aufgebaut, Erweitern Sie das Diagramm

#### Führen Sie nun die Schritte im Repo-README durch

