<properties
	pageTitle="Come usare Twilio per le funzionalità voce e SMS (.NET) | Microsoft Azure"
	description="Informazioni su come effettuare una chiamata telefonica e inviare un SMS con il servizio API Twilio API in Azure. Esempi di codice scritti in .NET."
	services=""
	documentationCenter=".net"
	authors="devinrader"
	manager="twilio"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/24/2015"
	ms.author="devinrader"/>

# Come usare Twilio per le funzionalità voce ed SMS da Azure

In questa guida viene illustrato come eseguire attività di programmazione comuni con il servizio API Twilio in Azure. Gli scenari presentati includono la composizione di una chiamata telefonica e l'invio di un messaggio SMS (Short Message Service). Per altre informazioni su Twilio e sull'utilizzo delle funzionalità voce ed SMS nelle applicazioni, vedere la sezione [Passaggi successivi](#NextSteps).

## <a id="WhatIs"></a>Informazioni su Twilio
Twilio è una tecnologia all'avanguardia per le comunicazioni aziendali che consente agli sviluppatori di incorporare funzionalità voce, VoIP e di messaggistica nelle applicazioni. Consente di virtualizzare tutta l'infrastruttura necessaria in un ambiente globale basato su cloud, esponendolo attraverso la piattaforma API per le comunicazioni Twilio. Le applicazioni sono scalabili e facili da compilare. Offre flessibilità, grazie a un modello di prezzi con pagamento al consumo, e l'affidabilità del cloud.

**Twilio Voice** consente alle applicazioni di effettuare e ricevere chiamate telefoniche. **Twilio SMS** consente alle applicazioni di inviare e ricevere SMS. **Twilio Client** consente di effettuare chiamate VoIP da qualsiasi telefono, tablet o browser e supporta WebRTC.

## <a id="Pricing"></a>Prezzi e offerte speciali di Twilio
I clienti di Azure riceveranno un'[offerta speciale](http://www.twilio.com/azure): $ 10 di credito Twilio all'aggiornamento dell'account Twilio. Il credito Twilio può essere applicato a qualsiasi utilizzo di Twilio ($ 10 di credito equivalgono all'invio di 1.000 SMS o a 1.000 minuti voce per le chiamate in entrata, a seconda della località del numero di telefono, del messaggio o della destinazione della chiamata). Per riscattare il credito Twilio e iniziare a utilizzare il servizio, visitare la pagina all'indirizzo [ahoy.twilio.com/azure](http://ahoy.twilio.com/azure).

Twilio è un servizio con pagamento in base al consumo. Non prevede spese iniziali ed è possibile chiudere l'account in qualsiasi momento. Per altre informazioni, vedere la pagina relativa ai [prezzi di Twilio](http://www.twilio.com/voice/pricing).

## <a id="Concepts"></a>Concetti
L'API Twilio è un'API RESTful che fornisce funzionalità voce ed SMS per le applicazioni. Le librerie client sono disponibili in più lingue. Per un elenco, vedere la pagina relativa alle [librerie dell'API Twilio][twilio_libraries].

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

## <a id="create_app"></a>Creare un'applicazione Azure
Un'applicazione Azure che ospita un'applicazione compatibile con Twilio non è diversa da qualsiasi altra applicazione Azure. Aggiungere la libreria Twilio .NET e configurare il ruolo per l'uso delle librerie Twilio .NET. Per informazioni sulla creazione di un progetto Azure iniziale, vedere [Creazione di un progetto Azure con Visual Studio][vs_project].

## <a id="configure_app"></a>Configurare l'applicazione per l'uso delle librerie Twilio
Twilio fornisce un set di librerie helper .NET che copre vari aspetti di Twilio per fornire modi semplici e pratici per interagire con l'API REST di Twilio e il client Twilio per generare risposte TwiML.

Twilio offre cinque librerie per sviluppatori .NET:
Libreria|Descrizione
---|---
Twilio.API|La libreria Twilio di base che include l'API REST Twilio in una libreria .NET intuitiva. Questa libreria è disponibile per .NET, Silverlight e Windows Phone 7.
Twilio.TwiML|Fornisce un modo intuitivo per generare il markup TwiML in .NET.
Twilio.MVC|Per gli sviluppatori che utilizzano ASP.NET MVC, questa libreria include un controller TwilioController, una classe ActionResult TwiML e un attributo di convalida della richiesta.
Twilio.WebMatrix|Per gli sviluppatori che utilizzano lo strumento di sviluppo WebMatrix gratuito di Microsoft, questa libreria contiene gli helper della sintassi Razor per varie azioni Twilio.
Twilio.Client.Capability|Contiene il generatore di token Capability per l'utilizzo con l'SDK JavaScript per il client Twilio.

Si noti che tutte le librerie richiedono .NET 3.5, Silverlight 4 o Windows Phone 7 o versioni successive.

Nell'esempio illustrato in questa guida viene usata la libreria Twilio.API.

Le librerie possono essere [installate tramite l'estensione Gestione pacchetti NuGet](http://www.twilio.com/docs/csharp/install) disponibile per Visual Studio 2010 e 2012. Il codice sorgente è ospitato in [GitHub][twilio_github_repo], che include un wiki contenente la documentazione completa per l'uso delle librerie.

Per impostazione predefinita, con Microsoft Visual Studio 2010 viene installata la versione 1.2 di NuGet. L'installazione delle librerie Twilio richiede NuGet 1.6 o versioni successive. Per informazioni sull'installazione o l'aggiornamento di NuGet, vedere [http://nuget.org/][nuget].

> [AZURE.NOTE] Per installare la versione più recente di NuGet, è innanzitutto necessario disinstallare la versione caricata tramite Gestione estensioni di Visual Studio. A questo scopo, è necessario eseguire Visual Studio come amministratore. In caso contrario, il pulsante Disinstalla è disabilitato.

### <a id="use_nuget"></a>Per aggiungere le librerie Twilio al progetto di Visual Studio:

1.  Aprire la soluzione in Visual Studio.
2.  Fare clic con il pulsante destro del mouse su **Riferimenti**.
3.  Scegliere **Gestisci pacchetti NuGet...**
4.  Fare clic su **Online**.
5.  Nella casella di ricerca online digitare *twilio*.
6.  Fare clic su **Install** sul pacchetto Twilio.


## <a id="howto_make_call"></a>Procedura: Effettuare una chiamata in uscita
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

Per ulteriori informazioni sui parametri passati al metodo **client.InitiateOutboundCall**, vedere [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Come indicato in precedenza, questo codice utilizza un sito fornito da Twilio per restituire la risposta TwiML. Per fornire la risposta TwiML, è tuttavia possibile utilizzare il proprio sito. Per altre informazioni, vedere [Procedura: Fornire risposte TwiML dal proprio sito Web](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Procedura: Inviare un messaggio SMS
Nella schermata seguente è illustrato come inviare un messaggio SMS tramite la classe **TwilioRestClient**. Il numero in **From** per l'invio di messaggi SMS con gli account di valutazione è fornito da Twilio. Il numero in **To** deve essere verificato per l'account Twilio prima di eseguire il codice.

        // Use your account SID and authentication token instead
        // of the placeholders shown here.
        string accountSID = "your_twilio_account";
        string authToken = "your_twilio_authentication_token";

        // Create an instance of the Twilio client.
        TwilioRestClient client;
        client = new TwilioRestClient(accountSID, authToken);

        // Send an SMS message.
        Message result = client.SendMessage(
            "+14155992671", "+12069419717", "This is my SMS message.");

        if (result.RestException != null)
        {
            //an exception occurred making the REST call
            string message = result.RestException.Message;
        }

## <a id="howto_provide_twiml_responses"></a>Procedura: Fornire risposte TwiML dal proprio sito Web
Quando l'applicazione avvia una chiamata all'API Twilio, ad esempio tramite il metodo **client.InitiateOutboundCall**, Twilio invia la richiesta a un URL che deve restituire una risposta TwiML. L'esempio in [Procedura: Effettuare una chiamata in uscita](#howto_make_call) usa l'URL fornito da Twilio [http://twimlets.com/message][twimlet_message_url] per restituire la risposta.

> [AZURE.NOTE] Poiché TwiML è progettato per essere utilizzato da servizi Web, è possibile visualizzare il codice TwiML nel browser. Ad esempio, fare clic su [http://twimlets.com/message](twimlet_message_url) per visualizzare un elemento &lt;Response&gt; vuoto; per un altro esempio, fare clic su [http://twimlets.com/message?Message%5B0%5D=Hello%20World](twimlet_message_url_hello_world) per visualizzare un elemento &lt;Response&gt; contenente un elemento &lt;Say&gt;.

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

Come si evince dal codice riportato sopra, la risposta TwiML è semplicemente un documento XML. La libreria Twilio.TwiML contiene le classi che consentono di generare automaticamente le istruzioni TwiML. Nell'esempio seguente viene creata la stessa risposta descritta sopra, ma usando la classe TwilioResponse.

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

Per ulteriori informazioni su TwiML, vedere [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml).

Dopo avere configurato un modo per fornire risposte TwiML, è possibile passare l'URL nel metodo **client.InitiateOutboundCall**. Se, ad esempio, si dispone di un'applicazione Web denominata MyTwiML distribuita in un servizio cloud di Azure e il nome del gestore ASP.NET è mytwiml.ashx, è possibile passare l'URL a **client.InitiateOutboundCall** come illustrato nell'esempio di codice seguente:

    // Place the call From, To, and URL values into a hash map.
    // This sample uses the sandbox number provided by Twilio to make the call.
    options.From = "NNNNNNNNNN";
    options.To = "NNNNNNNNNN";
    options.Url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx";

    // Place the call.
    var call = client.InitiateOutboundCall(options);


Per altre informazioni sull'utilizzo di Twilio in Azure con ASP.NET, vedere [Come effettuare una chiamata tramite Twilio in un ruolo Web in Azure][howto_phonecall_dotnet].

[AZURE.INCLUDE [twilio-additional-services-and-next-steps](../includes/twilio-additional-services-and-next-steps.md)]





[howto_phonecall_dotnet]: partner-twilio-cloud-services-dotnet-phone-call-web-role.md



[twimlet_message_url]: http://twimlets.com/message

[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls

[vs_project]: http://msdn.microsoft.com/library/windowsazure/ee405487.aspx
[nuget]: http://nuget.org/
[twilio_github_repo]: https://github.com/twilio/twilio-csharp



[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]: https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#

<!---HONumber=AcomDC_0413_2016-->