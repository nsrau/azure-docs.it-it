<properties 
	pageTitle="Come usare Twilio per le funzionalità voce ed SMS (Java) - Azure" 
	description="Informazioni su come effettuare una chiamata telefonica e inviare un SMS con il servizio API Twilio API in Azure. Gli esempi di codice sono scritti in Java." 
	services="" 
	documentationCenter="java" 
	authors="devinrader" 
	manager="twilio" 
	editor="mollybos"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="11/25/2014" 
	ms.author="microsofthelp@twilio.com"/>

# Come usare Twilio per le funzionalità voce ed SMS in Java

In questa guida viene illustrato come eseguire attività di programmazione comuni con il servizio API Twilio in Azure. Gli scenari presentati includono la composizione di una chiamata telefonica e l'invio di un messaggio SMS (Short Message Service). Per ulteriori informazioni su Twilio e sull'utilizzo delle funzionalità voce ed SMS nelle applicazioni, vedere la sezione [Passaggi successivi](#NextSteps).

## <a id="WhatIs"></a>Informazioni su Twilio
Twilio è un'API per servizi Web di telefonia che consente di usare le competenze e i linguaggi Web esistenti per sviluppare applicazioni SMS e vocali. Twilio è un servizio di terze parti. Non si tratta di una funzionalità di Azure, né di un prodotto Microsoft.

**Twilio Voice** consente alle applicazioni di effettuare e ricevere chiamate telefoniche. **Twilio SMS** consente alle applicazioni di inviare e ricevere SMS. **Twilio Client** consente alle applicazioni di abilitare le comunicazioni vocali utilizzando le connessioni Internet esistenti, comprese le connessioni mobili.

## <a id="Pricing"></a>Prezzi e offerte speciali di Twilio
Per informazioni dettagliate, vedere la pagina relativa ai [prezzi di Twilio][twilio_pricing]. Per i clienti di Azure è disponibile un'[offerta speciale][special_offer]\: un credito gratuito per 1000 SMS o 1000 minuti di connessioni in entrata. Per avvalersi dell'offerta o per ulteriori informazioni, visitare la pagina [http://ahoy.twilio.com/azure][special_offer].

## <a id="Concepts"></a>Concetti
L'API Twilio è un'API RESTful che fornisce funzionalità voce ed SMS per le applicazioni. Le librerie client sono disponibili in più lingue. Per un elenco, vedere la pagina relativa alle [librerie dell'API Twilio][twilio_libraries].

I concetti principali dell'API Twilio sono costituiti dai verbi Twilio e dal linguaggio di markup Twilio (Twilio Markup Language, TwiML).

### <a id="Verbs"></a>Verbi Twilio
Nell'API vengono usati i verbi Twilio. Il verbo **&lt;Say&gt;**, ad esempio, indica a Twilio di fornire un messaggio acustico per una chiamata.

Di seguito è riportato un elenco dei verbi Twilio.

* **&lt;Dial&gt;**: connette il chiamante a un altro telefono.
* **&lt;Gather&gt;**: raccoglie i numeri digitati sul tastierino del telefono.
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

## <a id="create_app"></a>Creare un'applicazione Java
1. Ottenere il file JAR di Twilio e aggiungerlo al percorso di compilazione Java e all'assembly di distribuzione del file WAR. All'indirizzo [https://github.com/twilio/twilio-java][twilio_java] è possibile scaricare i file di origine disponibili in GitHub e creare un file JAR personalizzato o scaricarne uno precompilato, con o senza dipendenze.
2. Verificare che l'archivio chiavi **cacerts** del JDK contenga il certificato Equifax Secure Certificate Authority con ID digitale MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (il numero di serie è 35:DE:F4:CF e l'ID digitale SHA1 è D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Si tratta del certificato dell'Autorità di certificazione (CA) per il servizio [https://api.twilio.com][twilio_api_service], che viene chiamato quando si utilizzano le API Twilio. Per informazioni su come verificare che l'archivio chiavi **cacerts** del JDK contenga il certificato CA corretto, vedere [Aggiunta di un certificato all'archivio certificati CA di Java][add_ca_cert].

Per istruzioni dettagliate sull'utilizzo della libreria client Twilio per Java, vedere [Come effettuare una chiamata tramite Twilio in un'applicazione Java in Azure][howto_phonecall_java].

## <a id="configure_app"></a>Configurare l'applicazione per l'uso delle librerie Twilio
All'interno del codice è possibile aggiungere istruzioni **import** nella parte superiore dei file di origine per i pacchetti o le classi Twilio da utilizzare nell'applicazione.

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
Le istruzioni **import** possono variare in base ai pacchetti o alle classi Twilio che si desidera utilizzare.

## <a id="howto_make_call"></a>Procedura: Effettuare una chiamata in uscita
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

Per ulteriori informazioni sui parametri passati al metodo **CallFactory.create**, vedere [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Come indicato in precedenza, questo codice utilizza un sito fornito da Twilio per restituire la risposta TwiML. Per fornire la risposta TwiML è inoltre possibile utilizzare il proprio sito. Per ulteriori informazioni, vedere la [procedura per fornire risposte TwiML in un'applicazione Java in Azure](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Procedura: Inviare un messaggio SMS
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
    MessageFactory messageFactory = account.getMessageFactory();
    
    List<NameValuePair> params = new ArrayList<NameValuePair>();
    params.add(new BasicNameValuePair("To", "+14159352345")); // Replace with a valid phone number for your account.
    params.add(new BasicNameValuePair("From", "+14158141829")); // Replace with a valid phone number for your account.
    params.add(new BasicNameValuePair("Body", "Where's Wallace?"));
    
    Message sms = messageFactory.create(params);
        
Per ulteriori informazioni sui parametri passati al metodo **SmsFactory.create**, vedere [http://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms].

## <a id="howto_provide_twiml_responses"></a>Procedura: Fornire risposte TwiML dal proprio sito Web
Quando l'applicazione avvia una chiamata all'API Twilio, ad esempio tramite il metodo **CallFactory.create**, Twilio invia la richiesta a un URL che deve restituire una risposta TwiML. Nell'esempio precedente viene utilizzato l'URL fornito da Twilio [http://twimlets.com/message][twimlet_message_url]. Poiché TwiML è progettato per essere usato da servizi Web, è possibile visualizzare il codice TwiML nel browser. Ad esempio, fare clic su [http://twimlets.com/message][twimlet_message_url] per visualizzare un elemento **&lt;Response&gt;** vuoto oppure fare clic su [http://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] per visualizzare un elemento **&lt;Response&gt;** che contiene un elemento **&lt;Say&gt;**.

Anziché utilizzare l'URL fornito da Twilio, è possibile creare un sito Web personalizzato che restituisce risposte HTTP. È possibile creare il sito in qualsiasi linguaggio che restituisca risposte HTTP. In questo argomento si presuppone che l'URL verrà ospitato in una pagina JSP.

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

Il parametro **ApiVersion** è disponibile nelle richieste vocali Twilio, non nelle richieste SMS. Per visualizzare i parametri di richiesta disponibili per le richieste vocali e SMS di Twilio, vedere rispettivamente <https://www.twilio.com/docs/api/twiml/twilio_request> e <https://www.twilio.com/docs/api/twiml/sms/twilio_request>. La variabile di ambiente **RoleName** è disponibile come parte di una distribuzione Azure. Se si desidera aggiungere variabili di ambiente personalizzate in modo che possano essere recuperate da **System.getenv**, vedere la sezione relativa alle variabili di ambiente nelle [istruzioni sulle impostazioni di configurazione dei ruoli][misc_role_config_settings].

Dopo aver configurato la pagina JSP in modo da fornire risposte TwiML, utilizzare l'URL della pagina JSP come URL passato nel metodo **CallFactory.create**. Se, ad esempio, si dispone di un'applicazione Web denominata MyTwiML distribuita in un servizio ospitato in Azure e il nome della pagina JSP è mytwiml.jsp, è possibile passare l'URL a **CallFactory.create** come illustrato nell'esempio di codice seguente:

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN");
    params.put("To", "NNNNNNNNNN");
    params.put("Url", "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    CallFactory callFactory = account.getCallFactory();
    Call call = callFactory.create(params);

Un'altra possibilità per rispondere con TwiML consiste nell'utilizzare la classe **TwiMLResponse**, disponibile nel pacchetto **com.twilio.sdk.verbs**.

Per ulteriori informazioni sull'utilizzo di Twilio in Azure con Java, vedere [Come effettuare una chiamata tramite Twilio in un'applicazione Java in Azure][howto_phonecall_java].

## <a id="AdditionalServices"></a>Procedura: Utilizzare servizi Twilio aggiuntivi
Oltre agli esempi illustrati in questa pagina, Twilio offre API basate su Web che è possibile utilizzare per sfruttare altre funzionalità di Twilio dall'applicazione Azure. Per informazioni dettagliate, vedere la [documentazione sull'API Twilio][twilio_api_documentation].

## <a id="NextSteps"></a>Passaggi successivi
Dopo aver appreso le nozioni di base sul servizio Twilio, utilizzare i collegamenti seguenti per ulteriori informazioni:

* [Linee guida sulla sicurezza di Twilio][twilio_security_guidelines]
* [Procedure e codice di esempio di Twilio][twilio_howtos]
* [Esercitazioni con guide rapide su Twilio][twilio_quickstarts] 
* [Twilio su GitHub][twilio_on_github]
* [Contattare il supporto di Twilio][twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[howto_phonecall_java]: partner-twilio-java-phone-call-example.md
[misc_role_config_settings]: http://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: http://www.twilio.com/docs/api/rest/sending-sms
[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]: https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/api
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart

<!---HONumber=July15_HO5-->