# Tag 6

## Cold Backup

Bei einem Cold Backup stehen die Daten dem Benutzer `NICHT` zur Verfügung.

Unter Windows könnte mann den Command `Robocopy` nutzen, unter Linux `CP`.

Dies ist eine schnelle Methode und das Speichervolumen ist klein, jedoch muss der Serverdienst deaktiviert werden. Ausserdem lässt sich kein exakter Zeitpunkt wiederherstellen.

## Warm Backup

Bei einem Warm Backup stehen die Daten dem Benutzer `EINGESCHRÄNKT` zur Verfügung.

Dies könnte z.B. mit jenem SQL Dump geschehen:

`mysqldump --single-transaction -u root -p --all-databases > dump.sql`

Der Vorteil ist, es kann auf andere DBMS migriert werden, es ist lesbar und auch anpassbar.

Nachteil: Es dauert lange und hat ein hohes Volumen.

## Hot Backup

Bei einem Hot Backup stehen die Daten dem Benutzer `KOMPLETT` zur Verfügung.

## Logisches Backup

Ein logisches Backup ist für einen Menschen lesbar. Dies kann zum Beispiel ein SQL Script sein.

## Physisches Backup

Das Backup ist binär vorhanden. Das heisst es ist nicht für einen Menschen lesbar. Zum Beispiel ein Snapshot.

## Full oder Inkrementelles Backup

Dies haben wir bei Herr Foletti ausgiebig behandelt.
Full ist der Ganze Datenbestand, inkrementell werden alle geänderten Daten gespeichert.

Vorteil Full: Schnelle wiederherstellung.
Nachteil Full: Verbraucht viel Platz

Vorteil Ink.: Braucht wenig Platz
Nachteil: Mühsame wiederherstellung

## Migration

Eine Migration ist ein Backup und Restore von Daten, entweder auf dem Selben oder anderen Server.

Eine Migration kann Physisch oder logisch durchgeführt werden.
Wenn es Physisch gemacht wird, ist der Vorteil dass es platzsparend ist und es geht schnell. Das Problem ist, es ist nicht reparierbar. Falls es schief geht, kann mann nichts retten.

Bei einer logischen Migration kann die Migration verändert werden, jedoch ist sie gross und langsam.

## Percona XtraBackup

Dieses Tool bietet schnelle Backups für Datenbanken. Es kann als kostenloses AddOn für MySQL heruntergeladen werden und ist Open Source.

Der Grosse Vorteil ist, dass es Physische hot backups gibt sowie eine Point in Time Recovery. Mit hilfe von Transaction Logs kann man sehr spezifisch einen Zeitpunkt einer Datenbank wiederherstellen.

Die Backups werden ausserdem automatisch auf die funktionalität geprüft und kompressiert, was sehr platzsparend ist.
