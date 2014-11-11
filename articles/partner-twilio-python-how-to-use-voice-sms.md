<properties linkid="develop-php-how-to-twilio-sms-service" urlDisplayName="Twilio Voice/SMS Service" pageTitle="How to Use Twilio for Voice and SMS (PHP) - Azure" metaKeywords="Azure PHP Twilio, Azure phone calls, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in PHP." metaCanonical="" services="" documentationCenter="" title="How to Use Twilio for Voice and SMS Capabilities in PHP" authors="MicrosoftHelp@twilio.com" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com" />

# Come usare Twilio per le funzionalità voce ed SMS in PHP

In questa guida viene illustrato come eseguire attività di programmazione comuni con il servizio API Twilio in Azure. Gli scenari presentati includono la composizione di una chiamata telefonica e l'invio di un messaggio SMS (Short Message Service). Per altre informazioni su Twilio e sull'utilizzo delle funzionalità voce ed SMS nelle applicazioni, vedere la sezione [Passaggi successivi][Passaggi successivi].

## Sommario

-   [Informazioni su Twilio][Informazioni su Twilio]
-   [Prezzi di Twilio][Prezzi di Twilio]
-   [Concetti][Concetti]
-   [Creazione di un account Twilio][Creazione di un account Twilio]
-   [Verifica dei numeri di telefono][Verifica dei numeri di telefono]
-   [Creare un'applicazione PHP][Creare un'applicazione PHP]
-   [Configurare l'applicazione per l'uso delle librerie Twilio][Configurare l'applicazione per l'uso delle librerie Twilio]
-   [Procedura: Effettuare una chiamata in uscita][Procedura: Effettuare una chiamata in uscita]
-   [Procedura: Inviare un messaggio SMS][Procedura: Inviare un messaggio SMS]
-   [Procedura: Fornire risposte TwiML dal proprio sito Web][Procedura: Fornire risposte TwiML dal proprio sito Web]

## <span id="WhatIs"></span></a>Informazioni su Twilio

Twilio è una tecnologia all'avanguardia per le comunicazioni aziendali che consente agli sviluppatori di incorporare funzionalità voce, VoIP e di messaggistica nelle applicazioni. Consente di virtualizzare tutta l'infrastruttura necessaria in un ambiente globale basato su cloud, esponendolo attraverso la piattaforma API per le comunicazioni Twilio. Le applicazioni sono scalabili e facili da compilare. Offre flessibilità, grazie a un modello di prezzi con pagamento al consumo, e l'affidabilità del cloud.

**Twilio Voice** consente alle applicazioni di effettuare e ricevere chiamate telefoniche. **Twilio SMS** consente alle applicazioni di inviare e ricevere messaggi di testo. **Twilio Client** consente di effettuare chiamate VoIP da qualsiasi telefono, tablet o browser e supporta WebRTC.

## <span id="Pricing"></span></a>Prezzi Twilio e offerte speciali

I clienti di Azure ricevono un'[offerta speciale]: $ 10 di credito Twilio all'aggiornamento dell'account Twilio. Il credito Twilio può essere applicato a qualsiasi utilizzo di Twilio ($ 10 di credito equivalgono all'invio di 1.000 SMS o a 1.000 minuti voce per le chiamate in entrata, a seconda della località del numero di telefono, del messaggio o della destinazione della chiamata). Per riscattare il credito Twilio e iniziare a usare il servizio, visitare la pagina all'indirizzo: [ahoy.twilio.com/azure].

Twilio è un servizio con pagamento in base al consumo. Non prevede spese iniziali ed è possibile chiudere l'account in qualsiasi momento. Per altre informazioni, vedere la pagina relativa ai [prezzi di Twilio][prezzi di Twilio].

## <span id="Concepts"></span></a>Concetti

L'API Twilio è un'API RESTful che fornisce funzionalità voce ed SMS per le applicazioni. Le librerie client sono disponibili in più lingue. Per un elenco, vedere la pagina relativa alle [librerie dell'API Twilio][librerie dell'API Twilio].

I concetti principali dell'API Twilio sono costituiti dai verbi Twilio e dal linguaggio di markup Twilio (Twilio Markup Language, TwiML).

### <span id="Verbs"></span></a>Verbi Twilio

Nell'API vengono utilizzati i verbi Twilio: il verbo **\<Say\>**, ad esempio, indica a Twilio di recapitare un messaggio acustico in una chiamata.

Di seguito è riportato un elenco dei verbi Twilio. Per altre informazioni su altri verbi e funzionalità, vedere la [documentazione relativa a Twilio Markup Language] [<http://www.twilio.com/docs/api/twiml>].

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

Per altre informazioni sui verbi Twilio, i relativi attributi e il codice TwiML, vedere [TwiML][TwiML]. Per altre informazioni sull'API Twilio, vedere la pagina relativa all'[API Twilio][API Twilio].

## <span id="CreateAccount"></span></a>Creazione di un account Twilio

Se si desidera creare un account Twilio, iscriversi nella pagina relativa alla [registrazione gratuita di Twilio][registrazione gratuita di Twilio]. È possibile iniziare con un account gratuito ed eseguire l'aggiornamento in un secondo momento.

Quando si effettua l'iscrizione a un account Twilio, si riceveranno un ID account e un token di autenticazione. Entrambe queste informazioni sono necessarie per effettuare chiamate all'API Twilio. Per prevenire accessi autorizzati all'account, conservare il token di autenticazione in un luogo sicuro. L'ID account e il token di autorizzazione sono visualizzabili nella [pagina dell'account Twilio][pagina dell'account Twilio], rispettivamente nei campi **ACCOUNT SID** e **AUTH TOKEN**.

## <span id="VerifyPhoneNumbers"></span></a>Verifica dei numeri di telefono

I numeri di telefono utilizzati dall'account devono essere verificati con Twilio. Ad esempio, se si desidera effettuare chiamate in uscita usando il proprio numero di telefono esistente come ID chiamante, il numero di telefono deve essere verificato con Twilio. Analogamente, fino all'aggiornamento, se si desidera inviare messaggi SMS a un numero di telefono, tale numero dovrà essere verificato con Twilio. Dopo l'aggiornamento sarà possibile inviare messaggi SMS a qualsiasi numero senza verificarlo. Per informazioni su come verificare un numero di telefono, vedere la sezione relativa alla [gestione dei numeri][gestione dei numeri]. Parte del codice illustrato di seguito si basa su numeri di telefono che sarà necessario verificare con Twilio.

In alternativa, anziché usare un numero esistente per le applicazioni, è possibile acquistare un numero di telefono Twilio. Per informazioni sull'acquisto di un numero di telefono Twilio, vedere la [guida relativa ai numeri di telefono Twilio][guida relativa ai numeri di telefono Twilio].

## <span id="create_app"></span></a>Creare un'applicazione PHP

Un'applicazione PHP che utilizza il servizio Twilio e viene eseguita in Azure non è diversa da qualsiasi altra applicazione PHP che utilizza il servizio Twilio. Benché i servizi Twilio siano basati su REST e possano essere chiamati da PHP in molti modi, in questo articolo verrà illustrato solo come usare i servizi Twilio con la [libreria Twilio per PHP da Github][.]. Per altre informazioni sull'utilizzo della libreria Twilio per PHP, vedere [][]<http://readthedocs.org/docs/twilio-php/en/latest/index.html></a>.

Per istruzioni dettagliate sulla creazione e sulla distribuzione di un'applicazione Twilio/PHP in Azure, vedere [Come effettuare una chiamata tramite Twilio in un'applicazione PHP in Azure][Come effettuare una chiamata tramite Twilio in un'applicazione PHP in Azure].

## <span id="configure_app"></span></a>Configurare l'applicazione per l'uso delle librerie Twilio

È possibile configurare l'applicazione in modo che utilizzi la libreria Twilio per PHP in due modi:

1.  Scaricare la libreria Twilio per PHP da Github ([][.]<https://github.com/twilio/twilio-php></a>) e aggiungere la directory **Services** all'applicazione.

    -OPPURE-

2.  Installare la libreria Twilio per PHP come pacchetto PEAR. È possibile eseguire l'installazione tramite i comandi seguenti:

        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

Dopo avere installato la libreria Twilio per PHP, sarà possibile aggiungere un'istruzione **require\_once** nella parte superiore del file PHP, in modo che faccia riferimento alla libreria:

        require_once 'Services/Twilio.php';

Per altre informazioni, vedere [][1]<https://github.com/twilio/twilio-php/blob/master/README.md></a>.

## <span id="howto_make_call"></span></a>Procedura: Effettuare una chiamata in uscita

Di seguito è illustrato come effettuare una chiamata in uscita tramite la classe **Services\_Twilio**. Questo codice utilizza inoltre un sito fornito da Twilio per restituire la risposta TwiML (Twilio Markup Language). Sostituire i valori per i numeri di telefono **From** e **To** e assicurarsi di verificare il numero di telefono in **From** per l'account Twilio prima di eseguire il codice.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";

    // The number of the phone initiating the the call.
    // (Must be previously validated with Twilio.)
    $from_number = "NNNNNNNNNNN";

    // The number of the phone receiving call.
    $to_number = "NNNNNNNNNNN";

    // Use the Twilio-provided site for the TwiML response.
    $url = "http://twimlets.com/message";

    // The phone message text.
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    //Make the call.
    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
            $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

Come indicato in precedenza, questo codice utilizza un sito fornito da Twilio per restituire la risposta TwiML. Per fornire la risposta TwiML è inoltre possibile usare il proprio sito. Per altre informazioni, vedere la [procedura per fornire risposte TwiML dal proprio sito Web][Procedura: Fornire risposte TwiML dal proprio sito Web].

-   **Nota**: Per la risoluzione dei problemi relativi a errori di convalida del certificato SSL, vedere [][2]<http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html></a>

## <span id="howto_send_sms"></span></a>Procedura: Inviare un messaggio SMS

Nella schermata seguente è illustrato come inviare un messaggio SMS tramite la classe **Services\_Twilio**. Il numero in **From** per l'invio di messaggi SMS con gli account di valutazione è fornito da Twilio. Il numero in **To** deve essere verificato per l'account Twilio prima di eseguire il codice.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";


    $from_number = "NNNNNNNNNNN"; // With trial account, texts can only be sent from your Twilio number.
    $to_number = "NNNNNNNNNNN";
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    // Send the SMS message.
    try
    {
        $client->account->sms_messages->create($from_number, $to_number, $message);
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

## <span id="howto_provide_twiml_responses"></span></a>Procedura: Fornire risposte TwiML dal proprio sito Web

Quando l'applicazione avvia una chiamata all'API Twilio, Twilio invia la richiesta a un URL che deve restituire una risposta TwiML. Nell'esempio precedente viene usato l'URL fornito da Twilio [][3]<http://twimlets.com/message></a>. Poiché TwiML è progettato per essere usato da Twilio, è possibile visualizzarlo nel browser. Ad esempio, fare clic su [][3]<http://twimlets.com/message></a> per visualizzare un elemento `<Response>` vuoto oppure fare clic su [][4]<http://twimlets.com/message?Message%5B0%5D=Hello%20World></a> per visualizzare un elemento `<Response>` contenente un elemento `<Say>`.

Anziché usare l'URL fornito da Twilio, è possibile creare un sito personalizzato che restituisce risposte HTTP. È possibile creare il sito in qualsiasi linguaggio che restituisca risposte XML. In questo argomento si presuppone che si utilizzerà PHP per creare TwiML.

La pagina PHP seguente crea una risposta TwiML che pronuncia **Hello World** nella chiamata.

    <?php    
        header("content-type: text/xml");    
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>    
        <Say>Hello world.</Say>
    </Response>

Come si evince dal codice riportato sopra, la risposta TwiML è semplicemente un documento XML. La libreria Twilio per PHP contiene le classi che consentono di generare automaticamente le istruzioni TwiML. Nell'esempio seguente viene creata la stessa risposta descritta sopra, ma usando la classe **Services\_Twilio\_Twiml** nella libreria Twilio per PHP:

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

Per altre informazioni su TwiML, vedere [][5]<https://www.twilio.com/docs/api/twiml></a>.

Dopo aver configurato la pagina PHP in modo da fornire risposte TwiML, usare l'URL della pagina PHP come URL passato nel metodo `Services_Twilio->account->calls->create`. Se, ad esempio, si dispone di un'applicazione Web denominata **MyTwiML** distribuita in un servizio ospitato in Azure e il nome della pagina PHP è **mytwiml.php**, è possibile passare l'URL a **Services\_Twilio-\>account-\>calls-\>create**, come mostrato nell'esempio seguente:

    require_once 'Services/Twilio.php';

    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";
    $from_number = "NNNNNNNNNNN";
    $to_number = "NNNNNNNNNNN";
    $url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

    // The phone message text.
    $message = "Hello world.";

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
            $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

Per altre informazioni sull'utilizzo di Twilio in Azure con PHP, vedere [Come effettuare una chiamata tramite Twilio in un'applicazione PHP in Azure][Come effettuare una chiamata tramite Twilio in un'applicazione PHP in Azure].

## <span id="AdditionalServices"></span></a>Procedura: Usare servizi Twilio aggiuntivi

Oltre agli esempi illustrati in questa pagina, Twilio offre API basate su Web che è possibile usare per sfruttare altre funzionalità di Twilio dall'applicazione Azure. Per informazioni dettagliate, vedere la [documentazione sull'API Twilio][API Twilio].

## <span id="NextSteps"></span></a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base del servizio Twilio, usare i collegamenti seguenti per altre informazioni:

-   [Linee guida sulla sicurezza di Twilio][Linee guida sulla sicurezza di Twilio]
-   [Procedure e codice di esempio su Twilio][Procedure e codice di esempio su Twilio]
-   [Esercitazioni con guide rapide su Twilio][Esercitazioni con guide rapide su Twilio]
-   [Twilio su GitHub][Twilio su GitHub]
-   [Contattare il supporto di Twilio][Contattare il supporto di Twilio]

  [Passaggi successivi]: #NextSteps
  [Informazioni su Twilio]: #WhatIs
  [Prezzi di Twilio]: #Pricing
  [Concetti]: #Concepts
  [Creazione di un account Twilio]: #CreateAccount
  [Verifica dei numeri di telefono]: #VerifyPhoneNumbers
  [Creare un'applicazione PHP]: #create_app
  [Configurare l'applicazione per l'uso delle librerie Twilio]: #configure_app
  [Procedura: Effettuare una chiamata in uscita]: #howto_make_call
  [Procedura: Inviare un messaggio SMS]: #howto_send_sms
  [Procedura: Fornire risposte TwiML dal proprio sito Web]: #howto_provide_twiml_responses
  [prezzi di Twilio]: http://www.twilio.com/pricing
  [librerie dell'API Twilio]: https://www.twilio.com/docs/libraries
  [TwiML]: http://www.twilio.com/docs/api/twiml
  [API Twilio]: http://www.twilio.com/api
  [registrazione gratuita di Twilio]: https://www.twilio.com/try-twilio
  [pagina dell'account Twilio]: https://www.twilio.com/user/account
  [gestione dei numeri]: https://www.twilio.com/user/account/phone-numbers/verified#
  [guida relativa ai numeri di telefono Twilio]: https://www.twilio.com/help/faq/phone-numbers
  [.]: https://github.com/twilio/twilio-php
  []: http://readthedocs.org/docs/twilio-php/en/latest/index.html
  [Come effettuare una chiamata tramite Twilio in un'applicazione PHP in Azure]: http://windowsazure.com/it-it/documentation/articles/partner-twilio-php-make-phone-call
  [1]: https://github.com/twilio/twilio-php/blob/master/README.md
  [2]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
  [3]: http://twimlets.com/message
  [4]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
  [5]: https://www.twilio.com/docs/api/twiml
  [Linee guida sulla sicurezza di Twilio]: http://www.twilio.com/docs/security
  [Procedure e codice di esempio su Twilio]: http://www.twilio.com/docs/howto
  [Esercitazioni con guide rapide su Twilio]: http://www.twilio.com/docs/quickstart
  [Twilio su GitHub]: https://github.com/twilio
  [Contattare il supporto di Twilio]: http://www.twilio.com/help/contact
