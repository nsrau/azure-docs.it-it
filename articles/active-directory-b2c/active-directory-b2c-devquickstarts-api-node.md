<properties
	pageTitle="Anteprima di B2C: Proteggere un'API Web usando Node.js | Microsoft Azure"
	description="Come creare un'API Web NodeJS che accetti token da un tenant B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="brandwe"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
  	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="brandwe"/>

# Anteprima di B2C: Proteggere un'API Web usando Node.js

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]
	Questo articolo non descrive come implementare le esperienze di accesso, iscrizione e gestione del profilo con Azure AD B2C, ma illustra la chiamata delle API Web dopo che l'utente ha già effettuato l'autenticazione.
Se non è già stato fatto, iniziare con l'[esercitazione introduttiva per la creazione di un'app Web .NET](active-directory-b2c-devquickstarts-web-dotnet.md) per acquisire le nozioni di base su Azure AD B2C.

> [AZURE.NOTE]Questo esempio è stato scritto per [Applicazione di esempio iOS B2C.](active-directory-b2c-devquickstarts-ios.md) Eseguire prima la procedura dettagliata e in seguito seguire l’esempio.

**Passport** è il middleware di autenticazione per Node.js. Passport, estremamente flessibile e modulare, può essere rilasciato in modo non invadente in qualsiasi applicazione Web basata su Express o Resitify. Una gamma completa di strategie supporta l'autenticazione mediante nome utente e password, Facebook, Twitter e altro ancora. È stata sviluppata una strategia per Microsoft Azure Active Directory. Dopo l'installazione di questo modulo, verrà aggiunto il plug-in `passport-azure-ad` di Microsoft Azure Active Directory.

A questo scopo è necessario:

1. Registrare un'applicazione con Azure AD.
2. Impostare l'app per l'uso del plug-in azure-ad-passport di Passport.
3. Configurare un'applicazione client per chiamare l'API Web To Do List.

Il codice per questa esercitazione è salvato [su GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs). Per seguire la procedura è possibile [scaricare la struttura dell'app come file con estensione zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs/archive/skeleton.zip) o clonare la struttura:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs.git```

Al termine dell'esercitazione, verrà fornita anche l'applicazione completata.

> [AZURE.WARNING]Per l'Anteprima B2C è necessario utilizzare lo stesso ID client/ID applicazione e i criteri per il server dell’attività Web API e per il client che effettua la connessione. Questo vale per i tutorial iOS e Android. Se è stato creata in precedenza un'applicazione con una delle guide rapide, utilizzare tali valori anziché crearne di nuovi in basso.


## 1\. Ottenere una directory di Azure AD B2C

Prima di poter usare Azure AD B2C, è necessario creare una directory, o tenant. Una directory è un contenitore per utenti, app, gruppi e così via. Se non è già stato fatto, passare a [creare una directory B2C](active-directory-b2c-get-started.md) prima di continuare.

## 2\. Creare un'applicazione

A questo punto, è necessario creare un'app nella directory B2C, che fornisce ad Azure AD alcune informazioni necessarie per comunicare in modo sicuro con l'app. Sia l'app del client che l'API Web saranno rappresentate da un singolo **ID applicazione** in questo caso, poiché includono un'app per la logica. Per creare un'app, [seguire questa procedura](active-directory-b2c-app-registration.md): Assicurarsi di

- Includere un'**app Web/API Web** nell'applicazione
- Immettere `http://localhost/TodoListService` come **URL di risposta**: si tratta dell'URL predefinito per questo esempio di codice.
- Creare un **Segreto applicazione** per l'applicazione e prenderne nota, perché verrà richiesta a breve.
- Copiare l'**ID applicazione** assegnato all'app, perché anche questo verrà richiesto a breve.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 3\. Creare i criteri

In Azure AD B2C ogni esperienza utente è definita da [**criteri**](active-directory-b2c-reference-policies.md) specifici. Questa app contiene tre esperienze di identità: iscrizione, accesso e accesso con Facebook. Sarà necessario creare i criteri per ciascun tipo, come descritto nell'articolo [riferimento ai criteri](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Durante la creazione dei tre criteri, assicurarsi di:

- Scegliere il **Nome visualizzato** e alcuni altri attributi per l'iscrizione nei criteri di iscrizione.
- Scegliere il **Nome visualizzato** e l'**ID oggetto** come attestazioni dell'applicazione in tutti i criteri. È consentito scegliere anche altre attestazioni.
- Copiare il **Nome** di ciascun criterio dopo averlo creato. Dovrebbero mostrare il prefisso `b2c_1_`. Sarà necessario usare i nomi dei criteri a breve.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Dopo aver creato i tre criteri, è possibile passare alla creazione dell'app.

Si noti che questo articolo non illustra come usare i criteri appena creati. Per altre informazioni sul funzionamento dei criteri in Azure AD B2C, iniziare con l'[esercitazione introduttiva per la creazione di un'app Web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## Passaggio 4: Scaricare node.js per la piattaforma in uso
Per usare correttamente questo esempio, è necessario disporre di un'installazione funzionante di Node.js.

Installare Node.js da [http://nodejs.org](http://nodejs.org).

## Passaggio 5: Installare MongoDB nella piattaforma in uso

Per usare correttamente questo esempio, è necessario disporre di un'installazione funzionante di MongoDB. Si userà MongoDB per rendere l'API REST persistente nelle istanze del server.

Installare MongoDB da [http://mongodb.org](http://www.mongodb.org).

> [AZURE.NOTE]In questa procedura dettagliata si presume che si usino gli endpoint server e di installazione predefiniti per MongoDB, che al momento della stesura di questo articolo sono: mongodb://localhost

## Passaggio 6: Installare i moduli Restify nell'API Web

Si userà Resitfy per compilare l'API REST. Restify è un framework applicazioni di Node.js minimo e flessibile derivato da Express, che include una gamma completa di funzionalità per la compilazione di API REST su Connect.

### Installare Restify

Dalla riga di comando passare alla directory azuread. Se la directory **azuread** non esiste, crearla.

`cd azuread` - o - `mkdir azuread;`

Digitare il seguente comando:

`npm install restify`

Questo comando installa Restify.

#### È STATO VISUALIZZATO UN ERRORE?

Quando si usa npm in alcuni sistemi operativi, è possibile che vengano visualizzati un errore di errore: EPERM, chmod '/usr/local/bin/..' e una richiesta di provare a eseguire l'account come amministratore. In questo caso, usare il comando sudo per eseguire npm a un livello di privilegi più elevato.

#### È STATO VISUALIZZATO UN ERRORE RELATIVO A DTRACE?

Durante l'installazione di Restify, è possibile che venga visualizzato qualcosa di simile:

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: 2
gyp ERR! stack     at ChildProcess.onExit (/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/build.js:267:23)
gyp ERR! stack     at ChildProcess.EventEmitter.emit (events.js:98:17)
gyp ERR! stack     at Process.ChildProcess._handle.onexit (child_process.js:789:12)
gyp ERR! System Darwin 13.1.0
gyp ERR! command "node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
gyp ERR! cwd /Volumes/Development HD/azuread/node_modules/restify/node_modules/dtrace-provider
gyp ERR! node -v v0.10.11
gyp ERR! node-gyp -v v0.10.0
gyp ERR! not ok
npm WARN optional dep failed, continuing dtrace-provider@0.2.8
```


Restify offre un meccanismo efficace per tenere traccia delle chiamate REST usando DTrace. Tuttavia, per molti sistemi operativi DTrace non è disponibile. È possibile ignorare questi errori.


L'output di questo comando dovrebbe apparire simile al seguente:


	restify@2.6.1 node_modules/restify
	├── assert-plus@0.1.4
	├── once@1.3.0
	├── deep-equal@0.0.0
	├── escape-regexp-component@1.0.2
	├── qs@0.6.5
	├── tunnel-agent@0.3.0
	├── keep-alive-agent@0.0.1
	├── lru-cache@2.3.1
	├── node-uuid@1.4.0
	├── negotiator@0.3.0
	├── mime@1.2.11
	├── semver@2.2.1
	├── spdy@1.14.12
	├── backoff@2.3.0
	├── formidable@1.0.14
	├── verror@1.3.6 (extsprintf@1.0.2)
	├── csv@0.3.6
	├── http-signature@0.10.0 (assert-plus@0.1.2, asn1@0.1.11, ctype@0.5.2)
	└── bunyan@0.22.0 (mv@0.0.5)


## 7: Installare Passport.js nell'API Web

[Passport](http://passportjs.org/) è il middleware di autenticazione per Node.js. Passport, estremamente flessibile e modulare, può essere rilasciato in modo non invadente in qualsiasi applicazione Web basata su Express o Resitify. Una gamma completa di strategie supporta l'autenticazione mediante nome utente e password, Facebook, Twitter e altro ancora. È stata sviluppata una strategia per Azure Active Directory. Dopo l'installazione di questo modulo, verrà aggiunto il plug-in della strategia per Azure Active Directory.

Dalla riga di comando passare alla directory azuread.

Immettere il comando seguente per installare passport.js.

`npm install passport`

L'output del comando dovrebbe apparire simile al seguente:

	passport@0.1.17 node_modules\passport
	├── pause@0.0.1
	└── pkginfo@0.2.3

## 8: Aggiungere Passport-Azure-AD all'API Web

In seguito, mediante passport-azuread verrà aggiunta la strategia OAuth, una suite di strategie che connettono Azure Active Directory con Passport. In questo esempio di API REST si userà tale strategia per i token di connessione.

> [AZURE.NOTE]Anche se OAuth2 fornisce un framework in cui è possibile rilasciare qualsiasi tipo di token noto, solo determinati tipi di token sono usati su larga scala. Per proteggere gli endpoint, si è passati ai token di connessione. I token di connessione sono il tipo di token maggiormente rilasciato in OAuth2 e molte implementazioni presumono che i token di connessione siano l'unico tipo di token rilasciato.

Dalla riga di comando passare alla directory azuread.

Digitare il comando seguente per installare il modulo passport-azure-ad.

`npm install passport-azure-ad`

L'output del comando dovrebbe apparire simile al seguente:

``
passport-azure-ad@1.0.0 node_modules/passport-azure-ad
├── xtend@4.0.0
├── xmldom@0.1.19
├── passport-http-bearer@1.0.1 (passport-strategy@1.0.0)
├── underscore@1.8.3
├── async@1.3.0
├── jsonwebtoken@5.0.2
├── xml-crypto@0.5.27 (xpath.js@1.0.6)
├── ursa@0.8.5 (bindings@1.2.1, nan@1.8.4)
├── jws@3.0.0 (jwa@1.0.1, base64url@1.0.4)
├── request@2.58.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, tunnel-agent@0.4.1, oauth-sign@0.8.0, isstream@0.1.2, extend@2.0.1, json-stringify-safe@5.0.1, node-uuid@1.4.3, qs@3.1.0, combined-stream@1.0.5, mime-types@2.0.14, form-data@1.0.0-rc1, http-signature@0.11.0, bl@0.9.4, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
└── xml2js@0.4.9 (sax@0.6.1, xmlbuilder@2.6.4)
``

## 9: Aggiungere i moduli MongoDB all'API Web

MongoDB verrà usato come archivio dati. Per questo motivo, è necessario installare sia Mongoose, un plug-in molto usato per gestire modelli e schemi, che il driver di database per MongoDB, chiamato sempre MongoDB.


* `npm install mongoose`
* `npm install mongodb`

## 10: Installare moduli aggiuntivi

Ora si installeranno gli altri moduli necessari.


Dalla riga di comando passare alla cartella **azuread**, se necessario:

`cd azuread`


Immettere i comandi seguenti per installare i seguenti moduli nella directory node\_modules:

* `npm install crypto`
* `npm install assert-plus`
* `npm install posix-getopt`
* `npm install util`
* `npm install path`
* `npm install connect`
* `npm install xml-crypto`
* `npm install xml2js`
* `npm install xmldom`
* `npm install async`
* `npm install request`
* `npm install underscore`
* `npm install grunt-contrib-jshint@0.1.1`
* `npm install grunt-contrib-nodeunit@0.1.2`
* `npm install grunt-contrib-watch@0.2.0`
* `npm install grunt@0.4.1`
* `npm install xtend@2.0.3`
* `npm install bunyan`
* `npm update`


## 11: Creare un file server.js con le dipendenze

Il file server.js fornirà la maggior parte della funzionalità per il server API Web. La maggior parte del codice verrà aggiunta a questo file. Per la produzione, si effettuerebbe il refactoring della funzionalità in file più piccoli, ad esempio route e controller distinti. Ai fini di questa demo, si userà server.js per questa funzionalità.

Dalla riga di comando passare alla cartella **azuread**, se necessario:

`cd azuread`

Creare un file `server.js` nell'editor preferito e aggiungere le seguenti informazioni:

```Javascript
'use strict';
/**
* Module dependencies.
*/
var fs = require('fs');
var path = require('path');
var util = require('util');
var assert = require('assert-plus');
var mongoose = require('mongoose/');
var bunyan = require('bunyan');
var restify = require('restify');
var config = require('./config');
var passport = require('passport');
var OIDCBearerStrategy = require('passport-azure-ad').BearerStrategy;
```

Salvare il file. Servirà ancora tra poco.

## 12: Creare un file config per archiviare le impostazioni di Azure AD

Questo file di codice passa i parametri di configurazione dal portale di Azure Active Directory a Passport.js. Questi valori di configurazione sono stati creati quando si è aggiunta l'API Web al portale nella prima parte della procedura dettagliata. Dopo avere copiato il codice, verrà spiegato che cosa inserire nei valori di questi parametri.


Dalla riga di comando passare alla cartella **azuread**, se necessario:

`cd azuread`

Creare un file `config.js` nell'editor preferito e aggiungere le seguenti informazioni:

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
audience: '<your audience URI>',
identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration', // For using Microsoft you should never need to change this.
tenantName:'<tenant name>',
policyName:'b2c_1_<sign in policy name>',
};

```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### Valori richiesti

*IdentityMetadata*: l'area in cui passport-azure-ad cercherà i dati di configurazione per IdP, nonché le chiavi per la convalida dei token JWT. Se si usa Azure Active Directory, probabilmente non è necessario modificare questa impostazione.

*audience*: l'URI dal portale che identifica il servizio. L'esempio usa: `http://localhost/TodoListService`

*tenantName*: il nome del tenant (ad esempio contoso.onmicrosoft.com)

*policyName*: i criteri da usare per convalidare i token in ingresso nel server. Questo criterio dovrebbe essere il criterio utilizzato nell'applicazione client per l’accesso.

> [AZURE.NOTE]Per questa anteprima di B2C verranno usati gli stessi i criteri per entrambe le configurazioni client e server. Se è stata eseguita una procedura dettagliata in cui sono già stati creati questi criteri, non è necessario crearli di nuovo. Poiché è stata superata questa procedura dettagliata, non è necessario impostare nuovi criteri quando si eseguono le procedure dettagliate dei client su questo sito.


## 13: Aggiungere la configurazione al file server.js

È necessario leggere questi valori dal file config appena creato nell'applicazione. A tale scopo, è sufficiente aggiungere il file config come risorsa necessaria nell'applicazione e quindi impostare le variabili globali su quelle del documento config.js.

Dalla riga di comando passare alla cartella **azuread**, se necessario:

`cd azuread`

Aprire il file `server.js` nell'editor preferito e aggiungere le seguenti informazioni:

```Javascript
var config = require('./config');
```
Quindi aggiungere una nuova sezione a `server.js` con il codice seguente:

```Javascript
// We pass these options in to the ODICBearerStrategy.
var options = {
// The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    tenantName: config.creds.tenantName,
    policyName: config.creds.policyName,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience
};
// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;
// Our logger
var log = bunyan.createLogger({
name: 'Microsoft Azure Active Directory Sample'
});
```

## Passaggio 14: Aggiungere le informazioni su schemi e modelli MongoDB usando Moongoose

Ora si inizierà a vedere l'utilità di tutte queste operazioni di preparazione unendo i tre file insieme in un servizio API REST.

Per questa procedura dettagliata si userà MongoDB per archiviare le attività, come illustrato nel ***Passaggio 4***.

Come indicato nel file config.js creato nel passaggio 11, il database è stato chiamato *tasklist* come quello inserito alla fine dell'URL di connessione mogoose\_auth\_local. Non è necessario creare questo database in anticipo in MongoDB, perché verrà creato automaticamente alla prima esecuzione dell'applicazione server (presumendo che non esista già).

Ora che è stato indicato al server quale database MongoDB deve usare, è necessario scrivere un codice aggiuntivo per creare il modello e lo schema per le attività del server.

#### Descrizione del modello

Il modello di schema è molto semplice e, se necessario, è possibile espanderlo.

NAME: nome di chi è assegnato all'attività. Un valore ***String***

TASK: l'attività stessa. Un valore ***String***

DATE: data di scadenza dell'attività. Un valore ***DATETIME***

COMPLETED: indica se l'attività è stata completata o no. Un valore ***BOOLEAN***

#### Creazione dello schema nel codice


Dalla riga di comando passare alla cartella **azuread**, se necessario:

`cd azuread`

Aprire il file `server.js` nell'editor preferito e aggiungere le seguenti informazioni sotto la voce di configurazione:

```Javascript
// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 8080;
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');
```
Verrà così stabilita la connessione al server MongoDB e restituito un oggetto Schema.

#### Usando lo schema, creare il modello nel codice

Sotto il codice appena scritto aggiungere il codice seguente:

```Javascript
// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
owner: String,
task: String,
completed: Boolean,
date: Date
});
// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
Come si può vedere dal codice, si crea lo schema e quindi si crea un oggetto modello che verrà usato per archiviare i dati nel codice quando si definiscono le ***route***.

## Passaggio 15: Aggiungere le route per il server API REST delle attività

Ora che si dispone di un modello di database da usare, aggiungere le route che verranno usate per il server API REST.

### Informazioni sulle route in Restify

Il funzionamento delle route in Restify è identico a quello nello stack di Express. Definire le route usando l'URI che si prevede verrà chiamato dalle applicazioni client. Le route si definiscono di solito in un file separato. In questo caso, si inseriranno le route nel file server.js. Si consiglia di fattorizzarle nel relativo file per usarle in fase di produzione.

Un modello tipico per una route Restify è:

```Javascript
function createObject(req, res, next) {
// do work on Object
_object.name = req.params.object; // passed value is in req.params under object
///...
return next(); // keep the server going
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```


Questo è il modello più semplice. Resitfy (come anche Express) offre funzionalità molto più avanzate, ad esempio la definizione di tipi di applicazione e l'esecuzione di un routing complesso tra endpoint diversi. In questo caso, le route saranno molto semplici.

#### Aggiungere le route predefinite al server

Ora si aggiungeranno le route CRUD (creazione, recupero, aggiornamento ed eliminazione).

Dalla riga di comando passare alla cartella **azuread**, se necessario:

`cd azuread`

Aprire il file `server.js` nell'editor preferito e aggiungere le seguenti informazioni sotto le voci di database create prima:

```Javascript
/**
*
* APIs for our REST Task server
*/
// Create a task
function createTask(req, res, next) {
// Resitify currently has a bug which doesn't allow you to set default headers
// This headers comply with CORS and allow us to mongodbServer our response to any origin
res.header("Access-Control-Allow-Origin", "*");
res.header("Access-Control-Allow-Headers", "X-Requested-With");
// Create a new task model, fill it up and save it to Mongodb
var _task = new Task();
if (!req.params.task) {
req.log.warn({
params: p
}, 'createTodo: missing task');
next(new MissingTaskError());
return;
}
_task.owner = owner;
_task.task = req.params.task;
_task.date = new Date();
_task.save(function(err) {
if (err) {
req.log.warn(err, 'createTask: unable to save');
next(err);
} else {
res.send(201, _task);
}
});
return next();
}
// Delete a task by name
function removeTask(req, res, next) {
Task.remove({
task: req.params.task,
owner: owner
}, function(err) {
if (err) {
req.log.warn(err,
'removeTask: unable to delete %s',
req.params.task);
next(err);
} else {
log.info('Deleted task:', req.params.task);
res.send(204);
next();
}
});
}
// Delete all tasks
function removeAll(req, res, next) {
Task.remove();
res.send(204);
return next();
}
// Get a specific task based on name
function getTask(req, res, next) {
log.info('getTask was called for: ', owner);
Task.find({
owner: owner
}, function(err, data) {
if (err) {
req.log.warn(err, 'get: unable to read %s', owner);
next(err);
return;
}
res.json(data);
});
return next();
}
/// Simple returns the list of TODOs that were loaded.
function listTasks(req, res, next) {
// Resitify currently has a bug which doesn't allow you to set default headers
// This headers comply with CORS and allow us to mongodbServer our response to any origin
res.header("Access-Control-Allow-Origin", "*");
res.header("Access-Control-Allow-Headers", "X-Requested-With");
log.info("listTasks was called for: ", owner);
Task.find({
owner: owner
}).limit(20).sort('date').exec(function(err, data) {
if (err)
return next(err);
if (data.length > 0) {
log.info(data);
}
if (!data.length) {
log.warn(err, "There is no tasks in the database. Add one!");
}
if (!owner) {
log.warn(err, "You did not pass an owner when listing tasks.");
} else {
res.json(data);
}
});
return next();
}
```

### Aggiungere la gestione di errori per le route

È opportuno aggiungere la gestione di errori per poter comunicare al client il problema riscontrato in modo che possa comprenderlo.

Aggiungere il codice seguente sotto il codice scritto sopra:

```Javascript
///--- Errors for communicating something interesting back to the client
function MissingTaskError() {
restify.RestError.call(this, {
statusCode: 409,
restCode: 'MissingTask',
message: '"task" is a required parameter',
constructorOpt: MissingTaskError
});
this.name = 'MissingTaskError';
}
util.inherits(MissingTaskError, restify.RestError);
function TaskExistsError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 409,
restCode: 'TaskExists',
message: owner + ' already exists',
constructorOpt: TaskExistsError
});
this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);
function TaskNotFoundError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 404,
restCode: 'TaskNotFound',
message: owner + ' was not found',
constructorOpt: TaskNotFoundError
});
this.name = 'TaskNotFoundError';
}
util.inherits(TaskNotFoundError, restify.RestError);
```


## Passaggio 16: Creare il server

Dopo avere definito il database e inserito le route, resta solo da aggiungere l'istanza del server che gestirà le chiamate.

Restify (come anche Express) offre un elevato livello di personalizzazione per un server API REST, ma anche in questo caso si userà la configurazione più semplice.

```Javascript
/**
* Our Server
*/
var server = restify.createServer({
name: "Microsoft Azure Active Directroy TODO Server",
version: "2.0.1"
});
// Ensure we don't drop data on uploads
server.pre(restify.pre.pause());
// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());
// Handles annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());
// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());
// Allow 5 requests/second by IP, and burst to 10
server.use(restify.throttle({
burst: 10,
rate: 5,
ip: true,
}));
// Use the common stuff you probably want
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
mapParams: true
}));
```
## PASSAGGIO 17: aggiungere le route al server (senza autenticazione per ora)

```Javascript
/// Now the real handlers. Here we just CRUD
/**
/*
/* Each of these handlers are protected by our OIDCBearerStrategy by invoking 'oidc-bearer'
/* in the pasport.authenticate() method. We set 'session: false' as REST is stateless and
/* we don't need to maintain session state. You can experiement removing API protection
/* by removing the passport.authenticate() method like so:
/*
/* server.get('/tasks', listTasks);
/*
**/
server.get('/tasks', listTasks);
server.get('/tasks', listTasks);
server.get('/tasks/:owner', getTask);
server.head('/tasks/:owner', getTask);
server.post('/tasks/:owner/:task', createTask);
server.post('/tasks', createTask);
server.del('/tasks/:owner/:task', removeTask);
server.del('/tasks/:owner', removeTask);
server.del('/tasks', removeTask);
server.del('/tasks', removeAll, function respond(req, res, next) {
res.send(204);
next();
});
// Register a default '/' handler
server.get('/', function root(req, res, next) {
var routes = [
'GET /',
'POST /tasks/:owner/:task',
'POST /tasks (for JSON body)',
'GET /tasks',
'PUT /tasks/:owner',
'GET /tasks/:owner',
'DELETE /tasks/:owner/:task'
];
res.send(200, routes);
next();
});
server.listen(serverPort, function() {
var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
consoleMessage += '\n %s server is listening at %s';
consoleMessage += '\n Open your browser to %s/tasks\n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';
});
```
## 18: Eseguire il server prima di aggiungere il supporto OAuth

Testare il server prima di aggiungere l'autenticazione.

Il modo più semplice per farlo consiste nell'usare Curl in una riga di comando. Prima di procedere, è necessaria una semplice utilità che consente di analizzare l'output come JSON. A tale scopo, installare lo strumento JSON che verrà usato in tutti gli esempi seguenti.

`$npm install -g jsontool`

Questo comando installa lo strumento JSON a livello globale. A questo punto è possibile dedicarsi al server:

In primo luogo, assicurarsi che l'istanza di monogoDB sia in esecuzione.

`$sudo mongod`

Quindi, passare alla directory e iniziare a usare Curl.

`$ cd azuread` `$ node server.js`

`$ curl -isS http://127.0.0.1:8080 | json`

```Shell
HTTP/1.1 200 OK
Connection: close
Content-Type: application/json
Content-Length: 171
Date: Tue, 14 Jul 2015 05:43:38 GMT
[
"GET /",
"POST /tasks/:owner/:task",
"POST /tasks (for JSON body)",
"GET /tasks",
"PUT /tasks/:owner",
"GET /tasks/:owner",
"DELETE /tasks/:owner/:task"
]
```

Ora è possibile aggiungere un'attività in questo modo:

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

La risposta dovrebbe essere:

```Shell
HTTP/1.1 201 Created
Connection: close
Access-Control-Allow-Origin: *
Access-Control-Allow-Headers: X-Requested-With
Content-Type: application/x-www-form-urlencoded
Content-Length: 5
Date: Tue, 04 Feb 2014 01:02:26 GMT
Hello
```
È anche possibile elencare le attività di Brandon in questo modo:

`$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Se tutto funziona, si può aggiungere OAuth al server API REST.

**Ora si dispone di un'API REST con MongoDB.**

## 19: Aggiungere l'autenticazione al server dell'API REST

Ora che l'API REST è in esecuzione, è possibile usarla in Azure AD.

Dalla riga di comando passare alla cartella **azuread**, se necessario:

`cd azuread`

### 1: Usare l’OIDCBearerStrategy inclusa in passport-azure-ad

Nei passaggi precedenti è stato creato un tipico server REST TODO senza alcun tipo di autenticazione. Questa operazione è stata eseguita nel modo indicato di seguito.

Innanzitutto è necessario indicare che si desidera usare Passport. Inserire il codice subito dopo la configurazione dell'altro server:

```Javascript
// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support
```

> [AZURE.TIP]Durante la scrittura delle API è sempre necessario collegare i dati a un elemento univoco dal token in modo che l'utente non possa eseguire lo spoofing. Quando archivia gli elementi TODO, il server esegue questa operazione in base all'ID dell’oggetto dell'utente nel token (chiamato mediante token.oid) presente nel campo "owner". Questo garantisce che soltanto l'utente in questione possa accedere ai propri elementi TODO e che nessun altro possa accedere agli elementi TODO immessi. Nell'API di "owner" non è presente alcuna esposizione, così che un utente esterno può richiedere TODO di altri anche se questi non sono autenticati.

Di seguito verrà usata la strategia Bearer fornita con passport-azure-ad. Per il momento limitarsi a esaminare il codice, che verrà illustrato tra breve. Inserire il codice dopo quanto mostrato sopra:

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users
/*
/* Passport pattern provides the need to manage users and info tokens
/* with a FindorCreate() method that must be provided by the implementor.
/* Here we just autoregister any user and implement a FindById().
/* You'll want to do something smarter.
**/
var findById = function(id, fn) {
for (var i = 0, len = users.length; i < len; i++) {
var user = users[i];
if (user.sub === id) {
log.info('Found user: ', user);
return fn(null, user);
}
}
return fn(null, null);
};
var oidcStrategy = new OIDCBearerStrategy(options,
function(token, done) {
log.info('verifying the user');
log.info(token, 'was the token retreived');
findById(token.sub, function(err, user) {
if (err) {
return done(err);
}
if (!user) {
// "Auto-registration"
log.info('User was added automatically as they were new. Their sub is: ', token.sub);
users.push(token);
owner = token.sub;
return done(null, token);
}
owner = token.sub;
return done(null, user, token);
});
}
);
passport.use(oidcStrategy);
```

Passport usa un modello simile per tutte le strategie (Twitter, Facebook e così via) che soddisfano i requisiti degli scrittori della strategia. Osservando la strategia, è possibile notare che a quest'ultima è stata passata una funzione() con parametri token e done. La strategia verrà restituita al termine dell'esecuzione. Una volta restituita, è opportuno archiviare l'utente e mettere da parte il token in modo che non sia necessario richiederlo nuovamente.

> [AZURE.IMPORTANT]Il codice precedente accetta qualsiasi utente che esegue l'autenticazione al server. Questa operazione è nota come registrazione automatica. Nei server di produzione è preferibile non consentire l'accesso a chiunque senza prima prevedere un processo di registrazione. Questo è il modello in genere adottato per le app consumer che consentono di eseguire la registrazione con Facebook, ma che chiedono di immettere informazioni aggiuntive. Se non si trattasse di un programma della riga di comando, si sarebbe estratto il messaggio di posta elettronica dall'oggetto token restituito e si sarebbe chiesto di immettere informazioni aggiuntive. Poiché si tratta di un server di test, è sufficiente aggiungere le informazioni al database in memoria.

### 2\. Proteggere alcuni endpoint

Per proteggere gli endpoint, specificare la chiamata a passport.authenticate() con il protocollo preferito.

Si può eseguire un'operazione più interessante modificando la route nel codice del server:

```Javascript
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## 20: eseguire nuovamente l'applicazione server e assicurarsi che rifiuti l'utente

Per verificare se la protezione OAuth2 per gli endpoint è attiva, usare nuovamente `curl`. Questa operazione verrà eseguita prima di eseguire uno qualsiasi degli SDK client in questo endpoint. Le intestazioni restituite dovrebbero bastare a indicare che si sta seguendo la strada giusta.

In primo luogo, assicurarsi che l'istanza di monogoDB sia in esecuzione:

	$sudo mongod

Quindi, passare alla directory e iniziare a usare Curl.

	$ cd azuread
	$ node server.js

Provare un'operazione POST di base:

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

401 è la risposta prevista qui, perché indica che il livello Passport sta tentando il reindirizzamento all'endpoint di autorizzazione, che è esattamente ciò che si vuole.


## Congratulazioni. Il servizio API REST sta usando OAuth2

Sono state eseguite tutte le operazioni possibili con questo server senza usare un client compatibile con OAuth2. Sarà necessario eseguire un'altra procedura dettagliata.

Se servivano solo informazioni per implementare un'API REST usando Restify e OAuth2, il codice fornito è più che sufficiente per continuare a sviluppare il servizio e imparare a compilare partendo da questo esempio.

Come riferimento, l'esempio completato (senza i valori di configurazione) [è disponibile in un file con estensione zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs/archive/complete.zip). In alternativa, è possibile clonarlo da GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs.git```


## Passaggi successivi

Ora è possibile passare ad argomenti più avanzati. È possibile consultare:

[Connettersi a un'API Web tramite iOS con B2C >>](active-directory-b2c-devquickstarts-ios.md)

<!---HONumber=AcomDC_1203_2015-->