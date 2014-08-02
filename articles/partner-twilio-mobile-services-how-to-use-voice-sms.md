<properties linkid="develop-mobile-tutorials-twilio-for-voice-and-sms" pageTitle="Use Twilio for Voice and SMS Capabilities | Mobile Dev Center" metaKeywords="" description="Learn how to perform common tasks using the Twilio API with Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="How to use Twilio for voice and SMS capabilities from Mobile Services" authors="twilio" solutions="" manager="" editor="" />

Come utilizzare Twilio per le funzionalità voce ed SMS da Servizi mobili
========================================================================

In questo argomento viene illustrato come eseguire attività comuni utilizzando l'API Twilio con Servizi mobili di Azure. In questa esercitazione si apprenderà come creare script di API personalizzate che utilizzano l'API Twilio per iniziare una chiamata telefonica e inviare un messaggio SMS (Short Message Service).

Informazioni su Twilio
----------------------

Twilio è una tecnologia all'avanguardia per le comunicazioni aziendali che consente agli sviluppatori di incorporare funzionalità voce, VoIP e di messaggistica nelle applicazioni. Consente di virtualizzare tutta l'infrastruttura necessaria in un ambiente globale basato su cloud, esponendolo attraverso la piattaforma API per le comunicazioni Twilio. Le applicazioni sono scalabili e facili da compilare. Offre flessibilità, grazie a un modello di prezzi con pagamento al consumo, e l'affidabilità del cloud.

**Twilio Voice** consente alle applicazioni di effettuare e ricevere chiamate telefoniche. **Twilio SMS** consente alle applicazioni di inviare e ricevere SMS. **Twilio Client** consente di effettuare chiamate VoIP da qualsiasi telefono, tablet o browser e supporta WebRTC.

Prezzi Twilio e offerte speciali
--------------------------------

Per i clienti di Azure è disponibile un'[offerta speciale](http://ahoy.twilio.com/azure): credito Twilio aggiuntivo di $ 10 all'aggiornamento dell'account Twilio. Il credito Twilio può essere applicato a qualsiasi utilizzo di Twilio ($ 10 di credito equivalgono all'invio di 1.000 SMS o a 1.000 minuti voce per le chiamate in entrata, a seconda della località del numero di telefono, del messaggio o della destinazione della chiamata). Per riscattare il credito Twilio e iniziare a utilizzare il servizio, visitare la pagina all'indirizzo [ahoy.twilio.com/azure](http://ahoy.twilio.com/azure).

Twilio è un servizio con pagamento in base al consumo. Non prevede spese iniziali ed è possibile chiudere l'account in qualsiasi momento. Per ulteriori informazioni, vedere la pagina relativa ai [prezzi di Twilio](http://www.twilio.com/pricing).

Concetti
--------

L'API Twilio è un'API RESTful che fornisce funzionalità voce ed SMS per le applicazioni. Le librerie client sono disponibili in più lingue. Per un elenco, vedere la pagina relativa alle [librerie dell'API Twilio](https://www.twilio.com/docs/libraries). Sono disponibili esercitazioni aggiuntive relative all'utilizzo di Twilio in qualsiasi applicazione di Azure scritta in [.NET](/en-us/develop/net/how-to-guides/twilio-voice-and-sms-service/), [node.js](/en-us/develop/nodejs/how-to-guides/twilio-voice-and-sms-service/), [Java](/en-us/develop/java/how-to-guides/twilio-voice-and-sms-service/), [PHP](/en-us/develop/php/how-to-guides/twilio-voice-and-sms-service/), [Python](/en-us/develop/python/how-to-guides/twilio-voice-and-sms-service/) o [Ruby](/en-us/develop/ruby/how-to-guides/twilio-voice-and-sms-service/).

I concetti principali dell'API Twilio sono costituiti dai verbi Twilio e dal linguaggio di markup Twilio (Twilio Markup Language, TwiML).

### Verbi Twilio

Nell'API vengono utilizzati i verbi Twilio: il verbo **&lt;Say\>**, ad esempio, indica a Twilio di recapitare un messaggio acustico in una chiamata.

Di seguito è riportato un elenco dei verbi Twilio. Per ulteriori informazioni su altri verbi e funzionalità, vedere la [documentazione relativa a Twilio Markup Language](http://www.twilio.com/docs/api/twiml).

-   **&lt;Dial\>**: connette il chiamante a un altro telefono.
-   **&lt;Gather\>**: raccoglie i numeri digitati dal chiamante sulla tastiera del telefono.
-   **&lt;Hangup\>**: termina una chiamata.
-   **&lt;Play\>**: riproduce un file audio.
-   **&lt;Pause\>**: attende in modo silenzioso per un numero di secondi specificato.
-   **&lt;Record\>**: registra la voce del chiamante e restituisce l'URL del file contenente la registrazione.
-   **&lt;Redirect\>**: trasferisce il controllo di una chiamata o di un SMS al codice TwiML presso un URL diverso.
-   **&lt;Reject\>**: rifiuta una chiamata in arrivo al numero Twilio senza alcun addebito
-   **&lt;Say\>**: effettua la sintesi vocale del testo durante una chiamata.
-   **&lt;Sms\>**: invia un messaggio SMS.

### TwiML

TwiML è un set di istruzioni basate su XML e sui verbi Twilio che indicano a Twilio come elaborare una chiamata o un SMS.

Ad esempio, il codice TwiML seguente effettua la sintesi vocale del testo **Hello World**.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Quando l'applicazione chiama l'API Twilio, uno dei parametri dell'API è l'URL che restituisce la risposta TwiML. Ai fini dello sviluppo, è possibile utilizzare gli URL offerti da Twilio per fornire le risposte TwiML utilizzate dalle applicazioni. È inoltre possibile ospitare gli URL per produrre le risposte TwiML oppure utilizzare l'oggetto **TwiMLResponse**.

Per ulteriori informazioni sui verbi Twilio, i relativi attributi e il codice TwiML, vedere [TwiML](http://www.twilio.com/docs/api/twiml). Per ulteriori informazioni sull'API Twilio, vedere la pagina relativa all'[API Twilio](http://www.twilio.com/api).

Creazione di un account Twilio
------------------------------

Se si desidera creare un account Twilio, iscriversi nella pagina relativa alla [registrazione gratuita di Twilio](https://www.twilio.com/try-twilio). È possibile iniziare con un account gratuito ed eseguire l'aggiornamento in un secondo momento.

Quando si effettua l'iscrizione a un account Twilio, si riceverà un ID account e un token di autenticazione. Entrambe queste informazioni sono necessarie per effettuare chiamate all'API Twilio. Per prevenire accessi autorizzati all'account, conservare il token di autenticazione in un luogo sicuro. L'ID account e il token di autorizzazione sono visualizzabili nella [pagina dell'account Twilio](https://www.twilio.com/user/account), rispettivamente nei campi **ACCOUNT SID** e **AUTH TOKEN**.

Verifica dei numeri di telefono
-------------------------------

I numeri di telefono utilizzati dall'account devono essere verificati con Twilio. Ad esempio, se si desidera effettuare chiamate in uscita, il numero di telefono deve essere verificato come ID chiamante in uscita con Twilio. Allo stesso modo, se si desidera che un numero di telefono riceva messaggi SMS, il numero di telefono di destinazione deve essere verificato con Twilio. Per informazioni su come verificare un numero di telefono, vedere la sezione relativa alla [gestione dei numeri](https://www.twilio.com/user/account/phone-numbers/verified#). Parte del codice illustrato di seguito si basa su numeri di telefono che sarà necessario verificare con Twilio.

In alternativa, anziché utilizzare un numero esistente per le applicazioni, è possibile acquistare un numero di telefono Twilio. Per informazioni sull'acquisto di un numero di telefono Twilio, vedere la [guida relativa ai numeri di telefono Twilio](https://www.twilio.com/help/faq/phone-numbers).

Creazione di un servizio mobile
-------------------------------

Un servizio mobile che ospita un'applicazione compatibile con Twilio non è diverso da qualsiasi altro servizio mobile. È sufficiente aggiungere la libreria node.js di Twilio, in modo da potervi fare riferimento dagli script di API personalizzate del servizio mobile. Per informazioni sulla creazione di un servizio mobile iniziale, vedere [Introduzione a Servizi mobili](http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started/).

Configurazione del servizio mobile per l'utilizzo della libreria Node.js di Twilio
----------------------------------------------------------------------------------

Twilio fornisce una libreria Node.js che copre vari aspetti di Twilio per fornire modi semplici e pratici per interagire con l'API REST di Twilio e il client Twilio per generare risposte TwiML.

Per utilizzare la libreria Node.js di Twilio nel servizio mobile, è necessario avvalersi del supporto per il modulo npm di Servizi mobili, archiviando a tale scopo gli script nel controllo del codice sorgente. Nell'esercitazione [Archiviazione di script nel controllo del codice sorgente](http://www.windowsazure.com/en-us/develop/mobile/tutorials/store-scripts-in-source-control/) viene illustrato come configurare per la prima volta il controllo del codice sorgente in Servizi mobili e come archiviare gli script del server in un archivio Git.

Dopo avere configurato il controllo del codice sorgente per il servizio mobile, aprire la scheda Configure nel dashboard del servizio mobile, quindi individuare e copiare l'URL di Git.

Incollare tale URL in un browser e sostituire il nome dell'archivio con */DebugConsole/index.html*.

Ad esempio, modificare:

    https://twilioSample.scm.azure-mobile.net/twilioSample.git

in:

    https://twilioSample.scm.azure-mobile.net/DebugConsole/index.html

Quando richiesto, immettere le credenziali utilizzate durante la configurazione del controllo del codice sorgente per il servizio. Dopo avere effettuato l'accesso, sarà possibile visualizzare la console Servizio mobile di Azure.

![Console Servizio mobile](./media/partner-twilio-mobile-services-how-to-use-voice-sms/twilio-kuduconsole.png)

Nella console cambiare la directory, specificando la cartella scripts:

    cd site\wwwroot\App_Data\config\scripts

Selezionare la cartella api, quindi installare il modulo del nodo Twilio eseguendo il comando seguente:

    npm install twilio

È ora possibile fare riferimento alla libreria Twilio e utilizzarla negli script delle API personalizzate e delle tabelle.

Procedura: Effettuare una chiamata in uscita
--------------------------------------------

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

Per ulteriori informazioni sui parametri passati alla funzione **client.makeCall**, vedere <http://www.twilio.com/docs/api/rest/making-calls>.

Come indicato in precedenza, questo codice utilizza un sito fornito da Twilio per restituire la risposta TwiML. Per fornire la risposta TwiML, è tuttavia possibile utilizzare il proprio sito. Per ulteriori informazioni, vedere [Procedura: Fornire risposte TwiML dal proprio sito Web](#howto_provide_twiml_responses).

Procedura: Inviare un messaggio SMS
-----------------------------------

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

Procedura: Fornire risposte TwiML dal proprio sito Web
------------------------------------------------------

Quando l'applicazione avvia una chiamata all'API Twilio, ad esempio tramite il metodo client.InitiateOutboundCall, Twilio invia la richiesta a un URL che deve restituire una risposta TwiML. Nell'esempio illustrato in Procedura: Effettuare una chiamata in uscita viene utilizzato l'URL fornito da Twilio http://twimlets.com/message per restituire la risposta.

**Nota**

Poiché TwiML è progettato per essere utilizzato da servizi Web, è possibile visualizzare il codice TwiML nel browser. Ad esempio, fare clic su [twimlet\_message\_url](http://twimlets.com/message) per visualizzare un elemento &lt;Response\> vuoto oppure fare clic su [twimlet\_message\_url\_hello\_world](http://twimlets.com/message?Message%5B0%5D=Hello%20World) per visualizzare un elemento &lt;Response\> contenente un elemento &lt;Say\>.

Anziché utilizzare l'URL fornito da Twilio, è possibile creare un sito Web personalizzato che restituisce risposte HTTP. Il sito può essere creato in un linguaggio qualsiasi purché restituisca risposte HTTP. In questo argomento si presuppone che l'URL verrà ospitato da un gestore generico ASP.NET.

Lo script seguente crea una risposta TwiML che pronuncia Hello World nella chiamata.

    var twilio = require('twilio');

    exports.post = function(request, response) {
        var resp = new twilio.TwimlResponse();
        resp.say({voice:'woman'}, 'ahoy hoy! Testing Twilio and node.js');
        response.set('Content-Type', 'text/xml');
        response.send(200, resp.toString());
        };

Per ulteriori informazioni su TwiML, vedere <https://www.twilio.com/docs/api/twiml>.

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

[WACOM.INCLUDE [twilio\_additional\_services\_and\_next\_steps](../includes/twilio_additional_services_and_next_steps.md)]

