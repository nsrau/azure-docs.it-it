<properties  linkid="dev-nodejs-how-to-twilio-voice-sms-service" urlDisplayName="Twilio Voice and SMS Service" pageTitle="Using Twilio for Voice, VoIP, and SMS Messaging in Azure" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="Node.js" title=" VoIP" authors="" solutions="" manager="" editor="" />

# Utilizzo di Twilio per le funzionalità voce, VoIP e SMS in Azure

In questa guida viene illustrato come sviluppare app che comunicano con Twilio e Node.js in Azure.

## Sommario

* [Informazioni su Twilio](#whatis)
* [Iscrizione a Twilio (sconto Microsoft)](#signup)
* [Creazione e distribuzione di un sito Web Node.js in Azure](#azuresite)
* [Configurazione del modulo Twilio](#twiliomodule)
* [Esecuzione di una chiamata in uscita](#makecall)
* [Invio di un messaggio SMS](#sendmessage)
* [Passaggi successivi](#nextsteps)

<a id="whatis" ></a> 
## Informazioni su Twilio

Twilio è una piattaforma API che consente agli sviluppatori di abilitare facilmente l'invio e la ricezione di chiamate telefoniche o SMS, nonché di incorporare funzionalità VoIP all'interno di applicazioni mobili native e basate su browser. Prima di entrare nel dettaglio, verrà illustrato brevemente il funzionamento della piattaforma.

### Ricevere chiamate e SMS

Twilio consente agli sviluppatori di [acquistare numeri di telefono programmabili][1] che è possibile utilizzare per inviare e ricevere chiamate e SMS. Quando un numero Twilio riceve una chiamata o un SMS in ingresso, invia all'applicazione Web una richiesta HTTP di tipo POST o GET, in cui vengono chieste istruzioni per la gestione della chiamata o del messaggio. Il server dell'utente risponderà alla richiesta HTTP di Twilio con [TwiML][2], un semplice set di tag XML che contengono istruzioni per la gestione di una chiamata o un SMS. Più avanti verranno illustrati esempi del linguaggio TwiML.

### Effettuare chiamate e inviare SMS

Inviando richieste HTTP all'API del servizio Web Twilio, gli sviluppatori possono inviare SMS o avviare chiamate in uscita. Per le chiamate in uscita, lo sviluppatore deve inoltre specificare un URL che restituisca le istruzioni TwiML per la gestione della chiamata in uscita dopo la connessione.

### Incorporare funzionalità VoIP nel codice dell'interfaccia utente (JavaScript, iOS o Android)

Twilio offre un SDK lato client che consente di trasformare qualsiasi Web browser desktop, app per iOS o app per Android in un telefono VoIP. In questo articolo verrà illustrato l'utilizzo delle funzionalità VoIP nel browser. Oltre all'SDK JavaScript di Twilio in esecuzione nel browser,è necessario utilizzare un'applicazione lato server (in questo caso un'applicazione Node.js) per l'emissione di un "capability token" per il client JavaScript. Per ulteriori informazioni sull'utilizzo di VoIP con Node.js, vedere il [blog per sviluppatori di Twilio][3].


## Iscrizione a Twilio (sconto Microsoft)

Prima di utilizzare i servizi Twilio, è necessario [iscriversi per creare un account][4]. Per i clienti di Microsoft Azure è disponibile uno sconto speciale [effettuando l'iscrizione qui][4].



## Creazione e distribuzione di un sito Web Node.js in Azure

Si procederà ora alla creazione di un sito Web Node.js eseguito in Azure. [La documentazione ufficiale per eseguire la procedura è disponibile qui][5]. A livello generale, la procedura è la seguente:

* Creazione di un account Azure, se non già disponibile
* Creazione di un nuovo sito Web tramite la console di amministrazione
  di Azure
* Aggiunta del supporto per il controllo del codice sorgente (si
  presuppone che sia stato utilizzato git)
* Creazione di un file `server.js` con una semplice applicazione Web
  Node.js
* Distribuzione dell'applicazione in Azure

<a id="twiliomodule" ></a> 
## Configurazione del modulo Twilio

Si inizierà a scrivere una semplice applicazione Node.js che utilizza l'API Twilio. Prima di iniziare, è necessario configurare le credenziali dell'account Twilio.

### Configurare le credenziali Twilio in variabili di ambiente di sistema

Per effettuare richieste autenticate nel back-end Twilio sono necessari il SID e il token di autorizzazione dell'account, che hanno lo stesso utilizzo del nome utente e della password impostate per l'account Twilio. Il modo più sicuro per configurare il SID e il token per l'utilizzo con il modulo Node in Azure consiste nell'utilizzare variabili di ambiente di sistema, configurandole direttamente nella console di amministrazione di Azure.

Selezionare il sito Web Node.js e fare clic sul collegamento "CONFIGURE". Scorrendo verso il basso verrà visualizzata un'area in cui è possibile impostare le proprietà di configurazione per l'applicazione. Immettere le credenziali dell'account Twilio ([disponibili nel dashboard di Twilio][6]) come illustrato, denominandole rispettivamente "TWILIO_ACCOUNT_SID" e "TWILIO_AUTH_TOKEN":

![Console di amministrazione di Azure](./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png)

Dopo aver configurato le variabili, riavviare l'applicazione nella console di Azure.

### Dichiarare il modulo Twilio in package.json

Verrà ora creato un file package.json per gestire le dipendenze del modulo Node tramite [npm][7]. Allo stesso livello del file "server.js" creato nell'esercitazione su Azure e Node.js, creare un file denominato "package.json". All'interno del file inserire il codice seguente:

  {
    "name": "application-name",
    "version": "0.0.1",
    "private": true,
    "scripts": {
      "start": "node server"
    },
    "dependencies": {
      "express": "3.1.0",
      "ejs": "*",
      "twilio":"*"
    }
  }

Questo codice dichiara il modulo Twilio come dipendenza, così come il diffuso [framework Web express][8] e il motore di rendering dei modelli EJS. A questo punto, è possibile iniziare a scrivere il codice.

<a id="makecall" ></a> 
## Esecuzione di una chiamata in uscita

Verrà ora creato un modulo molto semplice per l'esecuzione di una chiamata verso un numero scelto. Aprire server.js e immettere il codice seguente. Si noti che nel punto in cui nel codice è scritto "CHANGE_ME" è necessario inserire il nome del proprio sito Web di Azure:

  // Module dependencies
  var express = require('express'), 
      path = require('path'), 
      http = require('http'), 
      twilio = require('twilio');

  // Create Express web application
  var app = express();

  // Express configuration
  app.configure(function(){
      app.set('port', process.env.PORT || 3000);
      app.set('views', __dirname + '/views');
      app.set('view engine', 'ejs');
      app.use(express.favicon());
      app.use(express.logger('dev'));
      app.use(express.bodyParser());
      app.use(express.methodOverride());
      app.use(app.router);
      app.use(express.static(path.join(__dirname, 'public')));
  });
  app.configure('development', function(){
      app.use(express.errorHandler());
  });

  // Render an HTML user interface for the application's home page
  app.get('/', function(request, response) {
      response.render('index');
  });

  // Handle the form POST to place a call
  app.post('/call', function(request, response) {
      var client = twilio();
      client.makeCall({
          // make a call to this number
          to:request.body.number,

          // Change to a Twilio number you bought - see:
          // https://www.twilio.com/user/account/phone-numbers/incoming
          from:'+15558675309',

          // A URL in our app which generates TwiML
          // Change "CHANGE_ME" to your app's name
          url:'https://CHANGE_ME.azurewebsites.net/outbound_call'
      }, function(error, data) {
          // Go back to the home page
          response.redirect('/');
      });
  });

  // Generate TwiML to handle an outbound call
  app.post('/outbound_call', function(request, response) {
      var twiml = new twilio.TwimlResponse();

      // Say a message to the call's receiver 
      twiml.say('hello - thanks for checking out Twilio and Azure', {
          voice:'woman'
      });

      response.set('Content-Type', 'text/xml');
      response.send(twiml.toString());
  });

  // Start server
  http.createServer(app).listen(app.get('port'), function(){
    console.log("Express server listening on port " + app.get('port'));
  });

Creare quindi una directory denominata "views". All'interno di tale directory creare un file denominato "index.ejs" con il contenuto seguente:

  	<DOCTYPE html>
  		<html>
  			<head>
      			<title>Twilio Test</title>
      			<style>
      			input { height:20px; width:300px; font-size:18px; margin:5px; padding:5px; }
      			</style>
  			</head>
  			<body>
      			<h1>Twilio Test</h1>
      				<form action="/call" method="POST">
          				<input placeholder="Enter a phone number" name="number"/>
          				<br/>
          				<input type="submit" value="Call the number above"/>
      				</form>
  			</body>
  	    </html>

Distribuire ora il sito Web in Azure e aprire la Home page. Dovrebbe essere possibile immettere il proprio numero di telefono nel campo di testo e ricevere una chiamata dal numero Twilio.

<a id="sendmessage" ></a> 
## Invio di un messaggio SMS

Verranno ora create un'interfaccia utente e la logica di gestione dei moduli per l'invio di un SMS. Aprire il file "server.js" e aggiungere il codice seguente dopo l'ultima chiamata ad "app.post":

  	app.post('/sms', function(request, response) {
      	var client = twilio();
      	client.sendSms({
          	// send a text to this number
         	to:request.body.number,

           // A Twilio number you bought - see:
           // https://www.twilio.com/user/account/phone-numbers/incoming
           from:'+15558675309',

           // The body of the text message
           body: request.body.message
          
      	   }, function(error, data) {
          // Go back to the home page
          response.redirect('/');
        });
  	 });

In "views/index.ejs" aggiungere un altro modulo sotto il primo per inviare un numero e un messaggio di testo:

  	<form action="/sms" method="POST">
      	<input placeholder="Enter a phone number" name="number"/>
      	<br/>
      	<input placeholder="Enter a message to send" name="message"/>
      	<br/>
     	<input type="submit" value="Send text to the number above"/>
    </form>

Ridistribuire l'applicazione in Azure. Dovrebbe essere possibile inviare il modulo e inviare un SMS.

<a id="nextsteps" ></a> 
## Passaggi successivi

In questa esercitazione si sono apprese le nozioni di base sull'utilizzo di Node.js e Twilio per creare applicazioni di comunicazione. Questi esempi, tuttavia, offrono solo un'idea delle possibilità offerte da Twilio e Node.js. Per ulteriori informazioni sull'utilizzo di Twilio con Node.js, vedere le risorse seguenti:

* [Documentazione ufficiale del modulo][9]
* [Esercitazione sull'utilizzo di VoIP con le applicazioni Node.js][3]
* [Votr: un'applicazione di voto via SMS in tempo reale con Node.js e CouchDB (tre parti)][10]
* [Programmazione in coppia nel browser con Node.js][11]

A questo punto, non resta che sperimentare tutte le possibilità offerte dall'utilizzo di Node.js e Twilio in Azure.



[1]: https://www.twilio.com/user/account/phone-numbers/available/local
[2]: https://www.twilio.com/docs/api/twiml
[3]: http://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
[4]: http://ahoy.twilio.com/azure
[5]: http://www.windowsazure.com/en-us/develop/nodejs/tutorials/create-a-website-(mac)/
[6]: https://www.twilio.com/user/account
[7]: http://npmjs.org
[8]: http://expressjs.com
[9]: http://twilio.github.io/twilio-node/
[10]: http://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
[11]: http://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html}

[purchase_phone]: https://www.twilio.com/user/account/phone-numbers/available/local
[twiml]: https://www.twilio.com/docs/api/twiml
[signup]: http://ahoy.twilio.com/azure
[azure_new_site]: http://www.windowsazure.com/en-us/develop/nodejs/tutorials/create-a-website-(mac)/
[twilio_dashboard]: https://www.twilio.com/user/account
[npm]: http://npmjs.org
[express]: http://expressjs.com
[voipnode]: http://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
[docs]: http://twilio.github.io/twilio-node/
[votr]: http://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
[pair]: http://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
[azure-admin-console]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png
