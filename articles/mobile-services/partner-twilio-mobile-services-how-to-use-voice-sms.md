<properties
	pageTitle="Usare Twilio per le funzionalità voce ed SMS in Java | Microsoft Azure"
	description="Informazioni su come eseguire attività comuni usando l'API Twilio con Servizi mobili di Azure."
	services="mobile-services"
	documentationCenter=""
	authors="devinrader"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="03/03/2016"
	ms.author="glenga"/>


# Come utilizzare Twilio per le funzionalità voce ed SMS da Servizi mobili

In questo argomento viene illustrato come eseguire attività comuni utilizzando l'API Twilio con Servizi mobili di Azure. In questa esercitazione si apprenderà come creare script di API personalizzate che utilizzano l'API Twilio per iniziare una chiamata telefonica e inviare un messaggio SMS (Short Message Service).

## <a id="WhatIs"></a>Informazioni su Twilio
Twilio è una tecnologia all'avanguardia per le comunicazioni aziendali che consente agli sviluppatori di incorporare funzionalità voce, VoIP e di messaggistica nelle applicazioni. Consente di virtualizzare tutta l'infrastruttura necessaria in un ambiente globale basato su cloud, esponendolo attraverso la piattaforma API per le comunicazioni Twilio. Le applicazioni sono scalabili e facili da compilare. Offre flessibilità, grazie a un modello di prezzi con pagamento al consumo, e l'affidabilità del cloud.

**Twilio Voice** consente alle applicazioni di effettuare e ricevere chiamate telefoniche. **Twilio SMS** consente alle applicazioni di inviare e ricevere SMS. **Twilio Client** consente di effettuare chiamate VoIP da qualsiasi telefono, tablet o browser e supporta WebRTC.

## <a id="Pricing"></a>Prezzi e offerte speciali di Twilio
I clienti di Azure riceveranno un'[offerta speciale][special_offer]\: $ 10 di credito Twilio all'aggiornamento dell'account Twilio. Il credito Twilio può essere applicato a qualsiasi utilizzo di Twilio ($ 10 di credito equivalgono all'invio di 1.000 SMS o a 1.000 minuti voce per le chiamate in entrata, a seconda della località del numero di telefono, del messaggio o della destinazione della chiamata). Per riscattare il credito Twilio e iniziare a utilizzare il servizio, visitare la pagina all'indirizzo [ahoy.twilio.com/azure][special_offer].

Twilio è un servizio con pagamento in base al consumo. Non prevede spese iniziali ed è possibile chiudere l'account in qualsiasi momento. Per altre informazioni, vedere la pagina relativa ai [prezzi di Twilio][twilio_pricing].

## <a id="Concepts"></a>Concetti
L'API Twilio è un'API RESTful che fornisce funzionalità voce ed SMS per le applicazioni. Le librerie client sono disponibili in più lingue. Per un elenco, vedere la pagina relativa alle [librerie dell'API Twilio][twilio_libraries]. Sono disponibili esercitazioni aggiuntive relative all'utilizzo di Twilio in qualsiasi applicazione di Azure scritta in [.NET][azure_twilio_howto_dotnet], [node.js][azure_twilio_howto_node], [Java][azure_twilio_howto_java], [PHP][azure_twilio_howto_php], [Python][azure_twilio_howto_python] o [Ruby][azure_twilio_howto_ruby].

I concetti principali dell'API Twilio sono costituiti dai verbi Twilio e dal linguaggio di markup Twilio (Twilio Markup Language, TwiML).

### <a id="Verbs"></a>Verbi Twilio
Nell'API vengono usati i verbi Twilio. Il verbo **&lt;Say&gt;**, ad esempio, indica a Twilio di fornire un messaggio acustico per una chiamata.

Di seguito è riportato un elenco dei verbi Twilio. Per altre informazioni su altri verbi e funzionalità, vedere la [documentazione relativa a Twilio Markup Language](http://www.twilio.com/docs/api/twiml).

* **&lt;Dial&gt;**: connette il chiamante a un altro telefono.
* **&lt;Gather&gt;**: raccoglie i numeri digitati dal chiamante sul tastierino del telefono.
* **&lt;Hangup&gt;**: termina una chiamata.
* **&lt;Play&gt;**: riproduce un file audio.
* **&lt;Pause&gt;**: attende in modo silenzioso per un numero di secondi specificato.
* **&lt;Record&gt;**: registra la voce del chiamante e restituisce l'URL di un file che contiene la registrazione.
* **&lt;Redirect&gt;**: trasferisce il controllo di una chiamata o un SMS al codice TwiML in un URL diverso.
* **&lt;Reject&gt;**: rifiuta una chiamata in arrivo al numero Twilio senza alcun addebito.
* **&lt;Say&gt;**: effettua la sintesi vocale del testo durante una chiamata.
* **&lt;Sms&gt;**: invia un messaggio SMS.

### <a id="TwiML"></a>TwiML
TwiML è un set di istruzioni basate su XML e sui verbi Twilio che indicano a Twilio come elaborare una chiamata o un SMS.

Ad esempio, il codice TwiML seguente effettua la sintesi vocale del testo **Hello World**.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Quando l'applicazione chiama l'API Twilio, uno dei parametri dell'API è l'URL che restituisce la risposta TwiML. Ai fini dello sviluppo, è possibile utilizzare gli URL offerti da Twilio per fornire le risposte TwiML utilizzate dalle applicazioni. È inoltre possibile ospitare gli URL per produrre le risposte TwiML oppure utilizzare l'oggetto **TwiMLResponse**.

Per altre informazioni sui verbi Twilio, i relativi attributi e il codice TwiML, vedere [TwiML][twiml]. Per altre informazioni sull'API Twilio, vedere la pagina relativa all'[API Twilio][twilio_api].

## <a id="CreateAccount"></a>Creare un account Twilio
Se si desidera creare un account Twilio, iscriversi nella pagina relativa alla [registrazione gratuita di Twilio][try_twilio]. È possibile iniziare con un account gratuito ed eseguire l'aggiornamento in un secondo momento.

Quando si effettua l'iscrizione a un account Twilio, si riceverà un ID account e un token di autenticazione. Entrambe queste informazioni sono necessarie per effettuare chiamate all'API Twilio. Per prevenire accessi autorizzati all'account, conservare il token di autenticazione in un luogo sicuro. L'ID account e il token di autorizzazione sono visualizzabili nella [pagina dell'account Twilio][twilio_account], rispettivamente nei campi **ACCOUNT SID** e **AUTH TOKEN**.

## <a id="create_app"></a>Creare un servizio mobile
Un servizio mobile che ospita un'applicazione compatibile con Twilio non è diverso da qualsiasi altro servizio mobile. È sufficiente aggiungere la libreria node.js di Twilio, in modo da potervi fare riferimento dagli script di API personalizzate del servizio mobile. Per informazioni sulla creazione di un servizio mobile iniziale, vedere [Introduzione a Servizi mobili](mobile-services-ios-get-started.md).

## <a id="ConfigureMobileService"></a>Configurare il servizio mobile per l'uso della libreria Node.js di Twilio
Twilio fornisce una libreria Node.js che copre vari aspetti di Twilio per fornire modi semplici e pratici per interagire con l'API REST di Twilio e il client Twilio per generare risposte TwiML.

Per utilizzare la libreria Node.js di Twilio nel servizio mobile, è necessario avvalersi del supporto per il modulo npm di Servizi mobili, archiviando a tale scopo gli script nel controllo del codice sorgente.

1. Completare l'esercitazione [Archiviare script nel controllo del codice sorgente](mobile-services-store-scripts-source-control.md) che illustra come configurare il controllo del codice sorgente per Servizi mobili e come archiviare gli script del server in un repository Git.

2. Dopo aver configurato il controllo del codice sorgente per il servizio mobile, aprire il repository nel computer locale, selezionare la sottocartella `\services`, aprire il file package.json in un editor di testo e aggiungere il campo seguente all'oggetto **dependencies**:

		"twilio": "~1.7.0"

3. Dopo aver aggiunto il riferimento al pacchetto Twilio all'oggetto **dependencies**, il file package.json sarà simile al seguente:

		{
		  "name": "todolist",
		  "version": "1.0.0",
		  "description": "todolist - hosted on Azure Mobile Services",
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

	>[AZURE.NOTE]La dipendenza per Twilio deve essere aggiunta come `"twilio": "~1.7.0"`, con una tilde (~). Un riferimento con l'accento circonflesso (^) non è supportato.

4. Eseguire il commit dell'aggiornamento del file ed effettuarne il push nel servizio mobile.

	Questo aggiornamento al file package.json riavvierà il servizio mobile.

A questo punto il servizio mobile installa e carica il pacchetto Twilio in modo da poter fare riferimento alla libreria Twilio e usarla negli script delle API personalizzate e delle tabelle.

## <a id="howto_make_call"></a>Procedura: Effettuare una chiamata in uscita
Lo script seguente illustra come iniziare una chiamata in uscita dal servizio mobile utilizzando la funzione **makeCall**. Questo codice utilizza inoltre un sito fornito da Twilio per restituire la risposta TwiML (Twilio Markup Language). Sostituire i valori per i numeri di telefono **From** e **To** e assicurarsi di verificare il numero di telefono in **From** per l'account Twilio prima di eseguire il codice.

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

Per ulteriori informazioni sui parametri passati alla funzione **client.makeCall**, vedere [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Come indicato in precedenza, questo codice utilizza un sito fornito da Twilio per restituire la risposta TwiML. Per fornire la risposta TwiML, è tuttavia possibile utilizzare il proprio sito. Per altre informazioni, vedere [Procedura: Fornire risposte TwiML dal proprio sito Web](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Procedura: Inviare un messaggio SMS
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


## <a id="howto_provide_twiml_responses"></a>Procedura: Fornire risposte TwiML dal proprio sito Web

Quando l'applicazione avvia una chiamata all'API Twilio, ad esempio tramite il metodo client.InitiateOutboundCall, Twilio invia la richiesta a un URL che deve restituire una risposta TwiML. L'esempio riportato nella sezione Procedura: Effettuare una chiamata in uscita usa l'URL fornito da Twilio http://twimlets.com/message per restituire la risposta.

> [AZURE.NOTE] Poiché TwiML è progettato per essere utilizzato da servizi Web, è possibile visualizzare il codice TwiML nel browser. Ad esempio, fare clic su [twimlet\_message\_url](http://twimlets.com/message) per visualizzare un elemento &lt;Response&gt; vuoto oppure fare clic su [twimlet\_message\_url\_hello\_world](http://twimlets.com/message?Message%5B0%5D=Hello%20World) per visualizzare un elemento &lt;Response&gt; contenente un elemento &lt;Say&gt;.

Anziché utilizzare l'URL fornito da Twilio, è possibile creare un sito Web personalizzato che restituisce risposte HTTP. Il sito può essere creato in un linguaggio qualsiasi purché restituisca risposte HTTP. In questo argomento si presuppone che l'URL verrà ospitato da un gestore generico ASP.NET.

Lo script seguente crea una risposta TwiML che pronuncia Hello World nella chiamata.

    var twilio = require('twilio');

    exports.post = function(request, response) {
        var resp = new twilio.TwimlResponse();
        resp.say({voice:'woman'}, 'ahoy hoy! Testing Twilio and node.js');
        response.set('Content-Type', 'text/xml');
        response.send(200, resp.toString());
    };

Per ulteriori informazioni su TwiML, vedere [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml).

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

[AZURE.INCLUDE [twilio\_additional\_services\_and\_next\_steps](../../includes/twilio_additional_services_and_next_steps.md)]


[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls

[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]: https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#


[azure_twilio_howto_dotnet]: /twilio-dotnet-how-to-use-for-voice-sms.md
[azure_twilio_howto_java]: /partner-twilio-java-how-to-use-voice-sms.md
[azure_twilio_howto_node]: /partner-twilio-nodejs-how-to-use-voice-sms.md
[azure_twilio_howto_ruby]: /partner-twilio-ruby-how-to-use-voice-sms.md
[azure_twilio_howto_python]: /partner-twilio-python-how-to-use-voice-sms.md
[azure_twilio_howto_php]: /partner-twilio-php-how-to-use-voice-sms.md

<!---HONumber=AcomDC_0309_2016-->