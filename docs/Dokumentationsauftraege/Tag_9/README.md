# Tag 9

## Attribute indexieren

Wenn man einen Select durchführt ohne Indexierung wird ein "Full Table Scan" der Tabelle durchgeführt. Dies führt dazu, dass jeder einzelne Wert überprüft wird. Sobald beim Explain Befehl und der Spalte `Possible_keys` NULL steht, handelt es sich um einen Full Table Scan. Ausserdem sieht man, dass 500 Rows überprüft werden.

```mysql
mysql> explain select customer_id, customer_name from customers where customer_id='140385';
+----+-------------+-----------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table     | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+-----------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | customers | NULL       | ALL  | NULL          | NULL | NULL    | NULL |  500 |    10.00 | Using where |
+----+-------------+-----------+------------+------+---------------+------+---------+------+------+----------+-------------+
```

### Index erstellen

Die Lösung für das obige Problem ist indexieren. Dies erfolgt so:  
`mysql> Create index customer_id ON customers (customer_Id);`

Und so sieht der Explain danach aus:

```mysql
mysql> Explain select customer_id, customer_name from customers where customer_id='140385';
+----+-------------+-----------+------------+------+---------------+-------------+---------+-------+------+----------+-------+
| id | select_type | table     | partitions | type | possible_keys | key         | key_len | ref   | rows | filtered | Extra |
+----+-------------+-----------+------------+------+---------------+-------------+---------+-------+------+----------+-------+
|  1 | SIMPLE      | customers | NULL       | ref  | customer_id   | customer_id | 13      | const |    1 |   100.00 | NULL  |
+----+-------------+-----------+------------+------+---------------+-------------+---------+-------+------+----------+-------+
```

## Weitere Tipps

### Unions

Werden in einem SQL Query zu häufig ORS oder gar SELECT * durchgeführt, führt dies zu einem Full Table Scan. Daher sollte man mit Unions arbeiten.

`mysql> select  from students where first_name like  'Ade%'  union all select  from students where last_name like  'Ade%' ;`

### Wildcards

Führende Wildcards sollten vermieden werden.

### Full Text Searches

Anstelle von WIldcards kann eine Full Text Suche durchgeführt werden.

```mysql
mysql>Alter table students ADD FULLTEXT (first_name, last_name);
mysql>Select * from students where match(first_name, last_name) AGAINST ('Ade');

-- EXPLAIN

mysql> explain Select * from students where match(first_name, last_name) AGAINST ('Ade');
+----+-------------+----------+------------+----------+---------------+------------+---------+-------+------+----------+-------------------------------+
| id | select_type | table    | partitions | type     | possible_keys | key        | key_len | ref   | rows | filtered | Extra                         |
+----+-------------+----------+------------+----------+---------------+------------+---------+-------+------+----------+-------------------------------+
|  1 | SIMPLE      | students | NULL       | fulltext | first_name    | first_name | 0       | const |    1 |   100.00 | Using where; Ft_hints: sorted |
+----+-------------+----------+------------+----------+---------------+------------+---------+-------+------+----------+-------------------------------+
```

### Datenbank Design

Folgende Dinge sollten beim erstellen des Design beachtet werden:

- Normalisierte Daten
- Keine redundanten Daten (Grundsätzlich)
- NULL Werte vermeiden
- Nur so viele Attribute wie nötig

### Caching

In MySQL gibt es Caching. Jenes kann im Konfig File angepasst werden bzw. aktiviert werden. (Im etc/mysql/my.cnf)

query_cache_type=1          # 1 ist ein / 0 ist aus
query_cache_size = 10M      # default 1MB / erhöhen auf 10MB
query_cache_limit=1M

Überprüfen des Cachings:  
`mysql> show variables like 'query_cache_%' ;`