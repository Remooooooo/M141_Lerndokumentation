# Tag 14

## Replication MySQL

Motivationen für Cluster unter MySQL:

- Hohe Read-Workloads (Select-Queries) = Replika
- Hohe Write-Workloads (Insert/Update/Delete) = Sharding
- Separate Instanz für Backup- und Restore-Procedere
- Separate Instanz für Reporting/Datawarehousing
- Separate Instanz für Umsysteme mit hohen Workloads

Unter MySQL gilt ACID, daher muss sich auch bei Replika Clustern daran gehalten werden.
Die Replika wird mit den Änderungsinformationen, dem Bin Log, gefüttert. (Im Binary log werden alle Transactions gespeichert)

Grundsätzlich gibt es 3 Arten von Binary Logs:

Statement-based:  
Hier werden alle Befehle 1 zu 1 kopiert und wiederholt. Dies ist sehr einfach, kann jedoch zu Problemen führen. `nicht-deterministische Queries`
Diese werden nicht auf allen Systemen gleich geordnet, daher können unterschiedliche Datensätze gelöscht werden.

Row-based:  
Hier wird definiert `was` geändert wurde. Das Problem,das Binary Log kann sehr gross werden.

mixed:  
Hier wird versucht die Vorteile aus beiden Welten zu vereinen. Es wird `statement-based`
gearbeitet und bei nicht-deterministischen Aufgaben wird `row-based` gearbeitet.

### Active/Passiv

Ein Active Passiv Cluster kommt zum Beispiel für Backup zum Einsatz. Bei dieser Art von Replika werden die Änderungen einfach kopiert.

### Active/Read Pool

Beim Active Read Pool werden alle Read Anfragen von den Replika Servern bearbeitet und alle Write Anfragen von der Haupt Instanz.
Damit dies Funktioniert, wird ein Proxy benötigt.

### Proxy

Der Proxy übernimmt das Routing der Anfragen. Der Proxy bei diesen Clustern ist ähnlich wie Reverse Proxys.

Features:

- Caching
- Blocking
- Monitoring
- Rate Limiting

Anforderungen:

- Hochverfügbar
- Skalierbar
- Leichgewichtig
- Automatisch konfigurierbar (REST API...)

### Sharding

Dient zum steigern der Write Performance unter MySQL Clustern. Dies ist jedoch sehr komplex. Es gibt für Sharding bereits vorgefertigte Tools.
