---
title: 'Azure AD B2C: proteggere un&quot;API Web usando Node.js | Microsoft Docs'
description: Come compilare un&quot;API Web Node.js che accetta token da un tenant B2C
services: active-directory-b2c
documentationcenter: 
author: xerners
manager: mbaldwin
editor: 
ms.assetid: fc2b9af8-fbda-44e0-962a-8b963449106a
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: hero-article
ms.date: 01/07/2017
ms.author: xerners
translationtype: Human Translation
ms.sourcegitcommit: 3b5d9162e2d39e2b0f011383a478545644c57861
ms.openlocfilehash: c9742c7f505417577857889ef307083afb9bab18
ms.lasthandoff: 02/14/2017


---
# <a name="azure-ad-b2c-secure-a-web-api-by-using-nodejs"></a>Azure AD B2C: proteggere un'API Web usando Node.js
<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Azure Active Directory (Azure AD) B2C permette di proteggere un'API Web usando i token di accesso OAuth 2.0. I token di accesso consentono alle app client che usano Azure AD B2C di eseguire l'autenticazione all'API. Questo articolo illustra come creare un'API di elenco attività che consenta agli utenti di aggiungere ed elencare attività. L'API Web è protetta con Azure AD B2C e consente soltanto agli utenti autenticati di gestire il proprio elenco attività.

> [!NOTE]
> Questo esempio è stato scritto per l' [applicazione di esempio iOS B2C](active-directory-b2c-devquickstarts-ios.md). Eseguire prima questa procedura dettagliata e quindi proseguire con l'esempio.
>
>

**Passport** è il middleware di autenticazione per Node.js. Estremamente flessibile e modulare, Passport può essere installato in modo non invadente in qualsiasi applicazione Web basata su Express o Restify. Un set completo di strategie supporta l'autenticazione tramite nome utente e password, Facebook, Twitter e altro ancora. È stata sviluppata una strategia per Azure Active Directory (Azure AD). Dopo l'installazione di questo modulo, aggiungere il plug-in `passport-azure-ad` di Azure AD.

Per questo esempio è necessario:

1. Registrare un'applicazione con Azure AD.
2. Configurare l'applicazione per l'uso del plug-in `azure-ad-passport` di Passport.
3. Configurare un'applicazione client per chiamare l'API Web To Do List.

## <a name="get-an-azure-ad-b2c-directory"></a>Ottenere una directory di Azure AD B2C
Prima di poter usare Azure AD B2C, è necessario creare una directory, o tenant.  Una directory è un contenitore per utenti, app, gruppi e altro ancora.  Se non ne è già disponibile una, [creare una directory B2C](active-directory-b2c-get-started.md) prima di continuare.

## <a name="create-an-application"></a>Creare un'applicazione
A questo punto è necessario creare un'app nella directory B2C, che fornisce ad Azure AD alcune informazioni necessarie per comunicare in modo sicuro con l'app. In questo caso, sia l'app client che l'API Web sono rappresentate da un unico **ID applicazione** perché includono una sola app per la logica. Per creare un'app, [seguire questa procedura](active-directory-b2c-app-registration.md). Assicurarsi di:

* Includere un' **app Web/API Web** nell'applicazione
* Immettere `http://localhost/TodoListService` come **URL di risposta**. Si tratta dell'URL predefinito per questo esempio di codice.
* Creare un **segreto applicazione** per l'applicazione e prenderne nota. Questi dati saranno necessari in un secondo momento. Si noti che prima di usare questo valore è necessario [inserire un carattere di escape XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) .
* Copiare l' **ID applicazione** assegnato all'app. Questi dati saranno necessari in un secondo momento.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Creare i criteri
In Azure AD B2C ogni esperienza utente è definita da [criteri](active-directory-b2c-reference-policies.md)specifici. Questa app contiene due esperienze di identità: iscrizione e accesso. È necessario creare i criteri per ogni tipo, come descritto nell' [articolo di riferimento per i criteri](active-directory-b2c-reference-policies.md#create-a-sign-up-policy).  Durante la creazione dei tre criteri assicurarsi di:

* Scegliere **Nome visualizzato** e altri attributi nei criteri di iscrizione.
* Scegliere le attestazioni dell'applicazione **Nome visualizzato** e **ID oggetto** in tutti i criteri.  È consentito scegliere anche altre attestazioni.
* Copiare il **Nome** di ciascun criterio dopo averlo creato. Dovrebbero mostrare il prefisso `b2c_1_`.  I nomi dei criteri saranno necessari in un secondo momento.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Dopo aver creato i tre criteri, è possibile passare alla compilazione dell'app.

Per informazioni sul funzionamento dei criteri in Azure AD B2C, iniziare dall' [esercitazione introduttiva per la compilazione di un'app Web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="download-the-code"></a>Scaricare il codice
Il codice per questa esercitazione è [disponibile in GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS). Per compilare l'esempio passo dopo passo, è possibile [scaricare un progetto bozza come file ZIP](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/skeleton.zip). È anche possibile clonare la struttura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS.git
```

L'app completata è anche [disponibile come file ZIP](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/complete.zip) o nel ramo `complete` dello stesso repository.

## <a name="download-nodejs-for-your-platform"></a>Scaricare node.js per la piattaforma corrente
Per usare correttamente questo esempio è necessaria un'installazione funzionante di Node.js.

Installare Node.js da [nodejs.org](http://nodejs.org).

## <a name="install-mongodb-for-your-platform"></a>Installare MongoDB per la piattaforma corrente
Per usare correttamente questo esempio è necessaria un'installazione funzionante di MongoDB. MongoDB viene usato per rendere l'API REST persistente nelle istanze del server.

Installare MongoDB da [mongodb.org](http://www.mongodb.org).

> [!NOTE]
> In questa procedura dettagliata si presume che si usino gli endpoint server e di installazione predefiniti per MongoDB, al momento della stesura di questo articolo `mongodb://localhost`.
>
>

## <a name="install-the-restify-modules-in-your-web-api"></a>Installare i moduli Restify nell'API Web
Restify verrà usato per compilare l'API REST. Restify è un framework applicazioni di Node.js minimo e flessibile derivato da Express, che include una gamma completa di funzionalità per la compilazione di API REST basate su Connect.

### <a name="install-restify"></a>Installare Restify
Dalla riga di comando passare alla directory `azuread`. Se la directory `azuread` non esiste, crearla.

`cd azuread` oppure `mkdir azuread;`

Immettere il comando seguente:

`npm install restify`

Questo comando installa Restify.

#### <a name="did-you-get-an-error"></a>È stato visualizzato un errore?
In alcuni sistemi operativi, quando si usa `npm` potrebbero essere visualizzati l'errore `Error: EPERM, chmod '/usr/local/bin/..'` e una richiesta di eseguire l'account come amministratore. In tal caso, usare il comando `sudo` per eseguire `npm` a un livello di privilegi più elevato.

#### <a name="did-you-get-a-dtrace-error"></a>È stato visualizzato un errore di DTrace?
Durante l'installazione di Restify potrebbe essere visualizzato un testo simile al seguente:

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

L'output del comando avrà un aspetto simile al seguente:

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

## <a name="install-passport-in-your-web-api"></a>Installare Passport nell'API Web
Dalla riga di comando passare alla directory `azuread`, se non è già la posizione corrente.

Installare Passport usando il comando seguente:

`npm install passport`

L'output del comando sarà simile al seguente:

    passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3

## <a name="add-passport-azuread-to-your-web-api"></a>Aggiungere passport-azuread all'API Web
A questo punto aggiungere la strategia di OAuth usando `passport-azuread`, una suite di strategie che connettono Azure AD a Passport. Usare questa strategia per i token di connessione nell'esempio di API REST.

> [!NOTE]
> Anche se OAuth2 fornisce un framework in cui è possibile rilasciare qualsiasi tipo di token noto, solo determinati tipi di token sono usati su larga scala. I token per la protezione degli endpoint sono token di connessione e sono i tipi di token maggiormente rilasciati in OAuth2. Molte implementazioni presumono che i token di connessione sono l'unico tipo di token rilasciato.
>
>

Dalla riga di comando passare alla directory `azuread`, se non è già la posizione corrente.

Immettere il comando seguente per installare il modulo Passport `passport-azure-ad` :

`npm install passport-azure-ad`

L'output del comando sarà simile al seguente:

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

## <a name="add-mongodb-modules-to-your-web-api"></a>Aggiungere i moduli MongoDB all'API Web
Questo esempio usa MongoDB come archivio dati. A tale scopo installare Mongoose, un plug-in diffuso per la gestione di modelli e schemi.

* `npm install mongoose`

## <a name="install-additional-modules"></a>Installare moduli aggiuntivi
A questo punto, installare gli altri moduli necessari.

Dalla riga di comando passare alla directory `azuread`, se non è già la posizione corrente:

`cd azuread`

Installare i moduli nella directory `node_modules` :

* `npm install assert-plus`
* `npm install ejs`
* `npm install ejs-locals`
* `npm install express`
* `npm install bunyan`

## <a name="create-a-serverjs-file-with-your-dependencies"></a>Creare un file server.js con le dipendenze
Il file `server.js` offre la maggior parte della funzionalità per il server API Web.

Dalla riga di comando passare alla directory `azuread`, se non è già la posizione corrente:

`cd azuread`

Creare un file `server.js` in un editor. Aggiungere le informazioni seguenti:

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

Salvare il file. Servirà ancora successivamente.

## <a name="create-a-configjs-file-to-store-your-azure-ad-settings"></a>Creare un file config.js per archiviare le impostazioni di Azure AD
Questo file di codice passa i parametri di configurazione dal portale di Azure AD al file `Passport.js` . Questi valori di configurazione sono stati creati quando si è aggiunta l'API Web al portale nella prima parte della procedura dettagliata. Dopo aver copiato il codice, verrà spiegato che cosa inserire nei valori di questi parametri.

Dalla riga di comando passare alla directory `azuread`, se non è già la posizione corrente:

`cd azuread`

Creare un file `config.js` in un editor. Aggiungere le informazioni seguenti:

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
clientID: <your client ID for this Web API you created in the portal>
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
audience: '<your audience URI>', // the Client ID of the application that is calling your API, usually a web API or native client
identityMetadata: 'https://login.microsoftonline.com/<tenant name>/.well-known/openid-configuration', // Make sure you add the B2C tenant name in the <tenant name> area
tenantName:'<tenant name>',
policyName:'b2c_1_<sign in policy name>' // This is the policy you'll want to validate against in B2C. Usually this is your Sign-in policy (as users sign in to this API)
passReqToCallback: false // This is a node.js construct that lets you pass the req all the way back to any upstream caller. We turn this off as there is no upstream caller.
};

```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### <a name="required-values"></a>Valori richiesti
`clientID`: ID client dell'applicazione API Web.

`IdentityMetadata`: qui `passport-azure-ad` cerca i dati di configurazione per il provider di identità e le chiavi di convalida dei token JSON Web.

`audience`: URI (Uniform Resource Identifier) dal portale che identifica l'applicazione chiamante.

`tenantName`: nome del tenant, ad esempio **contoso.onmicrosoft.com**.

`policyName`: criteri da usare per convalidare i token in ingresso nel server. Usare gli stessi criteri usati nell'applicazione client per l'accesso.

> [!NOTE]
> Per questo esempio, usare gli stessi i criteri per entrambe le configurazioni client e server. Se è già stata completata una procedura dettagliata in cui sono stati creati questi criteri, non è necessario crearli di nuovo. Dal momento che la procedura dettagliata è stata completata, non è necessario impostare nuovi criteri per procedure dettagliate relative ai client nel sito.
>
>

## <a name="add-configuration-to-your-serverjs-file"></a>Aggiungere la configurazione al file server.js
Per leggere i valori dal file `config.js` creato, aggiungere il file `.config` come risorsa necessaria nell'applicazione e quindi impostare le variabili globali su quelle del documento `config.js`.

Dalla riga di comando passare alla directory `azuread`, se non è già la posizione corrente:

`cd azuread`

Aprire il file `server.js` in un editor. Aggiungere le informazioni seguenti:

```Javascript
var config = require('./config');
```
Aggiungere una nuova sezione a `server.js` con il codice seguente:

```Javascript
// We pass these options in to the ODICBearerStrategy.

var options = {
    // The URL of the metadata document for your app. We put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    tenantName: config.creds.tenantName,
    policyName: config.creds.policyName,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience,
    passReqToCallback: config.creds.passReqToCallback

};
```

Aggiungere quindi alcuni segnaposto per gli utenti ricevuti dalle applicazioni chiamanti.

```Javascript
// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;
```

Creare anche il logger.

```Javascript
// Our logger
var log = bunyan.createLogger({
    name: 'Microsoft Azure Active Directory Sample'
});
```

## <a name="add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>Aggiungere le informazioni su schemi e modelli MongoDB usando Moongoose
Le attività preliminari risulteranno utili quando si riuniranno questi tre file in un servizio API REST.

Per questa procedura dettagliata, usare MongoDB per archiviare le attività, come indicato in precedenza.

Nel file `config.js` , il database è stato denominato **tasklist**. Questo nome è stato anche inserito alla fine dell'URL di connessione `mongoose_auth_local` . Non è necessario creare in anticipo il database in MongoDB. Il database verrà creato automaticamente alla prima esecuzione dell'applicazione server.

Dopo aver indicato al server quale database MongoDB usare, è necessario scrivere del codice aggiuntivo per creare il modello e lo schema per le attività del server.

### <a name="expand-the-model"></a>Espandere il modello
Questo modello di schema è semplice ed è possibile espanderlo in base alle esigenze.

`owner`: utente assegnato all'attività. Questo oggetto è di tipo **stringa**.  

`Text`: l'attività stessa. Questo oggetto è di tipo **stringa**.

`date`: data di scadenza dell'attività. Questo oggetto è di tipo **datetime**.

`completed`: indica se l'attività è stata completata. Questo oggetto è di tipo **booleano**.

### <a name="create-the-schema-in-the-code"></a>Creare lo schema nel codice
Dalla riga di comando passare alla directory `azuread`, se non è già la posizione corrente:

`cd azuread`

Aprire il file `server.js` in un editor. Aggiungere le informazioni seguenti sotto la voce di configurazione:

```Javascript
// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 3000; // Note we are hosting our API on port 3000
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;

// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');

// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
    owner: String,
    Text: String,
    completed: Boolean,
    date: Date
});

// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
Creare prima lo schema e quindi creare un oggetto modello da usare per l'archiviazione dei dati nel codice quando si definiscono le **route**.

## <a name="add-routes-for-your-rest-api-task-server"></a>Aggiungere le route per il server delle attività dell'API REST
Ora che è disponibile un modello di database, aggiungere le route da usare per il server API REST.

### <a name="about-routes-in-restify"></a>Informazioni sulle route in Restify
Il funzionamento delle route in Restify è identico al funzionamento nello stack di Express. Definire le route usando l'URI che si prevede verrà chiamato dalle applicazioni client.

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

Restify ed Express offrono funzionalità molto più avanzate, ad esempio la definizione di tipi di applicazione e l'esecuzione di un routing complesso tra endpoint diversi. Ai fini di questa esercitazione verranno usate route semplici.

#### <a name="add-default-routes-to-your-server"></a>Aggiungere le route predefinite al server
Verranno ora aggiunte le route CRUD di base **create** e **list** per l'API REST. Sono disponibili altre route nel ramo `complete` dell'esempio.

Dalla riga di comando passare alla directory `azuread`, se non è già la posizione corrente:

`cd azuread`

Aprire il file `server.js` in un editor. Aggiungere le informazioni seguenti sotto le voci di database create in precedenza:

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

    if (!req.params.Text) {
        req.log.warn({
            params: req.params
        }, 'createTodo: missing task');
        next(new MissingTaskError());
        return;
    }

    _task.owner = owner;
    _task.Text = req.params.Text;
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
```

```Javascript
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


#### <a name="add-error-handling-for-the-routes"></a>Aggiungere la gestione di errori per le route
Aggiungere la gestione di errori per poter comunicare al client eventuali problemi riscontrati in un modo comprensibile per il client stesso.

Aggiungere il codice seguente:

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


## <a name="create-your-server"></a>Creare il server
Dopo aver definito il database e inserito le route, resta solo da aggiungere l'istanza del server che gestirà le chiamate.

Restify ed Express offrono un livello elevato di personalizzazione per un server API REST, ma in questo caso si userà la configurazione più semplice.

```Javascript

**
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
})); // Allows for JSON mapping to REST
server.use(restify.authorizationParser()); // Looks for authorization headers

// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support


```
## <a name="add-the-routes-to-the-server-without-authentication"></a>Aggiungere le route al server (senza autenticazione)
```Javascript
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.head('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.post('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.post('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.del('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeAll, function respond(req, res, next) {
    res.send(204);
    next();
});


// Register a default '/' handler

server.get('/', function root(req, res, next) {
    var routes = [
        'GET     /',
        'POST    /api/tasks/:owner/:task',
        'POST    /api/tasks (for JSON body)',
        'GET     /api/tasks',
        'PUT     /api/tasks/:owner',
        'GET     /api/tasks/:owner',
        'DELETE  /api/tasks/:owner/:task'
    ];
    res.send(200, routes);
    next();
});
```

```Javascript

server.listen(serverPort, function() {

    var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
    consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
    consoleMessage += '\n %s server is listening at %s';
    consoleMessage += '\n Open your browser to %s/api/tasks\n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
    consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';

    //log.info(consoleMessage, server.name, server.url, server.url, server.url);

});

```

## <a name="add-authentication-to-your-rest-api-server"></a>Aggiungere l'autenticazione al server API REST
A questo punto, il server API REST in esecuzione può essere usato in Azure AD.

Dalla riga di comando passare alla directory `azuread`, se non è già la posizione corrente:

`cd azuread`

### <a name="use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>Usare l'oggetto OIDCBearerStrategy incluso in passport-azure-ad
> [!TIP]
> Durante la scrittura delle API è sempre necessario collegare i dati a un elemento univoco dal token in modo che l'utente non possa eseguire lo spoofing. Quando archivia gli elementi ToDo, il server si basa sull' **OID** dell'utente nel token, chiamato tramite token.oid, da inserire nel campo "owner". Questo valore assicura che solo tale utente possa accedere i propri elementi ToDo. Il valore di "owner" non viene esposto nell'API, così che un utente esterno può richiedere elementi TODO di altri anche se sono autenticati.
>
>

Usare quindi la strategia di connessione fornita con `passport-azure-ad`.

```Javascript
var findById = function(id, fn) {
    for (var i = 0, len = users.length; i < len; i++) {
        var user = users[i];
        if (user.oid === id) {
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
                log.info('User was added automatically as they were new. Their sub is: ', token.oid);
                users.push(token);
                owner = token.oid;
                return done(null, token);
            }
            owner = token.sub;
            return done(null, user, token);
        });
    }
);

passport.use(oidcStrategy);
```

Passport usa lo stesso modello per tutte le strategie. Viene passato un oggetto `function()` con `token` e `done` come parametri. La strategia risponde dopo avere eseguito tutte le relative operazioni. Archiviare quindi l'utente e salvare il token per non doverlo richiedere nuovamente.

> [!IMPORTANT]
> Il codice precedente accetta qualsiasi utente che esegue l'autenticazione al server. Questa operazione è nota come registrazione automatica. Nei server di produzione non consentire l'accesso degli utenti all'API senza prima un processo di registrazione. Questo processo è il modello in genere adottato per le app consumer che consentono di eseguire la registrazione usando Facebook, ma che chiedono di immettere informazioni aggiuntive. Se non si trattasse di un programma della riga di comando, sarebbe possibile estrarre il messaggio di posta elettronica dall'oggetto token restituito e chiedere agli utenti di immettere informazioni aggiuntive. Trattandosi di un esempio, le informazioni verranno aggiunte a un database in memoria.
>
>

## <a name="run-your-server-application-to-verify-that-it-rejects-you"></a>Eseguire l'applicazione server per verificare che rifiuti l'utente
Per verificare se la protezione OAuth2 per gli endpoint è attiva, usare `curl` . Le intestazioni restituite dovrebbero essere sufficienti a capire se si sta procedendo nel modo corretto.

Assicurarsi che l'istanza di MongoDB sia in esecuzione.

    $sudo mongodb

Passare alla directory ed eseguire il server:

    $ cd azuread
    $ node server.js

In una nuova finestra del terminale eseguire `curl`

Provare un'operazione POST di base:

`$ curl -isS -X POST http://127.0.0.1:3000/api/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

L'errore 401 è la risposta prevista. Indica che il livello Passport sta provando a eseguire il reindirizzamento all'endpoint di autorizzazione.

## <a name="you-now-have-a-rest-api-service-that-uses-oauth2"></a>Ora è disponibile un servizio API REST che usa OAuth2
È stata implementata un'API REST usando Restify e OAuth. Il codice ora disponibile consente di continuare a sviluppare il servizio sulla base di questo esempio. Sono state eseguite tutte le operazioni possibili con questo server senza usare un client compatibile con OAuth2. Per il passaggio successivo usare una procedura dettagliata aggiuntiva come [Azure AD B2C: chiamare un'API Web da un'applicazione iOS con una libreria di terze parti](active-directory-b2c-devquickstarts-ios.md) .

## <a name="next-steps"></a>Passaggi successivi
Ora è possibile passare ad argomenti più avanzati, ad esempio:

[Connettersi a un'API Web usando iOS con B2C &gt;&gt;](active-directory-b2c-devquickstarts-ios.md)

