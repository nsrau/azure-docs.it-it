<properties urlDisplayName="Twilio Voice/SMS Service" pageTitle="Come usare Twilio per le funzionalità voce e SMS (PHP) - Azure" metaKeywords="Twilio in Azure con PHP, telefonate in Azure, telefonate in Azure, Twilio in Azure, SMS in Azure, SMS in Azure, chiamate vocali in Azure, chiamate vocali in Azure, messaggi di testo in Azure, messaggi di testo in Azure" description="Informazioni su come effettuare una chiamata telefonica e inviare un SMS con il servizio API Twilio API in Azure. Gli esempi di codice sono scritti in PHP." metaCanonical="" services="" documentationCenter="" title="How to Use Twilio for Voice and SMS Capabilities in PHP" authors="MicrosoftHelp@twilio.com" solutions="" manager="twilio" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com" />





# Come usare Twilio per le funzionalità voce e SMS in PHP
Questa guida illustra come eseguire attività di programmazione comuni con il servizio API Twilio in Azure. Gli scenari presentati includono la composizione di una chiamata telefonica e l'invio di un messaggio SMS (Short Message Service). Per altre informazioni su Twilio e sull'uso delle funzionalità voce e SMS nelle applicazioni, vedere la sezione [Passaggi successivi](#NextSteps).

## Sommario
* [Informazioni su Twilio](#WhatIs)
* [Prezzi di Twilio](#Pricing)
* [Concetti](#Concepts)
* [Creare un account Twilio](#CreateAccount)
* [Verificare i numeri di telefono](#VerifyPhoneNumbers)
* [Creare un'applicazione PHP](#create_app)
* [Configurare l'applicazione per l'uso delle librerie Twilio](#configure_app)
* [Procedura: Effettuare una chiamata in uscita](#howto_make_call)
* [Procedura: Inviare un messaggio SMS](#howto_send_sms)
* [Procedura: Fornire risposte TwiML dal proprio sito Web](#howto_provide_twiml_responses)

<h2><a id="WhatIs"></a>Informazioni su Twilio</h2>
Twilio è una tecnologia all'avanguardia per le comunicazioni aziendali che consente agli sviluppatori di incorporare funzionalità voce, VoIP e di messaggistica nelle applicazioni. Consente di virtualizzare tutta l'infrastruttura necessaria in un ambiente globale basato sul cloud, esponendolo attraverso la piattaforma API per le comunicazioni Twilio. Le applicazioni sono scalabili e facili da compilare. Offre flessibilità, grazie a un modello di prezzi con pagamento al consumo, e l'affidabilità del cloud.

**Twilio Voice** consente alle applicazioni di effettuare e ricevere chiamate telefoniche. **Twilio SMS** consente alle applicazioni di inviare e ricevere messaggi di testo. **Twilio Client** consente di effettuare chiamate VoIP da qualsiasi telefono, tablet o browser e supporta WebRTC.

<h2><a id="Pricing"></a>Prezzi Twilio e offerte speciali</h2>

I clienti di Azure ricevono un'[offerta speciale]: $ 10 di credito Twilio all'aggiornamento dell'account Twilio. Il credito Twilio può essere applicato a qualsiasi uso di Twilio ($ 10 di credito equivalgono all'invio di 1.000 SMS o a 1.000 minuti voce per le chiamate in entrata, a seconda della località del numero di telefono, del messaggio o della destinazione della chiamata). Per riscattare il credito Twilio e iniziare a usare il servizio, visitare la pagina all'indirizzo: [ahoy.twilio.com/azure].

Twilio è un servizio con pagamento in base al consumo. Non prevede spese iniziali ed è possibile chiudere l'account in qualsiasi momento. Per altre informazioni, vedere la pagina relativa ai [prezzi di Twilio] [twilio_pricing].

<h2><a id="Concepts"></a>Concetti</h2>
L'API Twilio è un'API RESTful che fornisce funzionalità voce e SMS per le applicazioni. Le librerie client sono disponibili in più lingue. Per un elenco, vedere la pagina relativa alle [librerie dell'API Twilio] [twilio_libraries].

I concetti principali dell'API Twilio sono costituiti dai verbi Twilio e dal linguaggio di markup Twilio (Twilio Markup Language, TwiML).

<h3><a id="Verbs"></a>Verbi Twilio</h3>
Nell'API vengono usati i verbi Twilio: il verbo **&lt;Say&gt;**, ad esempio, indica a Twilio di recapitare un messaggio acustico in una chiamata.

Di seguito è riportato un elenco dei verbi Twilio. Per altre informazioni su altri verbi e funzionalità, vedere la [documentazione relativa a Twilio Markup Language] [http://www.twilio.com/docs/api/twiml].

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

<h3><a id="TwiML"></a>TwiML</h3>
TwiML è un set di istruzioni basate su XML e sui verbi Twilio che indicano a Twilio come elaborare una chiamata o un SMS.

Ad esempio, il codice TwiML seguente effettua la sintesi vocale del testo **Hello World**.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Quando l'applicazione chiama l'API Twilio, uno dei parametri dell'API è l'URL che restituisce la risposta TwiML. Ai fini dello sviluppo, è possibile usare gli URL offerti da Twilio per fornire le risposte TwiML usate dalle applicazioni. È anche possibile ospitare gli URL per produrre le risposte TwiML oppure usare l'oggetto **TwiMLResponse**.

Per altre informazioni sui verbi Twilio, i relativi attributi e il codice TwiML, vedere [TwiML] [twiml]. Per altre informazioni sull'API Twilio, vedere la pagina relativa all'[API Twilio] [twilio_api].

<h2><a id="CreateAccount"></a>Creare un account Twilio</h2>
Se si vuole creare un account Twilio, effettuare l'iscrizione tramite la pagina relativa alla [registrazione gratuita di Twilio] [try_twilio]. È possibile iniziare con un account gratuito ed eseguire l'aggiornamento in un secondo momento.

Quando si effettua l'iscrizione a un account Twilio, si riceveranno un ID account e un token di autenticazione. Entrambe queste informazioni sono necessarie per effettuare chiamate all'API Twilio. Per prevenire accessi autorizzati all'account, conservare il token di autenticazione in un luogo sicuro. L'ID account e il token di autenticazione sono visualizzabili nella [pagina dell'account Twilio] [twilio_account], rispettivamente nei campi **ACCOUNT SID** e **AUTH TOKEN**.

<h2><a id="VerifyPhoneNumbers"></a>Verificare i numeri di telefono</h2>
I numeri di telefono usati dall'account devono essere verificati con Twilio. Ad esempio, se si vuole effettuare chiamate in uscita usando il proprio numero di telefono esistente come ID chiamante, il numero di telefono deve essere verificato con Twilio. Analogamente, fino all'aggiornamento, se si vuole inviare messaggi SMS a un numero di telefono, tale numero dovrà essere verificato con Twilio. Dopo l'aggiornamento sarà possibile inviare messaggi SMS a qualsiasi numero senza verificarlo. Per informazioni su come verificare un numero di telefono, vedere la sezione relativa alla [gestione dei numeri] [verify_phone]. Parte del codice illustrato di seguito si basa su numeri di telefono che sarà necessario verificare con Twilio.

In alternativa, anziché usare un numero esistente per le applicazioni, è possibile acquistare un numero di telefono Twilio. Per informazioni sull'acquisto di un numero di telefono Twilio, vedere la [guida relativa ai numeri di telefono Twilio](https://www.twilio.com/help/faq/phone-numbers).

<h2><a id="create_app"></a>Creare un'applicazione PHP</h2>
Un'applicazione PHP che usa il servizio Twilio e viene eseguita in Azure non è diversa da qualsiasi altra applicazione PHP che usa il servizio Twilio. Benché i servizi Twilio siano basati su REST e possano essere chiamati da PHP in molti modi, in questo articolo verrà illustrato solo come usare i servizi Twilio con la [.][twilio_php] Per altre informazioni sull'uso della libreria Twilio per PHP, vedere [http://readthedocs.org/docs/twilio-php/en/latest/index.html][twilio_lib_docs].

Per istruzioni dettagliate sulla creazione e sulla distribuzione di un'applicazione Twilio/PHP in Azure, vedere [Come effettuare una chiamata tramite Twilio in un'applicazione PHP in Azure][howto_phonecall_php].

<h2><a id="configure_app"></a>Configurare l'applicazione per l'uso delle librerie Twilio</h2>
È possibile configurare l'applicazione in modo che usi la libreria Twilio per PHP in due modi:

1. Scaricare la libreria Twilio per PHP da Github ([https://github.com/twilio/twilio-php][twilio_php]) e aggiungere la directory **Services** all'applicazione.

	-OPPURE-

2. Installare la libreria Twilio per PHP come pacchetto PEAR. È possibile eseguire l'installazione tramite i comandi seguenti:

		$ pear channel-discover twilio.github.com/pear
		$ pear install twilio/Services_Twilio

Dopo avere installato la libreria Twilio per PHP, sarà possibile aggiungere un'istruzione **require_once** nella parte superiore del file PHP, in modo che faccia riferimento alla libreria:

    	require_once 'Services/Twilio.php';

Per altre informazioni, vedere [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme].

<h2><a id="howto_make_call"></a>Procedura: Effettuare una chiamata in uscita</h2>
Di seguito è illustrato come effettuare una chiamata in uscita tramite la classe **Services_Twilio**. Questo codice usa inoltre un sito fornito da Twilio per restituire la risposta TwiML (Twilio Markup Language). Sostituire i valori per i numeri di telefono **From** e **To** e assicurarsi di verificare il numero di telefono in **From** per l'account Twilio prima di eseguire il codice.

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

Come indicato in precedenza, questo codice usa un sito fornito da Twilio per restituire la risposta TwiML. Per fornire la risposta TwiML è anche possibile usare il proprio sito. Per altre informazioni, vedere la [procedura per fornire risposte TwiML dal proprio sito Web](#howto_provide_twiml_responses).


- **Nota**: Per la risoluzione dei problemi relativi a errori di convalida del certificato SSL, vedere [http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html][ssl_validation] 


<h2><a id="howto_send_sms"></a>Procedura: Inviare un messaggio SMS</h2>
La schermata seguente illustra come inviare un messaggio SMS tramite la classe **Services_Twilio**. Il numero in **From** per l'invio di messaggi SMS con gli account di valutazione è fornito da Twilio. Il numero in **To** deve essere verificato per l'account Twilio prima di eseguire il codice.

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

<h2><a id="howto_provide_twiml_responses"></a>Procedura: Fornire risposte TwiML dal proprio sito Web</h2>
Quando l'applicazione avvia una chiamata all'API Twilio, Twilio invia la richiesta a un URL che deve restituire una risposta TwiML. Nell'esempio precedente viene usato l'URL fornito da Twilio [http://twimlets.com/message][twimlet_message_url]. Poiché TwiML è progettato per essere usato da Twilio, è possibile visualizzarlo nel browser. Ad esempio, fare clic su [http://twimlets.com/message][twimlet_message_url] per visualizzare un elemento `<Response>` vuoto oppure fare clic su [http://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] per visualizzare un elemento `<Response>` contenente un elemento `<Say>`.

Anziché usare l'URL fornito da Twilio, è possibile creare un sito personalizzato che restituisce risposte HTTP. È possibile creare il sito in qualsiasi linguaggio che restituisca risposte XML. In questo argomento si presuppone che si userà PHP per creare TwiML.

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

Per altre informazioni su TwiML, vedere [https://www.twilio.com/docs/api/twiml][twiml_reference]. 

Dopo aver configurato la pagina PHP in modo da fornire risposte TwiML, usare l'URL della pagina PHP come URL passato nel metodo `Services_Twilio->account->calls->create`. Se, ad esempio, si dispone di un'applicazione Web denominata **MyTwiML** distribuita in un servizio ospitato in Azure e il nome della pagina PHP è **mytwiml.php**, è possibile passare l'URL a **Services_Twilio->account->calls->create**, come mostrato nell'esempio seguente:

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

Per altre informazioni sull'uso di Twilio in Azure con PHP, vedere [Come effettuare una chiamata tramite Twilio in un'applicazione PHP in Azure][howto_phonecall_php].

<h2><a id="AdditionalServices"></a>Procedura: Usare servizi Twilio aggiuntivi</h2>
Oltre agli esempi illustrati in questa pagina, Twilio offre API basate su Web che è possibile usare per sfruttare altre funzionalità di Twilio dall'applicazione Azure. Per informazioni dettagliate, vedere la [documentazione sull'API Twilio] [twilio_api_documentation].

<h2><a id="NextSteps"></a>Passaggi successivi</h2>
A questo punto, dopo aver appreso le nozioni di base del servizio Twilio, usare i collegamenti seguenti per altre informazioni:

* [Linee guida sulla sicurezza di Twilio] [twilio_security_guidelines]
* [Procedure e codice di esempio su Twilio] [twilio_howtos]
* [Esercitazioni con guide rapide su Twilio][twilio_quickstarts] 
* [Twilio su GitHub] [twilio_on_github]
* [Contattare il supporto di Twilio] [twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: http://readthedocs.org/docs/twilio-php/en/latest/index.html
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html

[howto_phonecall_php]: http://windowsazure.com/it-it/documentation/articles/partner-twilio-php-make-phone-call
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: http://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/api
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart
