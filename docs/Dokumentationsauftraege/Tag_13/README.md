# Tag 13

## Dokumentieren Sie die wichtigsten Regeln für das Datenbankdesign bei MongoDB

### Vergleich Relational und Schemalos

In einer Relationalen Datenbank werden Joins benutzt, wenn Daten aus mehreren Tabellen angezeigt werden sollen. Der Vorteil in den Schemalosen Datenbanken wie MongoDB sind die Nested Documents.
Mit ihnen können die Daten strukturiert abgebildet werden.

```bash
{
    "first_name": "Remo",
    "surname": "Stark",
    "canton": "Thurgau",
    "hobbies": ["gaming", "shooting"],
    "cars": [
        {
            "model": "Ferrari",
            "year": 1999
        },
        {
            "model": "Corvette",
            "year": 2016
        }
    ]
}
```

### Embedding vs Referencing

Mittels $lookup kann man einen Query aufrufen, der einen Left Outer Join erstellt.
Wenn wir 2 Collections haben können wir die so miteinander verbinden. (referenz, join, aggregation, etc.)

(Dies erstellt wie unter SQL eine Primary zu Foreign Key connection)

Grob kann man sagen, man soll immer embedden wenn es möglich ist.  
Ausser es gibt gute Gründe dagegen. Zum Beispiel, wenn die Elemente des Arrays ins unendliche ansteigen kann. (Log Nachriten mit einem User)
Anderes Beispiel wäre wenn man explizit weiss, das man ein einzelnes Objekt anzeigen muss. Falls es nested wäre, müssten immer alle Daten geladen werden.

(Hier noch als Regeln)

1. Favorisieren Sie Embedded Documents, ausser es gibt gute Gründe dagegen
2. Ist es nötig ein einzelnes Document auch einzeln anzeigen zu können, kann das ein Grund sein dieses Document nicht zu embedden
3. Versuchen Sie Joins/Lookups wann immer möglich zu vermeiden. Hilft es allerdings ein besseres(performanteres) Schema zu erreichen - arbeiten Sie mit Referenzen
4. Nested Arrays sollten nicht "unbegrenzt" gross werden können.
5. Many-To-Many - hier müssen Sie mit Referenzen arbeiten

```bash

{
   $lookup:
     {
       from: <collection to join>,
       localField: <field from the input documents>,
       foreignField: <field from the documents of the "from" collection>,
       as: <output array field>
     }
}

db.orders.aggregate( [
   {
     $lookup:
       {
         from: "inventory",
         localField: "item",
         foreignField: "sku",
         as: "inventory_docs"
       }
  }
] )

{
   "_id" : 1,
   "item" : "almonds",
   "price" : 12,
   "quantity" : 2,
   "inventory_docs" : [
      { "_id" : 1, "sku" : "almonds", "description" : "product 1", "instock" : 120 }
   ]
}
{
   "_id" : 2,
   "item" : "pecans",
   "price" : 20,
   "quantity" : 1,
   "inventory_docs" : [
      { "_id" : 4, "sku" : "pecans", "description" : "product 4", "instock" : 70 }
   ]
}
{
   "_id" : 3,
   "inventory_docs" : [
      { "_id" : 5, "sku" : null, "description" : "Incomplete" },
      { "_id" : 6 }
   ]
}

```

### Schlusswort Referencing

Vorteile:

- Kleinere Dokumente als nur 16MB
- Allgemein kleinere Dokumente
- Daten müssen nicht immer neu geladen werden
- Weniger Redundanzen

Nachteil:

Die Abfragen werden komplexer.

### Schlusswort Embeding

Vorteile:

- Keine Joins
- ACID kann umgesetzt werden
- Eine einzelne Query lädt alle Daten

Nachteil:

Kann durch den einzelnen Query zu Performence issues führen.

### Beispiele

#### One to One

Mittels Key-Value Pairs

```bash
{
 "_id": "ObjectId('AAA')",
 "name": "Joe Karlsson",
 "company": "MongoDB",
 "twitter": "@JoeKarlsson1",
 "twitch": "joe_karlsson",
 "tiktok": "joekarlsson",
 "website": "joekarlsson.com"
}
```

#### One to Few

Hier ist die Lösung ein Array aus Dokumenten. (Nested Documents)

```bash
{
 "_id": "ObjectId('AAA')",
 "name": "Joe Karlsson",
 "company": "MongoDB",
 "twitter": "@JoeKarlsson1",
 "twitch": "joe_karlsson",
 "tiktok": "joekarlsson",
 "website": "joekarlsson.com",
 "addresses": [
     { "street": "123 Sesame St", "city": "Anytown", "cc": "USA" },  
     { "street": "123 Avenue Q",  "city": "New York", "cc": "USA" }
 ]
}
```

#### One to Many

 Hier bietet es sich an, zwei Collections zu bauen und mittels Referenzierung zu arbeiten.

Beispiel:

```bash
# Products:
{
   "name": "left-handed smoke shifter",
   "manufacturer": "Acme Corp",
   "catalog_number": "1234",
   "parts": ["ObjectID('AAAA')", "ObjectID('BBBB')", "ObjectID('CCCC')"]
}
# Parts:
{
   "_id" : "ObjectID('AAAA')",
   "partno" : "123-aff-456",
   "name" : "#4 grommet",
   "qty": "94",
   "cost": "0.94",
   "price":" 3.99"
}
```

#### One to Squillions

Haben Sie riesige Mengen von Daten (z.B. Log-Messages), und diese Daten sind zudem noch unberechenbar in der Anzahl - arbeiten Sie mit Referenzen.

```bash
# Hosts :
{
   "_id": ObjectID("AAAB"),
   "name": "goofy.example.com",
   "ipaddr": "127.66.66.66"
}
# Log Message:
{
   "time": ISODate("2014-03-28T09:42:41.382Z"),
   "message": "cpu is on fire!",
   "host": ObjectID("AAAB")
}
```

## Indexing

### Index erstellen

Beim Indexieren wird immer ein Key - Value Pair erstellt.

Es gibt verschiedene Indexarten, hier ein paar Beispiele:

```bash
#Beispiel mit aufsteigender Sortierung
db.movies.createIndex(
        {year: 1}
)

# Beispiel mit Namensgebung vom Index

db.customer.createIndex(
        {customerID : 5},
    {name : "CustomerIdIndex"}
);

# Index mit mehreren Feldern
db.collection.createIndex({
    field1: type,
    field2: type,
    field3: type,
    ...
});

# Unique Index - Einzigartiges Feld in der Collection

db.collection.createIndex({ field: 1}, {unique: true});

#Bei mehreren Feldern
 db.collection.createIndex({field1: 1, field2: 1}, {unique: true});
```

Wenn der Index erstellt wurde, gibt es immer eine Rückmeldung.

### Index anzeigen

Auf einer Collection kann man Indexes anzeigen:

`db.movies.getIndexes()`

Es wird angezeigt, wie viele Indexes man hat, sowie welche Version das es ist und welche Felder indexiert werden.

### Index löschen

Einzelner Index löschen über deb Baneb:

`db.collection.dropIndex(index)`

Alle Indicies einer Collection löschen:

`db.collection.dropIndexes()`

### Analyse von Indexes

Bevor man Indexes produktiv nimmt, sollte man jene Analysieren. Dies macht man mit explain.

Weitere Befehle für explain:

- remove()
- update()
- count()
- aggregate()
- distinct()
- findAndModify()

Beispiel:

```bash
db.movies.explain("executionStats").find(
    {
        "year" : 2015
    },
    {
        "title" : 1,
        "awards.wins" : 1
    }
).sort(

    {"awards.wins" : -1}
)
```

Die wichtigsten Teile des Outputs sind dann:

```
...
                         "inputStage" : {
                              "stage" : "FETCH",
...
                              "inputStage" : {
                                   "stage" : "IXSCAN",
...
```

Dies ist gut, schlecht wäre ein COLLSCAN, da würde er die ganze Collection durchsuchen.

Weitere zu beachtende Parameter sind:

- executionTimeMillis: Die totale Zeit für die Ausführung der Query
- totalKeysExamined: Wieviele Dokumente wurden durchsucht
- totalDocsExamined: Wieviele Dokumente durch bearbeitet für die Query Condition
- nReturned: Wieviele Dokumente wurden zurück gegeben

## Sharding und Replication unter MongoDB

Falls die Performance von einer Applikation nicht mehr ausreicht, kann eine dieser Arten verwendet werden.

Vertikal: Mehr Ressourcen

Horizontal: Mehr nodes (Instanzen)

## Sharding

Unter Sharding versteht man, wenn eine Arbeitslast auf mehrere Nodes verteilt werden. Jedes Node kann unterschiedliche Aufgaben ausführen.

Das Sharding wird mittels Hashes oder Algorithmen oder mit Ranges ausgeführt. Jenes ist in der Erstellung sehr komplex.

### Sharding erstellen unter Mongo DB

(Copy Paste von C.Inauen)

Bei MongoDB ist die Implementierung von Sharding wie folgt gemacht. Wir benötigen:

"Shard" : Das ist eine MongoDB-Instanz, welche ein Teil der Daten hält
"Config Server" : Das ist eine MongoDB-Instanz, welche die Metadaten über den Cluster vorhält. Der Config-Server organisiert im Wesentlichen wo, welche Daten liegen
"Router" : Das ist eine MongoDB-Instanz, welche die Anfragen an die jeweiligen Nodes weiterleitet

### Schritt für Schritt Sharding

Schritt 1: ConfigDB erstellen mkdir ```/data/configdb```
Schritt 2: Starten der Instanz im Config-Modus ```mongod –configdb ServerD:27019```
Schritt 3: Starten der Cluster-Instanz als Shared Cluster ```mongos –configdb ServerD:27019```
Schritt 4: Wir verbinden uns auf den Config/Mongos Server ```mongo ServerD:27019```
Schritt 5: Wir führen unserem Cluster zwei Shard hinzu

```bash
sh.addShard("ServerA:27017")
sh.addShard("ServerB:27017")
```

Schritt 6: Wir aktivieren Sharding für eine Datenbank ```sh.enableSharding(Employeedb)```
Schritt 7: Wir aktivieren Sharding für eine Collection ```sh.shardCollection("db.Employee" , { "Employeeid" : 1 , "EmployeeName" : 1})```

## Replication

Unter Replication versteht man, wenn gleiche Nodes die gleichen Arbeiten erledigen.
Meist werden in der Praxis die Replika Server auf Read Only gestellt, da die meisten anfragen Read sind.

Zum Schluss wird mit einem Loadbalancer die Anfragen aufgeteilt.

Die beiden Arten lassen sich für eine noch bessere Skalierung auch kombinieren, was die beste Performance sowie Redundanz bietet, jedoch ist jene sehr teuer.

### Replication unter MongoDB

(Copy Paste von C.Inauen)

Bei MongoDB spricht man bei einer Gruppe von Nodes die zu einem Cluster gehören von einem "Replica Set". Dabei ist es so, dass man einen Primary Server in Betrieb nimmt der sich um alle Write-Aktivitäten kümmert. Die Secondary-Servers kümmern sich um die Read-Aktivitäten im Cluster.

### Replication Schritt für Schritt Anleitung

Schritt 1: Kontrollieren der Konnektivität zwischen den Instanzen
Schritt 2: Starten Sie eine Verbindung auf eine Instanz mit der replSet-Option ```mongo –replSet "Replica1```
Schritt 3: Nachdem wir das Replica Set erstellt haben, initialisieren wir den Cluster ```rs.initiate()```
Schritt 4: Weitere Server hinzufügen

```MongoDB
rs.add("ServerB")
rs.add("ServerC")
```

Kontrolle 1: Konfiguration betrachten ```rs.conf()```
Kontrolle 2: Status überprüfen ```rs.status()```
Kontrolle 3: Server entfernen ```rs.remove(ServerC)```
