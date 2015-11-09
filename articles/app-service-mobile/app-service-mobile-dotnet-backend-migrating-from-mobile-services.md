<properties 
	pageTitle="Eseguire la migrazione da Servizi mobili a un'app per dispositivi mobili del servizio app" 
	description="Informazioni su come eseguire facilmente la migrazione dell'applicazione Servizi mobili a un'app per dispositivi mobili del servizio app" 
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
	ms.date="08/11/2015" 
	ms.author="mahender"/>

# Eseguire la migrazione del servizio mobile di Azure esistente a un'app per dispositivi mobili del servizio app

Questo argomento descrive come eseguire la migrazione di un'applicazione esistente da Servizi mobili di Azure a una nuova app per dispositivi mobili del servizio app. È possibile eseguire la migrazione di tutte le app di Servizi mobili esistenti a una nuova app per dispositivi mobili del servizio app. Durante una migrazione, l'applicazione Servizi mobili esistente può continuano a funzionare. Nel corso del tempo, il processo di migrazione diventerà ancora più semplice, ma gli utenti che vogliono eseguire subito la migrazione possono usare i passaggi seguenti.

>[AZURE.NOTE]Le migrazioni sono attualmente supportate solo per i clienti che usano il back-end .NET di Servizi mobili. Le applicazioni che usano il back-end Node.js dovranno per il momento rimanere in Servizi mobili.

##<a name="understand"></a>Informazioni sulle app per dispositivi mobili del servizio app

Le app per dispositivi mobili del servizio app offrono un nuovo modo per creare applicazioni mobili usando Microsoft Azure. Altre informazioni sulle app per dispositivi mobili sono disponibili nell'argomento [Informazioni sulle app per dispositivi mobili].

In una migrazione ad app per dispositivi mobili, tutte le funzionalità, e il codice, delle app esistenti possono essere conservate. Inoltre, sono disponibili nuove funzionalità per l'applicazione. Nel modello di app per dispositivi mobili il codice viene effettivamente eseguito in un'app Web (la nuova versione di Siti Web di Azure). L'utente ha il controllo completo sull'app Web e sul suo funzionamento. Inoltre, ora possono essere usate funzionalità delle app Web che in precedenza non erano disponibili ai clienti di Servizi mobili, ad esempio Gestione traffico e Slot di sviluppo.

Il nuovo modello risolve inoltre una delle principali difficoltà associate all'uso di Servizi mobili. Ora qualsiasi nuova versione di un pacchetto NuGet può essere distribuita senza doversi preoccupare dei conflitti di dipendenza. Altre informazioni sui vantaggi della migrazione sono disponibili nell'argomento [Qual è l'utilità del servizio app per chi usa già i siti Web e i Servizi mobili?].

##<a name="overview"></a>Panoramica della migrazione di base
Il modo più semplice per eseguire la migrazione consiste nel creare una nuova istanza di un'app per dispositivi mobili del servizio app. In molti casi, la migrazione sarà semplice come passare al nuovo SDK del server e ripubblicare il codice in una nuova app per dispositivi mobili. Esistono, tuttavia, alcuni scenari che richiedono alcune configurazioni aggiuntive, ad esempio l'autenticazione avanzata e l'uso dei processi pianificati. Ciascuno di questi scenari viene trattato nelle sezioni seguenti.

>[AZURE.NOTE]Si consiglia di leggere e comprendere integralmente il resto di questo argomento prima di avviare una migrazione. Prendere nota delle funzionalità usate che sono indicate di seguito.

È possibile spostare e testare il codice in base alle proprie esigenze. Quando il back-end dell'app per dispositivi mobili è pronto, è possibile rilasciare una nuova versione dell'applicazione client. A questo punto, saranno disponibili due copie del back-end dell'applicazione eseguite contemporaneamente. È necessario assicurarsi che le correzioni apportate vengano applicate a entrambe le copie. Infine, una volta che gli utenti hanno effettuato l'aggiornamento alla versione più recente, è possibile eliminare il servizio mobile originale.

Il set completo di passaggi per la migrazione è il seguente:

1. Creare e configurare una nuova app per dispositivi mobili
2. Risolvere gli eventuali problemi di autenticazione
3. Rilasciare una nuova versione dell'applicazione client
4. Eliminare l'istanza di Servizi mobili originale


##<a name="mobile-app-version"></a>Impostazione di una versione di app per dispositivi mobili dell'applicazione
Il primo passaggio per la migrazione prevede la creazione della risorsa di app per dispositivi mobili che ospiterà la nuova versione dell'applicazione. È possibile creare una nuova app per dispositivi mobili in [Anteprima del portale di gestione di Azure]. È possibile consultare l'argomento [Creare un'app per dispositivi mobili] per altre informazioni.

Potrebbe essere utile usare lo stesso database e hub di notifica usati in Servizi mobili. È possibile copiare questi valori dalla scheda **Configura** della sezione Servizi mobili del [portale di gestione di Azure]. In **Stringhe di connessione** copiare `MS_NotificationHubConnectionString` e `MS_TableConnectionString`. Passare al sito dell'app per dispositivi mobili, selezionare **Impostazioni**, **Impostazioni applicazione**, quindi aggiungere questi valori alla sezione **Stringhe di connessione**, sovrascrivendo eventuali valori esistenti.

Le app per dispositivi mobili forniscono un nuovo [Mobile App Server SDK] che offre molte delle funzionalità del runtime di Servizi mobili. È prima di tutto necessario rimuovere il pacchetto NuGet per Servizi mobili dal progetto esistente e includere invece l'SDK del server. Per questa migrazione, la maggior parte degli sviluppatori vorrà scaricare e installare il pacchetto `Microsoft.Azure.Mobile.Server.Quickstart`, che permette di ottenere l'intero set necessario. In WebApiConfig.cs è quindi possibile sostituire

    // Use this class to set configuration options for your mobile service
    ConfigOptions options = new ConfigOptions();
    
    // Use this class to set WebAPI configuration options
    HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));

con

    HttpConfiguration config = new HttpConfiguration();

    new MobileAppConfiguration()
	    .UseDefaultConfiguration()
	    .ApplyTo(config);


>[AZURE.NOTE]Per altre informazioni sul nuovo SDK del server e su come aggiungere/rimuovere funzionalità dall'app, vedere l'argomento [Come usare l'SDK del server .NET].

Esistono altre modifiche tra Servizi mobili e gli SDK per le app per dispositivi mobili, ma sono facili da gestire. Potrebbe essere necessario modificare alcune istruzioni using nell'intero progetto, avvalendosi delle funzionalità di Visual Studio.

È necessario aggiungere l'attributo `[MobileAppController]` a tutti gli ApiController, inserendo semplicemente un elemento Decorator immediatamente prima della definizione della classe.

Non è più disponibile un attributo `[AuthorizeLevel]` ed è invece necessario decorare i controller e i metodi con l'attributo `[Authorize]` ASP.NET standard. Si noti anche che eventuali controller privi di `[AuthorizeLevel]` non sono più protetti da una chiave applicazione e verranno considerati come pubblici. Il nuovo SDK non usa più la chiave applicazione e la chiave master.

Per il push, l'elemento principale che potrebbe risultare mancante dall'SDK del server è la classe PushRegistrationHandler. Le registrazioni vengono gestite in modo leggermente diverso nelle app per dispositivi mobili e le registrazioni prive di tag sono abilitate per impostazione predefinita. La gestione dei tag può essere eseguita usando API personalizzate. Vedere l'argomento [Aggiungere notifiche push all'app per dispositivi mobili] per altre informazioni.

I processi pianificati non vengono compilati nelle app per dispositivi mobili, pertanto sarà necessario eseguire la migrazione dei singoli processi esistenti eventualmente presenti nel back-end .NET. Una possibilità consiste nel creare un [processo Web] pianificato nel sito del codice dell'app per dispositivi mobili. È anche possibile impostare un controller che contiene il codice del processo e configurare l'[Utilità di pianificazione di Azure] per accedere a tale endpoint in base alla pianificazione prevista.

L'oggetto `ApiServices` non è più incluso nell'SDK. Per accedere alle impostazioni dell'app per dispositivi mobili, è possibile usare il codice seguente:

    MobileAppSettingsDictionary settings = this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings(); 

Analogamente, la registrazione viene ora effettuata mediante la scrittura di tracce ASP.NET standard:

    ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
    traceWriter.Info("Hello, World");  

##<a name="authentication"></a>Considerazioni sull'autenticazione
Una delle maggiori differenze tra app per dispositivi mobili e Servizi mobili risiede nel fatto che l'accesso viene ora gestito dal gateway del servizio app nel caso di app per dispositivi mobili, non dal sito del che esegue il codice. Se il gruppo di risorse non ha ancora un gateway, è possibile effettuare il provisioning di un gateway passando all'app per dispositivi mobili di Azure nel portale di gestione. Selezionare quindi **Impostazioni**, **Autenticazione utente** nella categoria **Dispositivi mobili**. Fare clic su **Crea** per associare un gateway all'app per dispositivi mobili.

Per la maggior parte delle applicazioni, inoltre, ciò non richiederà alcuna azione aggiuntiva, ma esistono alcuni scenari avanzati che è opportuno notare.

Per la maggior parte delle applicazioni è possibile usare semplicemente una nuova registrazione con il provider di identità di destinazione ed è possibile ottenere informazioni sull'aggiunta di identità a un'app del servizio app seguendo l'esercitazione [Aggiungere l'autenticazione all'app per dispositivi mobili].

Se l'applicazione stabilisce dipendenze in ID utente, ad esempio memorizzandole in un database, è importante notare che gli ID utente tra Servizi mobili e app per dispositivi mobili del servizio app siano diversi. Tuttavia, è possibile ottenere l'ID utente di Servizi mobili nell'applicazione App per dispositivi mobili del servizio app usando il comando seguente (con Facebook come esempio):

    MobileAppUser = (MobileAppUser) this.User;
    FacebookCredentials creds = await user.GetIdentityAsync<FacebookCredentials>();
    string mobileServicesUserId = creds.Provider + ":" + creds.UserId;

Inoltre, se si stabiliscono dipendenze sugli ID utente, è importante sfruttare la stessa registrazione con un provider di identità, se possibile. Gli ID utente sono in genere destinati alla registrazione dell'applicazione che è stata usata, pertanto l'introduzione di una nuova registrazione potrebbe creare problemi di associazione degli utenti ai relativi dati. Nel caso in cui l'applicazione dovesse per qualsiasi motivo usare la stessa registrazione del provider di identità, è possibile eseguire la procedura seguente:

1. Copiare l'ID client e le informazioni di connessione del segreto client per ogni provider usato dall'applicazione.
2. Aggiungere gli endpoint /signin-* del gateway come un URI di reindirizzamento aggiuntive per ciascun provider. 

>[AZURE.NOTE]Alcuni provider, ad esempio Twitter e Account Microsoft, non consentono di specificare più URI di reindirizzamento in domini diversi. Se l'app usa uno di questi provider e stabilisce una dipendenza su ID utente, è consigliabile evitare i provare a eseguire la migrazione in questo momento.

##<a name="updating-clients"></a>Aggiornamento dei client
Dopo aver creato un back-end dell'app per dispositivi mobili operativo, è possibile aggiornare l'applicazione client per usare la nuova app per dispositivi mobili. Le app per dispositivi mobili includeranno anche una nuova versione di SDK del client di Servizi mobili, che consentirà agli sviluppatori di sfruttare i vantaggi delle nuove funzionalità di servizio app. Dopo aver creato una versione di app per dispositivi mobili del back-end, è possibile rilasciare una nuova versione dell'applicazione client che sfrutta la nuova versione dell'SDK.

La modifica principale che sarà necessario apportare al codice client riguarda il costruttore. Oltre all'URL del sito dell'app per dispositivi mobili e alla chiave dell'applicazione, è necessario fornire l'URL del gateway del servizio app che ospita le impostazioni di autenticazione:

    public static MobileServiceClient MobileService = new MobileServiceClient(
        "https://contoso.azurewebsites.net", // URL of the Mobile App
        "https://contoso-gateway.azurewebsites.net", // URL of the App Service Gateway
        "" // Formerly app key. To be removed in future client SDK update
    );

Ciò consentirà al client di reindirizzare le richieste ai componenti dell'app per dispositivi mobili. Altre informazioni specifiche per la piattaforma di destinazione sono disponibili nell'argomento [Creare un'app per dispositivi mobili].

Nello stesso aggiornamento sarà necessario modificare qualsiasi chiamata di registrazione di notifica push effettuata. Sono disponibili nuove API che consentono di apportare miglioramenti al processo di registrazione (con Windows come esempio):

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    await MobileService.GetPush().Register(channel.Uri); 

Vedere gli argomenti [Aggiungere notifiche push all'app per dispositivi mobili]e [Inviare notifiche push multipiattaforma] per informazioni dettagliate specifiche per la piattaforma di destinazione.

Una volta che i clienti hanno avuto l'opportunità di ricevere gli aggiornamenti, è possibile eliminare la versione di Servizi mobili dell'app. A questo punto è stata completata la migrazione a un'app per dispositivi mobili del servizio app.

<!-- URLs. -->

[Anteprima del portale di gestione di Azure]: https://portal.azure.com/
[portale di gestione di Azure]: https://manage.windowsazure.com/
[Informazioni sulle app per dispositivi mobili]: app-service-mobile-value-prop.md
[Qual è l'utilità del servizio app per chi usa già i siti Web e i Servizi mobili?]: /it-IT/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Creare un'app per dispositivi mobili]: app-service-mobile-xamarin-ios-get-started.md
[Aggiungere notifiche push all'app per dispositivi mobili]: app-service-mobile-xamarin-ios-get-started-push.md
[Aggiungere l'autenticazione all'app per dispositivi mobili]: app-service-mobile-xamarin-ios-get-started-users.md
[Utilità di pianificazione di Azure]: /it-IT/documentation/services/scheduler/
[processo Web]: ../app-service-web/websites-webjobs-resources.md
[Inviare notifiche push multipiattaforma]: app-service-mobile-xamarin-ios-push-notifications-to-user.md
[Come usare l'SDK del server .NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md

<!---HONumber=Nov15_HO1-->