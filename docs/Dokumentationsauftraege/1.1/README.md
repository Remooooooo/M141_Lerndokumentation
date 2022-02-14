# 1.1

## SQL

SQL ist ein relationales Datenbankmanagementsystem. Diese Sprache besteht aus drei wesentlichen Aufgaben:

- Daten Abfragen
  
  ```sql
  Select, Delete, Insert
  ```

- Datenstrukturen erstellen
  
  ```sql
  Create, Drop
  ```

- Daten Verändern
  
  ```sql
  Alter
  ```
  
## SQL DDL

## SQL DML

## ACID

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

### B

### A

### S

### E

## Transaktion

Teilschritte einer Übermittlung.
Transaction control language definiert den ganzen Ablauf, falls ein Fehlschag geschieht oder die Schritte erfolgreich Übermittelt wurden.
