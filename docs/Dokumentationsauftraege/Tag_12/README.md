# Tag 12

## URI

Das Ziel ist es, eine Ressoruce zu Identifizieren.

Eine Ressource kann, Websiten, Datein, Webservice, Email oder Dantenbanken sein.

Das configinc.php muss z.B. ins gitignore wenn man mit Github arbeitet. (Das Passwort steht in cleartext)

### Aufbau

Schema : Wie Protokoll
Autorithy: Username Passwort TCP Port
Path: Route - Datenbank
Weitere Key Value Pairs

Teils werden keine Parameter oder Logins benötogt.

### MySQL Aufbau

Falls der Port weggelassen wird, wird der standard Port verwendet. Bei SQL 3306.

### MongoDB

## Variablen (Nicht Prüfungsrelevnat)

Die Variablen sid Global, daher sollte man JS Tricks anwenden. Jene heissen local scop und sind leere Objekte.
Der Sourcecode wird dann darin abgelegt und dann sind die Variablen Lokal.

Solang man im selben Scope ist kann man auf die Prüfung zugreiffen.

## Authetifizierung

Prüfung des Benutzers auf die Identität. (Pass, Username, ID....)

Per Default ist bei Mongo DB keine Authentifizierung eingeschaltet.
Da dies ein hohes Sicherheitsrisko ist, sollte man im Konfigfile die Security enablen.

File: /etc/mongod.conf

```bash
security:
    authorization: enabled
```

Alternativ kann beim Start --auth als Parameter mitgegeben werden.

Unter MongoDB gibt es folgende Auth. Methoden:

- Scram
- Zertifikate
- Kerberos (Wie AD nur in MongoDB)
- LDAP (Zugriffsprotokoll für AD Services)

Wir werden Scram verwenden, es ist der Default.

### Authorisierung

Welche Rechte haben die authentifizierten Users?

Dies nennt sich Role Based Acces Control. Dies bedeutet dass eine spezielle Rolle Rechte auf eine Ressource haben kann und auf diesen Ressourcen kann man ebenfalls die Actions steuern.

### Begriffe

Built-in Rollen:

Sind vordefiniert mit vordefinierten Rechten

self-defined:

Selber definiert auf welchen DB lese und schreibzugriffe.

priviliges:

Priviliges sind actions auf ressourcen. Jene definieren die Rolle. Es kann auf jede Ressource actions definiert werden.

## ADM User erstellen

In der Admindatenbank den create user Befehl ausführen. Zum Schluss wird als Array die Rollen mitgegeben.

`db.createUser`

## Rolle erfassen

In der Admindatenbank den create Role Befehl ausführen.

`db.createRole`

## Weitere Methoden

Folgende Methoden sind noch wichtig für die Rollenvergabe:

| Name                       | Description                                  |
|----------------------------|----------------------------------------------|
| db.createRole()            | Creates a role and specifies its privileges. |
| db.grantPrivilegesToRole() | Assigns privileges to a user-defined role.   |
| db.updateRole()            | Updates a user-defined role.                 |

## Security Checklist

Copy Past der wichtigsten Security advises.

- Aktivieren Authentifizierung / Authorisierung (siehe vorhergehendes Kapitel)  
- Konfigurieren RBAC  
- Definieren Sie einen System Administrator  
- Definieren Sie einen eigenen Benutzer für jede Person/Applikation die MongoDB nutzen  
- Weisen Sie nur so viele Rechte zu, wie wirklich nötig sind. Arbeiten Sie spezifischen Rollen  
- Aktivieren Sie TLS/SSL für ihr System  
- Verschlüsseln Sie die Daten und sammeln Sie die Log-Dateien von MongoDB  
- Kontrollieren/Konfigurieren Sie die Netzwerk-Schnittstellen, über welche MongoDB verfügbar ist und hier  
- Loggen/Überwachen Sie Veränderungen an der Konfiguration und den Daten der MongoDB-Instanz  
- Deaktivieren Sie unsichere Funktionen  
- Konfigurieren Sie einen speziellen MongoDB-Betriebssystem-Benutzer ("nicht als root laufen lassen")  
- Informieren Sie sich regelmässig über Patches/CVEs der Software und die EoL-Dates
