# Tag 10

## Recherche zu MongoDB

### Grundätzliches

Mongodb wurde 2007 entwickelt und 2009 erstmals veröffentlicht. Im Jahr 2013 wude die Firma in MongoDB Inc. umbenannt.

MongoDB ist eine OpenSource Datenbank welche in C++ programmiert wurde. Der begriff leitet sich aus hunongolus ab, was wörtlich übersetzt gigantisch bedeutet.

Die DB ist eine Document Database, das bedeutet, dass sie JSON Dokumente verwalten kann.

Beispielanwendungen:

- IOT
- Craigslist (Performancesteigerung faktor 10 seit Wechsel auf MongoDB)
- Grosse Datenmengen

MongoDB verfügt über einen eigenen speziellen Lizenzschlüssel, da Sie direkt von Google etc. mit angeboten wurden.

### Vorteile

- Umgang mit Grossen Datenmengen
- Hoch Skalierbar mittels sharding
- Strukturierte Daten ablegen mittels JSON (Objekte in Objekten abbiden)

### Begrifflichkeiten

Anders als in RDBMS gibt es in MongoDB einige Abweichungen im Naming:

| Relational | MongoDB                                          |
|------------|--------------------------------------------------|
| Database   | Database                                         |
| Table      | Collection                                       |
| column     | Field                                            |
| Row        | Document                                         |
| Table Join | Embedded Documents                               |
| PK         | Primary Key (Default key_id provided by MongoDB) |

### Features

Somit besteht jede Datenbank aus Collections und in jenen sind Documents abgelegt.

Jene Documents müssen nicht strukturiert werden. Dies kann nachträglich geschehen. Diese Struktur ist auch näher an objektorientierten Programmiersprachen.

### BSON

MongoDB benutzt BSON, das Binary Format von JSON. Es besteht aus Key Values. So ist ein Document aufgebaut:

```mongodb
var mydoc = {
               _id: ObjectId("5099803df3f4948bd2f98391"),
               name: { first: "Alan", last: "Turing" },
               birth: new Date('Jun 23, 1912'),
               death: new Date('Jun 07, 1954'),
               contribs: [ "Turing machine", "Turing test", "Turingery" ],
               views : NumberLong(1250000)
            }
```

Folgende Datensätze sind vorhanden:

- _id holds an ObjectId.
- name holds an embedded document that contains the fields first and last.
- birth and death hold values of the Date type.
- contribs holds an array of strings.
- views holds a value of the NumberLong type.
