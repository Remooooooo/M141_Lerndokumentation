# Tag 10

## Installation MongoDB

### Voraussetzungen der Installation (VM)

Da der Updateserver von MongoDB genutzt wird, muss ein Key installiert werden.

`wget -qO - https://www.mongodb.org/static/pgp/server-5.0.asc | sudo apt-key add -`

Falls wir kein OK erhalten, folgendes ausführen:

`sudo apt-get install gnupg`

Und nochmals installieren:

`wget -qO - https://www.mongodb.org/static/pgp/server-5.0.asc | sudo apt-key add -`

Update Source erfassen:

`echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/5.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-5.0.list`

### Prozess der Installation (Wie/Was habe ich es installiert?)

Update durchführen:

`sudo apt update`

Mongo DB Installieren

`sudo apt-get install -y mongodb-org`

### Test der Installation

Starten der Dienstes:

`sudo systemctl start mongod`

Stoppen der Dienstes:

`sudo systemctl stop mongod`

Status der Dienstes:

`sudo systemctl status mongod`

Sicherstellen das der Dienst auch nach dem Reboot gestartet wird:

`sudo systemctl enable mongod`

So sieht der Status aus und man erkennt das der Service läuft:

```mongodb
vagrant@m141vm:~$ sudo systemctl status mongod
● mongod.service - MongoDB Database Server
     Loaded: loaded (/lib/systemd/system/mongod.service; enabled; vendor preset: enabled)
     Active: active (running) since Thu 2022-04-21 10:33:16 CEST; 34s ago
       Docs: https://docs.mongodb.org/manual
   Main PID: 16112 (mongod)
     Memory: 62.7M
     CGroup: /system.slice/mongod.service
             └─16112 /usr/bin/mongod --config /etc/mongod.conf

Apr 21 10:33:16 m141vm systemd[1]: Started MongoDB Database Server.
```

## Erste Schritte mit MongoDB

`mongo`
Man landet in dem MongoDB Terminal. Standardverbindung auf localhost:27017.

`mongosh`
Man öffnet eine JS Konsole. (Eher neuer, gibt es auch in MySQL)

`use meinedatenbank`  
Erstellt die DB meinedatenbank

`db.createCollection[("meinecollection", {capped: true, size: 6142800, max: 10000})]`  
Erstellt Collection
Das Capped beschränkt die Dokumente in der Grösse, welche anschliessend angegeben wird. Es sind max. 10k Dokumente zugelassen.

`db.meinecollection.insertOne({Name:"Remo",Alter: 24, Geschlecht: "Männlich"})`  
Insert in die Collection

So sieht der befehl aus, beim Insert muss ein acknowledge true returned werden.
Neben dem verwendeten insert gibt es noch folgende:

```bash
.insertOne() # Fügt ein Dokument hinzu
.insertMany() # Füht verschiedene Dokumente hinzu
.insert() # Fügt mehrere Dokumente hinzu
```



```bash
> db.createCollection[("meinecollection", {capped: true, size: 6142800, max: 10000})]
> 
> db.meinecollection.insertOne({Name:"Remo",Alter: 24, Geschlecht: "Männlich"})
{
	"acknowledged" : true,
	"insertedId" : ObjectId("626118f4ad1fe209c2f21442")
}
> 
```

`db.meinecollection.find({Name:"Remo"})`  
Sucht alle Objekte mit dem Namen Remo

Folgender Befehl ist ein Update:

```bash
db.meinecollection.update(
{ Alter: 28 },
{
$set: { Alter: 30 }
}
)
```

`db.meinecollection.remove ( { Alter: 28 }, 1 )`
Remove Befehl mit dem just one Parameter. Ansonsten werden alle Datensätze mit dem Alter 28 gelöscht.

### Konfiguration

Data File:
Die Daten liegen auf dem /var/lib/mongodb und das Log Directory ist /var/log/mongodb.

ConfigurationFile:
Die Konfigurationsdatei ist /etc/mongod.conf. Die Datei ist in YAML formatiert.
Es beinhaltet die Einstellungen zu den Directories und die Konfigurationen zu Ports und IP Adressen.

#### Best Practice

Folgende Einstellungen für den Dienst werden als Best-Practise Konfiguriert:
Datei: /etc/systemd/system/<process-name>.service

```yaml
[Service]
# Other directives omitted
# (file size)
LimitFSIZE=infinity
# (cpu time)
LimitCPU=infinity
# (virtual memory size)
LimitAS=infinity
# (locked-in-memory size)
LimitMEMLOCK=infinity
# (open files)
LimitNOFILE=64000
# (processes/threads)
LimitNPROC=64000
```
