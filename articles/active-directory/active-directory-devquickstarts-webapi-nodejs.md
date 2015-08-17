<properties
	pageTitle="Introduzione a NodeJS per Azure AD | Microsoft Azure"
	description="Come compilare un'API Web per Node.js che si integra con Azure AD per l'autenticazione."
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
	ms.date="07/17/2015"
	ms.author="brandwe"/>

# Introduzione all'API WEB per Node

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Questa procedura dettagliata offre un modo rapido e semplice per configurare un servizio API REST integrato con Azure Active Directory per la protezione delle API tramite il protocollo OAuth2. Il server di esempio incluso nel download è progettato per poter essere eseguito su qualsiasi piattaforma, ma è destinato a OSX e Linux.

Al termine di questa procedura dettagliata, sarà possibile compilare un server API REST in esecuzione con le funzionalità seguenti:

* Un server node.js che esegue un'interfaccia API REST con JSON usando MongoDB come archivio permanente
* API REST che sfruttano la protezione delle API OAuth2 con i token Bearer mediante Azure Active Directory


Poiché tutto il codice sorgente per questo esempio in esecuzione è stato rilasciato in GitHub con una licenza Apache 2.0, è possibile clonarlo (o ancora meglio, biforcarlo) e inviare commenti e suggerimenti e richieste pull.

## Informazioni sui moduli Node.js

In questa procedura dettagliata verranno usati moduli Node.js. I moduli sono pacchetti JavaScript caricabili che forniscono funzionalità specifiche per l'applicazione. Di solito i moduli si installano usando lo strumento da riga di comando NPM di Node.js nella directory di installazione di NPM, ma alcuni moduli, come il modulo HTTP, sono inclusi nel pacchetto Node.js principale. I moduli installati vengono salvati nella directory node\_modules nella radice della directory di installazione di Node.js. Ogni modulo nella directory node\_modules mantiene la relativa directory node\_modules che contiene i moduli da cui dipende e ogni modulo necessario ha una directory node\_modules. Questa struttura di directory ricorsiva rappresenta la catena di dipendenze.

Questa struttura della catena di dipendenze comporta un footprint maggiore delle applicazioni, ma garantisce che vengano soddisfatte tutte le dipendenze e che la versione dei moduli usata durante lo sviluppo venga usata anche in fase di produzione. In questo modo il comportamento delle app di produzione è più prevedibile e si evitano problemi controllo delle versioni per gli utenti.

## Passaggio 1: Registrare un tenant di Azure AD

Per usare questo esempio, sarà necessario un tenant di Azure Active Directory. Se non si è certi di cosa sia tenant o di come ottenerne uno, vedere [Come ottenere un tenant di Azure AD](active-directory-howto-tenant.md).

## Passaggio 2: Aggiungere un'API Web al tenant

Dopo avere ottenuto il tenant di Azure Active Directory, aggiungere questa app di esempio al tenant e usarla per proteggere l'API.

Per consentire all'app di autenticare gli utenti, sarà innanzitutto necessario registrare una nuova applicazione nel tenant.

- Accedere al portale di gestione di Azure.
- Nel pannello di navigazione a sinistra fare clic su **Active Directory**.
- Selezionare il tenant in cui si desidera registrare l'applicazione.
- Fare clic sulla scheda **Applicazioni**, quindi fare clic su aggiungi nel pannello in basso.
- Seguire le istruzioni e creare una nuova **Applicazione Web e/o API Web**.
    - Il **nome** dell'applicazione deve essere una descrizione per gli utenti finali.
    -	L'**URL accesso** è l'URL di base dell'app. Il valore predefinito della struttura è `https://localhost:8888`.
    - L'**URI ID app** è un identificatore univoco dell'applicazione. Per convenzione si usa `https://<tenant-domain>/<app-name>`, ad esempio `https://contoso.onmicrosoft.com/my-first-aad-app`.
- Dopo avere completato la registrazione, AAD assegnerà all'app un identificatore client univoco. Poiché questo valore sarà necessario nelle sezioni successive, copiarlo dalla scheda Configura.

## Passaggio 3: Scaricare node.js per la piattaforma in uso
Per usare correttamente questo esempio, è necessario disporre di un'installazione funzionante di Node.js.

Installare Node.js da [http://nodejs.org](http://nodejs.org).

## Passaggio 4: Installare MongoDB nella piattaforma in uso

Per usare correttamente questo esempio, è necessario disporre di un'installazione funzionante di MongoDB. Si userà MongoDB per rendere l'API REST persistente nelle istanze del server.

Installare MongoDB da [http://mongodb.org](http://www.mongodb.org).

**NOTA:** in questa procedura dettagliata si presume che si usino gli endpoint server e di installazione predefiniti per MongoDB, che al momento della stesura di questo articolo sono: mongodb://localhost


## Passaggio 5: Installare i moduli Restify nell'API Web

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


## Passaggio 6: Installare Passport.js nell'l'API Web

[Passport](http://passportjs.org/) è il middleware di autenticazione per Node.js. Passport, estremamente flessibile e modulare, può essere rilasciato in modo non invadente in qualsiasi applicazione Web basata su Express o Resitify. Una gamma completa di strategie supporta l'autenticazione mediante nome utente e password, Facebook, Twitter e altro ancora. È stata sviluppata una strategia per Azure Active Directory. Dopo l'installazione di questo modulo, verrà aggiunto il plug-in della strategia per Azure Active Directory.

Dalla riga di comando passare alla directory azuread.

Immettere il comando seguente per installare passport.js.

`npm install passport`

L'output del comando dovrebbe apparire simile al seguente:

	passport@0.1.17 node_modules\passport
	├── pause@0.0.1
	└── pkginfo@0.2.3

## Passaggio 7: Aggiungere il supporto del token di connessione di Passport.js all'API Web

Ora si aggiungerà la strategia di connessione, usando passport-bearer-http, un gestore di connessione per [Passport](http://passportjs.org/). Si aggiungerà inoltre il supporto del gestore del token JWT usando il node-jwt.

**NOTA:** anche se OAuth2 fornisce un framework in cui è possibile rilasciare qualsiasi tipo di token noto, solo determinati tipi di token sono usati su larga scala. Per proteggere gli endpoint, si è passati ai token di connessione. I token di connessione sono il tipo di token maggiormente rilasciato in OAuth2 e molte implementazioni presumono che i token di connessione siano l'unico tipo di token rilasciato.

Dalla riga di comando passare alla directory **azuread**.

Digitare il comando seguente per installare i moduli Passport.js:

- `npm install passport-oauth`
- `npm install passport-http-bearer`
- `npm install node-jwt`

L'output del comando dovrebbe apparire simile al seguente:

	ms-passport-wsfed-saml2@0.3.8 node_modules\passport-oauth  
	├── xtend@2.0.3
	├── xml-crypto@0.0.9
	├── xmldom@0.1.13
	└── xml2js@0.1.14 (sax@0.5.2)


## Passaggio 8: Aggiungere i moduli MongoDB all'API Web

MongoDB verrà usato come archivio dati. Per questo motivo, è necessario installare sia Mongoose, un plug-in molto usato per gestire modelli e schemi, che il driver di database per MongoDB, chiamato sempre MongoDB.


* `npm install mongoose`
* `npm install mongodb`

## Passaggio 9: Installare moduli aggiuntivi

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


## Passaggio 10: Creare un file server.js con le dipendenze

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
```

Salvare il file. Servirà ancora tra poco.

## Passaggio 11: Creare un file config per archiviare le impostazioni di Azure AD

Questo file di codice passa i parametri di configurazione dal portale di Azure Active Directory a Passport.js. Questi valori di configurazione sono stati creati quando si è aggiunta l'API Web al portale nella prima parte della procedura dettagliata. Dopo avere copiato il codice, verrà spiegato che cosa inserire nei valori di questi parametri.


Dalla riga di comando passare alla cartella **azuread**, se necessario:

`cd azuread`

Creare un file `config.js` nell'editor preferito e aggiungere le seguenti informazioni:

```Javascript
// Don't commit this file to your public repos
    exports.creds = {
    mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
    openid_configuration: 'https://login.microsoftonline.com/common/.well-known/openid-configuration', // For using Microsoft you should never need to change this.
    openid_keys: 'https://login.microsoftonline.com/common/discovery/keys', // For using Microsoft you should never need to change this. If absent will attempt to get from openid_configuration
}

```



**NOTA:** probabilmente non sarà mai necessario modificare questi valori.

**NOTA:** le chiavi vengono registrate con una certa frequenza. Assicurarsi di effettuare sempre il pull dall'URL "openid\_keys" e che l'app possa accedere a Internet.


## Passaggio 12: Aggiungere la configurazione al file server.js

È necessario leggere questi valori dal file config appena creato nell'applicazione. A tale scopo, è sufficiente aggiungere il file config come risorsa necessaria nell'applicazione e quindi impostare le variabili globali su quelle del documento config.js.

Dalla riga di comando passare alla cartella **azuread**, se necessario:

`cd azuread`

Aprire il file `server.js` nell'editor preferito e aggiungere le seguenti informazioni:

```Javascript
var config = require('./config');
```
Quindi aggiungere una nuova sezione a `server.js` con il codice seguente:

```Javascript
/**
* Setup some configuration
*/
var mongoose = require('mongoose/');
var serverPort = process.env.PORT || 8888;
var serverURI = ( process.env.PORT ) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;

```
## Passaggio 13: Creare un file helper metadata.js per facilitare l'analisi di metadati/token

Poiché l'obiettivo è solo di mantenere la logica dell'applicazione nel file server.js, è opportuno inserire alcuni metodi helper in un file separato. Questi metodi semplicemente consentono di analizzare i metadati OpenID Connect e non sono correlati allo scenario principale. È consigliabile inserirli subito. Si aggiungeranno altri elementi a questo file nel seguito della procedura dettagliata.

***NOTA:*** si noterà che questo file metadata.js analizza XML per SAML e WS-Fed, oltre che JSON per OpenID Connect. Questo comportamento è previsto da progettazione perché questo file verrà usato anche in altri esempi. Per ora è possibile ignorarlo.

Dalla riga di comando passare alla cartella **azuread**, se necessario:

`cd azuread`

Creare un file `metadata.js` nell'editor preferito e aggiungere le seguenti informazioni:

```Javascript

'use strict';

var xml2js = require('xml2js');
var request = require('request');
var aadutils = require('./aadutils');
var async = require('async');

// Logging

var bunyan = require('bunyan');
var log = bunyan.createLogger({name: 'Microsoft OpenID Connect Passport Strategy'});

var Metadata = function (url, authtype) {


  if(!url) {
    throw new Error("Metadata: url is a required argument");
  }
  if(!authtype) {
    throw new Error('OIDCBearerStrategy requires an authentication type specified to metadata parser. Valid types are saml, wsfed, or odic"');
  }

  this.url = url;
  this.metadata = null;
  this.authtype = authtype;
  log.info(authtype, 'Metadata requested for authentication type');
};

Object.defineProperty(Metadata, 'url', {
  get: function () {
    return this.url;
  }
});

Object.defineProperty(Metadata, 'saml', {
  get: function () {
    return this.saml;
  }
});

Object.defineProperty(Metadata, 'wsfed', {
  get: function () {
    return this.wsfed;
  }
});

Object.defineProperty(Metadata, 'oidc', {
  get: function () {
    return this.oidc;
  }
});


Object.defineProperty(Metadata, 'metadata', {
  get: function () {
    return this.metadata;
  }
});

Metadata.prototype.updateSamlMetadata = function(doc, next) {
  log.info('Request to update the SAML Metadata');
  try {

    this.saml = {};

    var entity = aadutils.getElement(doc, 'EntityDescriptor');
    var idp = aadutils.getElement(entity, 'IDPSSODescriptor');
    var signOn = aadutils.getElement(idp[0], 'SingleSignOnService');
    var signOff = aadutils.getElement(idp[0], 'SingleLogoutService');
    var keyDescriptor = aadutils.getElement(idp[0], 'KeyDescriptor');
    this.saml.loginEndpoint = signOn[0].$.Location;
    this.saml.logoutEndpoint = signOff[0].$.Location;

    // copy the x509 certs from the metadata
    this.saml.certs = [];
    for (var j=0;j<keyDescriptor.length;j++) {
      this.saml.certs.push(keyDescriptor[j].KeyInfo[0].X509Data[0].X509Certificate[0]);
    }
    next(null);
  } catch (e) {
    next(new Error('Invalid SAMLP Federation Metadata ' + e.message));
  }
};

Metadata.prototype.updateOidcMetadata = function(doc, next) {
  log.info('Request to update the Open ID Connect Metadata');
  try {
    this.oidc = {};

    var issuer = doc['issuer'];
    var keyDescriptor = aadutils.getElement(idp[0], 'keys');

    // copy the x509 certs from the metadata
    this.oidc.certs = [];
    for (var j=0;j<keyDescriptor.length;j++) {
      this.oidc.certs.push(keyDescriptor[j].KeyInfo[0].X509Data[0].X509Certificate[0]);
    }
    next(null);
  } catch (e) {
    next(new Error('Invalid Open ID Connect Federation Metadata ' + e.message));
  }
};


Metadata.prototype.updateWsfedMetadata = function(doc, next) {
  log.info('Request to update the WS Federation Metadata');
  try {
    this.wsfed = {};
    var entity = aadutils.getElement(doc, 'EntityDescriptor');
    var roles = aadutils.getElement(entity, 'RoleDescriptor');
    for(var i = 0; i < roles.length; i++) {
      var role = roles[i];
      if(role['fed:SecurityTokenServiceEndpoint']) {
        var endpoint = role['fed:SecurityTokenServiceEndpoint'];
        var endPointReference = aadutils.getFirstElement(endpoint[0],'EndpointReference');
        this.wsfed.loginEndpoint = aadutils.getFirstElement(endPointReference,'Address');

        var keyDescriptor = aadutils.getElement(role, 'KeyDescriptor');
        // copy the x509 certs from the metadata
        this.wsfed.certs = [];
        for (var j=0;j<keyDescriptor.length;j++) {
          this.wsfed.certs.push(keyDescriptor[j].KeyInfo[0].X509Data[0].X509Certificate[0]);
        }
        break;
      }
    }

    return next(null);
  } catch (e) {
    next(new Error('Invalid WSFED Federation Metadata ' + e.message));
  }
};

Metadata.prototype.fetch = function(callback) {
  var self = this;
  log.info("Fetching metadata from the provided metadata URL: " + self.url);
  async.waterfall([
    // fetch the Federation metadata for the AAD tenant
    function(next){
      request(self.url, function (err, response, body) {
        if(err) {
          next(err);
        } else if(response.statusCode !== 200) {
          next(new Error("Error:" + response.statusCode +  " Cannot get AAD Federation metadata from " + self.url));
        } else {
          log.info(body, "retreived");
          next(null, body);
        }
      });
    },
    function(body, next){
      // parse the AAD Federation metadata xml

      if(self.authtype == "saml" || self.authtype == "wsfed") {
      log.info(body, "Parsing XML retreived from the endpoint");
      var parser = new xml2js.Parser({explicitRoot:true});
      // Note: xml responses from Azure AAD have a leading \ufeff which breaks xml2js parser!
      parser.parseString(body.replace("\ufeff", ""), function (err, data) {
        self.metatdata = data;
        next(err);

      });
    } else if(self.authtype == "oidc") {
      log.info(body, "Parsing JSON retreived from the endpoint");
      JSON.parse(body, function (err, data) {
        self.metatdata = data;
        next(err);
      });

    } else {

       next(new Error("Error: No Authentication type specified to metadata parser. Valid types are saml, wsfed, or odic"));
    }

    },

    function(next){
      if(self.authtype = "saml") {
      // update the SAML SSO endpoints and certs from the metadata
      self.updateSamlMetadata(self.metatdata, next);
    }},
    function(next){
      if(self.authtype = "wsfed") {
      // update the SAML SSO endpoints and certs from the metadata
      self.updateWsfedMetadata(self.metatdata, next);
    }},
    function(next){
      if(self.authtype = "oidc") {
      self.updateOidcMetadata(self.metadata, next);
    }},
  ], function (err) {
    // return err or success (err === null) to callback
    callback(err);
  });
};

exports.Metadata = Metadata;
```
Come si può vedere, il codice prende semplicemente l'URL openid passato in `config.js` e quindi lo analizza per cercare le informazioni che si useranno nel file `server.js`. Si invitano gli utenti a esaminare il codice e a espanderlo, se necessario.

### Caricare il file metadata.js in server.js

È necessario indicare il server dove ottenere i metodi appena scritti.

Dalla riga di comando passare alla cartella **azuread**, se necessario:

`cd azuread`

Aprire il file `server.js` nell'editor preferito e aggiungere le seguenti informazioni:

```Javascript
var metadata = require('./metadata);
```
Ora aggiungere alla fine della sezione `Configuration` questa chiamata per inviare il documento di metadati in `config.js` al parser appena scritto:

```Javascript
this.aadutils = new var Metadata = require('./metadata').Metadata;
```

## Passaggio 14: Aggiungere le informazioni su schemi e modelli MongoDB usando Moongoose

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
/**
*
* Connect to MongoDB
*/

global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;  
```
Verrà così stabilita la connessione al server MongoDB e restituito un oggetto Schema.

#### Usando lo schema, creare il modello nel codice

Sotto il codice appena scritto aggiungere il codice seguente:

```Javascript
/**
/ Here we create a schema to store our tasks. Pretty simple schema for now.
*/

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
 * APIs
 */

function createTask(req, res, next) {

	// Resitify currently has a bug which doesn't allow you to set default headers
  	// This headers comply with CORS and allow us to mongodbServer our response to any origin

  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it up and save it to Mongodb
  var _task = new Task();

        if (!req.params.task) {
                req.log.warn('createTask: missing task');
                next(new MissingTaskError());
                return;
        }


  _task.owner = req.params.owner;
   _task.task = req.params.task;
   _task.date = new Date();

  _task.save(function (err) {
  	if (err) {
        req.log.warn(err, 'createTask: unable to save');
        next(err);
    } else {
    res.send(201, _task);

			}
  });

  return next();

}


/**
 * Deletes a Task by name
 */
function removeTask(req, res, next) {

        Task.remove( { task:req.params.task }, function (err) {
                if (err) {
                        req.log.warn(err,
                                     'removeTask: unable to delete %s',
                                     req.params.task);
                        next(err);
                } else {
                        res.send(204);
                        next();
                }
        });
}

/**
 * Deletes all Tasks. A wipe
 */
function removeAll(req, res, next) {
        Task.remove();
        res.send(204);
        return next();
}    });
}


/**
 *
 *
 *
 */
function getTask(req, res, next) {


        Task.find(req.params.name, function (err, data) {
                if (err) {
                        req.log.warn(err, 'get: unable to read %s', req.params.name);
                        next(err);
                        return;
                }

                res.json(data);
        });

        return next();
}


/**
 * Simple returns the list of TODOs that were loaded.
 *
 */

function listTasks(req, res, next) {
  // Resitify currently has a bug which doesn't allow you to set default headers
  // This headers comply with CORS and allow us to mongodbServer our response to any origin

  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Headers", "X-Requested-With");

  console.log("server getTasks");

  Task.find().limit(20).sort('date').exec(function (err,data) {

    if (err)
      return next(err);

    if (data.length > 0) {
            console.log(data);
        }

    if (!data.length) {
            console.log('there was a problem');
            console.log(err);
            console.log("There is no tasks in the database. Did you initalize the database as stated in the README?");
        }

    else {

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


function TaskExistsError(name) {
        assert.string(name, 'name');

        restify.RestError.call(this, {
                statusCode: 409,
                restCode: 'TaskExists',
                message: name + ' already exists',
                constructorOpt: TaskExistsError
        });

        this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);


function TaskNotFoundError(name) {
        assert.string(name, 'name');

        restify.RestError.call(this, {
                statusCode: 404,
                restCode: 'TaskNotFound',
                message: name + ' was not found',
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
        name: "Azure Active Directroy TODO Server",
    version: "1.0.0",
    formatters: {
        'application/json': function(req, res, body){
            if(req.params.callback){
                var callbackFunctionName = req.params.callback.replace(/[^A-Za-z0-9_\.]/g, '');
                return callbackFunctionName + "(" + JSON.stringify(body) + ");";
            } else {
                return JSON.stringify(body);
            }
        },
        'text/html': function(req, res, body){
            if (body instanceof Error)
                        return body.stack;

                      if (Buffer.isBuffer(body))
                        return body.toString('base64');

                return util.inspect(body);
        },
        'application/x-www-form-urlencoded': function(req, res, body){
            if (body instanceof Error) {
                    res.statusCode = body.statusCode || 500;
                    body = body.message;
            } else if (typeof (body) === 'object') {
                body = body.task || JSON.stringify(body);
            } else {
                body = body.toString();
            }

        res.setHeader('Content-Length', Buffer.byteLength(body));
        return (body);
        }
    }
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

        // This lets us push JSON to the REST API endpoint as well. Maps x: y as /name:value

        server.use(restify.bodyParser({ mapParams: false }));

        /// Now the real handlers. Here we just CRUD

        server.get('/tasks', listTasks);
        server.head('/tasks', listTasks);
        server.get('/tasks/:name', getTask);
        server.head('/tasks/:name', getTask);
        server.post('/tasks/:name/:task', createTask);
        server.del('/tasks/:name/:task', removeTask);
        server.del('/tasks/:name', removeTask);
        server.del('/tasks', removeAll, function respond(req, res, next) { res.send(204); next(); });


        // Register a default '/' handler

        server.get('/', function root(req, res, next) {
                var routes = [
                        'GET     /',
                        'POST    /tasks/:name/:task',
                        'GET     /tasks',
                        'PUT     /tasks/:name',
                        'GET     /tasks/:name',
                        'DELETE  /tasks/:name/:task'
                ];
                res.send(200, routes);
                next();
        });

  server.listen(serverPort, function() {

  var consoleMessage = '\n Azure Active Directory Tutorial'
  consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++'
  consoleMessage += '\n %s server is listening at %s';
  consoleMessage += '\n Open your browser to %s/tasks\n';
  consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n'
  consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n'
  consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n'  

  console.log(consoleMessage, server.name, server.url, server.url, server.url);

});
```

## Passaggio 17: Eseguire il server prima di aggiungere il supporto OAuth

È consigliabile assicurarsi che non siano presenti errori prima di continuare con la parte della procedura dettagliata relativa a OAuth.

Il modo più semplice per farlo consiste nell'usare `curl` in una riga di comando. Prima di procedere, è necessaria una semplice utilità che consente di analizzare l'output come JSON. A tale scopo, installare lo strumento [json](https://github.com/trentm/json) che verrà usato in tutti gli esempi seguenti.

	$npm install -g jsontool

Questo comando installa lo strumento JSON a livello globale. Ora è possibile dedicarsi al server:

In primo luogo, assicurarsi che l'istanza di monogoDB sia in esecuzione.

	$sudo mongod

Quindi, passare alla directory e iniziare a usare Curl.

	$ cd azuread
	$ node server.js

	$ curl -isS http://127.0.0.1:8888 | json
	HTTP/1.1 200 OK
	Connection: close
	Content-Type: application/x-www-form-urlencoded
	Content-Length: 145
	Date: Wed, 29 Jan 2014 03:41:24 GMT

	[
  	"GET     /",
  	"POST    /tasks/:owner/:task",
  	"GET     /tasks",
  	"DELETE  /tasks",
  	"PUT     /tasks/:owner",
  	"GET     /tasks/:owner",
  	"DELETE  /tasks/:task"
	]

Ora è possibile aggiungere un'attività in questo modo:

	$ curl -isS -X POST http://127.0.0.1:8888/tasks/brandon/Hello

La risposta dovrebbe essere:

	HTTP/1.1 201 Created
	Connection: close
	Access-Control-Allow-Origin: *
	Access-Control-Allow-Headers: X-Requested-With
	Content-Type: application/x-www-form-urlencoded
	Content-Length: 5
	Date: Tue, 04 Feb 2014 01:02:26 GMT

	Hello

È anche possibile elencare le attività di Brandon in questo modo:

	$ curl -isS http://127.0.0.1:8888/tasks/brandon/

Se tutto funziona, si può aggiungere OAuth al server API REST.

## Passaggio 18: Aggiungere il codice Passport.js al server API REST

Ora che l'API REST è in esecuzione, è possibile usarla in Azure AD.

Dalla riga di comando passare alla cartella **azuread**, se necessario:

`cd azuread`

### Passaggio 1: Aggiungere i moduli di Passport

Aprire il file `server.js` nell'editor preferito e aggiungere le seguenti informazioni sotto la precedente istruzione di caricamento dei moduli che è quasi all'inizio del file, subito dopo l'importazione `var aadutils = require('./aadutils');`.

```Javascript
/*
*
* Load our old friend Passport for OAuth2 flows
*/

var passport = require('passport')
  , OAuth2Strategy = require('passport-oauth').OAuth2Strategy;
```

### 2\. Indicare al server che si usa l'autenticazione

Aprire il file `server.js` nell'editor preferito e aggiungere le seguenti informazioni **sotto il metodo server.get()**, in cui sono state definite le route, ma sopra il metodo **server.listen()**.


È necessario indicare a Restify di iniziare a usare `authorizationParser()` e a esaminare il contenuto dell'intestazione dell'autorizzazione.

```Javascript
        server.use(restify.authorizationParser());


```


### 3\. Aggiungere il modulo OAuth2 di Passport al codice

Qui si usano i parametri specifici di OAuth2 aggiunti al file config.js. Se il file `aadutils.js` ha analizzato correttamente il documento Federation Metadata, tutti questi valori verranno popolati automaticamente, anche se sono vuoti nel file config.js.

```Javascript
// Now our own handlers for authentication/authorization
// Here we only use Oauth2 from Passport.js

passport.use('provider', new OAuth2Strategy({
    authorizationURL: authEndpoint,
    tokenURL: tokenEndpoint,
    clientID: clientID,
    clientSecret: clientSecret,
    callbackURL: callbackURL
  },
  function(accessToken, refreshToken, profile, done) {
    User.findOrCreate({ UserId: profile.id }, function(err, user) {
      done(err, user);
    });
  }
));

// Let's start using Passport.js

server.use(passport.initialize());

```
### Passaggio 4: Aggiungere le route per l'autenticazione OAuth

```Javascript
// Redirect the user to the OAuth 2.0 provider for authentication.  When
// complete, the provider will redirect the user back to the application at
//     /auth/provider/callback

server.get('/auth/provider', passport.authenticate('provider'));

// The OAuth 2.0 provider has redirected the user back to the application.
// Finish the authentication process by attempting to obtain an access
// token.  If authorization was granted, the user will be logged in.
// Otherwise, authentication has failed.

server.get('/auth/provider/callback',
  passport.authenticate('provider', { successRedirect: '/',
                                      failureRedirect: '/login' }));
```

### Passaggio 5: Aggiungere un metodo helper IsAuthenticated() alle route

```Javascript
// Simple route middleware to ensure user is authenticated.
//   Use this route middleware on any resource that needs to be protected.  If
//   the request is authenticated (typically via a persistent login session),
//   the request will proceed.  Otherwise, the user will be redirected to the
//   login page.

var ensureAuthenticated = function(req, res, next) {
  if (req.isAuthenticated()) {
    return next();
  }
  res.redirect('/login');
};

```

### Passaggio 6: Aggiungere un meccanismo di memorizzazione nella cache per i cookie

```Javascript
// Passport session setup.
//   To support persistent login sessions, Passport needs to be able to
//   serialize users into and deserialize users out of the session.  Typically,
//   this will be as simple as storing the user ID when serializing, and finding
//   the user by ID when deserializing.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});
```
### Passaggio 7: Proteggere alcuni endpoint

Per proteggere gli endpoint, specificare la chiamata a passport.authenticate() con il protocollo preferito.

Si può eseguire un'operazione più interessante modificando la route nel codice del server:

```Javascript
server.get('/tasks', passport.authenticate('provider', { session: false }), listTasks);
```


## Passaggio 19: Eseguire nuovamente l'applicazione server e assicurarsi che rifiuti l'utente

Per verificare se la protezione OAuth2 per gli endpoint è attiva, usare nuovamente `curl`. Questa operazione verrà eseguita prima di eseguire uno qualsiasi degli SDK client in questo endpoint. Le intestazioni restituite dovrebbero bastare a indicare che si sta seguendo la strada giusta.

In primo luogo, assicurarsi che l'istanza di monogoDB sia in esecuzione.

	$sudo mongod

Quindi, passare alla directory e iniziare a usare Curl.

	$ cd azuread
	$ node server.js

Provare un'operazione GET di base:

	$ curl -isS http://127.0.0.1:8888/tasks/
	HTTP/1.1 302 Moved Temporarily
	Connection: close
	Location: https://login.windows.net/468a75f4-f9a7-4dc4-a527-4f4522734790/oauth2/authorize?response_type=code&redirect_uri=&client_id=123
	Content-Length: 0
	Date: Tue, 04 Feb 2014 02:15:14 GMT


302 è la risposta prevista qui, perché indica che il livello Passport sta tentando il reindirizzamento all'endpoint di autorizzazione, che è esattamente ciò che si vuole.

## Congratulazioni. Il servizio API REST sta usando OAuth2

Sono state eseguite tutte le operazioni possibili con questo server senza usare un client compatibile con OAuth2. Sarà necessario eseguire un'altra procedura dettagliata.

Se servivano solo informazioni per implementare un'API REST usando Restify e OAuth2, il codice fornito è più che sufficiente per continuare a sviluppare il servizio e imparare a compilare partendo da questo esempio.

Se si è interessati a proseguire l'esplorazione di ADAL, ecco alcuni client ADAL supportati, consigliati per continuare a lavorare:

Sarà sufficiente clonarli nel computer di sviluppo e configurarli come spiegato nella procedura dettagliata.

[ADAL per iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios)

[ADAL per Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android)

[ADAL per .Net](http://msdn.microsoft.com/library/windowsazure/jj573266.aspx)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
 

<!---HONumber=August15_HO6-->