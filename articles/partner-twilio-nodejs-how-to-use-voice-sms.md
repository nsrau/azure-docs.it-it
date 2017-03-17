---
title: "Uso di Twilio per le funzionalità voce, VoIP e SMS in Azure"
description: Informazioni su come effettuare una chiamata telefonica e inviare un SMS con il servizio API Twilio API in Azure. Gli esempi di codice sono scritti in Node.js.
services: 
documentationcenter: nodejs
author: devinrader
manager: wpickett
editor: 
ms.assetid: f558cbbd-13d2-416f-b9b1-33a99c426af9
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/25/2014
ms.author: wpickett
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: 74d2d3ca914b649b41e03fb3c2351ce6263ff9db
ms.lasthandoff: 03/07/2017


---
# <a name="using-twilio-for-voice-voip-and-sms-messaging-in-azure"></a>Uso di Twilio per le funzionalità voce, VoIP e SMS in Azure
In questa guida viene illustrato come sviluppare app che comunicano con Twilio e Node.js in Azure.

<a id="whatis"/>

## <a name="what-is-twilio"></a>Informazioni su Twilio
Twilio è una piattaforma API che consente agli sviluppatori di abilitare facilmente l'invio e la ricezione di chiamate telefoniche o SMS, nonché di incorporare funzionalità VoIP all'interno di applicazioni mobili native e basate su browser. Prima di entrare nel dettaglio, verrà illustrato brevemente il funzionamento della piattaforma.

### <a name="receiving-calls-and-text-messages"></a>Ricevere chiamate e SMS
Twilio consente agli sviluppatori di [acquistare numeri di telefono programmabili][purchase_phone] che è possibile usare per inviare e ricevere chiamate ed SMS. Quando un numero Twilio riceve una chiamata o un SMS in ingresso, invia all'applicazione Web una richiesta HTTP di tipo POST o GET, in cui vengono chieste istruzioni per la gestione della chiamata o del messaggio. Il server dell'utente risponderà alla richiesta HTTP di Twilio con [TwiML][twiml], un semplice set di tag XML che contengono istruzioni per la gestione di una chiamata o un SMS. Più avanti verranno illustrati esempi del linguaggio TwiML.

### <a name="making-calls-and-sending-text-messages"></a>Effettuare chiamate e inviare SMS
Inviando richieste HTTP all'API del servizio Web Twilio, gli sviluppatori possono inviare SMS o avviare chiamate in uscita. Per le chiamate in uscita, lo sviluppatore deve inoltre specificare un URL che restituisca le istruzioni TwiML per la gestione della chiamata in uscita dopo la connessione.

### <a name="embedding-voip-capabilities-in-ui-code-javascript-ios-or-android"></a>Incorporare funzionalità VoIP nel codice dell'interfaccia utente (JavaScript, iOS o Android)
Twilio offre un SDK lato client che consente di trasformare qualsiasi Web browser desktop, app per iOS o app per Android in un telefono VoIP. In questo articolo verrà illustrato l'utilizzo delle funzionalità VoIP nel browser. Oltre a *Twilio JavaScript SDK* in esecuzione nel browser, è necessario usare un'applicazione lato server (in questo caso un'applicazione Node.js) per inviare un "token Capability" al client JavaScript. Per altre informazioni sull'uso di VoIP con Node.js, vedere il [blog per sviluppatori di Twilio][voipnode].

<a id="signup"/>

## <a name="sign-up-for-twilio-microsoft-discount"></a>Iscriversi a Twilio (sconto Microsoft)
Prima di usare i servizi Twilio, è necessario [iscriversi per creare un account][signup]. Per i clienti di Microsoft Azure è disponibile uno sconto speciale [effettuando l'iscrizione qui][signup].

<a id="azuresite"/>

## <a name="create-and-deploy-a-nodejs-azure-website"></a>Creare e distribuire un sito Web Node.js in Azure
Si procederà ora alla creazione di un sito Web Node.js eseguito in Azure. [La documentazione ufficiale per eseguire la procedura è disponibile qui][azure_new_site]. A livello generale, la procedura è la seguente:

* Creazione di un account Azure, se non già disponibile
* Creazione di un nuovo sito Web tramite la console di amministrazione di Azure
* Aggiunta del supporto per il controllo del codice sorgente (si presuppone che sia stato usato git)
* Creazione di un file `server.js` con una semplice applicazione Web Node.js
* Distribuzione dell'applicazione in Azure

<a id="twiliomodule"/>

## <a name="configure-the-twilio-module"></a>Configurare il modulo Twilio
Si inizierà a scrivere una semplice applicazione Node.js che usa l'API Twilio. Prima di iniziare, è necessario configurare le credenziali dell'account Twilio.

### <a name="configuring-twilio-credentials-in-system-environment-variables"></a>Configurare le credenziali Twilio in variabili di ambiente di sistema
Per effettuare richieste autenticate nel back-end Twilio sono necessari il SID e il token di autorizzazione dell'account, che hanno lo stesso utilizzo del nome utente e della password impostate per l'account Twilio. Il modo più sicuro per configurare il SID e il token per l'utilizzo con il modulo Node in Azure consiste nell'usare variabili di ambiente di sistema, configurandole direttamente nella console di amministrazione di Azure.

Selezionare il sito Web Node.js e fare clic sul collegamento "CONFIGURE".  Scorrendo verso il basso verrà visualizzata un'area in cui è possibile impostare le proprietà di configurazione per l'applicazione.  Immettere le credenziali dell'account Twilio ([disponibili nella console di Twilio][twilio_console]) come illustrato, denominandole rispettivamente `TWILIO_ACCOUNT_SID` e `TWILIO_AUTH_TOKEN`:

![Console di amministrazione di Azure][azure-admin-console]

Dopo aver configurato le variabili, riavviare l'applicazione nella console di Azure.

### <a name="declaring-the-twilio-module-in-packagejson"></a>Dichiarare il modulo Twilio in package.json
Verrà ora creato un file package.json per gestire le dipendenze del modulo Node tramite [npm]. Allo stesso livello del file `server.js` creato nell'esercitazione su *Azure e Node.js*, creare un file denominato `package.json`.  All'interno del file inserire il codice seguente:

```json
{
  "name": "application-name",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "node server"
  },
  "dependencies": {
    "body-parser": "^1.16.1",
    "ejs": "^2.5.5",
    "errorhandler": "^1.5.0",
    "express": "^4.14.1",
    "morgan": "^1.8.1",
    "twilio": "^2.11.1"
  }
}
```

Questo codice dichiara il modulo Twilio come dipendenza, così come il noto [framework Web Express][express] e il motore di rendering dei modelli EJS.  A questo punto, è possibile iniziare a scrivere il codice.

<a id="makecall"/>

## <a name="make-an-outbound-call"></a>Effettuare una chiamata in uscita
Verrà ora creato un modulo molto semplice per l'esecuzione di una chiamata verso un numero scelto. Aprire `server.js` e immettere il codice seguente. Si noti che nel punto in cui nel codice è scritto "CHANGE_ME" è necessario inserire il nome del proprio sito Web di Azure:

```javascript
// Module dependencies
const express = require('express');
const path = require('path');
const http = require('http');
const twilio = require('twilio');
const logger = require('morgan');
const bodyParser = require('body-parser');
const errorHandler = require('errorhandler');
const accountSid = process.env.TWILIO_ACCOUNT_SID;
const authToken = process.env.TWILIO_AUTH_TOKEN;
// Create Express web application
const app = express();

// Express configuration
app.set('port', process.env.PORT || 3000);
app.set('views', __dirname + '/views');
app.set('view engine', 'ejs');
app.use(logger('tiny'));
app.use(bodyParser.urlencoded({ extended: false }))
app.use(bodyParser.json())
app.use(express.static(path.join(__dirname, 'public')));

if (app.get('env') !== 'production') {
  app.use(errorHandler());
}

// Render an HTML user interface for the application's home page
app.get('/', (request, response) => response.render('index'));

// Handle the form POST to place a call
app.post('/call', (request, response) => {
  var client = twilio(accountSid, authToken);

  client.makeCall({
    // make a call to this number
    to:request.body.number,

    // Change to a Twilio number you bought - see:
    // https://www.twilio.com/console/phone-numbers/incoming
    from:'+15558675309',

    // A URL in our app which generates TwiML
    // Change "CHANGE_ME" to your app's name
    url:'https://CHANGE_ME.azurewebsites.net/outbound_call'
  }, () => {
      // Go back to the home page
      response.redirect('/');
  });
});

// Generate TwiML to handle an outbound call
app.post('/outbound_call', (request, response) => {
  var twiml = new twilio.TwimlResponse();

  // Say a message to the call's receiver
  twiml.say('hello - thanks for checking out Twilio and Azure', {
      voice:'woman'
  });

  response.set('Content-Type', 'text/xml');
  response.send(twiml.toString());
});

// Start server
app.listen(app.get('port'), function(){
  console.log(`Express server listening on port ${app.get('port')}`);
});
```

Creare quindi una directory denominata `views`, all'interno della quale creare un file denominato `index.ejs` con il contenuto seguente:

```html
<!DOCTYPE html>
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
```

Distribuire ora il sito Web in Azure e aprire la Home page. Dovrebbe essere possibile immettere il proprio numero di telefono nel campo di testo e ricevere una chiamata dal numero Twilio.

<a id="sendmessage"/>

## <a name="send-an-sms-message"></a>Inviare un messaggio SMS
Verranno ora create un'interfaccia utente e la logica di gestione dei moduli per l'invio di un SMS. Aprire il file `server.js` e aggiungere il codice seguente dopo l'ultima chiamata a `app.post`:

```javascript
app.post('/sms', (request, response) => {
  const client = twilio(accountSid, authToken);

  client.sendSms({
      // send a text to this number
      to:request.body.number,

      // A Twilio number you bought - see:
      // https://www.twilio.com/console/phone-numbers/incoming
      from:'+15558675309',

      // The body of the text message
      body: request.body.message

  }, () => {
      // Go back to the home page
      response.redirect('/');
  });
});
```

In `views/index.ejs` aggiungere un altro modulo sotto il primo per inviare un numero e un messaggio di testo:

```html
<form action="/sms" method="POST">
  <input placeholder="Enter a phone number" name="number"/>
  <br/>
  <input placeholder="Enter a message to send" name="message"/>
  <br/>
  <input type="submit" value="Send text to the number above"/>
</form>
```

Ridistribuire l'applicazione in Azure. Dovrebbe essere possibile inviare il modulo e inviare un SMS (a se stessi o a un amico).

<a id="nextsteps"/>

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si sono apprese le nozioni di base sull'utilizzo di Node.js e Twilio per creare applicazioni di comunicazione. Questi esempi, tuttavia, offrono solo un'idea delle possibilità offerte da Twilio e Node.js. Per altre informazioni sull'uso di Twilio con Node.js, vedere le risorse seguenti:

* [Documentazione ufficiale del modulo][docs]
* [Tutorial on VoIP with node.js applications][voipnode] (Esercitazione sull'uso di VoIP con le applicazioni node.js)
* [Votr - a real-time SMS voting application with node.js and CouchDB (three parts)][votr] (Votr: un'applicazione di voto via SMS in tempo reale con node.js e CouchDB (tre parti))
* [Pair programming in the browser with node.js][pair] (Programmazione in coppia nel browser con node.js)

A questo punto, non resta che sperimentare tutte le possibilità offerte dall'uso di Node.js e Twilio in Azure.

[purchase_phone]: https://www.twilio.com/console/phone-numbers/search
[twiml]: https://www.twilio.com/docs/api/twiml
[signup]: http://ahoy.twilio.com/azure
[azure_new_site]: /app-service-web/web-sites-nodejs-develop-deploy-mac.md
[twilio_console]: https://www.twilio.com/console
[npm]: http://npmjs.org
[express]: http://expressjs.com
[voipnode]: http://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
[docs]: http://twilio.github.io/twilio-node/
[votr]: http://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
[pair]: http://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
[azure-admin-console]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png

