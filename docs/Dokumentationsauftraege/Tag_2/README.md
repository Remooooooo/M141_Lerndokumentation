# Tag 2

## Transaktion

Eine Transaktion besteht aus mehreren Teilschritten einer Übermittlung.
Transaction control language definiert den ganzen Ablauf, falls ein Fehlschag geschieht oder die Schritte erfolgreich Übermittelt wurden.

Eine einzelne Transaktion, z.B. das Überweisen von Geld auf ein Konto sieht wie folgt aus:

```
Lesen Saldo Konto A
Konto A -= 500 
Schreiben Saldo Konto A

Lesen Saldo Konto B
Konto B += 500
Schreiben Saldo Konto B
```

### Problematik

Das Problem mit Transaktionen ist, wenn zwei Aktionen parallel laufen und auf die selben Daten einer Datenbank zugreiffen wollen. Folgende Problematiken, auch Anomalien sind bekannt:

- Dirty Read
- Lost Updates
- Non-Repeatable Read
- Phantom Read

### Lösung

Die einfachste Lösung wäre es, jede Transaktion seriell laufen zu lassen. Dies würde jedoch zu grossen Performance einbussen führen. Es gibt auch die möglichkeit, RDBMS Systeme speziell zu konfigurieren . Je nach Einstellungsart werden gewisse Probleme ausgeschlossen, andere jedoch nicht.
Falls das System kritisch ist, z.B. Banken, darf es nicht vorkommen, dass irgendwelche fehler vorkommen.
Ist es jetzt aber ein Onlineshop, spielt es keine Rolle, ob bei den Artikel "Phantom Read" auftritt.

Folgende Einstellungsmöglichkeiten gibt es:

#### Read Uncomitted

Diese Ebene ist es den Leseoperationen möglich, troz sperren die Daten zu lesen. Dies kann zu allen bekannten Problemen führen. Die meisten DBMS nutzen desshalb eine Ebene höher.

#### Read Comitted

Diese Einstellung führt dazu, dass für die gesamte Transatktion alle Objekte welche verändert werden, schreibgesperrt werden.
Da die Lesesperre aber nur kurzzeitig eingesetzt wird, kann `Non-Repeatable Read` und `Phantom Read` auftreten.

#### Repeatable Read

Durch diese Isolationsebene wird garantiert, dass die repetition der Operationen mit den gleichen Parametern auch die exakt gleichen Ergebnisse haben.
Dies wird durch eine Lese und Schreibsperre der ganzen Dauer der Transaktion sichergestellt.
Einzige Anomalie welche auftreten kann ist `Phantom Read.`

#### Serializable

Dies ist die höchste Isolationsebene. Auf diese Weise kann garantiert werden, dass jede Transaktion insich geschlossen ist und keine andere beinflusst werden kann.

## CAP

Wie bei einem Projekt das Magische Dreieck exisitiert, gibt es bei Datenbanken das CAP.
Laut der CAP Theroie ist es nicht möglich, bei allen drei Faktoren 100% zu erreichen.

Google hat jedoch ein eigenes DBMS erstellt für GMail und ihnen ist es (mehr oder weniger) gelungen, alle 3 Werte komplett abzudecken.

### C - Consistency

Sind die Daten konsistent und gültig?

### A - Availablility

Sind die Daten immer verfügbar?

### P - Partion tolerance

Wie gut funktionieren die Daten wenn sie verteilt gespeichert werden.
