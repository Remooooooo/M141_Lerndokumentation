# Tag 12

## Install

Das File als Zip herunterladen [Express](https://github.com/sbhaseen/express-dashboard-demo)

### Commands

Start:

`npm run dev`

Webbrowser

`http://localhost:5000`

## Inbetriebnahme

```bash
vagrant@m141vm:~$ cd Desktop/express-dashboard-demo-master/
vagrant@m141vm:~/Desktop/express-dashboard-demo-master$ npm install
vagrant@m141vm:~/Desktop/express-dashboard-demo-master$ npm run dev

> express-locallibrary-tutorial@0.0.0 dev
> nodemon server.js

[nodemon] 2.0.4
[nodemon] to restart at any time, enter `rs`
[nodemon] watching path(s): *.*
[nodemon] watching extensions: js,mjs,json
[nodemon] starting `node server.js`
Server started on port: 5000
MongoDB connected successfully.
GET / 302 9.345 ms - 64
GET /dashboard 200 126.241 ms - -

```

## Testing

### Über welchen Controller wird kommunziert?

Je nach dem was erstellt wird ist der zugehörige Controller verantwortlich. Bei einer Maschine z.B. der `machineController`.

### Über welches Model wird der Datensatz in der Datenbank gespeichert

Selbes beim Model. Es wird über `machine.js` gespeichert. Die Schnittstelle ist mit get oder post

Bei const machine wird das Objekt erstellt. In diesem Objekt gibt es die Funktion Save.

## Anwendung modifizieren

Passen Sie die Applikation und Datenbank wie folgt an:

### Erfassen Sie auf der MongoDB einen Systemadministrator

mongo --port 27017 --host 127.0.0.1

> use admin
switched to db admin

db.createUser(
{
    user: "SysAdmin",
    pwd: passwordPrompt(), //Prompt führt zu Abfrage damit kein Cleartext da steht
    roles: [
    { role: "userAdminAnyDatabase", db: "admin" },
    { role: "readWriteAnyDatabase", db: "admin" }
    ]
}
)

Ausgabe:

```bash
Enter password: 
Successfully added user: {
	"user" : "SysAdmin",
	"roles" : [
		{
			"role" : "userAdminAnyDatabase",
			"db" : "admin"
		},
		{
			"role" : "readWriteAnyDatabase",
			"db" : "admin"
		}
	]
}
> 
```

Mongo DB stoppen:

`db.adminCommand( { shutdown: 1 } )`

Start der Instanz mit Authentifizierung

```bash
vagrant@m141vm:~/Desktop/express-dashboard-demo-master$ mongo --port 27017  --authenticationDatabase "admin" -u "SysAdmin" -p
MongoDB shell version v5.0.8
Enter password: 
connecting to: mongodb://127.0.0.1:27017/?authSource=admin&compressors=disabled&gssapiServiceName=mongodb
Implicit session: session { "id" : UUID("12eb05a1-78fa-4a0c-a5ff-d36c9a323b2d") }
MongoDB server version: 5.0.8
================
Warning: the "mongo" shell has been superseded by "mongosh",
which delivers improved usability and compatibility.The "mongo" shell has been deprecated and will be removed in
an upcoming release.
For installation instructions, see
https://docs.mongodb.com/mongodb-shell/install/
================
```

### Erfassen Sie auf der MongoDB einen Apllikationsbenutzer

Für den Appli User nehmen wir eine Built in Rolle.

> use admin
switched to db admin

db.createUser(
{
    user: "ApplUser",
    pwd: passwordPrompt(), //Prompt führt zu Abfrage damit kein Cleartext da steht
    roles: [
    { role: "userAdmin", db: "mfgdashboard" },
    ]
}
)

### Passen sie die URI in der Datei server.js entsprechend an

```js
// DB Connection
// const db = process.env.MONGODB_URI || 'mongodb://localhost:27017/mfgdashboard';
const db = process.env.MONGODB_URI || 'mongodb://expressdUser:ApplUser@localhost:27017/mfgdashboard?authSource=admin';
```
