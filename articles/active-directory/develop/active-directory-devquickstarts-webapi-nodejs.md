---
title: Introduzione a Node.js per Azure AD | Microsoft Docs
description: Come compilare un&quot;API Web REST per Node.js che si integra con Azure AD per l&quot;autenticazione.
services: active-directory
documentationcenter: nodejs
author: xerners
manager: mbaldwin
editor: 
ms.assetid: 7654ab4c-4489-4ea5-aba9-d7cdc256e42a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/07/2017
ms.author: brandwe
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 707bb2b7cd9796e0e05234aa08002bbc1820f871
ms.lasthandoff: 03/18/2017


---
# <a name="get-started-with-web-apis-for-nodejs"></a>Introduzione alle API Web per Node.js
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

*Passport* è il middleware di autenticazione per Node.js. Passport è flessibile e modulare e può essere rilasciato in modo non invadente in qualsiasi applicazione Web basata su Express o Restify. Una gamma completa di strategie supporta l'autenticazione con nome utente e password, Facebook, Twitter e altro ancora. È stata sviluppata una strategia per Microsoft Azure Active Directory (Azure AD). Dopo l'installazione di questo modulo, verrà aggiunto il plug-in `passport-azure-ad` di Microsoft Azure Active Directory.

A questo scopo, è necessario:

1. Registrare un'applicazione con Azure AD.
2. Impostare l'app per l'uso del plug-in `passport-azure-ad` di Passport.
3. Configurare un'applicazione client per chiamare l'API Web To Do List.

Il codice per questa esercitazione è salvato [su GitHub](https://github.com/Azure-Samples/active-directory-node-webapi).

> [!NOTE]
> Questo articolo non descrive come implementare le esperienze di accesso, iscrizione o gestione del profilo con Azure AD B2C, ma illustra la chiamata delle API Web dopo che l'utente ha già effettuato l'autenticazione.  È consigliabile iniziare con il documento sull'[Integrazione con Azure Active Directory](active-directory-how-to-integrate.md), per acquisire le nozioni di base su Azure Active Directory.
>
>

Tutto il codice sorgente per questo esempio in esecuzione è stato rilasciato in GitHub con una licenza MIT, è quindi possibile clonarlo o, ancora meglio, biforcarlo e inviare commenti e suggerimenti e richieste pull.

## <a name="about-nodejs-modules"></a>Informazioni sui moduli Node.js
In questa procedura dettagliata vengono usati i moduli Node.js. I moduli sono pacchetti JavaScript caricabili che forniscono funzionalità specifiche per l'applicazione. In genere, è possibile installare i moduli usando Node.js e lo strumento da riga di comando NPM nella directory di installazione di NPM. Tuttavia, alcuni moduli, ad esempio il modulo HTTP, sono inclusi nel pacchetto Node.js di base.

I moduli installati vengono salvati nella directory **node_modules** nella radice della directory di installazione di Node.js. Ogni modulo nella directory **node_modules** mantiene la relativa directory **node_modules**, che contiene i moduli da cui dipende. Ogni modulo necessario ha una directory **node_modules**. Questa struttura di directory ricorsiva rappresenta la catena di dipendenze.

Questa struttura della catena di dipendenze comporta un footprint maggiore delle applicazioni, ma garantisce che vengano soddisfatte tutte le dipendenze e che la versione dei moduli usata durante lo sviluppo venga usata anche in fase di produzione. In questo modo il comportamento delle app di produzione è più prevedibile e si evitano problemi controllo delle versioni per gli utenti.

## <a name="step-1-register-an-azure-ad-tenant"></a>Passaggio 1: Registrare un tenant di Azure AD
Per usare questo esempio è necessario un tenant di Azure Active Directory. Se non si è certi di cosa sia un tenant o di come ottenerne uno, vedere [Come ottenere un tenant di Azure AD](active-directory-howto-tenant.md).

## <a name="step-2-create-an-application"></a>Passaggio 2:Creare un'applicazione
A questo punto, si crea un'app nella directory che fornisce ad Azure AD le informazioni necessarie per comunicare in modo sicuro con l'app.  In questo caso, sia l'app client che l'API Web sono rappresentate da un unico **ID applicazione** perché includono una sola app per la logica.  Per creare un'app, [seguire questa procedura](active-directory-how-applications-are-added.md). Se si compila un'app line-of-business, [queste istruzioni aggiuntive](../active-directory-applications-guiding-developers-for-lob-applications.md) possono risultare utili.

Per creare un'applicazione:

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Selezionare l'account dal menu in alto. Nell'elenco **Directory** scegliere il tenant di Active Directory in cui si vuole registrare l'applicazione.

3. Nel menu a sinistra selezionare **Altri servizi** e quindi scegliere **Azure Active Directory**.

4. Selezionare **Registrazioni per l'app**, quindi scegliere **Aggiungi**.

5. Seguire le istruzioni per creare una nuova **applicazione Web e/o API Web**.

      * Il **nome** dell'applicazione descrive l'applicazione agli utenti.

      * L' **URL accesso** è l'URL di base dell'app.  L'URL predefinito del codice di esempio è `https://localhost:8080`.

6. Dopo la registrazione, Azure AD assegna all'app un ID applicazione univoco. Poiché questo valore sarà necessario nelle sezioni successive, copiarlo dalla pagina dell'applicazione.

7. Dalla pagina **Impostazioni** -> **Proprietà** dell'applicazione aggiornare l'URI dell'ID app. L' **URI ID app** è un identificatore univoco dell'applicazione. Per convenzione si usa `https://<tenant-domain>/<app-name>`, ad esempio: `https://contoso.onmicrosoft.com/my-first-aad-app`.

8. Creare una **chiave** per l'applicazione dalla pagina **Impostazioni** e copiarla, perché verrà richiesta a breve.

## <a name="step-3-download-nodejs-for-your-platform"></a>Passaggio 3: Scaricare Node.js per la piattaforma corrente
Per usare correttamente questo esempio, è necessario disporre di un'installazione funzionante di Node.js.

Installare Node.js da [http://nodejs.org](http://nodejs.org).

## <a name="step-4-install-mongodb-on-your-platform"></a>Passaggio 4: Installare MongoDB nella piattaforma
Per usare correttamente questo esempio, è necessario disporre di un'installazione funzionante di MongoDB. MongoDB viene usato per rendere l'API REST persistente nelle istanze del server.

Installare MongoDB da [http://mongodb.org](http://www.mongodb.org).

> [!NOTE]
> Questa procedura dettagliata presuppone l'uso degli endpoint server e di installazione predefiniti per MongoDB che, al momento della stesura di questo articolo, corrispondono a mongodb://localhost.
>
>

## <a name="step-5-install-the-restify-modules-in-your-web-api"></a>Passaggio 5: Installare i moduli Restify nell'API Web
Per compilare l'API REST si usa Restify. Restify è un framework applicazioni di Node.js minimo e flessibile derivato da Express, che include una gamma completa di funzionalità per la compilazione di API REST basate su Connect.

### <a name="install-restify"></a>Installare Restify
1. Dalla riga di comando passare alla directory **azuread**. Se la directory **azuread** non esiste, crearla.

        `cd azuread - or- mkdir azuread; cd azuread`

2. Digitare il seguente comando:

    `npm install restify`

    Questo comando installa Restify.

#### <a name="did-you-get-an-error"></a>È stato visualizzato un errore?
Quando si usa NPM in alcuni sistemi operativi, potrebbe essere visualizzato il messaggio di errore **Error: EPERM, chmod '/usr/local/bin/..'** con il suggerimento di provare a eseguire l'account come amministratore. In tal caso, usare il comando sudo per eseguire NPM a un livello di privilegi più elevato.

#### <a name="did-you-get-an-error-regarding-dtrace"></a>È stato visualizzato un errore relativo a DTrace?
Durante l'installazione di Restify, potrebbe essere visualizzato un errore simile al seguente:

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

L'output di questo comando dovrebbe avere un aspetto simile al seguente:

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


## <a name="step-6-install-passportjs-in-your-web-api"></a>Passaggio 6: Installare Passport.js nell'API Web
[Passport](http://passportjs.org/) è il middleware di autenticazione per Node.js. Passport è flessibile e modulare e può essere rilasciato in modo non invadente in qualsiasi applicazione Web basata su Express o Restify. Una gamma completa di strategie supporta l'autenticazione con nome utente e password, Facebook, Twitter e altro ancora.

È stata sviluppata una strategia per Azure Active Directory. Dopo l'installazione di questo modulo, viene aggiunto il plug-in della strategia per Azure Active Directory.

1. Dalla riga di comando passare alla directory **azuread**.

2. Per installare Passport.js, immettere il comando seguente:

    `npm install passport`

    L'output di questo comando dovrebbe avere un aspetto simile al seguente:

``
        passport@0.1.17 node_modules\passport
        ├── pause@0.0.1
        └── pkginfo@0.2.3
``

## <a name="step-7-add-passport-azure-ad-to-your-web-api"></a>Passaggio 7: Aggiungere Passport-Azure-AD all'API Web
A questo punto, si aggiunge la strategia OAuth usando `passport-azure-ad`, una suite di strategie che connettono Azure Active Directory a Passport. In questo esempio di API REST tale strategia viene usata per i token di connessione.

> [!NOTE]
> Anche se OAuth2 fornisce un framework in cui è possibile rilasciare qualsiasi tipo di token noto, i più diffusi sono solo alcuni. I token di connessione sono i più usati per la protezione degli endpoint e sono il tipo di token maggiormente rilasciato in OAuth2. Molte implementazioni presumono che i token di connessione siano l'unico tipo di token rilasciato.
>
>

Dalla riga di comando passare alla directory **azuread**.

Digitare il comando seguente per installare `passport-azure-ad module` per Passport.js:

`npm install passport-azure-ad`

L'output di questo comando dovrebbe avere un aspetto simile al seguente:


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



## <a name="step-8-add-mongodb-modules-to-your-web-api"></a>Passaggio 8: Aggiungere i moduli MongoDB all'API Web
MongoDB viene usato come archivio dati. Per questo motivo, è necessario installare il noto plug-in Mongoose per gestire i modelli e gli schemi. È necessario installare anche il driver del database per MongoDB, anche questo denominato MongoDB.

 `npm install mongoose`

## <a name="step-9-install-additional-modules"></a>Passaggio 9: Installare moduli aggiuntivi
A questo punto vengono installati gli altri moduli necessari.

1. Dalla riga di comando passare alla cartella **azuread**, se non è già stato fatto.

    `cd azuread`

2. Immettere i comandi seguenti per installare questi moduli nella directory **node_modules**:

    * `npm install assert-plus`
    * `npm install bunyan`
    * `npm update`

## <a name="step-10-create-a-serverjs-with-your-dependencies"></a>Passaggio 10: Creare un file server.js con le dipendenze
Il file server.js fornisce gran parte della funzionalità per il server API Web. La maggior parte del codice viene aggiunta a questo file. Per la produzione è consigliabile effettuare il refactoring della funzionalità in file più piccoli, ad esempio route e controller distinti. In questa dimostrazione si usa server.js per questa funzionalità.

1. Dalla riga di comando passare alla cartella **azuread**, se non è già stato fatto.

    `cd azuread`

2. Creare un file `server.js` nell'editor preferito e aggiungere le informazioni seguenti:

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

3. Salvare il file. Servirà ancora tra poco.

## <a name="step-11-create-a-config-file-to-store-your-azure-ad-settings"></a>Passaggio 11: Creare un file config per archiviare le impostazioni di Azure AD
Questo file di codice passa i parametri di configurazione dal portale di Azure Active Directory a Passport.js. Questi valori di configurazione sono stati creati quando si è aggiunta l'API Web al portale nella prima parte della procedura dettagliata. Dopo aver copiato il codice, verrà spiegato che cosa inserire nei valori di questi parametri.

1. Dalla riga di comando passare alla cartella **azuread**, se non è già stato fatto.

    `cd azuread`

2. Creare un file `config.js` nell'editor preferito e aggiungere le informazioni seguenti:

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
3. Salvare il file.

## <a name="step-12-add-configuration-values-to-your-serverjs-file"></a>Passaggio 12: Aggiungere i valori di configurazione al file server.js
È necessario leggere questi valori dal file config appena creato nell'applicazione. A tale scopo, aggiungere il file config come risorsa necessaria nell'applicazione e quindi impostare le variabili globali in modo che corrispondano a quelle del documento config.js.

1. Dalla riga di comando passare alla cartella **azuread**, se non è già stato fatto.

    `cd azuread`

2. Aprire il file `server.js` nell'editor preferito e aggiungere le informazioni seguenti:

    ```Javascript
    var config = require('./config');
    ```
3. Quindi aggiungere una nuova sezione a `server.js` con il codice seguente:

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

    // Array to hold logged in users and the current logged in user (owner).
    var users = [];
    var owner = null;

    // Our logger.
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

      // If the logging level is specified, switch to it.
      if (config.creds.loggingLevel) { log.levels("console", config.creds.loggingLevel); }

    // MongoDB setup.
    // Set up some configuration.
    var serverPort = process.env.PORT || 8080;
    var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
    ```

4. Salvare il file.

## <a name="step-13-add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>Passaggio 13: Aggiungere le informazioni su schemi e modelli MongoDB usando Moongoose
Terminate le queste operazioni di preparazione, unire i tre file in un servizio API REST.

Per questa procedura dettagliata si usa MongoDB per archiviare le attività, come illustrato nel Passaggio 4.

Come indicato nel file `config.js` creato nel Passaggio 11, il database è stato denominato `tasklist` in base a quanto inserito alla fine dell'URL di connessione **mogoose_auth_local**. Non è necessario creare in anticipo il database in MongoDB. Se il database non esiste già, viene creato automaticamente da MongoDB alla prima esecuzione dell'applicazione server.

Dopo aver indicato al server quale database MongoDB usare, è necessario scrivere del codice aggiuntivo per creare il modello e lo schema per le attività del server.

### <a name="discussion-of-the-model"></a>Descrizione del modello
Questo modello di schema è semplice ed è possibile espanderlo in base alle esigenze.

NAME: nome della persona assegnata all'attività. Valore **stringa**.

TASK: l'attività stessa. Valore **stringa**.

DATE: data di scadenza dell'attività. Valore **datetime**.

COMPLETED: indica se l'attività è stata completata o meno. Valore **booleano**.

### <a name="creating-the-schema-in-the-code"></a>Creazione dello schema nel codice
1. Dalla riga di comando passare alla cartella **azuread**, se non è già stato fatto.

    `cd azuread`

2. Aprire il file `server.js` nell'editor preferito e aggiungere le informazioni seguenti sotto la voce di configurazione:

    ```Javascript
    // Connect to MongoDB.
    global.db = mongoose.connect(serverURI);
    var Schema = mongoose.Schema;
    log.info('MongoDB Schema loaded');

    // Here we create a schema to store our tasks and users. It's a fairly simple schema for now.
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
Come si può vedere dal codice, prima di tutto viene creato lo schema. Si crea quindi un oggetto modello che viene usato per l'archiviazione dei dati in tutto il codice quando si definiscono le **route**.

## <a name="step-14-add-our-routes-for-our-task-rest-api-server"></a>Passaggio 14: Aggiungere le route per il server API REST delle attività
Ora che è disponibile un modello di database, aggiungere le route da usare per il server API REST.

### <a name="about-routes-in-restify"></a>Informazioni sulle route in Restify
Il funzionamento delle route in Restify è identico a quello nello stack di Express. Definire le route usando l'URI che si prevede verrà chiamato dalle applicazioni client. Le route si definiscono di solito in un file separato. In questo caso le route vengono inserite nel file server.js. È consigliabile inserirle in un file a parte per usarle in fase di produzione.

Di seguito è riportato un modello tipico per una route Restify:

```Javascript
function createObject(req, res, next) {

// Do work on object.

 _object.name = req.params.object; // passed value is in req.params under object

 ///...

return next(); // Keep the server going.
}

....

server.post('/service/:add/:object', createObject); // Calls createObject on routes that match this.

```


Questo è il modello più semplice. Restify ed Express offrono funzionalità molto più avanzate, ad esempio la definizione di tipi di applicazione e l'esecuzione di un routing complesso tra endpoint diversi. Ai fini di questa procedura dettagliata, vengono usate route semplici.

### <a name="add-default-routes-to-our-server"></a>Aggiungere le route predefinite al server
A questo punto, aggiungere le route CRUD di creazione, recupero, aggiornamento ed eliminazione.

1. Dalla riga di comando passare alla cartella **azuread**, se non è già stato fatto.

    `cd azuread`

2. Aprire il file `server.js` nell'editor preferito e aggiungere le informazioni seguenti sotto le voci di database create in precedenza:

```Javascript

/**
 *
 * APIs for our REST Task server.
 */

// Create a task.

function createTask(req, res, next) {

    // Restify currently has a bug which doesn't allow you to set default headers.
    // These headers comply with CORS and allow us to mongodbServer our response to any origin.

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it, and save it to Mongodb.
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

/// Simple returns the list of TODOs that were loaded.

function listTasks(req, res, next) {
    // Restify currently has a bug which doesn't allow you to set default headers.
    // These headers comply with CORS and allow us to mongodbServer our response to any origin.

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
            log.warn(err, "There is no tasks in the database. Did you initialize the database as stated in the README?");
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

### <a name="add-error-handling-in-our-apis"></a>Aggiungere la gestione degli errori nelle API
```

///--- Errors for communicating something interesting back to the client.

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


## <a name="step-15-create-your-server"></a>Passaggio 15: Creare il server
Ora che il database è stato definito e le route sono state aggiunte, resta solo da aggiungere l'istanza del server che gestisce le chiamate.

Restify ed Express consentono un livello elevato di personalizzazione per un server API REST, ma anche in questo caso viene usata la configurazione più semplice.

```Javascript
/**
 * Our server.
 */


var server = restify.createServer({
    name: "Azure Active Directroy TODO Server",
    version: "2.0.1"
});

// Ensure we don't drop data on uploads.
server.pre(restify.pre.pause());

// Clean up sloppy paths like //todo//////1//.
server.pre(restify.pre.sanitizePath());

// Handle annoying user agents (curl).
server.pre(restify.pre.userAgentConnection());

// Set a per request bunyan logger (with requestid filled in).
server.use(restify.requestLogger());

// Allow five requests per second by IP, and burst to 10.
server.use(restify.throttle({
    burst: 10,
    rate: 5,
    ip: true,
}));

// Use the common stuff you probably want.
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
    mapParams: true
})); // Allow for JSON mapping to REST.
```

## <a name="step-16-add-the-routes-to-the-server-without-authentication-for-now"></a>Passaggio 16: Aggiungere le route al server senza l'autenticazione
```Javascript
/// Now the real handlers. Here we just CRUD.
/**
/*
/* Each of these handlers is protected by our OIDCBearerStrategy by invoking 'oidc-bearer'.
/* In the pasport.authenticate() method. We set 'session: false' because REST is stateless and
/* we don't need to maintain session state. You can experiment with removing API protection
/* by removing the passport.authenticate() method as follows:
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
// Register a default '/' handler.
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

## <a name="step-17-run-the-server-before-adding-oauth-support"></a>Passaggio 17: Eseguire il server prima di aggiungere il supporto OAuth
Testare il server prima di aggiungere l'autenticazione.

Il modo più semplice per farlo consiste nell'usare Curl in una riga di comando. Prima di procedere, è necessaria una utilità che consente di analizzare l'output come JSON.

1. Installare lo strumento JSON seguente, usato per tutti gli esempi riportati di seguito:

    `$npm install -g jsontool`

    Questo comando installa lo strumento JSON a livello globale. A questo punto è possibile dedicarsi al server:

2. Assicurarsi prima di tutto che l'istanza di MongoDB sia in esecuzione:

    `$sudo mongod`

3. Quindi, passare alla directory e iniziare a usare Curl:

    `$ cd azuread`
    `$ node server.js`

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

4. Ora è possibile aggiungere un'attività in questo modo:

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

Ora si dispone di un'API REST con MongoDB.

## <a name="step-18-add-authentication-to-our-rest-api-server"></a>Passaggio 18: Aggiungere l'autenticazione al server API REST
Ora che l'API REST è in esecuzione è possibile iniziare a usarla con Azure AD.

Dalla riga di comando passare alla cartella **azuread**, se non è già stato fatto.

`cd azuread`

### <a name="use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>Usare l'oggetto OIDCBearerStrategy incluso in passport-azure-ad
Nei passaggi precedenti è stato creato un tipico server REST TODO senza alcun tipo di autenticazione. Questa operazione è stata eseguita nel modo indicato di seguito.

1. Innanzitutto è necessario indicare che si desidera usare Passport. Inserire il codice subito dopo la configurazione dell'altro server:

    ```Javascript
            // Let's start using Passport.js.

            server.use(passport.initialize()); // Starts passport.
            server.use(passport.session()); // Provides session support.
    ```
    > [!TIP]
    > Durante la scrittura delle API è sempre consigliabile collegare i dati a un elemento univoco, dal token di cui l'utente non può effettuare lo spoofing. Quando archivia gli elementi TODO, il server esegue questa operazione in base all'ID dell'oggetto dell'utente nel token, chiamato mediante token.oid, inserito nel campo "owner". In questo modo, solo l'utente può accedere ai relativi elementi TODO. Il valore di "owner" non viene esposto nell'API, quindi un utente esterno può richiedere elementi TODO di altri anche se sono autenticati.                    

2. Usare quindi la strategia di connessione fornita con `passport-azure-ad`. Per il momento, esaminare il codice. Il resto verrà illustrato tra breve. Inserire il codice seguente dopo quanto incollato in precedenza:

```Javascript
    /**
    /*
    /* Calling the OIDCBearerStrategy and managing users.
    /*
    /* Passport pattern provides the need to manage users and info tokens
    /* with a FindorCreate() method that must be provided by the implementor.
    /* Here we just auto-register any user and implement a FindById().
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

Passport usa un modello simile per tutte le strategie (Twitter, Facebook e così via) seguite dagli scrittori della strategia. Si noti che alla strategia è stata passata una funzione con parametri token e done. La strategia torna indietro dopo eseguito il suo lavoro. A questo punto, archiviare l'utente e accantonare il token in modo che non sia necessario richiederlo nuovamente.

> [!IMPORTANT]
> Il codice precedente accetta qualsiasi utente che esegue l'autenticazione al server. Questa operazione è nota come registrazione automatica. Nei server di produzione è preferibile non consentire l'accesso a chiunque senza prima prevedere un processo di registrazione. Questo è il modello in genere adottato per le app consumer che consentono di eseguire la registrazione con Facebook, ma che chiedono di immettere informazioni aggiuntive. Se non si trattasse di un programma della riga di comando, sarebbe stato possibile estrarre il messaggio di posta elettronica dall'oggetto token restituito e chiedere agli utenti di immettere informazioni aggiuntive. Trattandosi di un server di test, è sufficiente aggiungere le informazioni al database in memoria.
>
>

### <a name="protect-some-endpoints"></a>Proteggere alcuni endpoint
Per proteggere gli endpoint, specificare la chiamata a `passport.authenticate()` con il protocollo preferito.

Per fare in modo che il codice server esegua operazioni più complesse, è possibile modificare la route.

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

## <a name="step-19-run-your-server-application-again-and-ensure-it-rejects-you"></a>Passaggio 19: Eseguire nuovamente l'applicazione server e assicurarsi che rifiuti l'utente
Per verificare se la protezione OAuth2 per gli endpoint è attiva, usare nuovamente `curl`. Eseguire il test prima di eseguire qualsiasi SDK client in questo endpoint. Le intestazioni restituite dovrebbero essere sufficienti a capire se si sta procedendo nel modo corretto.

1. Assicurarsi prima di tutto che l'istanza di MongoDB sia in esecuzione:

    `$sudo mongod`

2. Quindi, passare alla directory e iniziare a usare Curl.

      `$ cd azuread`
      `$ node server.js`

3. Provare un'operazione POST di base.

    `$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

    ```Shell
    HTTP/1.1 401 Unauthorized
    Connection: close
    WWW-Authenticate: Bearer realm="Users"
    Date: Tue, 14 Jul 2015 05:45:03 GMT
    Transfer-Encoding: chunked
    ```

In questo caso la risposta prevista è 401, perché indica che il livello Passport sta tentando il reindirizzamento all'endpoint di autorizzazione, ovvero il comportamento voluto.

## <a name="next-steps"></a>Passaggi successivi
Sono state eseguite tutte le operazioni possibili con questo server senza usare un client compatibile con OAuth2. Sarà necessario eseguire un'altra procedura dettagliata.

Si è appreso come implementare un'API REST usando Restify e OAuth2. Il codice disponibile è sufficiente per continuare a sviluppare il servizio e imparare a compilare partendo da questo esempio.

Se si è interessati a proseguire l'esplorazione di ADAL, di seguito sono riportati alcuni client ADAL supportati con cui continuare a lavorare.

È possibile clonarli nel computer di sviluppo e configurarli come illustrato nella procedura dettagliata.

[ADAL per iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios)

[ADAL per Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

