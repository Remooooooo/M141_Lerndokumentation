# Tag 5

## Datentypen

Datentypen werden in MySQL beim erstellen einer Tabelle definiert.

Die Datentypen werden in 3 Hauptklassen unterteilt:

- String Data Types
- Numeric Data Types
- Date abd Time Data Types

Die gängigsten sind varchar, int, float, date, time

### JSON

JSON ist bekannterweise ein Array, welcher eine Liste von getrennten Werten beinhaltet.
Der Vorteil von JSON ist, dass die Werte mit Indexes gespeichert werden, was performance bietet.

JSON arbeitet häufig mit Key Value Pairs und kommt unter anderem in MongoDB vor, weil es sich arbeitet mit nested Documents zu arbeiten.

Beispiel:

```mysql
mysql> CREATE TABLE Test (jdoc JSON);
Query OK, 0 rows affected (0.20 sec)

mysql> INSERT INTO Test VALUES('{"key1": "value1", "key2": "value2"}');
Query OK, 1 row affected (0.01 sec)
```

### ENUM

Ein ENUM ist ein String, welcher nur einen Wert haben kann. Dieser wert kann von einer vorgefertigten Liste ausgewählt werden.

Beispiel:

```mysql
CREATE TABLE shirts (
    name VARCHAR(40),
    size ENUM('x-small', 'small', 'medium', 'large', 'x-large')
);
INSERT INTO shirts (name, size) VALUES ('dress shirt','large'), ('t-shirt','medium'),
  ('polo shirt','small');
  ```

Der Vorteil bei ENUM ist, dass ENUM mit Indexes arbeitet. So werden die vorgegebenen Werte nicht jedes mal als Text abgespeichert, sondern als nummerischer Index, was sehr platzsparend ist.

## Indexierung

Ein Index ist eine Struktur, welche das durchsuchen von Datensätzen beschleunigt.
Durch einen Index wird eine zusätzliche Datenstruktur erstellt. Wenn grosse Datensätze häufig dursucht werden, macht es Sinn, mit Indexen zu arbeiten.
Ohne Index muss die DB bei einem Where die ganze Tabelle durchsuchen, daher kann es bei grossen Datensätzen mehrere Sekunden dauern.
Auf einem Index wird ebenfalls gespeichert, wo auf der Festplatte der gewünschte Wert gespeichert ist.

Folgende Optionen zum Indexieren werden von MySQL angeboten:

- Primary Key - oft eine ID, muss eindeutig sein, einmalig
- Unique - eindeutiger Wert, beliebig viele Werte
- Index - doppelte Werte pro Spalte
- Fulltext - Jedes Wort eines längeren Fulltext wird Inexiert. Macht nur bedingt Sinn.

Erstellung:
Ein Index kann z.B. über phpMyAdmin erstellt werden und in einer Tabelle per Dropdown ausgewählt werden.

Der Nachteil ist, dass eine zusätzliche Datenstruktur (Der Index) gepflegt werden muss und es zusätzlich Speicherplatz benötigt.

Es ist auch möglich, Indiezes über mehrere Spalten anzulegen, falls also mehrere Daten aus einer Spalte gesucht werden.
