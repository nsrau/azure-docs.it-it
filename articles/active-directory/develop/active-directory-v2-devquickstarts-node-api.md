---
title: Proteggere un'API Web di Azure Active Directory versione 2.0 tramite Node.js | Microsoft Docs
description: Informazioni su come creare un'API Web Node.js che accetta token sia da un account Microsoft personale che da account aziendali o dell'istituto di istruzione.
services: active-directory
documentationcenter: nodejs
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: 0b572fc1-2aaf-4cb6-82de-63010fb1941d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 05/13/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 94e945a52b9df7c495de1611baa08083357670c9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="secure-a-web-api-by-using-nodejs"></a>Proteggere un'API Web usando node.js
> [!NOTE]
> Non tutti gli scenari e le funzionalità di Azure Active Directory usano l'endpoint v2.0. Per determinare se è necessario usare l'endpoint v2.0 o l'endpoint v1.0, leggere le informazioni sulle [limitazioni della versione 2.0](active-directory-v2-limitations.md).
> 
> 

Quando si usa l'endpoint di Azure Active Directory (Azure AD) v 2.0, è possibile usare i token di accesso [OAuth 2.0](active-directory-v2-protocols.md) per proteggere l'API Web. Con i token di accesso OAuth 2.0, gli utenti che dispongono sia di un account Microsoft personale che di un account aziendale o dell'istituto di istruzione possono accedere in modo sicuro all'API Web.

*Passport* è il middleware di autenticazione per Node.js. Passport, flessibile e modulare, può essere rilasciato in modo non invadente in qualsiasi applicazione Web basata su Express o Restify. In Passport una gamma completa di strategie supporta l'autenticazione usando un nome utente e password, Facebook, Twitter o altre opzioni. È stata sviluppata una strategia per Azure AD. Questo articolo illustra come installare il modulo e quindi aggiungere il plug-in `passport-azure-ad` di Azure AD.

## <a name="download"></a>Scaricare
Il codice per questa esercitazione è salvato [su GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs). Per seguire l'esercitazione, è possibile [scaricare la struttura dell'app come file con estensione zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/skeleton.zip) o clonare la struttura:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

Al termine dell'esercitazione, è anche possibile ottenere l'applicazione completata.

## <a name="1-register-an-app"></a>1: Registrare un'app
Per registrare un'app creare una nuova app in [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) o seguire questa [procedura dettagliata](active-directory-v2-app-registration.md). Assicurarsi di:

* Copiare l'**ID applicazione** assegnato all'app. Per questa esercitazione occorre:
* Aggiungere la piattaforma **Mobile** per l'app.
* Copiare l' **URI di reindirizzamento** dal portale. È necessario usare il valore URI predefinito `urn:ietf:wg:oauth:2.0:oob`.

## <a name="2-install-nodejs"></a>2: Installare Node.js
Per usare l'esempio di questa esercitazione, è necessario [installare Node.js](http://nodejs.org).

## <a name="3-install-mongodb"></a>3: Installare MongoDB
Per usare correttamente questo esempio, è necessario [installare MongoDB](http://www.mongodb.org). Questo esempio usa MongoDB per rendere l'API REST persistente nelle istanze del server.

> [!NOTE]
> In questo articolo si presuppone che si usino gli endpoint di server e di installazione predefiniti per MongoDB: mongodb://localhost.
> 
> 

## <a name="4-install-the-restify-modules-in-your-web-api"></a>4: Installare i moduli Restify nell'API Web
Si usa Resitfy per compilare l'API REST. Restify è un framework applicazioni di Node.js minimo e flessibile derivato da Express, Restify è un set affidabile di funzionalità che è possibile usare per compilare API REST su Connect.

### <a name="install-restify"></a>Installare Restify
1.  Al prompt dei comandi passare alla directory **azuread**:

    `cd azuread`

    Se la directory **azuread** non esiste, crearla:

    `mkdir azuread`

2.  Installare Restify:

    `npm install restify`

    L'output di questo comando dovrebbe essere simile al seguente:

    ```
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
    └── bunyan@0.22.0(mv@0.0.5)
    ```

#### <a name="did-you-get-an-error"></a>È stato visualizzato un errore?
In alcuni sistemi operativi, quando si usa il comando `npm`, è possibile che venga visualizzato questo messaggio: `Error: EPERM, chmod '/usr/local/bin/..'`. Il messaggio di errore è seguito da una richiesta in cui è indicato di provare a eseguire l'account come amministratore. In tal caso, usare il comando `sudo` per eseguire `npm` a un livello di privilegi più elevato.

#### <a name="did-you-get-an-error-related-to-dtrace"></a>È stato visualizzato un errore relativo a DTrace?
Quando si installa Restify, è possibile che venga visualizzato questo messaggio:

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: two
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

Restify dispone di un meccanismo efficace per tenere traccia delle chiamate REST usando DTrace. Tuttavia, in molti sistemi operativi DTrace non è disponibile. È possibile ignorare questo messaggio di errore.


## <a name="5-install-passportjs-in-your-web-api"></a>5: Installare Passport.js nell'API Web
1.  Al prompt dei comandi passare alla directory **azuread**.

2.  Installare Passport.js:

    `npm install passport`

    L'output di questo comando dovrebbe essere simile al seguente:

    ```
     passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3
    ```

## <a name="6-add-passport-azure-ad-to-your-web-api"></a>6: Aggiungere passport-azure-ad all'API Web
Quindi, aggiungere la strategia di OAuth, usando passport azuread. `passport-azuread` è una suite di strategie che connettono Azure AD a Passport. In questo esempio di API REST tale strategia viene usata per i token di connessione.

> [!NOTE]
> Anche se OAuth 2.0 fornisce un framework in cui è possibile rilasciare qualsiasi tipo di token noto, sono comunemente usati alcuni tipi di token. I token di connessione vengono comunemente usati per proteggere gli endpoint. I token di connessione sono il tipo di token maggiormente rilasciato in OAuth 2.0. Molte implementazioni di OAuth 2.0 presumono che i token di connessione siano l'unico tipo di token rilasciato.
> 
> 

1.  Al prompt dei comandi passare alla directory **azuread**.

    `cd azuread`

2.  Installare il modulo `passport-azure-ad` di Passport.js:

    `npm install passport-azure-ad`

    L'output di questo comando dovrebbe essere simile al seguente:

    ```
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
    ```

## <a name="7-add-mongodb-modules-to-your-web-api"></a>7: Aggiungere i moduli MongoDB all'API Web
Questo esempio usa MongoDB come archivio dati. 

1.  Installare Mongoose, un plug-in diffuso per la gestione di modelli e schemi: 

    `npm install mongoose`

2.  Installare il driver del database per MongoDB, anche questo denominato MongoDB:

    `npm install mongodb`

## <a name="8-install-additional-modules"></a>8. Installare moduli aggiuntivi
Installare gli altri moduli necessari.

1.  Al prompt dei comandi passare alla directory **azuread**:

    `cd azuread`

2.  Immettere i comandi seguenti. I comandi installano i moduli seguenti nella directory node_modules:

    *   `npm install crypto`
    *   `npm install assert-plus`
    *   `npm install posix-getopt`
    *   `npm install util`
    *   `npm install path`
    *   `npm install connect`
    *   `npm install xml-crypto`
    *   `npm install xml2js`
    *   `npm install xmldom`
    *   `npm install async`
    *   `npm install request`
    *   `npm install underscore`
    *   `npm install grunt-contrib-jshint@0.1.1`
    *   `npm install grunt-contrib-nodeunit@0.1.2`
    *   `npm install grunt-contrib-watch@0.2.0`
    *   `npm install grunt@0.4.1`
    *   `npm install xtend@2.0.3`
    *   `npm install bunyan`
    *   `npm update`

## <a name="9-create-a-serverjs-file-for-your-dependencies"></a>9: Creare un file Server.js per le dipendenze
Il file Server.js fornirà la maggior parte della funzionalità per il server API Web. Aggiungere la maggior parte del codice a questo file. Per la produzione, è possibile eseguire il refactoring della funzionalità in file più piccoli, come per route e controller distinti. Questo articolo usa Server.js a questo scopo.

1.  Al prompt dei comandi passare alla directory **azuread**:

    `cd azuread`

2.  Usando un editor di propria scelta, creare un file Server.js. Aggiungere al file le informazioni seguenti:

    ```Javascript
    'use strict';
    /**
    * Module dependencies.
    */
    var util = require('util');
    var assert = require('assert-plus');
    var mongoose = require('mongoose/');
    var bunyan = require('bunyan');
    var restify = require('restify');
    var config = require('./config');
    var passport = require('passport');
    var OIDCBearerStrategy = require('passport-azure-ad').OIDCStrategy;
    ```

3.  Salvare il file. Servirà ancora tra poco.

## <a name="10-create-a-config-file-to-store-your-azure-ad-settings"></a>10: Creare un file config per archiviare le impostazioni di Azure AD
Questo file di codice passa i parametri di configurazione dal portale di Azure AD a Passport.js. Questi valori di configurazione sono stati creati quando si è aggiunta l'API Web al portale nella prima parte dell'articolo. Dopo aver copiato il codice, verrà spiegato che cosa inserire nei valori di questi parametri.

1.  Al prompt dei comandi passare alla directory **azuread**:

    `cd azuread`

2.  In un editor creare un file Config.js. Aggiungere le informazioni seguenti:

    ```Javascript
    // Don't commit this file to your public repos. This config is for first-run.
    exports.creds = {
    mongoose_auth_local: 'mongodb://localhost/tasklist', // Your Mongo auth URI goes here.
    issuer: 'https://sts.windows.net/**<your application id>**/',
    audience: '<your redirect URI>',
    identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration' // For Microsoft, you should never need to change this.
    };

    ```



### <a name="required-values"></a>Valori richiesti

*   **IdentityMetadata**: l'area in cui `passport-azure-ad` cercherà i dati di configurazione per il provider di identità (IDP), nonché le chiavi per la convalida dei token JSON Web (JWT). Se si usa Azure AD, probabilmente non si desidera modificare questa impostazione.

*   **audience**: URI di reindirizzamento dal portale.

> [!NOTE]
> Le chiavi vengono registrate con una certa frequenza. Assicurarsi di eseguire sempre il pull dall'URL "openid_keys" e che l'app possa accedere a Internet.
> 
> 

## <a name="11-add-the-configuration-to-your-serverjs-file"></a>11: Aggiungere la configurazione al file Server.js
L'applicazione deve leggere questi valori dal file config appena creato. Aggiungere il file config come risorsa necessaria nell'applicazione. Impostare le variabili globali su quelle in Config.js.

1.  Al prompt dei comandi passare alla directory **azuread**:

    `cd azuread`

2.  In un editor aprire Server.js. Aggiungere le informazioni seguenti:

    ```Javascript
    var config = require('./config');
    ```

3.  Aggiungere una nuova sezione a Server.js:

    ```Javascript
    // Pass these options in to the ODICBearerStrategy.
    var options = {
    // The URL of the metadata document for your app. Put the keys for token validation from the URL found in the jwks_uri tag in the metadata.
    identityMetadata: config.creds.identityMetadata,
    issuer: config.creds.issuer,
    audience: config.creds.audience
    };
    // Array to hold signed-in users and the current signed-in user (owner).
    var users = [];
    var owner = null;
    // Your logger
    var log = bunyan.createLogger({
    name: 'Microsoft Azure Active Directory Sample'
    });
    ```

## <a name="12-add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>12: Aggiungere le informazioni su schemi e modelli MongoDB usando Mongoose
Successivamente, connettere questi tre file in un servizio API REST.

Questo articolo usa MongoDB per archiviare le attività. Questa operazione viene illustrata nel *passaggio 4*.

Nel file Config.js creato nel passaggio 11 il database è denominato *tasklist*. Questo database è stato inserito alla fine dell'URL di connessione mongoose_auth_local. Non è necessario creare in anticipo il database in MongoDB. Il database viene creato alla prima esecuzione dell'applicazione server (presupponendo che il database non esista già).

Si è indicato al server quale database MongoDB usare. Successivamente, è necessario scrivere codice aggiuntivo per creare il modello e lo schema per le attività del server.

### <a name="the-model"></a>Il modello
Il modello dello schema è molto semplice. Se necessario, è possibile espanderlo. 

Il modello dello schema presenta questi valori:

*   **NAME**. Persona assegnata all'attività. Valore **stringa**.
*   **TASK**. Nome dell'attività. Valore **stringa**.
*   **DATE**. Data di scadenza dell'attività. Valore **datetime**.
*   **COMPLETED**. Indica se l'attività è stata completata o meno. Valore **booleano**.

### <a name="create-the-schema-in-the-code"></a>Creare lo schema nel codice
1.  Al prompt dei comandi passare alla directory **azuread**:

    `cd azuread`

2.  Nell'editor aprire Server.js. Sotto la voce di configurazione, aggiungere le informazioni seguenti:

    ```Javascript
    // MongoDB setup.
    // Set up some configuration.
    var serverPort = process.env.PORT || 8080;
    var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
    // Connect to MongoDB.
    global.db = mongoose.connect(serverURI);
    var Schema = mongoose.Schema;
    log.info('MongoDB Schema loaded');
    ```

Questo codice si connette al server MongoDB. Restituisce inoltre un oggetto schema.

#### <a name="using-the-schema-create-your-model-in-the-code"></a>Usando lo schema, creare il modello nel codice
Sotto il codice precedente aggiungere il codice seguente:

```Javascript
// Create a basic schema to store your tasks and users.
var TaskSchema = new Schema({
owner: String,
task: String,
completed: Boolean,
date: Date
});
// Use the schema to register a model.
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```

Come si può vedere dal codice, per prima cosa viene creato lo schema. Si crea quindi un oggetto modello. Usare l'oggetto modello per archiviare i dati in tutto il codice quando si definiscono le **route**.

## <a name="13-add-your-routes-for-your-task-rest-api-server"></a>13: Aggiungere le route per il server API REST delle attività
Ora che è disponibile un modello di database con cui lavorare, aggiungere le route che verranno usate per il server API REST.

### <a name="about-routes-in-restify"></a>Informazioni sulle route in Restify
Le route in Restify funzionano esattamente come avviene quando si usa lo stack di Express. Definire le route usando l'URI che si prevede verrà chiamato dalle applicazioni client. Le route si definiscono di solito in un file separato. In questa esercitazione, le route sono inserite in Server.js. È consigliabile fattorizzarle nel relativo file per usarle in fase di produzione.

Un modello tipico per una route Restify è:

```Javascript
function createObject(req, res, next) {
// Do work on object.
_object.name = req.params.object; // Passed value is in req.params under object.
///...
return next(); // Keep the server going.
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```


Questo è il modello più semplice. Restify ed Express offrono funzionalità molto più avanzate come la definizione di tipi di applicazione e il routing complesso tra endpoint diversi.

#### <a name="add-default-routes-to-your-server"></a>Aggiungere le route predefinite al server
Aggiungere le route CRUD di base: **creazione**, **recupero**, **aggiornamento** ed **eliminazione**.

1.  Al prompt dei comandi passare alla directory **azuread**:

    `cd azuread`

2.  In un editor aprire Server.js. Aggiungere le informazioni seguenti sotto le voci di database create in precedenza:

    ```Javascript
    /**
    *
    * APIs for your REST task server
    */
    // Create a task.
    function createTask(req, res, next) {
    // Resitify currently has a bug that doesn't allow you to set default headers.
    // These headers comply with CORS, and allow you to use MongoDB Server as your response to any origin.
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");
    // Create a new task model, fill it, and save it to MongoDB.
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
    // Delete a task by name.
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
    // Delete all tasks.
    function removeAll(req, res, next) {
    Task.remove();
    res.send(204);
    return next();
    }
    // Get a specific task based on name.
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
    /// Returns the list of TODOs that were loaded.
    function listTasks(req, res, next) {
    // Resitify currently has a bug that doesn't allow you to set default headers.
    // These headers comply with CORS, and allow us to use MongoDB Server as our response to any origin.
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
    log.warn(err, "There are no tasks in the database. Add one!");
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

### <a name="add-error-handling-for-the-routes"></a>Aggiungere la gestione di errori per le route
Aggiungere la gestione di errori per poter comunicare al client il problema che si è verificato.

Aggiungere il codice seguente sotto il codice già scritto:

```Javascript
///--- Errors for communicating something more information back to the client.
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


## <a name="14-create-your-server"></a>14: Creare il server
L'ultima operazione da eseguire consiste nell'aggiungere l'istanza del server. L'istanza del server gestisce le chiamate.

Restify ed Express hanno un elevato livello di personalizzazione che è possibile usare con un server API REST. Per questa esercitazione si userà la configurazione più semplice.

```Javascript
/**
* Your server
*/
var server = restify.createServer({
name: "Microsoft Azure Active Directory TODO Server",
version: "2.0.1"
});
// Ensure that you don't drop data on uploads.
server.pre(restify.pre.pause());
// Clean up imprecise paths like //todo//////1//.
server.pre(restify.pre.sanitizePath());
// Handle annoying user agents (curl).
server.pre(restify.pre.userAgentConnection());
// Set a per-request Bunyan logger (with requestid filled in).
server.use(restify.requestLogger());
// Allow 5 requests/second by IP address, and burst to 10.
server.use(restify.throttle({
burst: 10,
rate: 5,
ip: true,
}));
// Use common commands, such as:
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
mapParams: true
}));
```
## <a name="15-add-the-routes-without-authentication-for-now"></a>15: Aggiungere le route (per ora senza autenticazione)
```Javascript
/// Use CRUD to add the real handlers.
/**
/*
/* Each of these handlers is protected by your Open ID Connect Bearer strategy. Invoke 'oidc-bearer'
/* in the pasport.authenticate() method. Because REST is stateless, set 'session: false'. You 
/* don't need to maintain session state. You can experiment with removing API protection.
/* To do this, remove the passport.authenticate() method:
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
## <a name="16-run-the-server"></a>16: Eseguire il server
È consigliabile eseguire il test del server prima di aggiungere l'autenticazione.

Il modo più semplice per farlo consiste nell'usare Curl al prompt dei comandi. È necessaria una semplice utilità che consente di analizzare l'output come JSON. 

1.  Installare lo strumento JSON usato negli esempi seguenti:

    `$npm install -g jsontool`

    Questo comando installa lo strumento JSON a livello globale.

2.  Assicurarsi che l'istanza di MongoDB sia in esecuzione.

    `$sudo mongod`

3.  Passare alla directory **azuread** e quindi eseguire Curl:

    `$ cd azuread`
    `$ node server.js`

    `$ curl -isS http://127.0.0.1:8080 | json`

    ```Shell
    HTTP/1.1 2.0OK
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

4.  Per aggiungere un'attività:

    `$ curl -isS -X POST http://127.0.0.1:8888/tasks/brandon/Hello`

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

5.  Elenco delle attività Brandon:

    `$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Se tutti questi comandi vengono eseguiti senza errori, è possibile aggiungere OAuth al server dell'API REST.

*Ora si dispone di un server API REST con MongoDB*.

## <a name="17-add-authentication-to-your-rest-api-server"></a>17: Aggiungere l'autenticazione al server API REST
Ora che l'API REST è in esecuzione, è possibile configurarla per usarla in Azure AD.

Al prompt dei comandi passare alla directory **azuread**:

`cd azuread`

### <a name="use-the-oidcbearerstrategy-thats-included-with-passport-azure-ad"></a>Usare l'oggetto oidcbearerstrategy incluso in passport-azure-ad
Nei passaggi precedenti è stato creato un tipico server REST TODO senza alcun tipo di autenticazione. Aggiungere l'autenticazione.

In primo luogo, indicare che si desidera usare Passport. Inserire il codice subito dopo la configurazione del server precedente:

```Javascript
// Start using Passport.js.

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support
```

> [!TIP]
> Durante la scrittura delle API è sempre consigliabile collegare i dati a un elemento univoco del token di cui l'utente non può eseguire lo spoofing. Quando archivia gli elementi TODO, il server esegue questa operazione in base all'ID sottoscrizione dell'utente nel token (chiamato mediante token.sub). Inserire il token.sub nel campo "owner". In questo modo, solo l'utente può accedere ai propri elementi TODO. Nessun altro può accedere agli elementi TODO immessi. Nell'API di "owner" non è presente alcuna esposizione. Un utente esterno può richiedere gli elementi TODO di altri utenti, anche se sono autenticati.
> 
> 

Usare quindi la strategia Open ID Connect Bearer fornita con `passport-azure-ad`. Inserire il codice seguente dopo quanto inserito in precedenza:

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users.
/*
/* Because of the Passport pattern, you need to manage users and info tokens
/* with a FindorCreate() method. The method must be provided by the implementor.
/* In the following code, you autoregister any user and implement a FindById().
/* It's a good idea to do something more advanced.
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
log.info(token, 'was the token retrieved');
findById(token.sub, function(err, user) {
if (err) {
return done(err);
}
if (!user) {
// "Auto-registration"
log.info('User was added automatically, because they were new. Their sub is: ', token.sub);
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

Passport usa un modello simile per tutte le strategie (Twitter, Facebook e così via). Tutti i writer di strategie rispettano questo modello. Passare alla strategia un elemento `function()` che usa un token e `done` come parametri. La strategia viene restituita dopo l'esecuzione di tutte le operazioni. Archiviare l'utente e mettere da parte il token per non doverlo richiedere nuovamente.

> [!IMPORTANT]
> Il codice precedente accetta qualsiasi utente che può eseguire l'autenticazione al server. Questa operazione è nota come registrazione automatica. Nei server di produzione è preferibile non consentire l'accesso a chiunque senza prima prevedere un processo di registrazione a scelta. Questo è il modello in genere adottato per le app consumer. L'app potrebbe consentire di eseguire la registrazione con Facebook, ma poi richiede di inserire informazioni aggiuntive. Se per questa esercitazione non è stato usato un programma della riga di comando, è possibile estrarre il messaggio di posta elettronica dall'oggetto token restituito. Quindi, è possibile chiedere all'utente di inserire informazioni aggiuntive. Trattandosi di un server di test, è possibile aggiungere l'utente direttamente al database in-memory.
> 
> 

### <a name="protect-endpoints"></a>Proteggere gli endpoint
Proteggere gli endpoint, specificando la chiamata a **passport.authenticate()** con il protocollo preferito.

È possibile modificare la route nel codice del server per un'opzione più avanzata:

```Javascript
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## <a name="18-run-your-server-application-again"></a>18: Eseguire nuovamente l'applicazione server
Per verificare se la protezione OAuth 2.0 per gli endpoint è attiva, usare nuovamente Curl. Eseguire questa operazione prima di usare uno qualsiasi degli SDK client in questo endpoint. Le intestazioni restituite dovrebbero indicare se l'autenticazione funziona correttamente.

1.  Assicurarsi che l'istanza di MongoDB sia in esecuzione:

    `$sudo mongod`

2.  Passare alla directory **azuread**, quindi usare Curl:

    `$ cd azuread`

    `$ node server.js`

3.  Provare un'operazione POST di base:

    `$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

    ```Shell
    HTTP/1.1 401 Unauthorized
    Connection: close
    WWW-Authenticate: Bearer realm="Users"
    Date: Tue, 14 Jul 2015 05:45:03 GMT
    Transfer-Encoding: chunked
    ```

Una risposta 401 indica che il livello Passport sta tentando il reindirizzamento all'endpoint di autorizzazione, ovvero il comportamento voluto.

*Ora è disponibile un servizio API REST che usa OAuth 2.0*.

Sono state eseguite tutte le operazioni possibili con questo server senza usare un client compatibile con OAuth 2.0. A tale scopo, è necessario esaminare un'esercitazione aggiuntiva.

## <a name="next-steps"></a>Passaggi successivi
Come riferimento viene fornito l'esempio completato, senza i valori di configurazione, [come file zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/complete.zip). È anche possibile clonarlo da GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

Ora è possibile passare ad argomenti più avanzati. Si potrebbe volere [proteggere un'app Web Node.js usando l'endpoint v2.0](active-directory-v2-devquickstarts-node-web.md).

Altre risorse:

* [Guida per sviluppatori Azure AD v2.0](active-directory-appmodel-v2-overview.md)
* [StackOverflow: tag "azure-active-directory"](http://stackoverflow.com/questions/tagged/azure-active-directory)

### <a name="get-security-updates-for-our-products"></a>Ottenere aggiornamenti della sicurezza per i prodotti
È consigliabile eseguire l'iscrizione per ricevere una notifica quando si verificano problemi di protezione. Nella pagina [Notifiche sulla sicurezza Microsoft](https://technet.microsoft.com/security/dd252948) effettuare la sottoscrizione agli advisory sulla sicurezza.

