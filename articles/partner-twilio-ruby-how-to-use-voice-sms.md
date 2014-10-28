<properties linkid="develop-ruby-how-to-twilio-sms-voice-service" urlDisplayName="Twilio Voice/SMS Service" pageTitle="How to Use Twilio for Voice and SMS (Ruby) - Azure" metaKeywords="Azure Ruby Twilio, Azure phone calls, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in Ruby." metaCanonical="" services="" documentationCenter="Ruby" title="How to Use Twilio for Voice and SMS Capabilities in PHP" authors="MicrosoftHelp@twilio.com" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com"></tags>

# Come usare Twilio per le funzionalità voce ed SMS in Ruby

In questa guida viene illustrato come eseguire attività di programmazione comuni con il servizio API Twilio in Azure. Gli scenari presentati includono la composizione di una chiamata telefonica e l'invio di un messaggio SMS (Short Message Service). Per altre informazioni su Twilio e sull'utilizzo delle funzionalità voce ed SMS nelle applicazioni, vedere la sezione [Passaggi successivi][Passaggi successivi].

## Sommario

-   [Informazioni su Twilio][Informazioni su Twilio]
-   [Prezzi di Twilio][Prezzi di Twilio]
-   [Concetti][Concetti]
-   [Creazione di un account Twilio][Creazione di un account Twilio]
-   [Creazione di un'applicazione Sinatra in Ruby][Creazione di un'applicazione Sinatra in Ruby]
-   [Configurare l'applicazione per l'uso delle librerie Twilio][Configurare l'applicazione per l'uso delle librerie Twilio]
-   [Procedura: Effettuare una chiamata in uscita][Procedura: Effettuare una chiamata in uscita]
-   [Procedura: Ricevere un messaggio SMS][Procedura: Ricevere un messaggio SMS]
-   [Procedura: Usare servizi Twilio aggiuntivi][Procedura: Usare servizi Twilio aggiuntivi]
-   [Passaggi successivi][Passaggi successivi]

## <span id="WhatIs"></span></a>Informazioni su Twilio

Twilio è un'API per servizi Web di telefonia che consente di usare le competenze e i linguaggi Web esistenti per sviluppare applicazioni SMS e vocali. Twilio è un servizio di terze parti. Non si tratta di una funzionalità di Azure, né di un prodotto Microsoft.

**Twilio Voice** consente alle applicazioni di effettuare e ricevere chiamate telefoniche. **Twilio SMS** consente alle applicazioni di inviare e ricevere SMS. **Twilio Client** consente alle applicazioni di abilitare le comunicazioni vocali usando le connessioni Internet esistenti, comprese le connessioni mobili.

## <span id="Pricing"></span></a>Prezzi Twilio e offerte speciali

Per informazioni dettagliate, vedere la pagina relativa ai [prezzi di Twilio][prezzi di Twilio]. Per i clienti di Azure è disponibile un'[offerta speciale][offerta speciale]: un credito gratuito per 1000 SMS o 1000 minuti di chiamate in entrata. Per avvalersi dell'offerta o per altre informazioni, visitare la pagina [][offerta speciale]<http://ahoy.twilio.com/azure></a>.

## <span id="Concepts"></span></a>Concetti

L'API Twilio è un'API RESTful che fornisce funzionalità voce ed SMS per le applicazioni. Le librerie client sono disponibili in più lingue. Per un elenco, vedere la pagina relativa alle [librerie dell'API Twilio][librerie dell'API Twilio].

### <span id="TwiML"></span></a>TwiML

TwiML è un set di istruzioni basate su XML che indicano a Twilio come elaborare una chiamata o un SMS.

Ad esempio, il codice TwiML seguente effettua la sintesi vocale del testo **Hello World**.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Tutti i documenti TwiML dispongono di un elemento radice `<Response>`, da cui è possibile usare i verbi Twilio per definire il comportamento dell'applicazione.

### <span id="Verbs"></span></a>Verbi TwiML

I verbi Twilio sono tag XML che indicano a Twilio le **azioni da eseguire**. Il verbo **\<Say\>**, ad esempio, indica a Twilio di recapitare un messaggio acustico in una chiamata.

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

Per altre informazioni sui verbi Twilio, i relativi attributi e il codice TwiML, vedere [TwiML][TwiML]. Per altre informazioni sull'API Twilio, vedere la pagina relativa all'[API Twilio][API Twilio].

## <span id="CreateAccount"></span></a>Creazione di un account Twilio

Se si desidera creare un account Twilio, iscriversi nella pagina relativa alla [registrazione gratuita di Twilio][registrazione gratuita di Twilio]. È possibile iniziare con un account gratuito ed eseguire l'aggiornamento in un secondo momento.

Quando si effettua l'iscrizione a Twilio si riceve un numero di telefono gratuito per l'applicazione. Si ricevono inoltre il SID dell'account e un token di autorizzazione. Entrambe queste informazioni sono necessarie per effettuare chiamate all'API Twilio. Per prevenire accessi autorizzati all'account, conservare il token di autenticazione in un luogo sicuro. Il SID dell'account e il token di autorizzazione sono visualizzabili nella [pagina dell'account Twilio][pagina dell'account Twilio], rispettivamente nei campi **ACCOUNT SID** e **AUTH TOKEN**.

### <span id="VerifyPhoneNumbers"></span></a>Verifica dei numeri di telefono

Oltre al numero assegnato da Twilio, è possibile verificare altri numeri di cui si abbia il controllo, ad esempio quello del cellulare o quello dell'abitazione, per utilizzarli nelle applicazioni.

Per informazioni su come verificare un numero di telefono, vedere la sezione relativa alla [gestione dei numeri][gestione dei numeri].

## <span id="create_app"></span></a>Creazione di un'applicazione Ruby

Un'applicazione Ruby che utilizza il servizio Twilio e viene eseguita in Azure non è diversa da qualsiasi altra applicazione Ruby che utilizza il servizio Twilio. Benché i servizi Twilio siano di tipo RESTful e possano essere chiamati da Ruby in molti modi, in questo articolo verrà illustrato solo come usare i servizi Twilio con la [libreria Twilio per Ruby][libreria Twilio per Ruby].

In primo luogo, [configurare una nuova macchina virtuale Linux in Azure][configurare una nuova macchina virtuale Linux in Azure] che funga da host per la nuova applicazione Web Ruby. Ignorare i passaggi relativi alla creazione di un'app Rails e configurare semplicemente la VM. Assicurarsi di creare un endpoint con porta esterna 80 e porta interna 5000.

Negli esempi che seguono verrà usato [Sinatra][Sinatra], un framework Web molto semplice per Ruby. È comunque possibile usare la libreria helper Twilio per Ruby con qualsiasi altro web framework, compreso Ruby on Rails.

Connettersi tramite SSH alla nuova macchina virtuale e creare una directory per la nuova app. All'interno di tale directory creare un file denominato Gemfile e copiare al suo interno il codice seguente:

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

Nella riga di comando eseguire `bundle install`. Le dipendenze precedenti verranno installate. Creare quindi un file denominato `web.rb` in cui risiederà il codice per l'app Web. Copiare nel file il codice seguente:

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

A questo punto dovrebbe essere possibile eseguire il comando `ruby web.rb -p 5000`. Verrà avviato un server Web di dimensioni ridotte sulla porta 5000. Dovrebbe essere possibile passare all'app nel browser visitando l'URL configurato per la macchina virtuale di Azure. Non appena è possibile raggiungere l'app Web nel browser, è possibile iniziare a creare un'app Twilio.

## <span id="configure_app"></span></a>Configurare l'applicazione per l'uso delle librerie Twilio

Per configurare l'app Web per l'uso della libreria Twilio, aggiornare il file `Gemfile` in modo da includere la riga seguente:

    gem 'twilio-ruby'

Nella riga di comando eseguire `bundle install`. Aprire `web.rb` e includere la riga seguente all'inizio del file:

    require 'twilio-ruby'

È ora possibile usare la libreria helper Twilio per Ruby nell'app Web.

## <span id="howto_make_call"></span></a>Procedura: Effettuare una chiamata in uscita

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

Aprendo l'indirizzo `http://yourdomain.cloudapp.net/make_call` in un browser verrà attivata la chiamata all'API Twilio per l'esecuzione della chiamata telefonica. I primi due parametri in `client.account.calls.create` sono facilmente comprensibili: è il numero chiamante e `from` è il numero chiamato.`to`

Il terzo parametro (`url`) è l'URL utilizzato da Twilio per richiedere istruzioni sulle operazioni da eseguire dopo la connessione della chiamata. In questo caso verrà configurato un URL (`http://yourdomain.cloudapp.net`) che restituisce un documento TwiML molto semplice e utilizza il verbo `<Say>` per effettuare la sintesi vocale del testo e pronunciare la frase "Hello Monkey" al destinatario della chiamata.

## <span id="howto_recieve_sms"></span></a>Procedura: Ricevere un messaggio SMS

Nell'esempio precedente è stata avviata una chiamata telefonica **in uscita**. Questa volta verrà usato il numero ricevuto da Twilio dopo l'iscrizione per elaborare un SMS **in arrivo**.

In primo luogo, accedere al [dashboard Twilio][pagina dell'account Twilio]. Fare clic su "Numbers" sulla barra di spostamento superiore e quindi fare clic sul numero ricevuto da Twilio. Verranno visualizzati due URL che è possibile configurare, uno per le richieste vocali e uno per le richieste SMS. Si tratta degli URL chiamati da Twilio ogni volta che viene inviato un SMS o viene effettuata una chiamata telefonica verso il proprio numero. Gli URL sono noti anche come "hook Web".

Per elaborare gli SMS in arrivo è necessario aggiornare l'URL in `http://yourdomain.cloudapp.net/sms_url`. Effettuare la modifica e fare clic su Save Changes nella parte inferiore della pagina. Tornare a `web.rb` e programmare l'applicazione per la gestione dell'operazione seguente:

    post '/sms_url' do
      "<Response>
         <Sms>Hey, thanks for the ping! Twilio and Azure rock!</Sms>
       </Response>"
    end

Dopo aver apportato la modifica, riavviare l'app Web. Prendere il telefono e inviare un SMS al proprio numero Twilio. Si dovrebbe ricevere un SMS di risposta con il testo "Hey, thanks for the ping! Twilio and Azure rock!".

## <span id="additional_services"></span></a>Procedura: Usare servizi Twilio aggiuntivi

Oltre agli esempi illustrati in questa pagina, Twilio offre API basate su Web che è possibile usare per sfruttare altre funzionalità di Twilio dall'applicazione Azure. Per informazioni dettagliate, vedere la [documentazione sull'API Twilio][API Twilio].

### <span id="NextSteps"></span></a>Passaggi successivi

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
  [Creazione di un'applicazione Sinatra in Ruby]: #create_app
  [Configurare l'applicazione per l'uso delle librerie Twilio]: #configure_app
  [Procedura: Effettuare una chiamata in uscita]: #howto_make_call
  [Procedura: Ricevere un messaggio SMS]: #howto_recieve_sms
  [Procedura: Usare servizi Twilio aggiuntivi]: #additional_services
  [prezzi di Twilio]: http://www.twilio.com/pricing
  [offerta speciale]: http://ahoy.twilio.com/azure
  [librerie dell'API Twilio]: https://www.twilio.com/docs/libraries
  [TwiML]: http://www.twilio.com/docs/api/twiml
  [API Twilio]: http://www.twilio.com/api
  [registrazione gratuita di Twilio]: https://www.twilio.com/try-twilio
  [pagina dell'account Twilio]: https://www.twilio.com/user/account
  [gestione dei numeri]: https://www.twilio.com/user/account/phone-numbers/verified#
  [libreria Twilio per Ruby]: https://www.twilio.com/docs/ruby/install
  [configurare una nuova macchina virtuale Linux in Azure]: http://www.windowsazure.com/it-it/develop/ruby/tutorials/web-app-with-linux-vm/
  [Sinatra]: http://www.sinatrarb.com/
  [Linee guida sulla sicurezza di Twilio]: http://www.twilio.com/docs/security
  [Procedure e codice di esempio su Twilio]: http://www.twilio.com/docs/howto
  [Esercitazioni con guide rapide su Twilio]: http://www.twilio.com/docs/quickstart
  [Twilio su GitHub]: https://github.com/twilio
  [Contattare il supporto di Twilio]: http://www.twilio.com/help/contact
