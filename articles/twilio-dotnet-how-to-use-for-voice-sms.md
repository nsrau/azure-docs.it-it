<properties linkid="develop-net-how-to-twilio-sms-service" urlDisplayName="Twilio Voice/SMS Service" pageTitle="How to Use Twilio for Voice and SMS (.NET) - Azure" metaKeywords="Azure Twilio, Azure phone calls, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in .NET." metaCanonical="" services="" documentationCenter=".NET" title="How to use Twilio for voice and SMS capabilities from Azure" authors="MicrosoftHelp@twilio.com" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com"></tags>

# Come utilizzare Twilio per le funzionalità voce ed SMS da Azure

In questa guida viene illustrato come eseguire attività di programmazione comuni con il servizio API Twilio in Azure. Gli scenari presentati includono la composizione di una chiamata telefonica e l'invio di un messaggio SMS (Short Message Service). Per altre informazioni su Twilio e sull'utilizzo delle funzionalità voce ed SMS nelle applicazioni, vedere la sezione [Passaggi successivi][].

## Sommario

-   [Informazioni su Twilio][]
-   [Prezzi di Twilio][]
-   [Concetti][]
-   [Creazione di un account Twilio][]
-   [Verifica dei numeri di telefono][]
-   [Creazione di un'applicazione Azure][]
-   [Configurazione dell'applicazione per l'utilizzo delle librerie Twilio][]
-   [Procedura: Effettuare una chiamata in uscita][]
-   [Procedura: Inviare un messaggio SMS][]
-   [Procedura: Fornire risposte TwiML dal proprio sito Web][]
-   [Procedura: Utilizzare servizi Twilio aggiuntivi][]
-   [Passaggi successivi][]

## <span id="WhatIs"></span></a>Informazioni su Twilio

Twilio è una tecnologia all'avanguardia per le comunicazioni aziendali che consente agli sviluppatori di incorporare funzionalità voce, VoIP e di messaggistica nelle applicazioni. Consente di virtualizzare tutta l'infrastruttura necessaria in un ambiente globale basato su cloud, esponendolo attraverso la piattaforma API per le comunicazioni Twilio. Le applicazioni sono scalabili e facili da compilare. Offre flessibilità, grazie a un modello di prezzi con pagamento al consumo, e l'affidabilità del cloud.

**Twilio Voice** consente alle applicazioni di effettuare e ricevere chiamate telefoniche. **Twilio SMS** consente alle applicazioni di inviare e ricevere SMS. **Twilio Client** consente di effettuare chiamate VoIP da qualsiasi telefono, tablet o browser e supporta WebRTC.

## <span id="Pricing"></span></a>Prezzi Twilio e offerte speciali

Per i clienti di Azure è disponibile un'[offerta speciale][]: credito Twilio aggiuntivo di $ 10 all'aggiornamento dell'account Twilio. Il credito Twilio può essere applicato a qualsiasi utilizzo di Twilio ($ 10 di credito equivalgono all'invio di 1.000 SMS o a 1.000 minuti voce per le chiamate in entrata, a seconda della località del numero di telefono, del messaggio o della destinazione della chiamata). Per riscattare il credito Twilio e iniziare a utilizzare il servizio, visitare la pagina all'indirizzo [ahoy.twilio.com/azure][].

Twilio è un servizio con pagamento in base al consumo. Non prevede spese iniziali ed è possibile chiudere l'account in qualsiasi momento. Per altre informazioni, vedere la pagina relativa ai [prezzi di Twilio][].

## <span id="Concepts"></span></a>Concetti

L'API Twilio è un'API RESTful che fornisce funzionalità voce ed SMS per le applicazioni. Le librerie client sono disponibili in più lingue. Per un elenco, vedere la pagina relativa alle [librerie dell'API Twilio][].

I concetti principali dell'API Twilio sono costituiti dai verbi Twilio e dal linguaggio di markup Twilio (Twilio Markup Language, TwiML).

### <span id="Verbs"></span></a>Verbi Twilio

Nell'API vengono utilizzati i verbi Twilio: il verbo **\<Say\>**, ad esempio, indica a Twilio di recapitare un messaggio acustico in una chiamata.

Di seguito è riportato un elenco dei verbi Twilio. Per altre informazioni su altri verbi e funzionalità, vedere la [documentazione relativa a Twilio Markup Language][].

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

Quando l'applicazione chiama l'API Twilio, uno dei parametri dell'API è l'URL che restituisce la risposta TwiML. Ai fini dello sviluppo, è possibile utilizzare gli URL offerti da Twilio per fornire le risposte TwiML utilizzate dalle applicazioni. È inoltre possibile ospitare gli URL per produrre le risposte TwiML oppure utilizzare l'oggetto **TwiMLResponse**.

Per altre informazioni sui verbi Twilio, i relativi attributi e il codice TwiML, vedere [TwiML][documentazione relativa a Twilio Markup Language]. Per altre informazioni sull'API Twilio, vedere la pagina relativa all'[API Twilio][].

## <span id="CreateAccount"></span></a>Creazione di un account Twilio

Se si desidera creare un account Twilio, iscriversi nella pagina relativa alla [registrazione gratuita di Twilio][]. È possibile iniziare con un account gratuito ed eseguire l'aggiornamento in un secondo momento.

Quando si effettua l'iscrizione a un account Twilio, si riceverà un ID account e un token di autenticazione. Entrambe queste informazioni sono necessarie per effettuare chiamate all'API Twilio. Per prevenire accessi autorizzati all'account, conservare il token di autenticazione in un luogo sicuro. L'ID account e il token di autorizzazione sono visualizzabili nella [pagina dell'account Twilio][], rispettivamente nei campi **ACCOUNT SID** e **AUTH TOKEN**.

## <span id="VerifyPhoneNumbers"></span></a>Verifica dei numeri di telefono

I numeri di telefono utilizzati dall'account devono essere verificati con Twilio. Ad esempio, se si desidera effettuare chiamate in uscita, il numero di telefono deve essere verificato come ID chiamante in uscita con Twilio. Allo stesso modo, se si desidera che un numero di telefono riceva messaggi SMS, il numero di telefono di destinazione deve essere verificato con Twilio. Per informazioni su come verificare un numero di telefono, vedere la sezione relativa alla [gestione dei numeri][]. Parte del codice illustrato di seguito si basa su numeri di telefono che sarà necessario verificare con Twilio.

In alternativa, anziché utilizzare un numero esistente per le applicazioni, è possibile acquistare un numero di telefono Twilio. Per informazioni sull'acquisto di un numero di telefono Twilio, vedere la [guida relativa ai numeri di telefono Twilio][].

## <span id="create_app"></span></a>Creazione di un'applicazione Azure

Un'applicazione Azure che ospita un'applicazione compatibile con Twilio non è diversa da qualsiasi altra applicazione Azure. È sufficiente aggiungere la libreria .NET Twilio e configurare il ruolo per l'uso delle librerie .NET Twilio.
Per informazioni sulla creazione di un progetto Azure iniziale, vedere [Creazione di un progetto Azure con Visual Studio][].

## <span id="configure_app"></span></a>Configurazione dell'applicazione per l'utilizzo delle librerie Twilio

Twilio fornisce un set di librerie helper .NET che copre vari aspetti di Twilio per fornire modi semplici e pratici per interagire con l'API REST di Twilio e il client Twilio per generare risposte TwiML.

Twilio offre cinque librerie per sviluppatori .NET:

<table border="1">

<tr>

<th>
Libreria

</th>

<th>
Descrizione

</th>

</tr>

<tr>

<td>
Twilio.API

</td>

<td>
La libreria Twilio di base che include l'API REST Twilio in una libreria .NET intuitiva. Questa libreria è disponibile per .NET, Silverlight e Windows Phone 7.

</td>

</tr>

<tr>

<td>
Twilio.TwiML

</td>

<td>
Fornisce un modo intuitivo per generare il markup TwiML in .NET.

</td>

</tr>

<tr>

<td>
Twilio.MVC

</td>

<td>
Per gli sviluppatori che utilizzano ASP.NET MVC, questa libreria include un controller TwilioController, una classe ActionResult TwiML e un attributo di convalida della richiesta.

</td>

</tr>

<tr>

<td>
Twilio.WebMatrix

</td>

<td>
Per gli sviluppatori che utilizzano lo strumento di sviluppo WebMatrix gratuito di Microsoft, questa libreria contiene gli helper della sintassi Razor per varie azioni Twilio.

</td>

</tr>

<tr>

<td>
Twilio.Client.Capability

</td>

<td>
Contiene il generatore di token Capability per l'utilizzo con l'SDK JavaScript per il client Twilio.

</td>

</tr>

</table>
</p>
Si noti che tutte le librerie richiedono .NET 3.5, Silverlight 4 o Windows Phone 7 o versioni successive.

Nell'esempio illustrato in questa guida viene usata la libreria Twilio.API.

Le librerie possono essere [installate tramite l'estensione Gestione pacchetti NuGet][] disponibile per Visual Studio 2010 e 2012. Il codice sorgente è ospitato in [GitHub][], che include un wiki contenente la documentazione completa per l'utilizzo delle librerie.

Per impostazione predefinita, con Microsoft Visual Studio 2010 viene installata la versione 1.2 di NuGet. L'installazione delle librerie Twilio richiede NuGet 1.6 o versioni successive. Per informazioni sull'installazione o l'aggiornamento di NuGet, vedere [][]<http://nuget.org/></a>.

<div class="dev-callout">
<b>Nota</b>
<p>Per installare la versione pi&ugrave; recente di NuGet, &egrave; innanzitutto necessario disinstallare la versione caricata tramite Gestione estensioni di Visual Studio. A questo scopo, &egrave; necessario eseguire Visual Studio come amministratore. In caso contrario, il pulsante Disinstalla &egrave; disabilitato.</p>
</div>

### <span id="use_nuget"></span></a>Per aggiungere le librerie Twilio al progetto di Visual Studio:

1.  Aprire la soluzione in Visual Studio.
2.  Fare clic con il pulsante destro del mouse su **Riferimenti**.
3.  Scegliere **Gestisci pacchetti NuGet...**
4.  Fare clic su **Online**.
5.  Nella casella di ricerca online digitare *twilio*.
6.  Fare clic su **Install** sul pacchetto Twilio.

## <span id="howto_make_call"></span></a>Procedura: Effettuare una chiamata in uscita

Di seguito è illustrato come effettuare una chiamata in uscita tramite la classe **TwilioRestClient**. Questo codice utilizza inoltre un sito fornito da Twilio per restituire la risposta TwiML (Twilio Markup Language). Sostituire i valori per i numeri di telefono **From** e **To** e assicurarsi di verificare il numero di telefono in **From** per l'account Twilio prima di eseguire il codice.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    string accountSID = "your_twilio_account";
    string authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Use the Twilio-provided site for the TwiML response.
    String Url="http://twimlets.com/message";
    Url = Url + "?Message%5B0%5D=Hello%20World";

    // Instantiate the call options that are passed
    // to the outbound call
    CallOptions options = new CallOptions();

    // Set the call From, To, and URL values to use for the call.
    // This sample uses the sandbox number provided by
    // Twilio to make the call.
    options.From = "+NNNNNNNNNN";
    options.To = "NNNNNNNNNN";
    options.Url = Url;

    // Make the call.
    var call = client.InitiateOutboundCall(options);

Per altre informazioni sui parametri passati al metodo **client.InitiateOutboundCall**, vedere [][1]<http://www.twilio.com/docs/api/rest/making-calls></a>.

Come indicato in precedenza, questo codice utilizza un sito fornito da Twilio per restituire la risposta TwiML. Per fornire la risposta TwiML, è tuttavia possibile utilizzare il proprio sito. Per altre informazioni, vedere [Procedura: Fornire risposte TwiML dal proprio sito Web][].

## <span id="howto_send_sms"></span></a>Procedura: Inviare un messaggio SMS

Nella schermata seguente è illustrato come inviare un messaggio SMS tramite la classe **TwilioRestClient**. Il numero in **From** per l'invio di messaggi SMS con gli account di valutazione è fornito da Twilio. Il numero in **To** deve essere verificato per l'account Twilio prima di eseguire il codice.

        // Use your account SID and authentication token instead
        // of the placeholders shown here.
        string accountSID = "your_twilio_account";
        string authToken = "your_twilio_authentication_token";

        // Create an instance of the Twilio client.
        TwilioRestClient client;
        client = new TwilioRestClient(accountSID, authToken);

        // Send an SMS message.
        SMSMessage result = client.SendSmsMessage(
            "+14155992671", "+12069419717", "This is my SMS message.");

        if (result.RestException != null)
        {
            //an exception occurred making the REST call
            string message = result.RestException.Message;
        }

## <span id="howto_provide_twiml_responses"></span></a>Procedura: Fornire risposte TwiML dal proprio sito Web

Quando l'applicazione avvia una chiamata all'API Twilio, ad esempio tramite il metodo **client.InitiateOutboundCall**, Twilio invia la richiesta a un URL che deve restituire una risposta TwiML. Nell'esempio illustrato in [Procedura: Effettuare una chiamata in uscita][] viene usato l'URL fornito da Twilio [][2]<http://twimlets.com/message></a> per restituire la risposta.

<div class="dev-callout">
<b>Nota</b>
<p>Poich&eacute; TwiML &egrave; progettato per essere utilizzato da servizi Web, &egrave; possibile visualizzare il codice TwiML nel browser. Ad esempio, fare clic su [http://twimlets.com/message](twimlet_message_url) per visualizzare un elemento &lt;Response&gt; vuoto oppure fare clic su [http://twimlets.com/message?Message%5B0%5D=Hello%20World](twimlet_message_url_hello_world) per visualizzare un elemento &lt;Response&gt; che contiene un elemento &lt;Say&gt;.</p>
</div>

Anziché utilizzare l'URL fornito da Twilio, è possibile creare un sito Web personalizzato che restituisce risposte HTTP. Il sito può essere creato in un linguaggio qualsiasi purché restituisca risposte HTTP. In questo argomento si presuppone che l'URL verrà ospitato da un gestore generico ASP.NET.

Il gestore ASP.NET seguente crea una risposta TwiML che pronuncia **Hello World** nella chiamata.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;

    namespace WebRole1
    {
        /// <summary>
        /// Summary description for Handler1
        /// </summary>
        public class Handler1 : IHttpHandler
        {

            public void ProcessRequest(HttpContext context)
            {
                context.Response.Clear();
                context.Response.ContentType = "text/xml";
                context.Response.ContentEncoding = System.Text.Encoding.UTF8;
                string twiMLResponse = "<Response><Say>Hello World.</Say></Response>";
                context.Response.Write(twiMLResponse);
                context.Response.End();
            }

            public bool IsReusable
            {
                get
                {
                    return false;
                }
            }
        }
    }

Come si evince dal codice riportato sopra, la risposta TwiML è semplicemente un documento XML. La libreria Twilio.TwiML contiene le classi che consentono di generare automaticamente le istruzioni TwiML. Nell'esempio seguente viene creata la stessa risposta descritta sopra, ma utilizzando la classe TwilioResponse.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;

    namespace WebRole1
    {
        /// <summary>
        /// Summary description for Handler1
        /// </summary>
        public class Handler1 : IHttpHandler
        {

            public void ProcessRequest(HttpContext context)
            {
                var twiml = new Twilio.TwiML.TwilioResponse();
                twiml.Say("Hello World.");

                context.Response.Clear();
                context.Response.ContentType = "text/xml";
                context.Response.Write(twiml.ToString());
                context.Response.End();
            }

            public bool IsReusable
            {
                get
                {
                    return false;
                }
            }
        }
    }

Per altre informazioni su TwiML, vedere [][3]<https://www.twilio.com/docs/api/twiml></a>.

Dopo avere configurato un modo per fornire risposte TwiML, è possibile passare l'URL nel metodo **client.InitiateOutboundCall**. Se, ad esempio, si dispone di un'applicazione Web denominata MyTwiML distribuita in un servizio cloud di Azure e il nome del gestore ASP.NET è mytwiml.ashx, è possibile passare l'URL a **client.InitiateOutboundCall** come illustrato nell'esempio di codice seguente:

    // Place the call From, To, and URL values into a hash map.
    // This sample uses the sandbox number provided by Twilio to make the call.
    options.From = "NNNNNNNNNN";
    options.To = "NNNNNNNNNN";
    options.Url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx";

    // Place the call.
    var call = client.InitiateOutboundCall(options);

Per altre informazioni sull'utilizzo di Twilio in Azure con ASP.NET, vedere [Come effettuare una chiamata tramite Twilio in un ruolo Web in Azure][].

[WACOM.INCLUDE [twilio\_additional\_services\_and\_next\_steps][]]

  [Passaggi successivi]: #NextSteps
  [Informazioni su Twilio]: #WhatIs
  [Prezzi di Twilio]: #Pricing
  [Concetti]: #Concepts
  [Creazione di un account Twilio]: #CreateAccount
  [Verifica dei numeri di telefono]: #VerifyPhoneNumbers
  [Creazione di un'applicazione Azure]: #create_app
  [Configurazione dell'applicazione per l'utilizzo delle librerie Twilio]: #configure_app
  [Procedura: Effettuare una chiamata in uscita]: #howto_make_call
  [Procedura: Inviare un messaggio SMS]: #howto_send_sms
  [Procedura: Fornire risposte TwiML dal proprio sito Web]: #howto_provide_twiml_responses
  [Procedura: Utilizzare servizi Twilio aggiuntivi]: #AdditionalServices
  [offerta speciale]: http://www.twilio.com/azure
  [ahoy.twilio.com/azure]: http://ahoy.twilio.com/azure
  [prezzi di Twilio]: http://www.twilio.com/voice/pricing
  [librerie dell'API Twilio]: https://www.twilio.com/docs/libraries
  [documentazione relativa a Twilio Markup Language]: http://www.twilio.com/docs/api/twiml
  [API Twilio]: http://www.twilio.com/api
  [registrazione gratuita di Twilio]: https://www.twilio.com/try-twilio
  [pagina dell'account Twilio]: https://www.twilio.com/user/account
  [gestione dei numeri]: https://www.twilio.com/user/account/phone-numbers/verified#
  [guida relativa ai numeri di telefono Twilio]: https://www.twilio.com/help/faq/phone-numbers
  [Creazione di un progetto Azure con Visual Studio]: http://msdn.microsoft.com/en-us/library/windowsazure/ee405487.aspx
  [installate tramite l'estensione Gestione pacchetti NuGet]: http://www.twilio.com/docs/csharp/install
  [GitHub]: https://github.com/twilio/twilio-csharp
  []: http://nuget.org/
  [1]: http://www.twilio.com/docs/api/rest/making-calls
  [2]: http://twimlets.com/message
  [3]: https://www.twilio.com/docs/api/twiml
  [Come effettuare una chiamata tramite Twilio in un ruolo Web in Azure]: ../partner-twilio-cloud-services-dotnet-phone-call-web-role/
  [twilio\_additional\_services\_and\_next\_steps]: ../includes/twilio_additional_services_and_next_steps.md
