# Tag 7

## Dokumentieren Sie Ihre Arbeiten für die Installation der Etherpad Applikation

### Vorbereitung

JS und Typescript installieren:

```bash
# Infrastruktur vorbereiten
curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -

# Pakete installieren
sudo apt install -y nodejs
```

Update VM:
`sudo apt upgrade`  
`sudo apt update`  

### Installation

Git repo klonen:

```bash
git clone --branch master https://github.com/ether/etherpad-lite.git
cd etherpad-lite
```

### Datenbank vorbereiten

Auf DB mit root verbinden. Danach folgende Befehle eingeben:

```bash
create database `etherpad_lite_db`;
-- Create a new database user. Replace <etherpaduser> and <password> with your own values. 
CREATE USER 'etherpaduser1'@'localhost' identified by 'Passwort1234.';
ALTER USER 'etherpaduser1'@'localhost' IDENTIFIED WITH mysql_native_password BY 'Passwort1234.';
-- Grant permissions to that new database account. Replace <etherpaduser> with your own value from above step. 
grant CREATE,ALTER,SELECT,INSERT,UPDATE,DELETE on `etherpad_lite_db`.* to 'etherpaduser1'@'localhost';
```

### Settings anpassen

```bash
# Kopieren Sie das Config-File
cp settings.json.template settings.json

# Bearbeiten Sie die Datei settings.json
"dbType" : "mysql",
"dbSettings" : {
    "user"    : "etherpaduser1",
    "port"    : "/var/run/mysqld/mysqld.sock",
    "password": "Passwort1234.",
    "database": "etherpad_lite_db",
    "charset" : "utf8mb4"
}
```

### Plugins installieren

```bash
# Befehl in Etherpad-Folder ausführen
npm install --no-save --legacy-peer-deps ep_headings2 ep_markdown ep_comments_page ep_align ep_font_color ep_webrtc ep_embedded_hyperlinks2
```

## Testen Sie Ihre Etherpad-Applikation aus und dokumentieren Sie die neu erstellten Daten aus der Datenbank

Führen Sie nun folgenden Befehl im Etherpad-Folder aus:

```bash
src/bin/run.sh
```

Jetzt sollte die App erreichbar sein unter `http://localhost:9001`

## Diskutieren Sie die Security-Angriffsvektoren anhand der Applikation Etherpad

Beispiel : "Angriffsvektor auf die Datenbank" <- Wie müsste die Applikation für einen Produktiv-Betrieb genutzt werden

