<properties
	pageTitle="Introduzione a NodeJS per Azure AD | Microsoft Azure"
	description="Come compilare un'API Web REST per Node.js che si integra con Azure AD per l'autenticazione."
	services="active-directory"
	documentationCenter="nodejs"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="brandwe"/>

# Introduzione all'API WEB per Node

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

**Passport** è il middleware di autenticazione per Node.js. Passport, estremamente flessibile e modulare, può essere rilasciato in modo non invadente in qualsiasi applicazione Web basata su Express o Resitify. Una gamma completa di strategie supporta l'autenticazione mediante nome utente e password, Facebook, Twitter e altro ancora. È stata sviluppata una strategia per Microsoft Azure Active Directory. Dopo l'installazione di questo modulo, verrà aggiunto il plug-in `passport-azure-ad` di Microsoft Azure Active Directory.

A questo scopo è necessario:

1. Registrare un'applicazione con Azure AD.
2. Impostare l'app per l'uso del plug-in azure-ad-passport di Passport.
3. Configurare un'applicazione client per chiamare l'API Web To Do List.

Il codice per questa esercitazione è salvato [su GitHub](https://github.com/Azure-Samples/active-directory-node-webapi).

> [AZURE.NOTE] Questo articolo non descrive come implementare le esperienze di accesso, iscrizione e gestione del profilo con Azure AD B2C, ma illustra la chiamata delle API Web dopo che l'utente ha già effettuato l'autenticazione. Se non è già stato fatto, iniziare con il documento sull'[Integrazione con Azure Active Directory](active-directory-how-to-integrate.md) per acquisire le nozioni di base su Azure Active Directory.


Poiché tutto il codice sorgente per questo esempio in esecuzione è stato rilasciato in GitHub con una licenza MIT, è possibile clonarlo (o ancora meglio, biforcarlo) e inviare commenti e suggerimenti e richieste pull.

## Informazioni sui moduli Node.js

In questa procedura dettagliata verranno usati moduli Node.js. I moduli sono pacchetti JavaScript caricabili che forniscono funzionalità specifiche per l'applicazione. Di solito i moduli si installano usando lo strumento da riga di comando NPM di Node.js nella directory di installazione di NPM, ma alcuni moduli, come il modulo HTTP, sono inclusi nel pacchetto Node.js principale. I moduli installati vengono salvati nella directory node\_modules nella radice della directory di installazione di Node.js. Ogni modulo nella directory node\_modules mantiene la relativa directory node\_modules che contiene i moduli da cui dipende e ogni modulo necessario ha una directory node\_modules. Questa struttura di directory ricorsiva rappresenta la catena di dipendenze.

Questa struttura della catena di dipendenze comporta un footprint maggiore delle applicazioni, ma garantisce che vengano soddisfatte tutte le dipendenze e che la versione dei moduli usata durante lo sviluppo venga usata anche in fase di produzione. In questo modo il comportamento delle app di produzione è più prevedibile e si evitano problemi controllo delle versioni per gli utenti.

## 1\. Registrare un tenant di Azure AD

Per usare questo esempio, sarà necessario un tenant di Azure Active Directory. Se non si è certi di cosa sia tenant o di come ottenerne uno, vedere [Come ottenere un tenant di Azure AD](active-directory-howto-tenant.md).

## 2\. Creare un'applicazione

A questo punto, è necessario creare un'app nella directory, che fornisce ad Azure AD alcune informazioni necessarie per comunicare in modo sicuro con l'app. Sia l'app del client che l'API Web saranno rappresentate da un singolo **ID applicazione** in questo caso, poiché includono un'app per la logica. Per creare un'app, seguire [questa procedura](active-directory-how-applications-are-added.md): Se si sviluppa un'app line-of-business, [queste istruzioni aggiuntive possono essere utili](active-directory-applications-guiding-developers-for-lob-applications.md).

Assicurarsi di:

- Accedere al portale di gestione di Azure.
- Nel pannello di navigazione a sinistra fare clic su **Active Directory**.
- Selezionare il tenant in cui si desidera registrare l'applicazione.
- Fare clic sulla scheda **Applicazioni**, quindi fare clic su aggiungi nel pannello in basso.
- Seguire le istruzioni e creare una nuova **Applicazione Web e/o API Web**.
    - Il **nome** dell'applicazione deve essere una descrizione per gli utenti finali.
    - L'**URL accesso** è l'URL di base dell'app. Il valore predefinito del codice di esempio è `https://localhost:8080`.
    - L'**URI ID app** è un identificatore univoco dell'applicazione. Per convenzione si usa `https://<tenant-domain>/<app-name>`, ad esempio `https://contoso.onmicrosoft.com/my-first-aad-app`.
- Dopo avere completato la registrazione, AAD assegnerà all'app un identificatore client univoco. Poiché questo valore sarà necessario nelle sezioni successive, copiarlo dalla scheda Configura.

- PROMEMORIA: creare un **Segreto applicazione** per l'applicazione e prenderne nota, perché verrà richiesto a breve.
- PROMEMORIA: copiare l'**ID applicazione** assegnato all'app, perché anche questo verrà richiesto a breve.


## 3\. Scaricare node.js per la piattaforma in uso
Per usare correttamente questo esempio, è necessario disporre di un'installazione funzionante di Node.js.

Installare Node.js da [http://nodejs.org](http://nodejs.org).

## 4\. Installare MongoDB nella piattaforma in uso

Per usare correttamente questo esempio, è necessario disporre di un'installazione funzionante di MongoDB. Si userà MongoDB per rendere l'API REST persistente nelle istanze del server.

Installare MongoDB da [http://mongodb.org](http://www.mongodb.org).

> [AZURE.NOTE] In questa procedura dettagliata si presume che si usino gli endpoint server e di installazione predefiniti per MongoDB, che al momento della stesura di questo articolo sono: mongodb://localhost


## 5\. Installare i moduli Restify nell'API Web

Si userà Resitfy per compilare l'API REST. Restify è un framework applicazioni di Node.js minimo e flessibile derivato da Express, che include una gamma completa di funzionalità per la compilazione di API REST su Connect.

### Installare Restify

Dalla riga di comando passare alla directory azuread. Se la directory **azuread** non esiste, crearla.

`cd azuread - or- mkdir azuread; cd azuread`

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


## 6\. Installare Passport.js nell'l'API Web

[Passport](http://passportjs.org/) è il middleware di autenticazione per Node.js. Passport, estremamente flessibile e modulare, può essere rilasciato in modo non invadente in qualsiasi applicazione Web basata su Express o Resitify. Una gamma completa di strategie supporta l'autenticazione mediante nome utente e password, Facebook, Twitter e altro ancora. È stata sviluppata una strategia per Azure Active Directory. Dopo l'installazione di questo modulo, verrà aggiunto il plug-in della strategia per Azure Active Directory.

Dalla riga di comando passare alla directory azuread.

Immettere il comando seguente per installare passport.js.

`npm install passport`

L'output del comando dovrebbe apparire simile al seguente:

	passport@0.1.17 node_modules\passport
	├── pause@0.0.1
	└── pkginfo@0.2.3

## 7\. Aggiungere Passport-Azure-AD all'API Web

In seguito, mediante passport-azuread verrà aggiunta la strategia OAuth, una suite di strategie che connettono Azure Active Directory con Passport. In questo esempio di API REST si userà tale strategia per i token di connessione.

> [AZURE.NOTE] Anche se OAuth2 fornisce un framework in cui è possibile rilasciare qualsiasi tipo di token noto, solo determinati tipi di token sono usati su larga scala. Per proteggere gli endpoint, si è passati ai token di connessione. I token di connessione sono il tipo di token maggiormente rilasciato in OAuth2 e molte implementazioni presumono che i token di connessione siano l'unico tipo di token rilasciato.

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



## 8\. Aggiungere i moduli MongoDB all'API Web

MongoDB verrà usato come archivio dati. Per questo motivo, è necessario installare sia Mongoose, un plug-in molto usato per gestire modelli e schemi, che il driver di database per MongoDB, chiamato sempre MongoDB.


* `npm install mongoose`

## 9\. Installare moduli aggiuntivi

Ora si installeranno gli altri moduli necessari.


Dalla riga di comando passare alla cartella **azuread**, se necessario:

`cd azuread`


Immettere i comandi seguenti per installare i seguenti moduli nella directory node\_modules:

* `npm install assert-plus`
* `npm install bunyan`
* `npm update`


## 10\. Creare un file server.js con le dipendenze

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
	var bunyan = require('bunyan');
	var getopt = require('posix-getopt');
	var mongoose = require('mongoose/');
	var restify = require('restify');
	var passport = require('passport');
  var BearerStrategy = require('passport-azure-ad').BearerStrategy;
```

Salvare il file. Servirà ancora tra poco.

## 11:. Creare un file config per archiviare le impostazioni di Azure AD

Questo file di codice passa i parametri di configurazione dal portale di Azure Active Directory a Passport.js. Questi valori di configurazione sono stati creati quando si è aggiunta l'API Web al portale nella prima parte della procedura dettagliata. Dopo avere copiato il codice, verrà spiegato che cosa inserire nei valori di questi parametri.


Dalla riga di comando passare alla cartella **azuread**, se necessario:

`cd azuread`

Creare un file `config.js` nell'editor preferito e aggiungere le seguenti informazioni:

```Javascript
 exports.creds = {
     mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
     clientID: 'your client ID',
     audience: 'your application URL',
    // you cannot have users from multiple tenants sign in to your server unless you use the common endpoint
  // example: https://login.microsoftonline.com/common/.well-known/openid-configuration
     identityMetadata: 'https://login.microsoftonline.com/<your tenant id>/.well-known/openid-configuration', 
     validateIssuer: true, // if you have validation on, you cannot have users from multiple tenants sign in to your server
     passReqToCallback: false,
     loggingLevel: 'info' // valid are 'info', 'warn', 'error'. Error always goes to stderr in Unix.

 };


```
Salvare il file.

## 12\. Aggiungere la configurazione al file server.js

È necessario leggere questi valori dal file config appena creato nell'applicazione. A tale scopo, è sufficiente aggiungere il file config come risorsa necessaria nell'applicazione e quindi impostare le variabili globali su quelle del documento config.js.

Dalla riga di comando passare alla cartella **azuread**, se necessario:

`cd azuread`

Aprire il file `server.js` nell'editor preferito e aggiungere le seguenti informazioni:

```Javascript
var config = require('./config');
```
Quindi aggiungere una nuova sezione a `server.js` con il codice seguente:

```Javascript
var options = {
    // The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience,
    passReqToCallback: config.creds.passReqToCallback,
    loggingLevel: config.creds.loggingLevel

};

// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;

// Our logger
var log = bunyan.createLogger({
    name: 'Azure Active Directory Bearer Sample',
         streams: [
        {
            stream: process.stderr,
            level: "error",
            name: "error"
        }, 
        {
            stream: process.stdout,
            level: "warn",
            name: "console"
        }, ]
});

  // if logging level specified, switch to it.
  if (config.creds.loggingLevel) { log.levels("console", config.creds.loggingLevel); }

// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 8080;
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
```

Salvare il file.



## 13\. Aggiungere le informazioni su schemi e modelli MongoDB usando Moongoose

Ora si inizierà a vedere l'utilità di tutte queste operazioni di preparazione unendo i tre file insieme in un servizio API REST.

Per questa procedura dettagliata si userà MongoDB per archiviare le attività, come illustrato nel ***Passaggio 4***.

Come indicato nel file `config.js` creato nel ***Passaggio 11***, il database è stato chiamato `tasklist` come quello inserito alla fine dell'URL di connessione mogoose\_auth\_local. Non è necessario creare questo database in anticipo in MongoDB, perché verrà creato automaticamente alla prima esecuzione dell'applicazione server (presumendo che non esista già).

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
// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');

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

## 14\. Aggiungere le route per il server API REST delle attività

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

### 1\. Aggiungere le route predefinite al server

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
        req.log.warn('createTodo: missing task');
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

        if (err) {
            return next(err);
        }

        if (data.length > 0) {
            log.info(data);
        }

        if (!data.length) {
            log.warn(err, "There is no tasks in the database. Did you initalize the database as stated in the README?");
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

### 2\. Aggiungere quindi alcune funzionalità di gestione degli errori alle API:

```

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


## 15\. Creare il server

Dopo avere definito il database e inserito le route, resta solo da aggiungere l'istanza del server che gestirà le chiamate.

Restify (come anche Express) offre un elevato livello di personalizzazione per un server API REST, ma anche in questo caso si userà la configurazione più semplice.

```Javascript
/**
 * Our Server
 */


var server = restify.createServer({
    name: "Azure Active Directroy TODO Server",
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
})); // Allows for JSON mapping to REST
```

## 16\. Aggiungere le route al server (senza autenticazione per ora)

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

## 17\. Eseguire il server prima di aggiungere il supporto OAuth.

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


## 18\. Aggiungere l'autenticazione al server dell'API REST

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

> [AZURE.TIP]
Durante la scrittura delle API è sempre necessario collegare i dati a un elemento univoco dal token in modo che l'utente non possa eseguire lo spoofing. Quando archivia gli elementi TODO, il server esegue questa operazione in base all'ID dell’oggetto dell'utente nel token (chiamato mediante token.oid) presente nel campo "owner". Questo garantisce che soltanto l'utente in questione possa accedere ai propri elementi TODO e che nessun altro possa accedere agli elementi TODO immessi. Nell'API di "owner" non è presente alcuna esposizione, così che un utente esterno può richiedere TODO di altri anche se questi non sono autenticati.

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


var bearerStrategy = new BearerStrategy(options,
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

passport.use(bearerStrategy);
```

Passport usa un modello simile per tutte le strategie (Twitter, Facebook e così via) che soddisfano i requisiti degli scrittori della strategia. Osservando la strategia, è possibile notare che a quest'ultima è stata passata una funzione() con parametri token e done. La strategia verrà restituita al termine dell'esecuzione. Una volta restituita, è opportuno archiviare l'utente e mettere da parte il token in modo che non sia necessario richiederlo nuovamente.

> [AZURE.IMPORTANT]
Il codice precedente accetta qualsiasi utente che esegue l'autenticazione al server. Questa operazione è nota come registrazione automatica. Nei server di produzione è preferibile non consentire l'accesso a chiunque senza prima prevedere un processo di registrazione. Questo è il modello in genere adottato per le app consumer che consentono di eseguire la registrazione con Facebook, ma che chiedono di immettere informazioni aggiuntive. Se non si trattasse di un programma della riga di comando, si sarebbe estratto il messaggio di posta elettronica dall'oggetto token restituito e si sarebbe chiesto di immettere informazioni aggiuntive. Poiché si tratta di un server di test, è sufficiente aggiungere le informazioni al database in memoria.

### 2\. Proteggere alcuni endpoint

Per proteggere gli endpoint, specificare la chiamata a `passport.authenticate()` con il protocollo preferito.

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

## 19\. Eseguire nuovamente l'applicazione server e assicurarsi che rifiuti l'utente

Per verificare se la protezione OAuth2 per gli endpoint è attiva, usare nuovamente `curl`. Questa operazione verrà eseguita prima di eseguire uno qualsiasi degli SDK client in questo endpoint. Le intestazioni restituite dovrebbero bastare a indicare che si sta seguendo la strada giusta.

In primo luogo, assicurarsi che l'istanza di monogoDB sia in esecuzione:

  $sudo mongod

Quindi, passare alla directory e iniziare a usare Curl.

  $ cd azuread $ node server.js

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

Se si è interessati a proseguire l'esplorazione di ADAL, ecco alcuni client ADAL supportati, consigliati per continuare a lavorare:

Sarà sufficiente clonarli nel computer di sviluppo e configurarli come spiegato nella procedura dettagliata.

[ADAL per iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios)

[ADAL per Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android)


[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0720_2016-->