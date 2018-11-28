# SEW VUE CLIENT

In diesem README wird die SEW VUE-Client Aufgabe Schritt für Schritt erklärt.

## TODO-Liste
* [x] TODO.md erstellen  
* [x] Git forken & upstream pushen  
* [x] Vue.js initialisieren -> ./src/main/vue/client  
* [x] Client mit Vue.js auf REST-Server verbinden (Auslesen der User)  
* [x] Cypress.io Test für das Listening der User schreiben  
* [ ] Travis.yml erstellen und das neue origin/master (Fork) mit TravisCI einbinden und server/client Tests durchführen  
* [ ] README erweitern und auf upstream/master pushen  
* [ ] weitere GUI-Elemente implementieren/testen/dokumentieren

## Requirements
* Ein Editor (vorzugsweise PyCharm)
* Ein Plugin zum bearbeiten von .vue Files
* Eine Aktuelle Version von NodeJS ([https://nodejs.org/en/](https://nodejs.org/en/))
* Git-Bash ([https://git-scm.com/downloads](https://git-scm.com/downloads)) oder ähnliches

## Anleitung

### 1. TODO.md erstellen
Im obersten Verzeichniss des Projektes ein Textfile namens `TODO.md` erstellen und den folgenden Inhalt einfügen:

```markdown
* [ ] TODO.md erstellen  
* [ ] Git forken & upstream pushen  
* [ ] Vue.js initialisieren -> ./src/main/vue/client  
* [ ] Client mit Vue.js auf REST-Server verbinden (Auslesen der User)  
* [ ] Cypress.io Test für das Listening der User schreiben  
* [ ] Travis.yml erstellen und das neue origin/master (Fork) mit TravisCI einbinden und server/client Tests durchführen  
* [ ] README erweitern und auf upstream/master pushen  
* [ ] weitere GUI-Elemente implementieren/testen/dokumentieren
```

### 2. Git forken & upstream pushen

* Das Repository forken:
Auf der Github-Seite seines Projekten auf den Fork-Knopf drücken
(Der Link zum Repository folgt dem Schema: `https://github.com/TGM-HIT/sew5-simple-user-database-<username>-tgm`)
* Den SSH/HTTPS Link für das geforkte Repository kann man hier bekommen:
![https://imgur.com/a/jNvhyrv]('https://imgur.com/a/jNvhyrv')
* Im Rootverzeichniss des Projektes muss ein Git-Bash (oder Linux, kein Standard Windows) Kommandofenster geöffnet werden
* Um seinen Code auf dieses Repository hochladen zu können muss dieser Befehl ausgeführt werden:
`git remote add forked <SSH/HTTPS Link>`
* Um Code auf das geforkte Repository zu pushen muss dieser Befehl ausgeführt werden:
`git push forked`
* Um auf beide Repositories gleichzeitig zu pushen kann dieser Befehl ausgeführt werden: (Braucht man nicht unbedingt)
`git remote | xargs -L1 -I R git push R master`

### 3. VueJS initialisieren

* Wenn bis jetzt NodeJS noch nicht installiert ist muss das jetzt gemacht werden
* Im Ordner `/src/main/vue/client` ein Git-Bash (oder Linux) Kommandofenster öffnen
* Das VueJS-CLI Packet installieren:
`npm install -g @vue/cli`
* VueJS initialisieren:
`vue init webpack`
`? Generate project in current directory? Yes`
`? Project name <name>`
`? Project description <description>`
`? Author <email>`
`? Vue build standalone`
`? Install vue-router? Yes`
`? Use ESLint to lint your code? No`
`? Set up unit tests No`
`? Setup e2e tests with Nightwatch? No`
`? Should we run 'npm install' for you after the project has been created? npm`
* Axios zum verwenden der REST-Schnittstelle installieren
`npm install --save axios`

### 4. Client mit Vue.js auf REST-Server verbinden (Auslesen der User)
Hier werden wir den generierten HelloWorldComponent verändern anstatt einen neuen zu generieren.
Im File `src/components/HelloWorld.vue` alles löschen und durch das ersetzen:
```html
<template>
	<div class="hello">
		<div v-for="student in students">
			{{ student.email }}
		</div>
	</div>
</template>  
  
<script>  
import axios from 'axios'
export default {
	name: 'HelloWorld',
	data () {
		return {
			students: null,
		}
	},
	methods:{
		getStudents() {
			const path = 'http://localhost:5000/students';
			axios.get(path).then((res) => {
				this.students = res.data;
			}).catch((error) => {
				console.error(error);
			});
		}
	},
	created(){
		this.getStudents()
	}
}
</script>
```
#### Code erklärung:
```html
<template>
	<div class="hello">
		<div v-for="student in students">
			{{ student.email }}
		</div>
	</div>
</template> 
```
Hier wird in den von VueJS verlangten `template` und `div class="hello"` durch jedes Element der Vue-Variable `students` iteriert und die Email Addresse jedes Schülers angezeigt.

```js
data () {
	return {
		students: null,
	}
}
```
Hier wird die Vue-Variable `students` deklariert.
```js
methods:{
	getStudents() {
		const path = 'http://localhost:5000/students';
		axios.get(path).then((res) => {
			this.students = res.data;
		}).catch((error) => {
			console.error(error);
		});
	},
	created(){
		this.getStudents()
	}
}
```
Hier werden 2 Methoden definiert:
**getStudents():**
Es wird mit dem axios Modul ein GET-Request auf die URL `http://localhost:5000/students` geschickt. Das Ergebnis wird in die Vue-Variable `students` gespeichert. Das Ergebnis ist ein JavaScript-Objekt, das heißt.
**created()**
Diese Methode wird aufgerufen wenn die Seite geladen wird. Sie ruft hier nur die `getStudents()` Methode auf.

Um den Client zu testen muss der Python Server gestartet werden und der folgende Befehl ausgefühlt werden:
`npm run dev`
Dann kann man im Browser unter `localhost:8080` überprüfen ob alles funktioniert
Wenn keine Daten aufscheinen kann es sein, dass in der Datenbank keine Daten sind. Hier sind ein paar Beispieleinträge: (funktionieren vielleicht nicht bei jedem)
```json
[
	{
		"username":"xxDarkL0rdxx",
		"email":"e@mail.com",
		"picture":"google.com"
	},
	{
		"username":"abc2",
		"email":"e2@mail.com",
		"picture":"google.com"
	},
	{
		"username":"asdasdIUHasd2",
		"email":"e3@mail.com",
		"picture":"google.com"
	}
]
```
### 5. Cypress.io Test für das Listening der User schreiben
* Um cypress zu installieren muss dieser Befehl ausgeführt werden:
`npm install --save cypress`
* Im `package.json` File müssen diese 2 Einträge unter `scripts` hinzugefügt werden:
```json
"scripts":{
	"cy:open": "cypress open",
	"cy:run": "cypress run"
}
```
* Um cypress zu initialisieren muss dieser Befehl ausgeführt werden:
`npm run cy:open`
* Nachdem das Fenster geladen ist und alle Dateien generiert wurden kann man das Fenster wieder schleißen
* Unter `cypress/integration` muss ein neues File namens `testing.spec.js` erstellt werden.
* Der Ordner `cypress/examples` kann gelöscht werden
