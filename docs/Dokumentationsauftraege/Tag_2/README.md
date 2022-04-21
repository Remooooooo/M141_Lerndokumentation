# Tag 2

## ACID

Durch ACID gewährleistet, dass auf einer Datenbank nur gütlige und Konsistente Daten vorhanden sind.
Es sind nicht alle DB mit ACID "kompatibel". Fast alle SQL DB's sind kompatibel, gewisse NoSQL jedoch nur zu einem gewissen grad.

### A - Atomaritaet

Wenn eine Transaktion erfolgt, sind das mehrere kleine Transaktionen. (Z.B. insert, update, delete) Die Anweisungen werden einzeln durchgeführt, wobei alle transaktionen durchgehen `müssen`, also wenn eine fehlschlägt, werden alle abgebrochen und der alte Zustand wiederhergestellt.

### C - Konsistenz

Die Konsistenz stellt sicher, dass die Daten, welche eingetragen werden sollen, `gültig` sind.
Falls sie nicht gültig sind, wird der alte Zustand der DB wieder erzeugt.
Die Bedingungen für die `Integrität` und die `logische Konsitenz` werden geprüft.
Die Konsistenz wird vor und nach einer Transaktion sichergestellt. Während der Transaktion kann es vorkommen, dass inkonsistente Zustände auftreten.

### I - Isolation

Wenn eine Transaktion läuft, werden die Daten zur bearbeitung `gesperrt` und werden erst nach der Transaktion wieder freigegeben. Gewisse Operationen sind jedoch noch freigeben. Jede Transaktion wird ist `individuell` und eine Transaktion kann nicht auf resultate anderer Transaktionen zugreiffen.

### D - Dauerhafitigkeit

Nach erfolgreicher Transaktion werden die Daten `permanent`gespeichert, damit sie `unverändert` und `vollständig` sind. Auch bei einem Absturz sorgt diese SQL Eigenschaft für die Sicherheit der übermittelten Daten.
Falls bei einem Absturz eine Transaktion noch nicht abgeschlossen war, wird die Transaktion abgebrochen.

## BASE

Im vergleich zu ACID, ist BASE eher auf eine hohe verfügbarkeit ausgelegt.
Da wie bereits bei ACID angesprochen vorallem NoSQL DB's nicht mit ACID kompatibel sind, verwenden jene BASE.

Die Abkürzung BASE ist eher als witz gedacht, da BASE das komplette gegenteil von ACID ist. Bei diesem Prinzip werden bewusst schwächen in kauf genommen, dafür gibt es andere stärken.

### BA - Basically Avaialbe

Anders als bei ACID stellt BASE nicht die Konsistenz als höchste Priorität, sondern die `Verfügbarkeit` der DB.

### S - Soft state

Während des Schreibvorgangs müssen die Daten `nicht konsistent` sein, weder müssen die Kopien der Daten identisch sein.

### E - Eventual consistent

Die Konsistenz der Daten wird erst zu einem `späteren Zeitpunkt` stattfunden.

## Rückschlüsse

Da beide, SQL und NoSQL, viele Vor- und Nachteile haben, werden in vielen Projekten die beiden Systeme parallel betrieben, mit dem Ziel, alle Vorteile zu nutzen und die Nachteile zu beheben.

Falls die Daten sehr wichtig sind und die Konsistenz der Daten unabdingbar ist, wie z.B. eine Nutzer Authentifizierung, würde ich zu SQL greiffen.
Falls die Daten auch ein wenig später ausgeliefert werden können, ist NoSQL eine gute Wahl.

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

Google(Und andere grosse Firmen) hat jedoch ein eigenes DBMS erstellt für GMail und ihnen ist es (mehr oder weniger) gelungen, alle 3 Werte komplett abzudecken.

### C - Consistency

Sind die Daten konsistent und gültig?

Zu jedem Zeitpunkt auf der DB.

### A - Availablility

Sind die Daten immer verfügbar?

Nicht nur wie lange läuft das System, sondern gibt es ein locking der Transaktionen.

### P - Partion tolerance

Wie gut funktionieren die Daten wenn sie verteilt gespeichert werden.

Können die Datensätze auf mehrere Systeme in mehreren RZ gleichzeitig betrieben werden.