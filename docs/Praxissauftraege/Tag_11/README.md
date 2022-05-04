# Tag 11

## Übung Cities

DB Erstellen:

`use cities;`

## Import

`mongoimport  --db=cities --collection=towns --legacy cities.json`

Es muss legacy verwendet werden.

## Query

Es müssen Alle Städte mit der Timezone Europa aufgelistet werden.

## Aggregate Beispiel

Folgender Query muss ausgeführt werden:

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

### Ergebnis

## Sort Beispiel

Folgender Query muss ausgeführt werden:

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

### Ergebnis

## Backup und Restore

Es muss ein Backup und Restore auf einer TestDB durchgeführt werden.