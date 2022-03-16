# Tag 5

## INNER JOIN

80% der Anfragen von uns sind Inner Joins. Es werden nur Daten gelistet, welche eine Bedingung erfüllen.

Beispiel:

```sql
SELECT Schueler.Vorname from Schueler s 
JOIN belegt b 
ON s.SNr = b.SNr;
```

Selektion von Daten

```sql
select s.Nachname, k.Fach from Schueler s
    join belegt b
    on s.SNr = b.SNr
    join Kurs k
    on b.KursNr = k.KursNr;
```

SELECT Schueler.Vorname from Schueler s
LEFT JOIN belegt b
ON s.SNr = b.SNr;

Left Join

SELECT Schueler.nachname, Schueler.vorname, Schueler.SNr, belegt.SNr, belegt.KursNr FROM Schueler
Right JOIN belegt
ON Schueler.SNr = belegt.SNr;

## Storage Engines bei MySQL

Storage Engines beschreiben wie eine Datenbank die Daten speichern. Es ist möglich, seine eigene Engine zu entwickeln, jedoch haben die meisten Datenbanken ihre eingenen Storage engines.

### Auflistung Restliche Engines

Mit dem Befehl `Show Engines;` können alle Storage Engines angezeigt werden, welche MySQL unterstützt.

```bash
mysql> show engines;

+--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
| Engine             | Support | Comment                                                        | Transactions | XA   | Savepoints |
+--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
| ARCHIVE            | YES     | Archive storage engine                                         | NO           | NO   | NO         |
| BLACKHOLE          | YES     | /dev/null storage engine (anything you write to it disappears) | NO           | NO   | NO         |
| MRG_MYISAM         | YES     | Collection of identical MyISAM tables                          | NO           | NO   | NO         |
| FEDERATED          | NO      | Federated MySQL storage engine                                 | NULL         | NULL | NULL       |
| MyISAM             | YES     | MyISAM storage engine                                          | NO           | NO   | NO         |
| PERFORMANCE_SCHEMA | YES     | Performance Schema                                             | NO           | NO   | NO         |
| InnoDB             | DEFAULT | Supports transactions, row-level locking, and foreign keys     | YES          | YES  | YES        |
| MEMORY             | YES     | Hash based, stored in memory, useful for temporary tables      | NO           | NO   | NO         |
| CSV                | YES     | CSV storage engine                                             | NO           | NO   | NO         |
+--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
```

Mit diesem Befehl kann die Standard Engine angepasst werden: `SET default_storage_engine=MyISAM;`

### Wichtigste Storage Engines

Wie man erkennen kann, ist InnoDB die Standard Engine auf MySQL. Dies ist jedoch erst seit version 5.5.1 der Fall. Davor war es MyISAM, da diese sehr effizient war und eine hohe Performance besass.
InnoDB ist nun der Standard, da mit ihr ACID am besten realisiert werden kann. Die konsistenz der Daten ist sehr hoch, jedoch ist der Zugriff dadurch ein wenig langsamer.

Grundsätzlich ist InnoDB eine gute Engine für Grosse Storage Systeme. MyISAM lohnt sich vorallem bei Web-Applikationen, da sie sehr perfomant ist.

### Anwendung auf Tabelle

Falls eine Tabelle mit einem Create erstellt wird, benutzt MySQL die Standard Engine. Falls jene nicht verwendet werden will, kann mit dem bereits aufgelisteten Befehl die Engine gewechselt werden.

Ausserdem besteht die möglichkeit, die Engine für jede Tabelle direkt beim Erstellen mitzugeben.

`CREATE TABLE Table (ID INT) ENGINE = MyISAM;`
