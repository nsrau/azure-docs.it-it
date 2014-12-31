<properties pageTitle="Usare Twilio per le funzionalità voce e SMS | Mobile Dev Center" metaKeywords="" description="Learn how to perform common tasks using the Twilio API with Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="How to use Twilio for voice and SMS capabilities from Mobile Services" authors="MicrosoftHelp@twilio.com" solutions="" manager="twilio" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/25/2014" ms.author="MicrosoftHelp@twilio.com" />


<h1>Come usare Twilio per le funzionalità voce e SMS da Servizi mobili</h1>

Questo argomento illustra come eseguire attività comuni usando l'API Twilio con Servizi mobili di Azure. In questa esercitazione si apprenderà come creare script di API personalizzate che usano l'API Twilio per iniziare una chiamata telefonica e inviare un messaggio SMS (Short Message Service). 

<h2><a id="WhatIs"></a>Informazioni su Twilio</h2>
Twilio è una tecnologia all'avanguardia per le comunicazioni aziendali che consente agli sviluppatori di incorporare funzionalità voce, VoIP e di messaggistica nelle applicazioni. Consente di virtualizzare tutta l'infrastruttura necessaria in un ambiente globale basato sul cloud, esponendolo attraverso la piattaforma API per le comunicazioni Twilio. Le applicazioni sono scalabili e facili da compilare. Offre flessibilità, grazie a un modello di prezzi con pagamento al consumo, e l'affidabilità del cloud.

**Twilio Voice** consente alle applicazioni di effettuare e ricevere chiamate telefoniche. **Twilio SMS** consente alle applicazioni di inviare e ricevere SMS. **Twilio Client** consente di effettuare chiamate VoIP da qualsiasi telefono, tablet o browser e supporta WebRTC.

<h2><a id="Pricing"></a>Prezzi Twilio e offerte speciali</h2>
I clienti di Azure ricevono un'[offerta speciale][special_offer]: $ 10 di credito Twilio all'aggiornamento dell'account Twilio. Il credito Twilio può essere applicato a qualsiasi uso di Twilio ($ 10 di credito equivalgono all'invio di 1.000 SMS o a 1.000 minuti voce per le chiamate in entrata, a seconda della località del numero di telefono, del messaggio o della destinazione della chiamata). Per riscattare il credito Twilio e iniziare a usare il servizio, visitare la pagina all'indirizzo [ahoy.twilio.com/azure][special_offer].

Twilio è un servizio con pagamento in base al consumo. Non prevede spese iniziali ed è possibile chiudere l'account in qualsiasi momento. Per altre informazioni, vedere la pagina relativa ai [prezzi di Twilio][twilio_pricing].  

<h2><a id="Concepts"></a>Concetti</h2>
L'API Twilio è un'API RESTful che fornisce funzionalità voce e SMS per le applicazioni. Le librerie client sono disponibili in più lingue. Per un elenco, vedere la pagina relativa alle [librerie dell'API Twilio] [twilio_libraries].  Sono disponibili esercitazioni aggiuntive relative all'uso di Twilio in qualsiasi applicazione di Azure scritta in [.NET][azure_twilio_howto_dotnet], [node.js][azure_twilio_howto_node], [Java][azure_twilio_howto_java], [PHP][azure_twilio_howto_php], [Python][azure_twilio_howto_python] o [Ruby][azure_twilio_howto_ruby].

I concetti principali dell'API Twilio sono costituiti dai verbi Twilio e dal linguaggio di markup Twilio (Twilio Markup Language, TwiML).

<h3><a id="Verbs"></a>Verbi Twilio</h3>
Nell'API vengono usati i verbi Twilio: il verbo **&lt;Say&gt;**, ad esempio, indica a Twilio di recapitare un messaggio acustico in una chiamata. 

Di seguito è riportato un elenco dei verbi Twilio.  Per altre informazioni su altri verbi e funzionalità, vedere la [documentazione relativa a Twilio Markup Language](http://www.twilio.com/docs/api/twiml).

* **&lt;Dial&gt;**: connette il chiamante a un altro telefono.
* **&lt;Gather&gt;**: raccoglie i numeri digitati dal chiamante sulla tastiera del telefono.
* **&lt;Hangup&gt;**: termina una chiamata.
* **&lt;Play&gt;**: riproduce un file audio.
* **&lt;Pause&gt;**: attende in modo silenzioso per un numero di secondi specificato.
* **&lt;Record&gt;**: registra la voce del chiamante e restituisce l'URL del file contenente la registrazione.
* **&lt;Redirect&gt;**: trasferisce il controllo di una chiamata o di un SMS al codice TwiML presso un URL diverso.
* **&lt;Reject&gt;**: rifiuta una chiamata in arrivo al numero Twilio senza alcun addebito.
* **&lt;Say&gt;**: effettua la sintesi vocale del testo durante una chiamata.
* **&lt;Sms&gt;**: invia un messaggio SMS.

<h3> <a id="TwiML"></a>TwiML</h3>
TwiML è un set di istruzioni basate su XML e sui verbi Twilio che indicano a Twilio come elaborare una chiamata o un SMS.

Ad esempio, il codice TwiML seguente effettua la sintesi vocale del testo **Hello World**.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Quando l'applicazione chiama l'API Twilio, uno dei parametri dell'API è l'URL che restituisce la risposta TwiML. Ai fini dello sviluppo, è possibile usare gli URL offerti da Twilio per fornire le risposte TwiML usate dalle applicazioni. È anche possibile ospitare gli URL per produrre le risposte TwiML oppure usare l'oggetto **TwiMLResponse** object.

Per altre informazioni sui verbi Twilio, i relativi attributi e il codice TwiML, vedere [TwiML][twiml]. Per altre informazioni sull'API Twilio, vedere la pagina relativa all'[API Twilio][twilio_api].

<h2><a id="CreateAccount"></a>Creare un account Twilio</h2>
Se si vuole creare un account Twilio, effettuare l'iscrizione tramite la pagina relativa alla [registrazione gratuita di Twilio][try_twilio]. È possibile iniziare con un account gratuito ed eseguire l'aggiornamento in un secondo momento.

Quando si effettua l'iscrizione a un account Twilio, si riceverà un ID account e un token di autenticazione. Entrambe queste informazioni sono necessarie per effettuare chiamate all'API Twilio. Per prevenire accessi autorizzati all'account, conservare il token di autenticazione in un luogo sicuro. L'ID account e il token di autenticazione sono visualizzabili nella [pagina dell'account Twilio][twilio_account], rispettivamente nei campi **ACCOUNT SID** e **AUTH TOKEN**.

<h2><a id="create_app"></a>Creare un servizio mobile</h2>
Un servizio mobile che ospita un'applicazione compatibile con Twilio non è diverso da qualsiasi altro servizio mobile. È sufficiente aggiungere la libreria node.js di Twilio, in modo da potervi fare riferimento dagli script di API personalizzate del servizio mobile. Per informazioni sulla creazione di un servizio mobile iniziale, vedere [Introduzione a Servizi mobili](/it-it/documentation/articles/mobile-services-ios-get-started/)

<h2><a id="ConfigureMobileService"></a>Configurare il servizio mobile per l'uso della libreria Node.js di Twilio</h2>
Twilio fornisce una libreria Node.js che copre vari aspetti di Twilio per fornire modi semplici e pratici per interagire con l'API REST di Twilio e il client Twilio per generare risposte TwiML.

Per usare la libreria Node.js di Twilio nel servizio mobile, è necessario avvalersi del supporto per il modulo npm di Servizi mobili, archiviando a tale scopo gli script nel controllo del codice sorgente. 

1. Completare l'esercitazione [Archiviare script nel controllo del codice sorgente](/it-it/documentation/articles/mobile-services-store-scripts-source-control/) che illustra come configurare il controllo del codice sorgente per Servizi mobili e come archiviare gli script del server in un repository Git.

2. Dopo avere configurato il controllo del codice sorgente per il servizio mobile, aprire il repository nel computer locale, selezionare la sottocartella `\services`, aprire il file package.json in un editor di testo e aggiungere il campo seguente all'oggetto **dependencies**:

		"twilio": "~1.7.0"
 
3. Dopo avere aggiunto il riferimento al pacchetto Twilio all'oggetto **dependencies**, il file package.json sarà simile al seguente:

		{
		  "name": "todolist",
		  "version": "1.0.0",
		  "description": "todolist - hosted on Windows Azure Mobile Services",
		  "main": "server.js",
		  "engines": {
		    "node": ">= 0.8.19"
		  },
		  "dependencies": {
			"twilio": "~1.7.0" 
		  },
		  "devDependencies": {},
		  "scripts": {},
		  "author": "unknown",
		  "licenses": [],
		  "keywords":[]
		}

	>[WACOM.NOTE]La dipendenza per Twilio deve essere aggiunta come `"twilio": "~1.7.0"`, con un carattere (~). Un riferimento con l'accento circonflesso (^) non è supportato. 

4. Eseguire il commit dell'aggiornamento del file ed effettuarne il push nel servizio mobile.

	Questo aggiornamento al file package.json riavvierà il servizio mobile.
	
A questo punto il servizio mobile installa e carica il pacchetto Twilio in modo da poter fare riferimento alla libreria Twilio e usarla negli script delle API personalizzate e delle tabelle.

<h2><a id="howto_make_call"></a>Procedura: Effettuare una chiamata in uscita</h2>
Lo script seguente illustra come iniziare una chiamata in uscita dal servizio mobile usando la funzione **makeCall**. Questo codice usa inoltre un sito fornito da Twilio per restituire la risposta TwiML (Twilio Markup Language). Sostituire i valori per i numeri di telefono **From** e **To** e assicurarsi di verificare il numero di telefono in **From** per l'account Twilio prima di eseguire il codice.

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

Per altre informazioni sui parametri passati alla funzione **client.makeCall**, vedere [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Come indicato in precedenza, questo codice usa un sito fornito da Twilio per restituire la risposta TwiML. Per fornire la risposta TwiML, è tuttavia possibile usare il proprio sito. Per altre informazioni, vedere [Procedura: Fornire risposte TwiML dal proprio sito Web](#howto_provide_twiml_responses).

<h2><a id="howto_send_sms"></a>Procedura: Inviare un messaggio SMS</h2>
Il codice seguente illustra come inviare un messaggio SMS tramite la funzione **sendSms**. Il numero in **From** per l'invio di messaggi SMS con gli account di valutazione è fornito da Twilio. Il numero in **To** deve essere verificato per l'account Twilio prima di eseguire il codice.

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


<h2><a id="howto_provide_twiml_responses"></a>Procedura: Fornire risposte TwiML dal proprio sito Web</h2>

Quando l'applicazione avvia una chiamata all'API Twilio, ad esempio tramite il metodo client.InitiateOutboundCall, Twilio invia la richiesta a un URL che deve restituire una risposta TwiML. Nell'esempio illustrato in Procedura: Effettuare una chiamata in uscita viene usato l'URL fornito da Twilio http://twimlets.com/message per restituire la risposta.

<div class="dev-callout">
<b>Nota</b>
<p>Poiché TwiML è progettato per essere usato da servizi Web, è possibile visualizzare il codice TwiML nel browser. Ad esempio, fare clic su <a href="http://twimlets.com/message">twimlet_message_url</a> per visualizzare un elemento &lt;Response&gt; vuoto oppure fare clic su <a href="http://twimlets.com/message?Message%5B0%5D=Hello%20World">twimlet_message_url_hello_world</a> per visualizzare un elemento &lt;Response&gt; contenente un elemento &lt;Say&gt;.</p>
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

Per altre informazioni su TwiML, vedere [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml).

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

[WACOM.INCLUDE [twilio_additional_services_and_next_steps](../includes/twilio_additional_services_and_next_steps.md)]


[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls

[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#


[azure_twilio_howto_dotnet]: /it-it/develop/net/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_java]: /it-it/develop/java/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_node]: /it-it/develop/nodejs/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_ruby]: /it-it/develop/ruby/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_python]: /it-it/develop/python/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_php]: /it-it/develop/php/how-to-guides/twilio-voice-and-sms-service/

<!--HONumber=35_1-->
