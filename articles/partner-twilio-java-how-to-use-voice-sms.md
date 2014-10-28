<properties linkid="develop-java-how-to-twilio-sms-service" urlDisplayName="Twilio Voice/SMS Service" pageTitle="How to Use Twilio for Voice and SMS (Java) - Azure" metaKeywords="Twilio, Twilio API, phone calls, SMS message, TwiML responses, Azure Twilio Java" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in Java." metaCanonical="" services="" videoId="" scriptId="" documentationCenter="Java" title="How to Use Twilio for Voice and SMS Capabilities in Java" authors="MicrosoftHelp@twilio.com; robmcm" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com; robmcm"></tags>

# Come usare Twilio per le funzionalità voce ed SMS in Java

In questa guida viene illustrato come eseguire attività di programmazione comuni con il servizio API Twilio in Azure. Gli scenari presentati includono la composizione di una chiamata telefonica e l'invio di un messaggio SMS (Short Message Service). Per altre informazioni su Twilio e sull'utilizzo delle funzionalità voce ed SMS nelle applicazioni, vedere la sezione [Passaggi successivi][Passaggi successivi].

## Sommario

-   [Informazioni su Twilio][Informazioni su Twilio]
-   [Prezzi di Twilio][Prezzi di Twilio]
-   [Concetti][Concetti]
-   [Creazione di un account Twilio][Creazione di un account Twilio]
-   [Verifica dei numeri di telefono][Verifica dei numeri di telefono]
-   [Creare un'applicazione Java][Creare un'applicazione Java]
-   [Configurare l'applicazione per l'uso delle librerie Twilio][Configurare l'applicazione per l'uso delle librerie Twilio]
-   [Procedura: Effettuare una chiamata in uscita][Procedura: Effettuare una chiamata in uscita]
-   [Procedura: Inviare un messaggio SMS][Procedura: Inviare un messaggio SMS]
-   [Procedura: Fornire risposte TwiML dal proprio sito Web][Procedura: Fornire risposte TwiML dal proprio sito Web]
-   [Procedura: Usare servizi Twilio aggiuntivi][Procedura: Usare servizi Twilio aggiuntivi]
-   [Passaggi successivi][Passaggi successivi]

## <span id="WhatIs"></span></a>Informazioni su Twilio

Twilio è un'API per servizi Web di telefonia che consente di usare le competenze e i linguaggi Web esistenti per sviluppare applicazioni SMS e vocali. Twilio è un servizio di terze parti. Non si tratta di una funzionalità di Azure, né di un prodotto Microsoft.

**Twilio Voice** consente alle applicazioni di effettuare e ricevere chiamate telefoniche. **Twilio SMS** consente alle applicazioni di inviare e ricevere SMS. **Twilio Client** consente alle applicazioni di abilitare le comunicazioni vocali usando le connessioni Internet esistenti, comprese le connessioni mobili.

## <span id="Pricing"></span></a>Prezzi Twilio e offerte speciali

Per informazioni dettagliate, vedere la pagina relativa ai [prezzi di Twilio][prezzi di Twilio]. Per i clienti di Azure è disponibile un'[offerta speciale][offerta speciale]: un credito gratuito per 1000 SMS o 1000 minuti di chiamate in entrata. Per avvalersi dell'offerta o per altre informazioni, visitare la pagina [][offerta speciale]<http://ahoy.twilio.com/azure></a>.

## <span id="Concepts"></span></a>Concetti

L'API Twilio è un'API RESTful che fornisce funzionalità voce ed SMS per le applicazioni. Le librerie client sono disponibili in più lingue. Per un elenco, vedere la pagina relativa alle [librerie dell'API Twilio][librerie dell'API Twilio].

I concetti principali dell'API Twilio sono costituiti dai verbi Twilio e dal linguaggio di markup Twilio (Twilio Markup Language, TwiML).

### <span id="Verbs"></span></a>Verbi Twilio

Nell'API vengono utilizzati i verbi Twilio: il verbo **\<Say\>**, ad esempio, indica a Twilio di recapitare un messaggio acustico in una chiamata.

Di seguito è riportato un elenco dei verbi Twilio.

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

Quando si effettua l'iscrizione a un account Twilio, si riceverà un ID account e un token di autenticazione. Entrambe queste informazioni sono necessarie per effettuare chiamate all'API Twilio. Per prevenire accessi autorizzati all'account, conservare il token di autenticazione in un luogo sicuro. L'ID account e il token di autorizzazione sono visualizzabili nella [pagina dell'account Twilio][pagina dell'account Twilio], rispettivamente nei campi **ACCOUNT SID** e **AUTH TOKEN**.

## <span id="VerifyPhoneNumbers"></span></a>Verifica dei numeri di telefono

I numeri di telefono utilizzati dall'account devono essere verificati con Twilio. Ad esempio, se si desidera effettuare chiamate in uscita, il numero di telefono deve essere verificato come ID chiamante in uscita con Twilio. Allo stesso modo, se si desidera che un numero di telefono riceva messaggi SMS, il numero di telefono di destinazione deve essere verificato con Twilio. Per informazioni su come verificare un numero di telefono, vedere la sezione relativa alla [gestione dei numeri][gestione dei numeri]. Parte del codice illustrato di seguito si basa su numeri di telefono che sarà necessario verificare con Twilio.

In alternativa, anziché usare un numero esistente per le applicazioni, è possibile acquistare un numero di telefono Twilio. Per informazioni sull'acquisto di un numero di telefono Twilio, vedere la [guida relativa ai numeri di telefono Twilio][guida relativa ai numeri di telefono Twilio].

## <span id="create_app"></span></a>Creare un'applicazione Java

1.  Ottenere il file JAR di Twilio e aggiungerlo al percorso di compilazione Java e all'assembly di distribuzione del file WAR. All'indirizzo [][]<https://github.com/twilio/twilio-java></a> è possibile scaricare i file di origine disponibili in GitHub e creare un file JAR personalizzato o scaricarne uno precompilato, con o senza dipendenze.
2.  Verificare che l'archivio chiavi **cacerts** del JDK contenga il certificato Equifax Secure Certificate Authority con ID digitale MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (il numero di serie è 35:DE:F4:CF e l'ID digitale SHA1 è D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Si tratta del certificato dell'Autorità di certificazione (CA) per il servizio [][1]<https://api.twilio.com></a>, che viene chiamato quando si utilizzano le API Twilio. Per informazioni su come verificare che l'archivio chiavi **cacerts** del JDK contenga il certificato CA corretto, vedere [Aggiunta di un certificato all'archivio certificati CA di Java][Aggiunta di un certificato all'archivio certificati CA di Java].

Per istruzioni dettagliate sull'utilizzo della libreria client Twilio per Java, vedere [Come effettuare una chiamata tramite Twilio in un'applicazione Java in Azure][Come effettuare una chiamata tramite Twilio in un'applicazione Java in Azure].

## <span id="configure_app"></span></a>Configurare l'applicazione per l'uso delle librerie Twilio

All'interno del codice è possibile aggiungere istruzioni **import** nella parte superiore dei file di origine per i pacchetti o le classi Twilio da usare nell'applicazione.

Per i file di origine Java:

    import com.twilio.*;
    import com.twilio.sdk.*;
    import com.twilio.sdk.resource.factory.*;
    import com.twilio.sdk.resource.instance.*;

Per i file di origine JSP (Java Server Page):

    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"

Le istruzioni **import** possono variare in base ai pacchetti o alle classi Twilio che si desidera usare.

## <span id="howto_make_call"></span></a>Procedura: Effettuare una chiamata in uscita

Di seguito è illustrato come effettuare una chiamata in uscita tramite la classe **CallFactory**. Questo codice utilizza inoltre un sito fornito da Twilio per restituire la risposta TwiML (Twilio Markup Language). Sostituire i valori per i numeri di telefono **From** e **To** e assicurarsi di verificare il numero di telefono in **From** per l'account Twilio prima di eseguire il codice.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the CallFactory.
    Account account = client.getAccount();

    // Use the Twilio-provided site for the TwiML response.
    String Url="http://twimlets.com/message";
    Url = Url + "?Message%5B0%5D=Hello%20World";

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN"); // Use your own value for the second parameter.
    params.put("To", "NNNNNNNNNN");   // Use your own value for the second parameter.
    params.put("Url", Url);

    // Create an instance of the CallFactory class.
    CallFactory callFactory = account.getCallFactory();

    // Make the call.
    Call call = callFactory.create(params);

Per altre informazioni sui parametri passati al metodo **CallFactory.create**, vedere [][2]<http://www.twilio.com/docs/api/rest/making-calls></a>.

Come indicato in precedenza, questo codice utilizza un sito fornito da Twilio per restituire la risposta TwiML. Per fornire la risposta TwiML è inoltre possibile usare il proprio sito. Per altre informazioni, vedere la [procedura per fornire risposte TwiML in un'applicazione Java in Azure][Procedura: Fornire risposte TwiML dal proprio sito Web].

## <span id="howto_send_sms"></span></a>Procedura: Inviare un messaggio SMS

Nella schermata seguente è illustrato come inviare un messaggio SMS tramite la classe **SmsFactory**. Il numero **4155992671** in **From** per l'invio di messaggi SMS con gli account di valutazione è fornito da Twilio. Il numero in **To** deve essere verificato per l'account Twilio prima di eseguire il codice.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the SmsFactory.
    Account account = client.getAccount();

    // Send an SMS message.
    // Place the call From, To and Body values into a hash map. 
    HashMap<String, String> smsParams = new HashMap<String, String>();
    smsParams.put("From", "4155992671"); // The second parameter is a phone number provided
                                         // by Twilio for trial accounts.
    smsParams.put("To", "NNNNNNNNNN");   // Use your own value for the second parameter.
    smsParams.put("Body", "This is my SMS message.");

    // Create an instance of the SmsFactory class.
    SmsFactory smsFactory = account.getSmsFactory();

    // Send the message.
    Sms sms = smsFactory.create(smsParams);

Per altre informazioni sui parametri passati al metodo **SmsFactory.create**, vedere [][3]<http://www.twilio.com/docs/api/rest/sending-sms></a>.

## <span id="howto_provide_twiml_responses"></span></a>Procedura: Fornire risposte TwiML dal proprio sito Web

Quando l'applicazione avvia una chiamata all'API Twilio, ad esempio tramite il metodo **CallFactory.create**, Twilio invia la richiesta a un URL che deve restituire una risposta TwiML. Nell'esempio precedente viene usato l'URL fornito da Twilio [][4]<http://twimlets.com/message></a>. Poiché TwiML è progettato per essere usato da servizi Web, è possibile visualizzare il codice TwiML nel browser. Ad esempio, fare clic su [][4]<http://twimlets.com/message></a> per visualizzare un elemento **\<Response\>** vuoto oppure fare clic su [][5]<http://twimlets.com/message?Message%5B0%5D=Hello%20World></a> per visualizzare un elemento **\<Response\>** contenente un elemento **\<Say\>**.

Anziché usare l'URL fornito da Twilio, è possibile creare un sito Web personalizzato che restituisce risposte HTTP. È possibile creare il sito in qualsiasi linguaggio che restituisca risposte HTTP. In questo argomento si presuppone che l'URL verrà ospitato in una pagina JSP.

La pagina JSP seguente crea una risposta TwiML che pronuncia **Hello World** nella chiamata.

    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello World</Say>
    </Response>

La pagina JSP seguente crea una risposta TwiML che pronuncia del testo, contiene diverse pause e pronuncia informazioni sulla versione dell'API Twilio e sul nome del ruolo di Azure.

    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello from Azure</Say>
        <Pause></Pause>
        <Say>The Twilio API version is <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>The Azure role name is <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Good bye.</Say>
    </Response>

Il parametro **ApiVersion** è disponibile nelle richieste vocali Twilio, non nelle richieste SMS. Per visualizzare i parametri di richiesta disponibili per le richieste vocali e SMS di Twilio, vedere rispettivamente <https://www.twilio.com/docs/api/twiml/twilio_request> e <https://www.twilio.com/docs/api/twiml/sms/twilio_request>. La variabile di ambiente **RoleName** è disponibile come parte di una distribuzione Azure. Se si desidera aggiungere variabili di ambiente personalizzate in modo che possano essere recuperate da **System.getenv**, vedere la sezione relativa alle variabili di ambiente nelle [istruzioni sulle impostazioni di configurazione dei ruoli][istruzioni sulle impostazioni di configurazione dei ruoli].

Dopo aver configurato la pagina JSP in modo da fornire risposte TwiML, usare l'URL della pagina JSP come URL passato nel metodo **CallFactory.create**. Se, ad esempio, si dispone di un'applicazione Web denominata MyTwiML distribuita in un servizio ospitato in Azure e il nome della pagina JSP è mytwiml.jsp, è possibile passare l'URL a **CallFactory.create** come illustrato nell'esempio di codice seguente:

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN");
    params.put("To", "NNNNNNNNNN");
    params.put("Url", "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    CallFactory callFactory = account.getCallFactory();
    Call call = callFactory.create(params);

Un'altra possibilità per rispondere con TwiML consiste nell'usare la classe **TwiMLResponse**, disponibile nel pacchetto **com.twilio.sdk.verbs**.

Per altre informazioni sull'utilizzo di Twilio in Azure con Java, vedere [Come effettuare una chiamata tramite Twilio in un'applicazione Java in Azure][Come effettuare una chiamata tramite Twilio in un'applicazione Java in Azure].

## <span id="AdditionalServices"></span></a>Procedura: Usare servizi Twilio aggiuntivi

Oltre agli esempi illustrati in questa pagina, Twilio offre API basate su Web che è possibile usare per sfruttare altre funzionalità di Twilio dall'applicazione Azure. Per informazioni dettagliate, vedere la [documentazione sull'API Twilio][API Twilio].

## <span id="NextSteps"></span></a>Passaggi successivi

Dopo aver appreso le nozioni di base sul servizio Twilio, usare i collegamenti seguenti per altre informazioni:

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
  [Creare un'applicazione Java]: #create_app
  [Configurare l'applicazione per l'uso delle librerie Twilio]: #configure_app
  [Procedura: Effettuare una chiamata in uscita]: #howto_make_call
  [Procedura: Inviare un messaggio SMS]: #howto_send_sms
  [Procedura: Fornire risposte TwiML dal proprio sito Web]: #howto_provide_twiml_responses
  [Procedura: Usare servizi Twilio aggiuntivi]: #AdditionalServices
  [prezzi di Twilio]: http://www.twilio.com/pricing
  [offerta speciale]: http://ahoy.twilio.com/azure
  [librerie dell'API Twilio]: https://www.twilio.com/docs/libraries
  [TwiML]: http://www.twilio.com/docs/api/twiml
  [API Twilio]: http://www.twilio.com/api
  [registrazione gratuita di Twilio]: https://www.twilio.com/try-twilio
  [pagina dell'account Twilio]: https://www.twilio.com/user/account
  [gestione dei numeri]: https://www.twilio.com/user/account/phone-numbers/verified#
  [guida relativa ai numeri di telefono Twilio]: https://www.twilio.com/help/faq/phone-numbers
  []: https://github.com/twilio/twilio-java
  [1]: https://api.twilio.com
  [Aggiunta di un certificato all'archivio certificati CA di Java]: ../java-add-certificate-ca-store
  [Come effettuare una chiamata tramite Twilio in un'applicazione Java in Azure]: ../partner-twilio-java-phone-call-example
  [2]: http://www.twilio.com/docs/api/rest/making-calls
  [3]: http://www.twilio.com/docs/api/rest/sending-sms
  [4]: http://twimlets.com/message
  [5]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
  [istruzioni sulle impostazioni di configurazione dei ruoli]: http://msdn.microsoft.com/it-it/library/windowsazure/hh690945.aspx
  [Linee guida sulla sicurezza di Twilio]: http://www.twilio.com/docs/security
  [Procedure e codice di esempio su Twilio]: http://www.twilio.com/docs/howto
  [Esercitazioni con guide rapide su Twilio]: http://www.twilio.com/docs/quickstart
  [Twilio su GitHub]: https://github.com/twilio
  [Contattare il supporto di Twilio]: http://www.twilio.com/help/contact
