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

Grob kann man sagen, man soll immer embedden wenn es möglich ist.  
Ausser es gibt gute Gründe dagegen. Zum Beispiel, wenn die Elemente des Arrays ins unendliche ansteigen kann. (Log Nachriten mit einem User)
Anderes Beispiel wäre wenn man explizit weiss, das man ein einzelnes Objekt anzeigen muss. Falls es nested wäre, müssten immer alle Daten geladen werden.

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
