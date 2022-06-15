# Tag 8

Heute gab es nichts zu Dokumentieren.

## View Beschreibung

Views werden eingesetzt um spezifische Tabellen anzuzeigen. So ist es zügig möglich, mehrere Tabellen auf einmal und miteinander anzuzeigen.

Views sind wie der Name schliessen lässt nicht nur zum lesen, Views können unter gewissen bedingungen auch zum Schreiben verwendet werden

Ausserdem können Views gecached werden, dies steigert ausserdem die Performance. Zur Security kann es auch beitragen, da so nur bestimmte Daten angezeigt werden, da über Benutzerrechte z.B. keine Filme ab 18 ausgeblendet werden können, jedoch ist dies mit Views möglich.

Meist wird für jede Applikation eine eigene View erstellt. So bleiben beim bearbeiten alle anderen Applikationen unberührt.

## SP Beschreibung

SP sind Anweisungen, die auf der DB gespeichert sind. Der Vorteil von SP's ist, dass sie schneller sind, da sie nur einmal eingelesen werden müssen und man sie immer wieder verwenden kann. Meist werden auch komplizierte Logiken gespeichert, da jene nur 1x eingetippt werden müssen.

Ausserdem bieten Sie den Vorteil der Sicherheit. Mann kann Applikationen z.B. nur auf SPs berechtigen, anstelle auf die ganze DB. So können sie nur den SP ausführen und nicht alle Tabellen bearbeiten.

Ein Hauptvorteil ist die Abstraktion, dass der Softwareentwickler und der Datenbank Entwickler getrennt arbeiten können. Ausserdem müssen nicht alle funktionale Aunforderungen usw. durchgetestet werden.

## Triggers Beschreibung

Ein Trigger ist ein Auslöser, der bei einem bestimmten Ereignis Code ausführt. Im MySQL Kontext ist so ein Ereignis zum Beispiel das Löschen eines Datensatzes in eine Tabelle. Jedes Mal, wenn also per DELETE Daten gelöscht werden, wird ein bestimmter Code ausgeführt.

In der Praxis werden Trigger oft genutzt, um die Konsistenz der Datenbank sicher zu stellen. Wird beispielsweise ein User gelöscht, kann man im Trigger automatisch auch alle Datensätze in anderen Tabellen löschen, die zum User gehören und damit ungültig werden.

Die Syntax für die Erstellung eines Trigger lautet wie folgt:

`CREATE TRIGGER Triggername Triggerzeitpunkt Triggerereignis ON Tabellenname FOR EACH ROW Triggeranweisungen`

Der Triggerzeitpunkt kann BEFORE oder AFTER sein und entscheidet darüber, ob erst die eigentliche Aktion ausgeführt wird (z.B. das DELETE) oder erst die Anweisungen des Triggers. Der Zeitpunkt hat auch Auswirkungen darauf, was im Trigger möglich ist und was nicht, mehr dazu später.

Das Triggerereignis kann DELETE, UPDATE oder INSERT sein, je nachdem bei welchem Ereignis der Trigger ausgelöst werden soll.
