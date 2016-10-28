<properties
	pageTitle="L'aggiornamento da Servizi mobili a Servizio app di Azure"
	description="Informazioni su come eseguire facilmente l'aggiornamento dell'applicazione Servizi mobili a un'app per dispositivi mobili del servizio app"
	services="app-service\mobile"
	documentationCenter=""
	authors="mattchenderson"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="07/25/2016"
	ms.author="mahender"/>

# Aggiornare il servizio mobile .NET di Azure esistente al servizio app

I dispositivi mobili del servizio app offrono un nuovo modo per creare applicazioni mobili usando Microsoft Azure. Per altre informazioni, vedere [Che cosa sono le app per dispositivi mobili?].

Questo argomento descrive come eseguire la migrazione di un'applicazione back-end .NET esistente da Servizi mobili di Azure a una nuova app per dispositivi mobili del servizio app. Durante una migrazione, l'applicazione Servizi mobili esistente può continuare a funzionare. Se si deve aggiornare un'applicazione back-end Node.js, vedere [Aggiornare il servizio mobile Node.js di Azure esistente al servizio app](./app-service-mobile-node-backend-upgrading-from-mobile-services.md).

Quando si esegue l'aggiornamento di un back-end per dispositivi mobili a Servizio app di Azure, questo ha accesso a tutte le funzionalità del servizio app e la fatturazione viene eseguita in base ai [prezzi del servizio app], non a quelli di Servizi mobili.

##Eseguire la migrazione o aggiornare

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

>[AZURE.TIP] Prima di procedere a un aggiornamento, è consigliabile [eseguire una migrazione](app-service-mobile-migrating-from-mobile-services.md). In questo modo, è possibile inserire entrambe le versioni dell'applicazione nello stesso piano del servizio app, senza sostenere costi aggiuntivi.

###Miglioramenti dell’SDK del server .NET di App per dispositivi mobili

L'aggiornamento al nuovo [SDK delle app per dispositivi mobili](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) offre i vantaggi seguenti:

- Maggiore flessibilità alle dipendenze di NuGet. L'ambiente di hosting non fornisce più le proprie versioni dei pacchetti NuGet, pertanto è possibile utilizzare le versioni compatibili alternative. Tuttavia, se sono presenti nuove correzioni critiche dei bug o aggiornamenti della protezione per l’SDK del Server mobile o dipendenze, è necessario aggiornare il servizio manualmente.

- Maggiore flessibilità nell’SDK per dispositivi mobili. È possibile controllare in modo esplicito le funzionalità e le route impostate, come autenticazione, API di tabella e endpoint di registrazione push. Per altre informazioni, vedere [Come usare l'SDK del server .NET per le app per dispositivi mobili di Azure](app-service-mobile-net-upgrading-from-mobile-services.md#server-project-setup).

- Supporto per altri tipi di progetto ASP.NET e route. È ora possibile ospitare controller MVC e API Web nello stesso progetto del progetto di back-end per dispositivi mobili.

- Supporto per le nuove funzionalità di autenticazione del Servizio app, che consentono di utilizzare una configurazione di autenticazione comune tra web e app per dispositivi mobili:

##<a name="overview"></a>Panoramica sull'aggiornamento di base

In molti casi, l'aggiornamento sarà semplice come passare al nuovo SDK del server di app per dispositivi mobili e ripubblicare il codice in una nuova istanza di app per dispositivi mobili. Esistono, tuttavia, alcuni scenari che richiedono alcune configurazioni aggiuntive, ad esempio l'autenticazione avanzata e l'uso dei processi pianificati. Ciascuno di questi scenari viene trattato nelle sezioni seguenti.

>[AZURE.TIP] Si consiglia di leggere e comprendere integralmente il resto di questo argomento prima di avviare un aggiornamento. Prendere nota delle funzionalità usate che sono indicate di seguito.

Gli SDK del client di Servizi mobili **non** sono compatibili con il nuovo SDK del server di App per dispositivi mobili. Per garantire la continuità del servizio per l'app, non devono essere pubblicate modifiche in un sito che usa client pubblicati. È invece necessario creare una nuova app per dispositivi mobili che agisce da duplicato. È possibile inserire questa applicazione nello stesso piano di servizio app per evitare di sostenere costi finanziari aggiuntivi.

Si avranno quindi due versioni dell'applicazione: una che rimane invariata e usa le app pubblicate e l'altra che è possibile aggiornare e usare come destinazione con una nuova versione del client. È possibile spostare e testare il codice in base alle esigenze, ma è necessario assicurarsi che tutte le correzioni di bug apportate vengano applicate a entrambe le versioni. Quando si ritiene di avere aggiornato alla versione più recente il numero desiderato di app client in circostanze normali, è possibile eliminare l'app migrata originale, se lo si desidera.

La struttura completa del processo di aggiornamento è la seguente:

1. Creare una nuova app per dispositivi mobili
2. Aggiornare il progetto per l'uso dei nuovi SDK del server
3. Rilasciare una nuova versione dell'applicazione client
4. (Facoltativo) Eliminare l'istanza migrata originale

##<a name="mobile-app-version"></a>Creazione di una seconda istanza dell'applicazione
Il primo passaggio per l'aggiornamento prevede la creazione della risorsa di app per dispositivi mobili che ospiterà la nuova versione dell'applicazione. Se è già stata eseguita la migrazione di un servizio mobile esistente, è consigliabile creare questa versione nello stesso piano di hosting. Aprire il [portale di Azure] e passare all'applicazione migrata. Prendere nota del piano di servizio app in cui è in esecuzione.

Creare quindi la seconda istanza dell'applicazione seguendo le [istruzioni per la creazione di back-end .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app). Quando viene richiesto di selezionare il piano di servizio app o "piano di hosting", scegliere il piano dell'applicazione migrata.

Potrebbe essere utile usare lo stesso database e hub di notifica usati in Servizi mobili. È possibile copiare questi valori aprendo il [portale di Azure] e passando all'applicazione originale, quindi facendo clic su **Impostazioni** > **Impostazioni applicazione**. In **Stringhe di connessione** copiare `MS_NotificationHubConnectionString` e `MS_TableConnectionString`. Passare al nuovo sito di aggiornamento e incollare i valori, sovrascrivendo gli eventuali valori esistenti. Ripetere questo processo per tutte le altre impostazioni dell'applicazione necessarie per l'app. Se non si usa un servizio migrato, è possibile leggere le stringhe di connessione e le impostazioni dell'app dalla scheda **Configura** della sezione Servizi mobili del [portale di Azure classico].

Creare una copia del progetto ASP.NET per l'applicazione e pubblicarla nel nuovo sito. Usando una copia dell'applicazione client aggiornata con il nuovo URL, verificare che tutto funzioni come previsto.

## Aggiornamento del progetto server

Le app per dispositivi mobili forniscono un nuovo [Mobile App Server SDK] che offre molte delle funzionalità del runtime di Servizi per dispositivi mobili. È innanzitutto necessario rimuovere tutti i riferimenti ai pacchetti di Servizi mobili. In Gestione pacchetti NuGet cercare `WindowsAzure.MobileServices.Backend`. Per la maggior parte delle app saranno presenti diversi pacchetti, tra cui `WindowsAzure.MobileServices.Backend.Tables` e `WindowsAzure.MobileServices.Backend.Entity`. In tal caso, iniziare dal pacchetto più basso nella struttura delle dipendenze, ad esempio `Entity`, e rimuoverlo. Quando richiesto, non rimuovere tutti i pacchetti dipendenti. Ripetere questo processo finché non è stato rimosso `WindowsAzure.MobileServices.Backend` stesso.

A questo punto si avrà un progetto che non fa più riferimento agli SDK di Servizi mobili.

Si aggiungeranno quindi i riferimenti agli SDK delle app per dispositivi mobili. Per questo aggiornamento, la maggior parte degli sviluppatori vorrà scaricare e installare il pacchetto `Microsoft.Azure.Mobile.Server.Quickstart`, che permette di ottenere l'intero set necessario.

Vi saranno alcuni errori del compilatore derivanti dalle differenze tra gli SDK, ma sono semplici da risolvere e vengono descritti nella parte rimanente di questa sezione.

### Configurazione di base

In WebApiConfig.cs è quindi possibile sostituire:

        // Use this class to set configuration options for your mobile service
        ConfigOptions options = new ConfigOptions();

        // Use this class to set WebAPI configuration options
        HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));

con

        HttpConfiguration config = new HttpConfiguration();
        new MobileAppConfiguration()
            .UseDefaultConfiguration()
        .ApplyTo(config);

>[AZURE.NOTE] Per altre informazioni sul nuovo SDK del server .NET e su come aggiungere/rimuovere funzionalità dall'app, vedere l'argomento [Come usare l'SDK del server .NET].

Se l'app fa uso delle funzionalità di autenticazione, sarà inoltre necessario registrare un middleware OWIN. In questo caso, è necessario spostare il codice di configurazione precedente in una nuova classe di avvio OWIN.

1. Aggiungere il pacchetto NuGet `Microsoft.Owin.Host.SystemWeb` se non è già incluso nel progetto.
2. In Visual Studio, fare clic con il pulsante destro del mouse sul progetto e selezionare **Aggiungi** -> **Nuovo elemento**. Selezionare **Web** -> **Generale** -> **Classe di avvio OWIN**.
3. Spostare il codice precedente per MobileAppConfiguration da `WebApiConfig.Register()` nel metodo `Configuration()` per la nuova classe di avvio.

Assicurarsi che il metodo `Configuration()` termini con:

        app.UseWebApi(config)
        app.UseAppServiceAuthentication(config);

Vi sono ulteriori modifiche relative all'autenticazione, che sono illustrate di seguito nella sezione relativa all'autenticazione completa.

### Uso dei dati

In Servizi mobili, il nome dell'app per dispositivi mobili veniva usato come nome dello schema predefinito nel programma di installazione di Entity Framework.

Per assicurarsi di fare riferimento allo stesso schema, usare il codice seguente per impostare lo schema in DbContext per l'applicazione:

        string schema = System.Configuration.ConfigurationManager.AppSettings.Get("MS_MobileServiceName");

Assicurarsi di avere impostato MS\_MobileServiceName se si esegue l'operazione precedente. È anche possibile fornire un altro nome di schema se l'applicazione lo ha personalizzato in precedenza.

### Proprietà di sistema

#### Denominazione

Nell'SDK del server di Servizi mobili di Azure le proprietà di sistema contengono sempre un prefisso con un doppio carattere di sottolineatura (`__`):

- \_\_createdAt
- \_\_updatedAt
- \_\_deleted
- \_\_version

Gli SDK client di Servizi mobili hanno una logica speciale per l'analisi delle proprietà di sistema in questo formato.

Nelle app per dispositivi mobili di Azure le proprietà di sistema non hanno più un formato speciale e sono denominate come segue:

- createdAt
- updatedAt
- deleted
- version

Gli SDK client delle app per dispositivi mobili usano i nuovi nomi delle proprietà di sistema, pertanto non sono necessarie modifiche al codice client. Tuttavia, se si effettuano direttamente chiamate REST al servizio, è necessario modificare le query di conseguenza.

#### Archivio locale

Le modifiche ai nomi delle proprietà di sistema implicano che un database locale di sincronizzazione offline per Servizi mobili non è compatibile con le applicazioni per dispositivi mobili. Se possibile, evitare di eseguire l'aggiornamento di app client da Servizi mobili alle app per dispositivi mobili finché le modifiche in sospeso non sono state inviate al server. L'app aggiornata deve quindi usare un nuovo nome di file di database.

Se un'app client viene aggiornata da Servizi mobili alle app per dispositivi mobili mentre sono presenti modifiche offline in sospeso nella coda delle operazioni, il database di sistema deve essere aggiornato per l'uso dei nuovi nomi di colonna. In iOS, questa operazione può essere eseguita usando migrazioni semplificate per modificare i nomi di colonna. In Android e nel client gestito .NET, è necessario scrivere codice SQL personalizzato per rinominare le colonne per le tabelle degli oggetti dati.

In iOS, è necessario modificare lo schema dei dati di base per le entità di dati in modo che corrispondano a quanto segue. Si noti che le proprietà `createdAt`, `updatedAt` e `version` non hanno più un prefisso `ms_`:

| Attributo | Tipo | Nota |
|---------- |  ------ | -----------------------------------------------------|
| id | Stringa, contrassegnata come obbligatoria | chiave primaria nell'archivio remoto |
| createdAt | Data | (facoltativo) viene mappato alla proprietà di sistema createdAt |
| updatedAt | Data | (facoltativo) viene mappato alla proprietà di sistema updatedAt |
| version | String | (facoltativo) usato per il rilevamento dei conflitti, viene mappato a version |

#### Query delle proprietà di sistema

In Servizi mobili di Azure, le proprietà di sistema non vengono inviate per impostazione predefinita, ma solo quando vengono richieste usando la stringa di query `__systemProperties`. Al contrario, nel sistema App per dispositivi mobili di Azure le proprietà sono **sempre selezionate** poiché fanno parte del modello a oggetti dell'SDK server.

Questa modifica influisce principalmente sulle implementazioni personalizzate di gestori di dominio, come le estensioni di `MappedEntityDomainManager`. In Servizi mobili, se un client non richiede mai proprietà del sistema, è possibile usare un `MappedEntityDomainManager` che non esegue effettivamente il mapping di tutte le proprietà. Tuttavia, in App per dispositivi mobili di Azure, queste proprietà non mappate genereranno un errore nelle query GET.

Il modo più semplice per risolvere il problema consiste nel modificare gli oggetti DTO in modo che ereditino da `ITableData` anziché `EntityData`. Aggiungere quindi l'attributo `[NotMapped]` ai campi che devono essere omessi.

Ad esempio, il codice seguente definisce `TodoItem` senza proprietà di sistema:

    using System.ComponentModel.DataAnnotations.Schema;

    public class TodoItem : ITableData
    {
        public string Text { get; set; }

        public bool Complete { get; set; }

        public string Id { get; set; }

        [NotMapped]
        public DateTimeOffset? CreatedAt { get; set; }

        [NotMapped]
        public DateTimeOffset? UpdatedAt { get; set; }

        [NotMapped]
        public bool Deleted { get; set; }

        [NotMapped]
        public byte[] Version { get; set; }
    }

Nota: se si verificano errori in `NotMapped`, aggiungere un riferimento all'assembly `System.ComponentModel.DataAnnotations`.

### CORS

Servizi mobili includeva un certo supporto per CORS eseguendo il wrapping della soluzione CORS di ASP.NET. Questo livello di wrapping è stato rimosso per offrire allo sviluppatore un maggiore controllo, pertanto è possibile sfruttare direttamente il [supporto per CORS di ASP.NET](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api).

Le principali aree problematiche rispetto all'uso di CORS sono rappresentate dal fatto che le intestazioni `eTag` e `Location` devono essere consentite per il corretto funzionamento degli SDK client.

### Notifiche push
Per il push, l'elemento principale che potrebbe risultare mancante dall'SDK del server è la classe PushRegistrationHandler. Le registrazioni vengono gestite in modo leggermente diverso nelle app per dispositivi mobili e le registrazioni prive di tag sono abilitate per impostazione predefinita. La gestione dei tag può essere eseguita usando API personalizzate. Per altre informazioni, vedere le istruzioni relative alla [registrazione per i tag](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags).

### Processi pianificati
I processi pianificati non vengono compilati nelle app per dispositivi mobili, pertanto sarà necessario eseguire l'aggiornamento dei singoli processi esistenti eventualmente presenti nel back-end .NET. Una possibilità consiste nel creare un [processo Web] pianificato nel sito del codice dell'app per dispositivi mobili. È anche possibile impostare un controller che contiene il codice del processo e configurare l'[Utilità di pianificazione di Azure] per accedere a tale endpoint in base alla pianificazione prevista.

### Modifiche varie
Tutti gli ApiController che verranno usati da un client mobile ora devono disporre dell'attributo `[MobileAppController]`. Questo non è più incluso per impostazione predefinita, in modo che gli altri ApiController non siano interessati dai formattatori per dispositivi mobili.

L'oggetto `ApiServices` non è più incluso nell'SDK. Per accedere alle impostazioni dell'app per dispositivi mobili, è possibile usare il codice seguente:

    MobileAppSettingsDictionary settings = this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

Analogamente, la registrazione viene ora effettuata mediante la scrittura di tracce ASP.NET standard:

    ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
    traceWriter.Info("Hello, World");  

##<a name="authentication"></a>Considerazioni sull'autenticazione

I componenti di autenticazione di Servizi mobili sono ora stati spostati nella funzionalità di autenticazione/autorizzazione del servizio app. Per informazioni sull'abilitazione di questa funzionalità per il sito, vedere l'argomento [Aggiungere l'autenticazione all'app per dispositivi mobili](app-service-mobile-ios-get-started-users.md).

Per alcuni provider, come AAD, Facebook e Google, dovrebbe essere possibile sfruttare la registrazione esistente dall'applicazione di copia. È sufficiente passare al portale del provider di identità e aggiungere un nuovo URL di reindirizzamento alla registrazione. Configurare quindi l'autenticazione/autorizzazione del servizio app con l'ID client e il segreto.

### Autorizzazione dell'azione del controller
Tutte le istanze dell'attributo `[AuthorizeLevel(AuthorizationLevel.User)]` ora devono essere modificate per l'uso dell'attributo ASP.NET standard `[Authorize]`. Inoltre, i controller ora sono anonimi per impostazione predefinita, come nelle altre applicazioni ASP.NET. Se si usava una delle altre opzioni AuthorizeLevel, ad esempio Admin o Application, tenere presente che ora non sono più disponibili. È invece possibile impostare AuthorizationFilters per i segreti condivisi o configurare un'entità servizio AAD per abilitare le chiamate da servizio a servizio in modo sicuro.

### Recupero di informazioni aggiuntive sull'utente

È possibile ottenere informazioni aggiuntive sull'utente, inclusi i token di accesso tramite il metodo `GetAppServiceIdentityAsync()`:

        FacebookCredentials creds = await this.User.GetAppServiceIdentityAsync<FacebookCredentials>();

Inoltre, se l'applicazione stabilisce dipendenze su ID utente, ad esempio memorizzandole in un database, è importante notare che gli ID utente tra Servizi mobili e App per dispositivi mobili del servizio app sono diversi. È tuttavia ancora possibile ottenere l'ID utente di Servizi mobili. Tutte le sottoclassi ProviderCredentials hanno una proprietà UserId. Pertanto, continuando dall'esempio precedente:

        string mobileServicesUserId = creds.Provider + ":" + creds.UserId;

Se l'app stabilisce dipendenze sugli ID utente, è importante sfruttare la stessa registrazione con un provider di identità, se possibile. Gli ID utente sono in genere destinati alla registrazione dell'applicazione che è stata usata, pertanto l'introduzione di una nuova registrazione potrebbe creare problemi di associazione degli utenti ai relativi dati.

### Autenticazione personalizzata

Se l'app usa una soluzione di autenticazione personalizzata, è necessario assicurarsi che il sito aggiornato abbia accesso al sistema. Seguire le nuove istruzioni per l'autenticazione personalizzata nella [panoramica di .NET Server SDK] per integrare la soluzione. Si noti che i componenti dell'autenticazione personalizzata sono ancora in versione di anteprima.

##<a name="updating-clients"></a>Aggiornamento dei client
Dopo aver reso operativo un back-end dell'app per dispositivi mobili, è possibile lavorare su una nuova versione dell'applicazione client che ne faccia uso. App per dispositivi mobili include anche una nuova versione degli SDK del client e, come nel caso dell'aggiornamento del server descritto in precedenza, sarà necessario rimuovere tutti i riferimenti agli SDK di Servizi mobili prima di installare le versioni di App per dispositivi mobili.

Una delle principali modifiche tra le versioni è rappresentata dai costruttori che non richiedono più una chiave applicazione. Ora è sufficiente passare l'URL dell'app per dispositivi mobili. Ad esempio, nei client .NET, il costruttore `MobileServiceClient` ora è:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

Per altre informazioni sull'installazione dei nuovi SDK e sull'uso della nuova struttura, visitare i collegamenti seguenti:

- [iOS versione 3.0.0 o successiva](app-service-mobile-ios-how-to-use-client-library.md)
- [.NET (Windows/Xamarin) versione 2.0.0 o successiva](app-service-mobile-dotnet-how-to-use-client-library.md)

Se l'applicazione usa le notifiche push, prendere nota delle specifiche istruzioni di registrazione per ogni piattaforma, perché sono state apportate alcune modifiche anche in questo ambito.

Quando la nuova versione del client è pronta, provarla con il progetto server aggiornato. Dopo averne verificato il funzionamento, è possibile rilasciare una nuova versione dell'applicazione per i clienti. Infine, quando i clienti avranno ricevuto gli aggiornamenti, sarà possibile eliminare la versione di Servizi mobili dell'app. A questo punto, sono stati aggiornati completamente a un'App per dispositivi mobili del servizio app utilizzando l’SDK più recente del server di App per dispositivi mobili.

<!-- URLs. -->

[portale di Azure]: https://portal.azure.com/
[portale di Azure classico]: https://manage.windowsazure.com/
[Che cosa sono le app per dispositivi mobili?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /it-IT/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Utilità di pianificazione di Azure]: /it-IT/documentation/services/scheduler/
[processo Web]: ../app-service-web/websites-webjobs-resources.md
[Come usare l'SDK del server .NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[prezzi del servizio app]: https://azure.microsoft.com/it-IT/pricing/details/app-service/
[panoramica di .NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md

<!---HONumber=AcomDC_0727_2016-->