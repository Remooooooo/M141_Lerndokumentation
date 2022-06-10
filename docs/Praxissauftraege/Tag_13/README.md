# Tag 13

## Dokumentieren Sie die Übung zu Indexierung

### Vorbereitung

Neue DB namens phones erstellen und eine neue Funktion hinzufügen, für das Erstellen von Testdaten.

`use phones`

```bash
populatePhones = function(area, start, stop) {
      for(var i = start; i < stop; i++) {
        var country = 1 + ((Math.random() * 8) << 0);
        var num = (country * 1e10) + (area * 1e7) + i;
        var fullNumber = "+" + country + " " + area + "-" + i;
        db.phones.insert({
          _id: num,
          components: {
            country: country,
            area: area,
            prefix: (i * 1e-4) << 0,
            number: i
          },
          display: fullNumber
        });
        print("Inserted number " + fullNumber);
      }
      print("Done!");
    }    
```

### Daten generieren

Funktionsaufruf: `populatePhones(800, 5550000, 5650000)`

Kontrolle mittels Aufruf: `db.phones.find().limit(2)`

```bash
> db.phones.find().limit(2)
{ "_id" : 28005550000, "components" : { "country" : 2, "area" : 800, "prefix" : 555, "number" : 5550000 }, "display" : "+2 800-5550000" }
{ "_id" : 38005550001, "components" : { "country" : 3, "area" : 800, "prefix" : 555, "number" : 5550001 }, "display" : "+3 800-5550001" }
> 
```

### Indices auslesen

```bash
db.getCollectionNames().forEach(function(collection) {
     print("Indexes for the " + collection + " collection:");
     printjson(db[collection].getIndexes());
});
```

Ausgabe:

```bash
Indexes for the phones collection:
[
	{
		"v" : 2,
		"key" : {
			"_id" : 1
		},
		"name" : "_id_",
		"ns" : "phones.phones"
	}
]
> 
```

### Testing

Explain: `db.phones.explain("executionStats").find({display: "+1 800-5650001"})`

executionTimeMillis

- 39 ms

totalDocsExamind

- 100000

Typ des Scans:

- COLLSCAN

COLLSCAN bedeutet Collection Scan, was schlecht ist, da die ganze Collection durchsucht wird. Dadurch ist die Query enorm langsam.

### Index erfassen

Index erstellen:

```bash
db.phones.createIndex(
  { display : 1 },
  { unique : true, dropDups : true } 
)
```

### Testing und Vergleich

Explain: `db.phones.explain("executionStats").find({display: "+1 800-5650001"})`

executionTimeMillis

- 8ms

totalDocsExamind

- 0

Typ des Scans:

- FETCH

Die Geschwindigkeit hat sich enorm verschnellert. Ausserdem ist der Typ jetzt FETCH, dadurch werden 0 Docs durchsucht.

## Dokumentieren Sie die Übung Sharding und Replication

### Vorbereitung Replica

Directorys erstellen:

```mkdir ./mongo1 ./mongo2 ./mongo3```

### Instanzen Starten

3 Instanzen mit 3 verschiedenen Ports starten.

```bash
mongod --replSet book --dbpath ./mongo1 --port 27011
mongod --replSet book --dbpath ./mongo2 --port 27012
mongod --replSet book --dbpath ./mongo3 --port 27013
```

### Starten

Einloggen:
`mongo -port 27011`

Initialisierung starten:

```bash
rs.initiate({
    _id: 'book',
    members: [
      {_id: 1, host: 'localhost:27011'},
      {_id: 2, host: 'localhost:27012'},
      {_id: 3, host: 'localhost:27013'}
    ]
})

# Test

rs.status().ok
```

Als Output vom Test sollte OK:1 returned werden.

### Insert testen

Auf den ganzen Cluster verbinden:

`mongo mongodb://localhost:27011,localhost:27012,localhost:27013/replicaSet=book`

Insert:

`db.echo.insert({ say : 'HELLO!' })`

Test:

`db.echo.find()`

## Sharding

### Vorbereitung Sharding

Config File Erstellen und Config Server.

Danach Instanz Starten

```bash
mkdir ./mongoconfig

mongod --configsvr --replSet "config" --dbpath ./mongoconfig --port 27016
```

Darauf verbinden und initialisieren:

```bash
mongo localhost:27016

rs.initiate()
```

Danach kommt eine Rückmeldung (Muss ok:1 sein)

### Shards einrichten

Directorys erstellen

`mkdir ./mongo4 ./mongo5`

Die Instanzen wieder in 2 Terminals laufen lassen.

```bash
mongod --shardsvr --replSet "shard1" --dbpath ./mongo4 --port 27020

mongod --shardsvr --replSet "shard2" --dbpath ./mongo5 --port 27021
```

### Shard initialisieren

Auf beide verbinden und initiate

Shard1:

`mongo localhost:27020`

`rs.initiate()`

Shard2:

`mongo localhost:27021`

`rs.initiate()`

Bei beiden sollte "OK" kommen.

### Mongos Query Router

Diese Mongo instanz verbindet sich mit dem Config Server,

```mongos --configdb config/localhost:27016 --port 27025```

### Shards hinzufügen

Verbinden:

`mongo localhost:27025`

Beide Shards hinzufügen:

```bash
sh.addShard("shard1/localhost:27020")
sh.addShard("shard2/localhost:27021")
```

Mittels `sh.status()` kann man die Shards überprüfen.

### Sharding einführen und testen

Nun muss alles noch aktiviert werden.

`sh.enableSharding("populations")`

Wieder ok als return.

Jetzt wollen wir in der DB die Colletion cities auf die Shards verteilen und dabei ist das Attribut country der Shardkey.

`sh.shardCollection("populations.cities", { "country": "hashed" })`

### Daten hinzufügen

Daten hinzufügen:

```bash
use populations

db.cities.insertMany([
  {"name": "Seoul", "country": "South Korea", "continent": "Asia", "population": 25.674 },
  {"name": "Mumbai", "country": "India", "continent": "Asia", "population": 19.980 },
  {"name": "Lagos", "country": "Nigeria", "continent": "Africa", "population": 13.463 },
  {"name": "Beijing", "country": "China", "continent": "Asia", "population": 19.618 },
  {"name": "Shanghai", "country": "China", "continent": "Asia", "population": 25.582 },
  {"name": "Osaka", "country": "Japan", "continent": "Asia", "population": 19.281 },
  {"name": "Cairo", "country": "Egypt", "continent": "Africa", "population": 20.076 },
  {"name": "Tokyo", "country": "Japan", "continent": "Asia", "population": 37.400 },
  {"name": "Karachi", "country": "Pakistan", "continent": "Asia", "population": 15.400 },
  {"name": "Dhaka", "country": "Bangladesh", "continent": "Asia", "population": 19.578 },
  {"name": "Rio de Janeiro", "country": "Brazil", "continent": "South America", "population": 13.293 },
  {"name": "São Paulo", "country": "Brazil", "continent": "South America", "population": 21.650 },
  {"name": "Mexico City", "country": "Mexico", "continent": "North America", "population": 21.581 },
  {"name": "Delhi", "country": "India", "continent": "Asia", "population": 28.514 },
  {"name": "Buenos Aires", "country": "Argentina", "continent": "South America", "population": 14.967 },
  {"name": "Kolkata", "country": "India", "continent": "Asia", "population": 14.681 },
  {"name": "New York", "country": "United States", "continent": "North America", "population": 18.819 },
  {"name": "Manila", "country": "Philippines", "continent": "Asia", "population": 13.482 },
  {"name": "Chongqing", "country": "China", "continent": "Asia", "population": 14.838 },
  {"name": "Istanbul", "country": "Turkey", "continent": "Europe", "population": 14.751 }
])
```

### Kontrolle

Anzeigen: von Stats:

`mongos> db.cities.getShardDistribution()`

### Anwendung

Die Datenbank kann nun genutzt werden. Das Routing wird vom Cluster übernommen.

`db.cities.find().explain()`
