# Tag 11

## Übung Cities

DB Erstellen:

`use cities;`

## Import

`mongoimport  --db=cities --collection=towns --legacy cities.json`

Es muss legacy verwendet werden.

## Query

Es müssen Alle Städte mit der Timezone Europa aufgelistet werden.

`db.towns.find({timezone : /Europe/})`

## Aggregate Beispiel

Folgender Query muss ausgeführt werden:
Mit `Match` werden die Bedingungen definiert und mit `Group` die Ausgabe der Werte.

```bash
> db.cities.aggregate([
    {
    $match: {
        'timezone': {
        $eq: 'Europe/London'
        }
    }
    },
    {
    $group: {
        _id: 'averagePopulation',
        avgPop: {
        $avg: '$population'
        }
    }
    }
])
```

## Sort Beispiel

Folgender Query muss ausgeführt werden:
Mit -1 werden die Einträge absteigend sortiert.

```bash

> db.cities.aggregate([
    {
    $match: {
        'timezone': {
        $eq: 'Europe/London'
        }
    }
    },
    {
    $sort: {
        population: -1
    }
    },
    {
    $project: {
        _id: 0,
        name: 1,
        population: 1
    }
    }
])
```

## Backup und Restore

Es muss ein Backup und Restore auf einer TestDB durchgeführt werden. Dafür gibt es den `mongodump` Befehl.

```mongodump --host=localhost --port=27017 --db=meinedatenbank --archive=meinedatenbank-02-05-2022.Archive```

So sieht die Ausgabe aus falls es funktioniert hat:

```bash
vagrant@m141vm:~/Desktop/M141_Lerndokumentation$ mongodump --host=localhost --port=27017 --db=meinedatenbank --archive=meinedatenbank-02-05-2022.Archive
2022-05-18T07:20:32.790+0200	writing meinedatenbank.meinecollection to archive 'meinedatenbank-02-05-2022.Archive'
2022-05-18T07:20:32.792+0200	done dumping meinedatenbank.meinecollection (1 document)
```

Für den Restore gibt es das Gegenstück, den `mongorestore`.

```mongorestore --host=localhost --port=27017 --archive=meinedatenbank-02-05-2022.Archive```

Da die DB leer ist gibt es schlussendlich einen Error, so sollte aber die Rückmeldung aussehen:

```bash
vagrant@m141vm:~/Desktop/M141_Lerndokumentation$ mongorestore --host=localhost --port=27017 --archive=meinedatenbank-02-05-2022.Archive
2022-05-18T07:20:57.911+0200	preparing collections to restore from
2022-05-18T07:20:57.927+0200	reading metadata for meinedatenbank.meinecollection from archive 'meinedatenbank-02-05-2022.Archive'
2022-05-18T07:20:57.937+0200	restoring to existing collection meinedatenbank.meinecollection without dropping
2022-05-18T07:20:57.938+0200	restoring meinedatenbank.meinecollection from archive 'meinedatenbank-02-05-2022.Archive'
2022-05-18T07:20:57.948+0200	continuing through error: E11000 duplicate key error collection: meinedatenbank.meinecollection index: _id_ dup key: { _id: ObjectId('626118f4ad1fe209c2f21442') }
2022-05-18T07:20:57.952+0200	finished restoring meinedatenbank.meinecollection (0 documents, 1 failure)
2022-05-18T07:20:57.952+0200	no indexes to restore for collection meinedatenbank.meinecollection
2022-05-18T07:20:57.953+0200	0 document(s) restored successfully. 1 document(s) failed to restore.
```
