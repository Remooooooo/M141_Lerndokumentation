# Tag 11

## Grundsätzlich

In MongoDB wird im Hintergrund mit Java Script gearbeitet. Dies bedeutet man kann mit Autovervollständigung arbeiten um zu sehen welche Funktionen möglich sind.

Da es sich um JS handelt, kann man mit typeof überprüfen, welchen Typ ein Objekt hat, z.B. ob es eine Funktion oder ein Objekt ist.

```js
> typeof db.meinecollection
object
> typeof db.meinecollection.insert
function
> 
```

Nützlich wären natürlich for loops oder auch Random generators welche mit Java erstellt werden für Beispieldaten. Dies war in SQL nicht möglich.

## Import  einfach

So wie wir bereits kennen können wir eifach mit der JS Funktion insert Daten einfügen.

```json
> use cities;
> db.towns.insert({
name: "Frauenfeld",
population: 25974,
lastCensus: ISODate("2022-02-22"),
famousFor: ["Open Air","Schloss","Kaserne"],
mayor : {name: "Anders Stokholm",party: "FDP" }
})
```

## Mongoimport

Es gibt die Funktion Mongoimport, mit welcher man Ganze JSON Files importieren kann. (Der untere Befehl funktioniert nur wenn das File am ausführort liegt, sonst Pfad anpassen)
Falls es sich um einen JSON Array handelt, den Parameter --jsonArray beifügen.
Falls die ID bereits vorhanden ist, mit --mode=upsert oder --mode=delete das file überschreiben oder löschen

mongoimport --uri="mongodb://mongodb0.example.com:27017/reporting"  --db=cities --collection=towns --file=contacts.json

`mongoimport --db=cities --collection=towns --file=cities.json`

Falls keine url angegeben wird, verbindet die DB direkt mit localhost.

### Legacy

Beim Legacy Import wird das --file= durch --legacy ersetzt.

`mongoimport  --db=cities --collection=towns --legacy cities.json`

Ohne angabe wird Extendet verwendet, es gibt auch noch --jsonFormat=cannical.

## Export

Basis Syntax:

`mongoexport --collection=<coll> <options> <connection-string>`

### Exoprt JSON

`mongoexport --uri="mongodb://mongodb0.example.com:27017/reporting"  --collection=events  --out=events.json [additional options]`

### Export CSV

`mongoexport --db=users --collection=contacts --type=csv --fields=name,address --out=contacts.csv`

## Backup Restore

Einschränkungen sind möglich über --db und --collection.

```bash
mongodump --host=mongodb1.example.net --port=27017 --username=user --authenticationDatabase=admin --out=/opt/backup/mongodump-2011-10-24

mongorestore --uri="mongodb://user@mongodb1.example.net:27017/?authSource=admin" /opt/backup/mongodump-2011-10-24
```

Die Backups lassen sich auch mit folgendem Befhel direkt archivieren:

`mongodump --archive=test.20150715.archive --db=test`

### Simultan

Durch Pipes kann man das Ergebnis des Exports direkt an den Import übergeben werden und somit simultan abgearbeitet werden.

`mongodump --archive --db=test | mongorestore --archive --nsFrom='test.*' --nsTo='examples.*'`

## Funktionen(Stored Procedures)

```json
function insertCity(
          name, population, lastCensus,
          famousFor, mayorInfo
        ) {
          db.towns.insert({
            name: name,
            population: population,
            lastCensus: ISODate(lastCensus),
            famousFor: famousFor,
            mayor : mayorInfo
          });
        }
```

Danach kann die Funktion genutzt werden:

```json
> insertCity("Weinfelden", 11588, '2022-02-22',
    ["Jacks", "BBZ", "Model"], { name : "Max Vögeli", party: "FDP"}
)
> insertCity("Kreuzlingen", 22390, '2022-02-22',
    ["Konstanz", "Grenze", "Karussell"], { name : "Thomas Niederberger", party: "FDP"}
)
```

## Querys

Es gibt für Mongo DB Operatoren wie lt, in, eq, gt, gte usw. um Querys zu erstellen.

Ausserdem kann REGEX verwendet werden, um die Suchergebnisse einzugrenzen.

```json
db.towns.find( { 
    name: /^F/, 
    population : { $lt : 30000 } 
    }, 
    { _id: 0, 
    name : 1, 
    population : 1 }
    )
```

Hier noch eine Tabelle der Schlüsselwörter:

| $regex     | Match by any PCRE-compliant regular expression string (or just use the // delimiters as shown earlier) |
|------------|--------------------------------------------------------------------------------------------------------|
| $ne        | Not equal to                                                                                           |
| $lt        | Less than                                                                                              |
| $lte       | Less than or equal to                                                                                  |
| $gt        | Greater than                                                                                           |
| $gte       | Greater than or equal to                                                                               |
| $exists    | Check for the existence of a field                                                                     |
| $all       | Match all elements in an array                                                                         |
| $in        | Match any elements in an array                                                                         |
| $nin       | Does not match any elements in an array                                                                |
| $elemMatch | Match all fields in an array of nested documents                                                       |
| $or        | or                                                                                                     |
| $nor       | Not or                                                                                                 |
| $size      | Match array of given size                                                                              |
| $mod       | Modulus                                                                                                |
| $type      | Match if field is a given datatype                                                                     |
| $not       | Negate the given operator check                                                                        |

## Nested Subdocuments

Da wir in unserem Beispiel Dokumente im Dokument haben, wird es besonders nützlich, die JS Funktionen anzuwenden.

```json
db.towns.find( {'mayor.party' : 'FDP'})
{ "_id" : ObjectId("6268063f788177dc9d73453e"), "name" : "Frauenfeld", "population" : 25974, "lastCensus" : ISODate("2022-02-22T00:00:00Z"), "famousFor" : [ "Open Air", "Militär", "Nebel" ], "mayor" : { "name" : "Anders Stokholm", "party" : "FDP" } }
{ "_id" : ObjectId("626807e6788177dc9d73453f"), "name" : "Weinfelden", "population" : "11588", "lastCensus" : ISODate("2022-02-22T00:00:00Z"), "famousFor" : [ "Jacks", "BBZ", "Model" ], "mayor" : { "name" : "Max Vögeli", "party" : "FDP" } }
{ "_id" : ObjectId("62680843788177dc9d734540"), "name" : "Kreuzlingen", "population" : "22390", "lastCensus" : ISODate("2022-02-22T00:00:00Z"), "famousFor" : [ "Konstanz", "Grenze", "Karussell" ], "mayor" : { "name" : "Thomas Niederberger", "party" : "FDP" } }
```
