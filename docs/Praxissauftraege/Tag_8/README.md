# Tag 8

# Tag 8

## Übungsdokumentation Sakila

### Installation

Die installation erfolgte über Moodle. Danach habe ich das File unzipt und so sah die Struktur im Ordner aus:

```bash
vagrant@m141vm:~/Desktop/sakila-db$ ls
sakila-data.sql  sakila.mwb  sakila-schema.sql
```

Die `Schema` Datei beinhaltet die Struktur und die `Data` Datei die Daten.

### Import

Wie wir gelernt haben führen wir einen Import durch:

```bash
vagrant@m141vm:~/Desktop/sakila-db$ mysql -u root -p < sakila-schema.sql
Enter password: 
vagrant@m141vm:~/Desktop/sakila-db$ mysql -u root -p < sakila-data.sql
Enter password: 
```

Ob der Import geklappt hat kann man so prüfen:

```mysql
mysql> USE sakila;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> SHOW FULL TABLES;
+----------------------------+------------+
| Tables_in_sakila           | Table_type |
+----------------------------+------------+
| actor                      | BASE TABLE |
| actor_info                 | VIEW       |
| address                    | BASE TABLE |
| category                   | BASE TABLE |
| city                       | BASE TABLE |
| country                    | BASE TABLE |
| customer                   | BASE TABLE |
| customer_list              | VIEW       |
| film                       | BASE TABLE |
| film_actor                 | BASE TABLE |
| film_category              | BASE TABLE |
| film_list                  | VIEW       |
| film_text                  | BASE TABLE |
| inventory                  | BASE TABLE |
| language                   | BASE TABLE |
| nicer_but_slower_film_list | VIEW       |
| payment                    | BASE TABLE |
| rental                     | BASE TABLE |
| sales_by_film_category     | VIEW       |
| sales_by_store             | VIEW       |
| staff                      | BASE TABLE |
| staff_list                 | VIEW       |
| store                      | BASE TABLE |
+----------------------------+------------+
23 rows in set (0.00 sec)

mysql> SELECT COUNT(*) FROM film_text;
+----------+
| COUNT(*) |
+----------+
|     1000 |
+----------+
1 row in set (0.00 sec)
```

### Stored Procedures Anzeigen

```mysql
mysql> SHOW PROCEDURE STATUS WHERE db = 'sakila';
+--------+-------------------+-----------+----------------+---------------------+---------------------+---------------+--------------------------------------------------+----------------------+----------------------+--------------------+
| Db     | Name              | Type      | Definer        | Modified            | Created             | Security_type | Comment                                          | character_set_client | collation_connection | Database Collation |
+--------+-------------------+-----------+----------------+---------------------+---------------------+---------------+--------------------------------------------------+----------------------+----------------------+--------------------+
| sakila | film_in_stock     | PROCEDURE | root@localhost | 2022-03-29 07:56:41 | 2022-03-29 07:56:41 | DEFINER       |                                                  | utf8mb4              | utf8mb4_0900_ai_ci   | utf8mb4_0900_ai_ci |
| sakila | film_not_in_stock | PROCEDURE | root@localhost | 2022-03-29 07:56:41 | 2022-03-29 07:56:41 | DEFINER       |                                                  | utf8mb4              | utf8mb4_0900_ai_ci   | utf8mb4_0900_ai_ci |
| sakila | rewards_report    | PROCEDURE | root@localhost | 2022-03-29 07:56:41 | 2022-03-29 07:56:41 | DEFINER       | Provides a customizable report on best customers | utf8mb4              | utf8mb4_0900_ai_ci   | utf8mb4_0900_ai_ci |
+--------+-------------------+-----------+----------------+---------------------+---------------------+---------------+--------------------------------------------------+----------------------+----------------------+--------------------+
3 rows in set (0.00 sec)
```

### Views Anzeigen

```mysql
mysql> show full tables where table_type = 'VIEW';
+----------------------------+------------+
| Tables_in_sakila           | Table_type |
+----------------------------+------------+
| actor_info                 | VIEW       |
| customer_list              | VIEW       |
| film_list                  | VIEW       |
| nicer_but_slower_film_list | VIEW       |
| sales_by_film_category     | VIEW       |
| sales_by_store             | VIEW       |
| staff_list                 | VIEW       |
+----------------------------+------------+
7 rows in set (0.00 sec)
```

### Triggers Anzeigen

```mysql
mysql> show triggers in sakila;
+----------------------+--------+----------
| Trigger              | Event  | Table    | Statement                                                                                                                                                                                                                                                                                                              | Timing | Created                | sql_mode                                                                                                                         | Definer        | character_set_client | collation_connection | Database Collation |

| customer_create_date | INSERT | customer | SET NEW.create_date = NOW()                                                                                                                                                                                                                                                                                            | BEFORE | 2022-03-29 07:57:19.83 | STRICT_TRANS_TABLES,STRICT_ALL_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,TRADITIONAL,NO_ENGINE_SUBSTITUTION | root@localhost | utf8mb4              | utf8mb4_0900_ai_ci   | utf8mb4_0900_ai_ci |
| ins_film             | INSERT | film     | BEGIN
    INSERT INTO film_text (film_id, title, description)
        VALUES (new.film_id, new.title, new.description);
  END                                                                                                                                                                                          | AFTER  | 2022-03-29 07:56:41.30 | STRICT_TRANS_TABLES,STRICT_ALL_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,TRADITIONAL,NO_ENGINE_SUBSTITUTION | root@localhost | utf8mb4              | utf8mb4_0900_ai_ci   | utf8mb4_0900_ai_ci |
| upd_film             | UPDATE | film     | BEGIN
    IF (old.title != new.title) OR (old.description != new.description) OR (old.film_id != new.film_id)
    THEN
        UPDATE film_text
            SET title=new.title,
                description=new.description,
                film_id=new.film_id
        WHERE film_id=old.film_id;
    END IF;
  END | AFTER  | 2022-03-29 07:56:41.30 | STRICT_TRANS_TABLES,STRICT_ALL_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,TRADITIONAL,NO_ENGINE_SUBSTITUTION | root@localhost | utf8mb4              | utf8mb4_0900_ai_ci   | utf8mb4_0900_ai_ci |
| del_film             | DELETE | film     | BEGIN
    DELETE FROM film_text WHERE film_id = old.film_id;
  END                                                                                                                                                                                                                                                     | AFTER  | 2022-03-29 07:56:41.31 | STRICT_TRANS_TABLES,STRICT_ALL_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,TRADITIONAL,NO_ENGINE_SUBSTITUTION | root@localhost | utf8mb4              | utf8mb4_0900_ai_ci   | utf8mb4_0900_ai_ci |
| payment_date         | INSERT | payment  | SET NEW.payment_date = NOW()                                                                                                                                                                                                                                                                                           | BEFORE | 2022-03-29 07:57:20.41 | STRICT_TRANS_TABLES,STRICT_ALL_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,TRADITIONAL,NO_ENGINE_SUBSTITUTION | root@localhost | utf8mb4              | utf8mb4_0900_ai_ci   | utf8mb4_0900_ai_ci |
| rental_date          | INSERT | rental   | SET NEW.rental_date = NOW()                                                                                                                                                                                                                                                                                            | BEFORE | 2022-03-29 07:57:20.76 | STRICT_TRANS_TABLES,STRICT_ALL_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,TRADITIONAL,NO_ENGINE_SUBSTITUTION | root@localhost | utf8mb4              | utf8mb4_0900_ai_ci   | utf8mb4_0900_ai_ci |

6 rows in set (0.00 sec)
```

### SP Anwenden

Die SP welche wir anwenden sucht nach Kopien eines Filmes, welche sich im Lager befinden. Folgende Parameter werden dem SP mitgegeben:

- p_film_id : Die ID des Films
- p_store_id : Die ID des Lagers
- p_film_count : Die Anzahl der Filme

Der Aufruf erfolgt so:

```mysql
mysql> CALL film_in_stock(1,1,@count);
+--------------+
| inventory_id |
+--------------+
|            1 |
|            2 |
|            3 |
|            4 |
+--------------+
4 rows in set (0.00 sec)
```

Bei diesem Datensatz sieht man schnell das es sich nun um 4 Exemplare handelt. Falls es jetzt tausende wäre könnte man den Parameter count auslesen.

```mysql
mysql> SELECT @count;
+--------+
| @count |
+--------+
|      4 |
+--------+
1 row in set (0.00 sec)
```

### View Anwenden

Den View den wir benutzen dient dazu, die Tabelle der Filme und Schauspieler miteinander zu verbinden.
Bei dieser View sieht man den Namen des Schauspielers sowie die Filme bei denen der Schauspieler mitgewirkt hat. (Inklusive Infos über den Film)

mysql> SELECT * from actor_info;

| actor_id | first_name  | last_name    | film_info

|        1 | PENELOPE    | GUINESS      | Animation: ANACONDA CONFESSIONS; Children: LANGUAGE COWBOY; Classics: COLOR PHILADELPHIA, WESTWARD SEABISCUIT; Comedy: VERTIGO NORTHWEST; Documentary: ACADEMY DINOSAUR; Family: KING EVOLUTION, SPLASH GUMP; Foreign: MULHOLLAND BEAST; Games: BULWORTH COMMANDMENTS, HUMAN GRAFFITI; Horror: ELEPHANT TROJAN, LADY STAGE, RULES HUMAN; Music: WIZARD COLDBLOODED; New: ANGELS LIFE, OKLAHOMA JUMANJI; Sci-Fi: CHEAPER CLYDE; Sports: GLEAMING JAWBREAKER

|        2 | NICK        | WAHLBERG     | Action: BULL SHAWSHANK; Animation: FIGHT JAWBREAKER; Children: JERSEY SASSY; Classics: DRACULA CRYSTAL, GILBERT PELICAN; Comedy: MALLRATS UNITED, RUSHMORE MERMAID; Documentary: ADAPTATION HOLES; Drama: WARDROBE PHANTOM; Family: APACHE DIVINE, CHISUM BEHAVIOR, INDIAN LOVE, MAGUIRE APACHE; Foreign: BABY HALL, HAPPINESS UNITED; Games: ROOF CHAMPION; Music: LUCKY FLYING; New: DESTINY SATURDAY, FLASH WARS, JEKYLL FROGMEN, MASK PEACH; Sci-Fi: CHAINSAW UPTOWN, GOODFELLAS SALUTE; Travel: LIAISONS SWEET, SMILE EARRING

## Übungsdokumentation Chat-Applikation

### Installation Chat

1. Git Repo klonen:

```bash
vagrant@m141vm:~/Desktop$ git clone https://github.com/psachan190/realtime-chat-application-using-nodejs-expressjs-and-web-socket.git
Cloning into 'realtime-chat-application-using-nodejs-expressjs-and-web-socket'...
remote: Enumerating objects: 5367, done.
remote: Counting objects: 100% (3/3), done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 5367 (delta 0), reused 0 (delta 0), pack-reused 5364
Receiving objects: 100% (5367/5367), 8.80 MiB | 14.46 MiB/s, done.
Resolving deltas: 100% (1373/1373), done.
vagrant@m141vm:~/Desktop$ 
```

2. Im neuen Verzeichnis dies ausführen:

   `npm install`

   (Ich musste noch meinen npm install updaten `sudo npm install -g npm`, danach ging es)

3. DB erstellen:

    `CREATE DATABASE chat;`

4. File bearbeiten:

   ```bash
    vagrant@m141vm:~/Desktop/realtime-chat-application-using-nodejs-expressjs-and-web-socket/database$ sudo nano db.js 
    var mysql = require('mysql');
    var con = mysql.createConnection({
        host: "localhost",
        user: "root",
        password: "Passwort",
        database: "chat"
    ```

### Starten

`node server.js`

### User erstellen

```mysql
INSERT INTO login VALUES ('1','user1','user1');
INSERT INTO login VALUES ('2','user2','user2');
```

### Testing

Nun sollte die URL ()[http://localhost:3000] aufrufbar sein und man kann sich mit den erstellten Logins anmelden.
