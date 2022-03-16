# Tag 1

## SQL

SQL ist ein relationales Datenbankmanagementsystem. Diese Sprache besteht aus drei wesentlichen Aufgaben:

- Daten Abfragen
  
  ```sql
  SELECT, INSERT, UPDATE, DELETE, MERGE, CALL
  ```

Beispiel:

```sql
SELECT *
FROM NAME
```

- Datenstrukturen erstellen
  
  ```sql
  CREATE, ALTER, DROP, RENAME
  ```

Beispiel:

```sql
CREATE TABLE Mitarbeiter (PersNr INT NOT NULL PRIMARY KEY, Anrede VARCHAR NOT NULL, Name VARCHAR NOT NULL, Vorname VARCHAR NOT NULL);
```

- Daten Verändern
  
  ```sql
  ALTER
  ```

Beispiel:

```sql
ALTER TABLE Mitarbeiter
ADD Email varchar(255);
```

Jene 3 Aufgaben lassen sich in verschiedene Kategorien unterteilen, auf 2 gehen wir in dieser Doku näher ein. Die anderen 2 wären DCL und TCL, wobei es sich beim DCL um Kontrolle und Rechte handelt und bei TCL um die Transaktionskontrolle mit Commands wie `COMMIT` oder `ROLLBACK`.

## SQL DDL

DDL steht ist die Datendefinitionssprache, welche verwendet wird um die Struktur zu erstellen und zu beschreiben.

Zu Ihr gehören unter anderem Folgende Befehle:

`CREATE, ALTER, DROP, COMMENT, RENAME`

## SQL DML

Die Datenbearbeitungssprache wird verwendet um, wie der Name schon sagt, Daten zu bearbeiten. Man kann mit ihr unter anderem Daten schreiben, löschen, lesen oder ändern.

Zu Ihr gehören unter anderem Folgende Befehle:

`SELECT, INSERT, UPDATE, DELETE, MERGE`
