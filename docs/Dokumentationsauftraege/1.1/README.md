# 1.1

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

## ACID

Durch ACID gewährleistet, dass auf einer Datenbank nur gütlige und Konsistente Daten vorhanden sind.
Es sind nicht alle DB mit ACID "kompatibel". Fast alle SQL DB's sind kompatibel, gewisse NoSQL jedoch nur zu einem gewissen grad.

### A - Atomaritaet

Wenn eine Transaktion erfolgt, sind das mehrere kleine Transaktionen. (Z.B. insert, update, delete) Die Anweisungen werden einzeln durchgeführt, wobei alle transaktionen durchgehen `müssen`, also wenn eine fehlschlägt, werden alle abgebrochen und der alte Zustand wiederhergestellt.

### C - Konsistenz

Die Konsistenz stellt sicher, dass die Daten, welche eingetragen werden sollen, `gültig` sind.
Falls sie nicht gültig sind, wird der alte Zustand der DB wieder erzeugt.
Die Bedingungen für die `Integrität` und die `logische Konsitenz` werden geprüft.
Die Konsistenz wird vor und nach einer Transaktion sichergestellt. Während der Transaktion kann es vorkommen, dass inkonsistente Zustände auftreten.

### I - Isolation

Wenn eine Transaktion läuft, werden die Daten zur bearbeitung `gesperrt` und werden erst nach der Transaktion wieder freigegeben. Gewisse Operationen sind jedoch noch freigeben. Jede Transaktion wird ist `individuell` und eine Transaktion kann nicht auf resultate anderer Transaktionen zugreiffen.

### D - Dauerhafitigkeit

Nach erfolgreicher Transaktion werden die Daten `permanent`gespeichert, damit sie `unverändert` und `vollständig` sind. Auch bei einem Absturz sorgt diese SQL Eigenschaft für die Sicherheit der übermittelten Daten.

## BASE

Im vergleich zu ACID, ist BASE eher auf eine hohe verfügbarkeit ausgelegt.
Da wie bereits bei ACID angesprochen vorallem NoSQL DB's nicht mit ACID kompatibel sind, verwenden jene BASE.

### BA - Basically Avaialbe

Anders als bei ACID stellt BASE nicht die Konsistenz als höchste Priorität, sondern die Verfügbarkeit der DB.

### S - Soft state

Während des Schreibvorgangs müssen die Daten nicht konsistent sein, weder müssen die Kopien der Daten identisch sein.

### E - Eventual consistent

Die Konsistenz der Daten wird erst zu einem späteren Zeitpunkt stattfunden.

## Rückschlüsse

Da beide, SQL und NoSQL, viele Vor- und Nachteile haben, werden in vielen Projekten die beiden Systeme parallel betrieben, mit dem Ziel, alle Vorteile zu nutzen und die Nachteile zu beheben.

Falls die Daten sehr wichtig sind und die Konsistenz der Daten unabdingbar ist, wie z.B. eine Nutzer Authentifizierung, würde ich zu SQL greiffen.
Falls die Daten auch ein wenig später ausgeliefert werden können, ist NoSQL eine gute Wahl.

## Transaktion

Teilschritte einer Übermittlung.
Transaction control language definiert den ganzen Ablauf, falls ein Fehlschag geschieht oder die Schritte erfolgreich Übermittelt wurden.
