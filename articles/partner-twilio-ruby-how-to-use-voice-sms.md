<properties urlDisplayName="Twilio Voice/SMS Service" pageTitle="Come usare Twilio per le funzionalità voce e SMS (Ruby) - Azure" metaKeywords="Twilio in Azure con Ruby, telefonate in Azure, telefonate in Azure, Twilio in Azure, SMS in Azure, SMS in Azure, chiamate vocali in Azure, chiamate vocali in Azure, messaggi di testo in Azure, messaggi di testo in Azure" description="Informazioni su come effettuare una chiamata telefonica e inviare un SMS con il servizio API Twilio API in Azure. Gli esempi di codice sono scritti in Ruby." metaCanonical="" services="" documentationCenter="Ruby" title="How to Use Twilio for Voice and SMS Capabilities in PHP" authors="MicrosoftHelp@twilio.com" solutions="" manager="twilio" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com" />





# Come usare Twilio per le funzionalità voce e SMS in Ruby
Questa guida illustra come eseguire attività di programmazione comuni con il servizio API Twilio in Azure. Gli scenari presentati includono la composizione di una chiamata telefonica e l'invio di un messaggio SMS (Short Message Service). Per altre informazioni su Twilio e sull'uso delle funzionalità voce e SMS nelle applicazioni, vedere la sezione [Passaggi successivi](#NextSteps).

## Sommario
* [Informazioni su Twilio](#WhatIs)
* [Prezzi di Twilio](#Pricing)
* [Concetti](#Concepts)
* [Creare un account Twilio](#CreateAccount)
* [Creare un'applicazione Sinatra in Ruby](#create_app)
* [Configurare l'applicazione per l'uso delle librerie Twilio](#configure_app)
* [Procedura: Effettuare una chiamata in uscita](#howto_make_call)
* [Procedura: Ricevere un messaggio SMS](#howto_recieve_sms)
* [Procedura: Usare servizi Twilio aggiuntivi](#additional_services)
* [Passaggi successivi](#NextSteps)

## <a id="WhatIs"></a>Informazioni su Twilio
Twilio è un'API per servizi Web di telefonia che consente di usare le competenze e i linguaggi Web esistenti per sviluppare applicazioni SMS e vocali. Twilio è un servizio di terze parti. Non si tratta di una funzionalità di Azure, né di un prodotto Microsoft.

**Twilio Voice** consente alle applicazioni di effettuare e ricevere chiamate telefoniche. **Twilio SMS** consente alle applicazioni di inviare e ricevere SMS. **Twilio Client** consente alle applicazioni di abilitare le comunicazioni vocali usando le connessioni Internet esistenti, comprese le connessioni mobili.

## <a id="Pricing"></a>Prezzi Twilio e offerte speciali
Per informazioni dettagliate, vedere la pagina relativa ai [prezzi di Twilio] [twilio_pricing]. Per i clienti di Azure è disponibile un'[offerta speciale][special_offer]: un credito gratuito per 1000 SMS o 1000 minuti di chiamate in entrata. Per avvalersi dell'offerta o per altre informazioni, visitare la pagina [http://ahoy.twilio.com/azure][special_offer].  

## <a id="Concepts"></a>Concetti
L'API Twilio è un'API RESTful che fornisce funzionalità voce e SMS per le applicazioni. Le librerie client sono disponibili in più lingue. Per un elenco, vedere la pagina relativa alle [librerie dell'API Twilio] [twilio_libraries].

### <a id="TwiML"></a>TwiML
TwiML è un set di istruzioni basate su XML che indicano a Twilio come elaborare una chiamata o un SMS.

Ad esempio, il codice TwiML seguente effettua la sintesi vocale del testo **Hello World**.

    ?xml version="1.0" encoding="UTF-8" ?
    <Response>
       <Say>Hello World</Say>
    </Response>

Tutti i documenti TwiML dispongono di un elemento radice `<Response>`, da cui è possibile usare i verbi Twilio per definire il comportamento dell'applicazione.

### <a id="Verbs"></a>Verbi TwiML
I verbi Twilio sono tag XML che indicano a Twilio le **azioni da eseguire**. Il verbo **&lt;Say&gt;**, ad esempio, indica a Twilio di recapitare un messaggio acustico in una chiamata. 

Di seguito è riportato un elenco dei verbi Twilio.

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

Per altre informazioni sui verbi Twilio, i relativi attributi e il codice TwiML, vedere [TwiML] [twiml]. Per altre informazioni sull'API Twilio, vedere la pagina relativa all'[API Twilio] [twilio_api].

## <a id="CreateAccount"></a>Creare un account Twilio
Se si vuole creare un account Twilio, effettuare l'iscrizione tramite la pagina relativa alla [registrazione gratuita di Twilio] [try_twilio]. È possibile iniziare con un account gratuito ed eseguire l'aggiornamento in un secondo momento.

Quando si effettua l'iscrizione a Twilio si riceve un numero di telefono gratuito per l'applicazione. Si ricevono inoltre il SID dell'account e un token di autorizzazione. Entrambe queste informazioni sono necessarie per effettuare chiamate all'API Twilio. Per prevenire accessi autorizzati all'account, conservare il token di autenticazione in un luogo sicuro. Il SID dell'account e il token di autorizzazione sono visualizzabili nella [pagina dell'account Twilio][twilio_account], rispettivamente nei campi **ACCOUNT SID** e **AUTH TOKEN**.

### <a id="VerifyPhoneNumbers"></a>Verificare i numeri di telefono
Oltre al numero assegnato da Twilio, è possibile verificare altri numeri di cui si abbia il controllo, ad esempio quello del cellulare o quello dell'abitazione, per usarli nelle applicazioni. 

Per informazioni su come verificare un numero di telefono, vedere la sezione relativa alla [gestione dei numeri] [verify_phone].

## <a id="create_app"></a>Creazione di un'applicazione Ruby
Un'applicazione Ruby che usa il servizio Twilio e viene eseguita in Azure non è diversa da qualsiasi altra applicazione Ruby che usa il servizio Twilio. Benché i servizi Twilio siano di tipo RESTful e possano essere chiamati da Ruby in molti modi, in questo articolo verrà illustrato solo come usare i servizi Twilio con la [libreria helper Twilio per Ruby][twilio_ruby].

In primo luogo, [configurare una nuova macchina virtuale Linux in Azure][azure_vm_setup] che funga da host per la nuova applicazione Web Ruby. Ignorare i passaggi relativi alla creazione di un'app Rails e configurare semplicemente la VM. Assicurarsi di creare un endpoint con porta esterna 80 e porta interna 5000.

Negli esempi che seguono verrà usato [Sinatra][sinatra], un framework Web molto semplice per Ruby. È comunque possibile usare la libreria helper Twilio per Ruby con qualsiasi altro framework Web, compreso Ruby on Rails.

Connettersi tramite SSH alla nuova macchina virtuale e creare una directory per la nuova app. All'interno di tale directory creare un file denominato Gemfile e copiare al suo interno il codice seguente:

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

Nella riga di comando eseguire `bundle install`. Le dipendenze precedenti verranno installate. Creare quindi un file denominato `web.rb`, in cui risiederà il codice per l'app Web. Copiare nel file il codice seguente:

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

A questo punto dovrebbe essere possibile eseguire il comando `ruby web.rb -p 5000`. Verrà avviato un server Web di dimensioni ridotte sulla porta 5000. Dovrebbe essere possibile passare all'app nel browser visitando l'URL configurato per la macchina virtuale di Azure. Non appena è possibile raggiungere l'app Web nel browser, è possibile iniziare a creare un'app Twilio.

## <a id="configure_app"></a>Configurare l'applicazione per l'uso delle librerie Twilio
Per configurare l'app Web per l'uso della libreria Twilio, aggiornare il file `Gemfile` in modo da includere la riga seguente:

    gem 'twilio-ruby'

Nella riga di comando eseguire `bundle install`. Aprire `web.rb` e includere la riga seguente all'inizio del file:

    require 'twilio-ruby'

È ora possibile usare la libreria helper Twilio per Ruby nell'app Web.

## <a id="howto_make_call"></a>Procedura: Effettuare una chiamata in uscita
Di seguito è illustrato come effettuare una chiamata in uscita. I principali concetti illustrati includono l'uso della libreria helper Twilio per Ruby per effettuare chiamate API REST e per il rendering di TwiML. Sostituire i valori per i numeri di telefono **From** e **To** e assicurarsi di verificare il numero di telefono in **From** per l'account Twilio prima di eseguire il codice.

Aggiungere a `web.md` la funzione seguente:

    # Set your account ID and authentication token.
	sid = "your_twilio_account_sid";
	token = "your_twilio_authentication_token";

	# The number of the phone initiating the the call.
    # This should either be a Twilio number or a number that you've verified
	from = "NNNNNNNNNNN";

	# The number of the phone receiving call.
	to = "NNNNNNNNNNN";

	# Use the Twilio-provided site for the TwiML response.
    url = "http://yourdomain.cloudapp.net/voice_url";
      
    get '/make_call' do
	  # Create the call client.
	  client = Twilio::REST::Client.new(sid, token);
      
      # Make the call
      client.account.calls.create(to: to, from: from, url: url)
    end

    post '/voice_url' do
      "<Response>
         <Say>Hello Monkey!</Say>
       </Response>"
    end
    
Aprendo l'indirizzo `http://yourdomain.cloudapp.net/make_call` in un browser verrà attivata la chiamata all'API Twilio per l'esecuzione della chiamata telefonica. I primi due parametri in `client.account.calls.create` sono facilmente comprensibili: `from` è il numero chiamante e `to` è il numero chiamato. 

l terzo parametro (`url`) è l'URL usato da Twilio per richiedere istruzioni sulle operazioni da eseguire dopo la connessione della chiamata. In questo caso verrà configurato un URL (`http://yourdomain.cloudapp.net`) che restituisce un documento TwiML molto semplice e usa il verbo `<Say>` per effettuare la sintesi vocale del testo e pronunciare la frase "Hello Monkey" al destinatario della chiamata.

## <a id="howto_recieve_sms"></a>Procedura: Ricevere un messaggio SMS
Nell'esempio precedente è stata avviata una chiamata telefonica **in uscita**. Questa volta verrà usato il numero ricevuto da Twilio dopo l'iscrizione per elaborare un SMS **in arrivo**.

In primo luogo, accedere al [dashboard Twilio][twilio_account]. Fare clic su "Numbers" sulla barra di spostamento superiore e quindi fare clic sul numero ricevuto da Twilio. Verranno visualizzati due URL che è possibile configurare, uno per le richieste vocali e uno per le richieste SMS. Si tratta degli URL chiamati da Twilio ogni volta che viene inviato un SMS o viene effettuata una chiamata telefonica verso il proprio numero. Gli URL sono noti anche come "hook Web".

Per elaborare gli SMS in arrivo è necessario aggiornare l'URL in `http://yourdomain.cloudapp.net/sms_url`. Effettuare la modifica e fare clic su Save Changes nella parte inferiore della pagina. Tornare a `web.rb` e programmare l'applicazione per la gestione dell'operazione seguente:

    post '/sms_url' do
      "<Response>
         <Sms>Hey, thanks for the ping! Twilio and Azure rock!</Sms>
       </Response>"
    end

Dopo aver apportato la modifica, riavviare l'app Web. Prendere il telefono e inviare un SMS al proprio numero Twilio. Si dovrebbe ricevere un SMS di risposta con il testo "Hey, thanks for the ping! Twilio and Azure rock!".

## <a id="additional_services"></a>Procedura: Usare servizi Twilio aggiuntivi
Oltre agli esempi illustrati in questa pagina, Twilio offre API basate su Web che è possibile usare per sfruttare altre funzionalità di Twilio dall'applicazione Azure. Per informazioni dettagliate, vedere la [documentazione sull'API Twilio] [twilio_api_documentation].

### <a id="NextSteps"></a>Passaggi successivi
Dopo aver appreso le nozioni di base sul servizio Twilio, usare i collegamenti seguenti per altre informazioni:

* [Linee guida sulla sicurezza di Twilio] [twilio_security_guidelines]
* [Procedure e codice di esempio su Twilio] [twilio_howtos]
* [Esercitazioni con guide rapide su Twilio][twilio_quickstarts] 
* [Twilio su GitHub] [twilio_on_github]
* [Contattare il supporto di Twilio] [twilio_support]

[twilio_ruby]: https://www.twilio.com/docs/ruby/install





[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
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
[sinatra]: http://www.sinatrarb.com/
[azure_vm_setup]: http://www.windowsazure.com/it-it/develop/ruby/tutorials/web-app-with-linux-vm/
