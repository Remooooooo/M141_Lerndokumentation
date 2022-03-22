# Tag 6

## Import Export

### Import von SQL-Dateien

Import - lokal:

`mysql -u USERNAME -pPASSWORD DBNAME < SQLFILE.sql`

Import - remote:

`mysql -u USERNAME -pPASSWORD DBNAME -h HOSTIP < SQLFILE.sql`

Import - CSV Files:

```bash
mysqlimport --ignore-lines=1 \ # Header überspringen
--fields-terminated-by=, \ # Feld-Begrenzungen: Komma oder Semikolons
--local -u root -p Database \ # Zugriff auf die DB
TableName.csv # Filename
```

### Export in SQL-Dateien

Der Export von SQL Daten sollte in das Verzeichnis `/var/lib/mysql-files/` gelegt werden, damit es keine Berechtigungsprobleme gibt.

Man kann eine Datenbank, mehrere Datenbanken, Tabellen oder alle Datenbanken exportieren. Hier ein Beispiel von einem Datenbank Export:

```mysqldump -u NAME -p DBNAME > dump.sql```

Mit dem Parameter `-h` kann man auf einen anderen Host zugreiffen.

### (SELECT)-Export von Daten in CSV-Datei

So sieht ein Select Export in CSV aus:

```mysql
SELECT
orderNumber, status, orderDate, requiredDate, comments
FROM
orders
WHERE
status = 'Cancelled'
INTO OUTFILE '/tmp/cancelled_orders.csv'
FIELDS ENCLOSED BY '"'
TERMINATED BY ';'
LINES TERMINATED BY '\n';
```

Folgendes wurde hier definiert:

- Select: Normaler SQL Select
- Into Outfile: Am besten mit Temp files Arbeiten, absoluten Pfad angeben
- Fields Encloesd by: Jedes feld wird mit dem Zeichen eingefasst
- Terminated by: Feld wird mit diesem Zeichen beendet
- Lines Terminated by: Zeilenumbruch, in Linux \n, WIndows \r\n

## Migration

Logische Migration:
Bei dieser Migration wird erst ein gezippter Dump erstellt. welcher danach über SSH übertragen wird. Danach enzippt. Danach Importiert.

```bash
# Dump erstellen und direkt zippen
mysqldump --single-transaction -u root -p DB | gzip -9 > /tmp/DUMP_DB.sql.gz
# Files auf einen anderen Server kopieren, meistens über SSH
scp /tmp/DUMP_DB.sql.gz root@ANDERERSERVERIP:/tmp/
# Auf dem Zielserver neue NEUEDB erstellen
# Direktes Entzippen und importieren
gunzip < DUMP_DB.sql.gz | mysql -u root -p NEUEDB
```

Diese Migration geht aber auch effizienter und zwar mit Hilfe von Pipes un deiner SSH Verbindung.
