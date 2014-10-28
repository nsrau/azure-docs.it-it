<properties linkid="develop-mobile-tutorials-twilio-for-voice-and-sms" pageTitle="Use Twilio for Voice and SMS Capabilities | Mobile Dev Center" metaKeywords="" description="Learn how to perform common tasks using the Twilio API with Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="How to use Twilio for voice and SMS capabilities from Mobile Services" authors="MicrosoftHelp@twilio.com" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com"></tags>

# Come usare Twilio per le funzionalità voce ed SMS da Servizi mobili

Questo argomento descrive come eseguire attività comuni usando l'API Twilio con Servizi mobili di Azure. In questa esercitazione si apprenderà come creare script di API personalizzate che utilizzano l'API Twilio per iniziare una chiamata telefonica e inviare un messaggio SMS (Short Message Service).

## <span id="WhatIs"></span></a>Informazioni su Twilio

Twilio è una tecnologia all'avanguardia per le comunicazioni aziendali che consente agli sviluppatori di incorporare funzionalità voce, VoIP e di messaggistica nelle applicazioni. Consente di virtualizzare tutta l'infrastruttura necessaria in un ambiente globale basato su cloud, esponendolo attraverso la piattaforma API per le comunicazioni Twilio. Le applicazioni sono scalabili e facili da compilare. Offre flessibilità, grazie a un modello di prezzi con pagamento al consumo, e l'affidabilità del cloud.

**Twilio Voice** consente alle applicazioni di effettuare e ricevere chiamate telefoniche. **Twilio SMS** consente alle applicazioni di inviare e ricevere SMS. **Twilio Client** consente di effettuare chiamate VoIP da qualsiasi telefono, tablet o browser e supporta WebRTC.

## <span id="Pricing"></span></a>Prezzi Twilio e offerte speciali

Per i clienti di Azure è disponibile un'[offerta speciale][offerta speciale]: credito Twilio aggiuntivo di $ 10 all'aggiornamento dell'account Twilio. Il credito Twilio può essere applicato a qualsiasi utilizzo di Twilio ($ 10 di credito equivalgono all'invio di 1.000 SMS o a 1.000 minuti voce per le chiamate in entrata, a seconda della località del numero di telefono, del messaggio o della destinazione della chiamata). Per riscattare il credito Twilio e iniziare a usare il servizio, visitare la pagina all'indirizzo [ahoy.twilio.com/azure][offerta speciale].

Twilio è un servizio con pagamento in base al consumo. Non prevede spese iniziali ed è possibile chiudere l'account in qualsiasi momento. Per altre informazioni, vedere la pagina relativa ai [prezzi di Twilio][prezzi di Twilio].

## <span id="Concepts"></span></a>Concetti

L'API Twilio è un'API RESTful che fornisce funzionalità voce ed SMS per le applicazioni. Le librerie client sono disponibili in più lingue. Per un elenco, vedere la pagina relativa alle [librerie dell'API Twilio][librerie dell'API Twilio]. Sono disponibili esercitazioni aggiuntive relative all'utilizzo di Twilio in qualsiasi applicazione di Azure scritta in [.NET][.NET], [node.js][node.js], [Java][Java], [PHP][PHP], [Python][Python] o [Ruby][Ruby].

I concetti principali dell'API Twilio sono costituiti dai verbi Twilio e dal linguaggio di markup Twilio (Twilio Markup Language, TwiML).

### <span id="Verbs"></span></a>Verbi Twilio

Nell'API vengono utilizzati i verbi Twilio: il verbo **\<Say\>**, ad esempio, indica a Twilio di recapitare un messaggio acustico in una chiamata.

Di seguito è riportato un elenco dei verbi Twilio. Per altre informazioni su altri verbi e funzionalità, vedere la [documentazione relativa a Twilio Markup Language][documentazione relativa a Twilio Markup Language].

-   **\<Dial\>**: connette il chiamante a un altro telefono.
-   **\<Gather\>**: raccoglie i numeri digitati dal chiamante sulla tastiera del telefono.
-   **\<Hangup\>**: termina una chiamata.
-   **\<Play\>**: riproduce un file audio.
-   **\<Pause\>**: attende in modo silenzioso per un numero di secondi specificato.
-   **\<Record\>**: registra la voce del chiamante e restituisce l'URL del file contenente la registrazione.
-   **\<Redirect\>**: trasferisce il controllo di una chiamata o di un SMS al codice TwiML presso un URL diverso.
-   **\<Reject\>**: rifiuta una chiamata in arrivo al numero Twilio senza alcun addebito
-   **\<Say\>**: effettua la sintesi vocale del testo durante una chiamata.
-   **\<Sms\>**: invia un messaggio SMS.

### <span id="TwiML"></span></a>TwiML

TwiML è un set di istruzioni basate su XML e sui verbi Twilio che indicano a Twilio come elaborare una chiamata o un SMS.

Ad esempio, il codice TwiML seguente effettua la sintesi vocale del testo **Hello World**.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Quando l'applicazione chiama l'API Twilio, uno dei parametri dell'API è l'URL che restituisce la risposta TwiML. Ai fini dello sviluppo, è possibile usare gli URL offerti da Twilio per fornire le risposte TwiML utilizzate dalle applicazioni. È inoltre possibile ospitare gli URL per produrre le risposte TwiML oppure usare l'oggetto **TwiMLResponse**.

Per altre informazioni sui verbi Twilio, i relativi attributi e il codice TwiML, vedere [TwiML][documentazione relativa a Twilio Markup Language]. Per altre informazioni sull'API Twilio, vedere la pagina relativa all'[API Twilio][API Twilio].

## <span id="CreateAccount"></span></a>Creazione di un account Twilio

Se si desidera creare un account Twilio, iscriversi nella pagina relativa alla [registrazione gratuita di Twilio][registrazione gratuita di Twilio]. È possibile iniziare con un account gratuito ed eseguire l'aggiornamento in un secondo momento.

Quando si effettua l'iscrizione a un account Twilio, si riceverà un ID account e un token di autenticazione. Entrambe queste informazioni sono necessarie per effettuare chiamate all'API Twilio. Per prevenire accessi autorizzati all'account, conservare il token di autenticazione in un luogo sicuro. L'ID account e il token di autorizzazione sono visualizzabili nella [pagina dell'account Twilio][pagina dell'account Twilio], rispettivamente nei campi **ACCOUNT SID** e **AUTH TOKEN**.

## <span id="VerifyPhoneNumbers"></span></a>Verifica dei numeri di telefono

I numeri di telefono utilizzati dall'account devono essere verificati con Twilio. Ad esempio, se si desidera effettuare chiamate in uscita, il numero di telefono deve essere verificato come ID chiamante in uscita con Twilio. Allo stesso modo, se si desidera che un numero di telefono riceva messaggi SMS, il numero di telefono di destinazione deve essere verificato con Twilio. Per informazioni su come verificare un numero di telefono, vedere la sezione relativa alla [gestione dei numeri][gestione dei numeri]. Parte del codice illustrato di seguito si basa su numeri di telefono che sarà necessario verificare con Twilio.

In alternativa, anziché usare un numero esistente per le applicazioni, è possibile acquistare un numero di telefono Twilio. Per informazioni sull'acquisto di un numero di telefono Twilio, vedere la [guida relativa ai numeri di telefono Twilio][guida relativa ai numeri di telefono Twilio].

## <span id="create_app"></span></a>Creazione di un servizio mobile

Un servizio mobile che ospita un'applicazione compatibile con Twilio non è diverso da qualsiasi altro servizio mobile. È sufficiente aggiungere la libreria node.js di Twilio, in modo da potervi fare riferimento dagli script di API personalizzate del servizio mobile. Per informazioni sulla creazione di un servizio mobile iniziale, vedere [Introduzione a Servizi mobili][Introduzione a Servizi mobili].

## <span id="VerifyPhoneNumbers"></span></a>Configurazione del servizio mobile per l'uso della libreria Node.js di Twilio

Twilio fornisce una libreria Node.js che copre vari aspetti di Twilio per fornire modi semplici e pratici per interagire con l'API REST di Twilio e il client Twilio per generare risposte TwiML.

Per usare la libreria Node.js di Twilio nel servizio mobile, è necessario avvalersi del supporto per il modulo npm di Servizi mobili, archiviando a tale scopo gli script nel controllo del codice sorgente. Nell'esercitazione [Archiviazione di script nel controllo del codice sorgente][Archiviazione di script nel controllo del codice sorgente] viene illustrato come configurare per la prima volta il controllo del codice sorgente in Servizi mobili e come archiviare gli script del server in un repository Git.

Dopo avere configurato il controllo del codice sorgente per il servizio mobile, aprire la scheda Configure nel dashboard del servizio mobile, quindi individuare e copiare l'URL di Git.

Incollare tale URL in un browser e sostituire il nome del repository con */DebugConsole/index.html*.

Ad esempio, modificare:

    https://twilioSample.scm.azure-mobile.net/twilioSample.git

to:

    https://twilioSample.scm.azure-mobile.net/DebugConsole

Quando richiesto, immettere le credenziali utilizzate durante la configurazione del controllo del codice sorgente per il servizio. Dopo avere effettuato l'accesso, sarà possibile visualizzare la console Servizio mobile di Azure.

![Console Servizio mobile][Console Servizio mobile]

Nella console cambiare la directory, specificando la cartella scripts:

    cd site\wwwroot\App_Data\config\scripts

Selezionare la cartella api, quindi installare il modulo del nodo Twilio eseguendo il comando seguente:

    npm install twilio

È ora possibile fare riferimento alla libreria Twilio e utilizzarla negli script delle API personalizzate e delle tabelle.

## <span id="howto_make_call"></span></a>Procedura: Effettuare una chiamata in uscita

Lo script seguente illustra come iniziare una chiamata in uscita dal servizio mobile usando la funzione **makeCall**. Questo codice utilizza inoltre un sito fornito da Twilio per restituire la risposta TwiML (Twilio Markup Language). Sostituire i valori per i numeri di telefono **From** e **To** e assicurarsi di verificare il numero di telefono in **From** per l'account Twilio prima di eseguire il codice.

    var twilio = require('twilio');

    exports.post = function(request, response) {

        var client = new twilio.RestClient('[ACCOUNT_SID]', 'AUTH_TOKEN');

        client.makeCall({
            to:'+16515556677', 
            from: '+14506667788',
            url: 'http://www.example.com/twiml.php' 

        }, function(err, responseData) {
            console.log(responseData.from); 
            response.send(200, '');
        });
    };

Per altre informazioni sui parametri passati alla funzione **client.makeCall**, vedere [][]<http://www.twilio.com/docs/api/rest/making-calls></a>.

Come indicato in precedenza, questo codice utilizza un sito fornito da Twilio per restituire la risposta TwiML. Per fornire la risposta TwiML, è tuttavia possibile usare il proprio sito. Per altre informazioni, vedere [Procedura: Fornire risposte TwiML dal proprio sito Web][Procedura: Fornire risposte TwiML dal proprio sito Web].

## <span id="howto_send_sms"></span></a>Procedura: Inviare un messaggio SMS

Nel codice seguente viene illustrato come inviare un messaggio SMS tramite la funzione **sendSms**. Il numero in **From** per l'invio di messaggi SMS con gli account di valutazione è fornito da Twilio. Il numero in **To** deve essere verificato per l'account Twilio prima di eseguire il codice.

    var twilio = require('twilio');

    exports.post = function(request, response) {

        var client = new twilio.RestClient('[ACCOUNT_SID]', 'AUTH_TOKEN');

        client.sendSms({
            to:'[]',
            from:'[]',
            body:'ahoy hoy! Testing Twilio and node.js'
        }, function(error, message) {

            // The "error" variable will contain error information, if any.
            // If the request was successful, this value will be "false"
            if (!error) {
                console.log('Success! The SID for this SMS message is: ' + message.sid);
                console.log('Message sent on: ' + message.dateCreated);
            }
            else {
                console.log('Oops! There was an error.');
            }
            response.send(200, { error : error } );
        });
    };

## <span id="howto_provide_twiml_responses"></span></a>Procedura: Fornire risposte TwiML dal proprio sito Web

Quando l'applicazione avvia una chiamata all'API Twilio, ad esempio tramite il metodo client.InitiateOutboundCall, Twilio invia la richiesta a un URL che deve restituire una risposta TwiML. Nell'esempio illustrato in Procedura: Effettuare una chiamata in uscita viene usato l'URL fornito da Twilio <http://twimlets.com/message> per restituire la risposta.

<div class="dev-callout">
<b>Nota</b>
<p>Poich&eacute; TwiML &egrave; progettato per essere usato da servizi Web, &egrave; possibile visualizzare il codice TwiML nel browser. Ad esempio, fare clic su <a href="http://twimlets.com/message">twimlet_message_url</a> per visualizzare un elemento &lt;Response&gt; vuoto oppure fare clic su <a href="http://twimlets.com/message?Message%5B0%5D=Hello%20World">twimlet_message_url_hello_world</a> per visualizzare un elemento &lt;Response&gt; contenente un elemento &lt;Say&gt;.</p>
</div>

Anziché usare l'URL fornito da Twilio, è possibile creare un sito Web personalizzato che restituisce risposte HTTP. Il sito può essere creato in un linguaggio qualsiasi purché restituisca risposte HTTP. In questo argomento si presuppone che l'URL verrà ospitato da un gestore generico ASP.NET.

Lo script seguente crea una risposta TwiML che pronuncia Hello World nella chiamata.

    var twilio = require('twilio');

    exports.post = function(request, response) {
        var resp = new twilio.TwimlResponse();
        resp.say({voice:'woman'}, 'ahoy hoy! Testing Twilio and node.js');
        response.set('Content-Type', 'text/xml');
        response.send(200, resp.toString());
    };

Per altre informazioni su TwiML, vedere [][1]<https://www.twilio.com/docs/api/twiml></a>.

Dopo avere configurato un modo per fornire risposte TwiML, è possibile passare l'URL nel metodo **client.makeCall**, come illustrato nell'esempio di codice seguente:

    var twilio = require('twilio');

    exports.post = function(request, response) {

        var client = new twilio.RestClient('[ACCOUNT_SID]', 'AUTH_TOKEN');

        client.makeCall({
            to:'+16515556677', 
            from: '+14506667788',
            url: 'http://<your_mobile_service>.azure-mobile.net/api/makeCall' 

        }, function(err, responseData) {

            console.log(responseData.from);
            response.send(200, '');
        });
    };

[WACOM.INCLUDE [twilio\_additional\_services\_and\_next\_steps][twilio\_additional\_services\_and\_next\_steps]]

  [offerta speciale]: http://ahoy.twilio.com/azure
  [prezzi di Twilio]: http://www.twilio.com/pricing
  [librerie dell'API Twilio]: https://www.twilio.com/docs/libraries
  [.NET]: /it-it/develop/net/how-to-guides/twilio-voice-and-sms-service/
  [node.js]: /it-it/develop/nodejs/how-to-guides/twilio-voice-and-sms-service/
  [Java]: /it-it/develop/java/how-to-guides/twilio-voice-and-sms-service/
  [PHP]: /it-it/develop/php/how-to-guides/twilio-voice-and-sms-service/
  [Python]: /it-it/develop/python/how-to-guides/twilio-voice-and-sms-service/
  [Ruby]: /it-it/develop/ruby/how-to-guides/twilio-voice-and-sms-service/
  [documentazione relativa a Twilio Markup Language]: http://www.twilio.com/docs/api/twiml
  [API Twilio]: http://www.twilio.com/api
  [registrazione gratuita di Twilio]: https://www.twilio.com/try-twilio
  [pagina dell'account Twilio]: https://www.twilio.com/user/account
  [gestione dei numeri]: https://www.twilio.com/user/account/phone-numbers/verified#
  [guida relativa ai numeri di telefono Twilio]: https://www.twilio.com/help/faq/phone-numbers
  [Introduzione a Servizi mobili]: http://www.windowsazure.com/it-it/develop/mobile/tutorials/get-started/
  [Archiviazione di script nel controllo del codice sorgente]: http://www.windowsazure.com/it-it/develop/mobile/tutorials/store-scripts-in-source-control/
  [Console Servizio mobile]: ./media/partner-twilio-mobile-services-how-to-use-voice-sms/twilio-kuduconsole.png
  []: http://www.twilio.com/docs/api/rest/making-calls
  [Procedura: Fornire risposte TwiML dal proprio sito Web]: #howto_provide_twiml_responses
  [twimlet\_message\_url]: http://twimlets.com/message
  [twimlet\_message\_url\_hello\_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
  [1]: https://www.twilio.com/docs/api/twiml
  [twilio\_additional\_services\_and\_next\_steps]: ../includes/twilio_additional_services_and_next_steps.md
