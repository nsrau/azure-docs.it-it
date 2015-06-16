<properties 
	pageTitle="Eseguire la migrazione da Servizi mobili a un'app mobile del servizio app" 
	description="Informazioni su come eseguire facilmente la migrazione dell'applicazione Servizi mobili a un'app mobile del servizio app" 
	services="app-service\mobile" 
	documentationCenter="" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/22/2015" 
	ms.author="mahender"/>

# Eseguire la migrazione del servizio mobile di Azure esistente a un'app mobile del servizio app

Questo argomento descrive come eseguire la migrazione di un'applicazione esistente da Servizi mobili di Azure a una nuova app mobile del servizio app. È possibile eseguire la migrazione di tutte le app di Servizi mobili esistenti a una nuova app mobile del servizio app. Durante una migrazione, l'applicazione Servizi mobili esistente può continuano a funzionare. Nel corso del tempo, il processo di migrazione diventerà ancora più semplice, ma gli utenti che desiderano eseguire subito la migrazione possono usare i passaggi seguenti.

>[AZURE.NOTE]Le migrazioni sono attualmente supportate solo per i clienti che usano il back-end .NET di Servizi mobili. Le applicazioni che usano il back-end Node.js dovranno per il momento rimanere in Servizi mobili.

##<a name="understand"></a>Informazioni sulle app per dispositivi mobili del servizio app

Le app per dispositivi mobili del servizio app offrono un nuovo modo per creare applicazioni mobili usando Microsoft Azure. Altre informazioni sulle app per dispositivi mobili sono disponibili nell'argomento [Informazioni sulle app per dispositivi mobili].

In una migrazione ad app per dispositivi mobili, tutte le funzionalità, e il codice, delle app esistenti possono essere conservate. Inoltre, sono disponibili nuove funzionalità per l'applicazione. Nel modello di app per dispositivi mobili il codice viene effettivamente eseguito in un'app Web (la nuova versione di Siti Web di Azure). L'utente ha il controllo completo sull'app Web e sul suo funzionamento. Inoltre, ora possono essere usate funzionalità delle app Web che in precedenza non erano disponibili ai clienti di Servizi mobili, ad esempio Gestione traffico e Slot di sviluppo.

Il nuovo modello risolve inoltre una delle principali difficoltà associate all'uso di Servizi mobili. Ora qualsiasi nuova versione di un pacchetto NuGet può essere distribuita senza doversi preoccupare dei conflitti di dipendenza. Altre informazioni sui vantaggi della migrazione sono disponibili nell'argomento [Qual è l'utilità del servizio app per chi usa già i siti Web e i Servizi mobili?].

Quando si crea un'app mobile del servizio app si ottiene:

- Una risorsa di app mobile, che contiene nuove funzionalità 
- Un sito di codice di app mobile, che esegue il progetto di API Web mediante il Mobile App Server SDK
- Un gateway del servizio mobile, che gestisce l'accesso e consente di aggiungere app per le API del servizio app all'applicazione

##<a name="overview"></a>Panoramica della migrazione di base
Il modo più semplice per eseguire la migrazione consiste nel creare una nuova istanza di un'app mobile del servizio app. In molti casi, la migrazione sarà semplice come passare al nuovo SDK del server e ripubblicare il codice in una nuova app mobile. Esistono, tuttavia, alcuni scenari che richiedono alcune configurazioni aggiuntive, ad esempio l'autenticazione avanzata e l'uso dei processi pianificati. Ciascuno di questi scenari viene trattato nelle sezioni seguenti.

>[AZURE.NOTE]Si consiglia di leggere e comprendere integralmente il resto di questo argomento prima di avviare una migrazione. Prendere nota delle funzionalità usate che sono indicate di seguito.

È possibile spostare e testare il codice in base alle proprie esigenze. Quando il back-end dell'app mobile è pronto, è possibile rilasciare una nuova versione dell'applicazione client. A questo punto, si disporrà di due copie dell'applicazione eseguite contemporaneamente. È necessario assicurarsi che le correzioni apportate vengano applicate a entrambe le copie. Infine, una volta che gli utenti hanno effettuato l'aggiornamento alla versione più recente, è possibile eliminare il servizio mobile originale.

Il set completo di passaggi per la migrazione è il seguente:

1. Creare e configurare una nuova app mobile
2. Risolvere gli eventuali problemi di autenticazione
3. Rilasciare una nuova versione dell'applicazione client
4. Eliminare l'istanza di Servizi mobili originale


##<a name="mobile-app-version"></a>Impostazione di una versione di app per dispositivi mobili dell'applicazione
Il primo passaggio per la migrazione prevede la creazione del servizio app che ospiterà la nuova versione dell'applicazione. È possibile creare una nuova app per dispositivi mobili in [Anteprima del portale di gestione di Azure]. È possibile consultare l'argomento [Creare un'app per dispositivi mobili] per altre informazioni.

Potrebbe essere utile usare lo stesso database e hub di notifica usati in Servizi mobili. È possibile copiare questi valori dalla scheda **Configura** della sezione Servizi mobili del [portale di gestione di Azure]. In **Stringhe di connessione** copiare `MS_NotificationHubConnectionString` e `MS_TableConnectionString`. Passare al sito del codice dell'app mobile e selezionare **Impostazioni**, **Impostazioni applicazione**, quindi aggiungere queste stringhe di connessione, sovrascrivendo gli eventuali valori esistenti. È inoltre necessario aggiungere questi valori anche alla risorsa di app mobile. A tale scopo, passare al pannello App per dispositivi mobili, selezionare **Impostazioni** e quindi **Proprietà**. Fare clic sul collegamento con l'etichetta **Host app per le API** per visualizzare il sito che ospita la risorsa di app per dispositivi mobili. Passare a **Impostazioni**, **Impostazioni applicazione** e incollare le stringhe di connessione come sito di codice. Non modificare altri valori perché questa azione potrebbe interrompere la funzionalità dell'app mobile. Si noti che, al momento, il pannello App mobile continuerà a mostrare le connessioni esistenti anche dopo questo passaggio di configurazione. Potrebbe essere necessario intraprendere azioni aggiuntive una volta aggiornata l'esperienza dell'app mobile.

Le app per dispositivi mobili forniscono un nuovo [Mobile App Server SDK] che offre molte delle funzionalità del runtime di Servizi per dispositivi mobili. È necessario rimuovere il NuGet per Servizi mobili dal progetto esistente e includere invece l'SDK del server. Potrebbe essere necessario modificare alcune istruzioni using, avvalendosi delle funzionalità di Visual Studio. L'elemento principale che potrebbe risultare mancante l'SDK del server è la classe PushRegistrationHandler. Le registrazioni vengono gestite in modo leggermente diverso nelle app per dispositivi mobili e le registrazioni prive di tag sono abilitate per impostazione predefinita. La gestione dei tag può essere eseguita usando API personalizzate. Vedere l'argomento [Aggiungere notifiche push all'app per dispositivi mobili] per altre informazioni.

I processi pianificati non vengono compilati nelle app per dispositivi mobili, pertanto sarà necessario eseguire la migrazione dei singoli processi esistenti eventualmente presenti nel back-end .NET. Una possibilità consiste nel creare un [processo Web] pianificato nel sito del codice dell'app per dispositivi mobili. È anche possibile impostare un controller che contiene il codice del processo e configurare l'[Utilità di pianificazione di Azure] per accedere a tale endpoint in base alla pianificazione prevista.


##<a name="authentication"></a>Considerazioni sull'autenticazione
Una delle maggiori differenze tra app per dispositivi mobili e Servizi mobili risiede nel fatto che l'accesso viene gestito dal gateway del servizio app nel caso di app per dispositivi mobili, non dal sito del codice. Per la maggior parte delle applicazioni, ciò non richiederà alcuna azione aggiuntiva, ma esistono alcuni scenari avanzati che è opportuno notare.

Per la maggior parte delle applicazioni è possibile usare semplicemente una nuova registrazione con il provider di identità di destinazione ed è possibile ottenere informazioni sull'aggiunta di identità a un'app del servizio app seguendo l'esercitazione [Aggiungere l'autenticazione all'app per dispositivi mobili].

Se l'applicazione stabilisce dipendenze in ID utente, ad esempio memorizzandole in un database, è importante notare che gli ID utente tra Servizi mobili e app per dispositivi mobili del servizio app siano diversi. Tuttavia, è possibile ottenere l'ID utente di Servizi mobili nell'applicazione App mobile del servizio app usando il comando seguente (con Facebook come esempio):

    ServiceUser user = (ServiceUser) this.User;
    FacebookCredentials creds = (await user.GetIdentitiesAsync()).OfType< FacebookCredentials >().FirstOrDefault();
    string mobileServicesUserId = creds.Provider + ":" + creds.UserId;

Inoltre, se si stabiliscono dipendenze sugli ID utente, è importante sfruttare la stessa registrazione con un provider di identità, se possibile. Gli ID utente sono in genere destinati alla registrazione dell'applicazione che è stata usata, pertanto l'introduzione di una nuova registrazione potrebbe creare problemi di associazione degli utenti ai relativi dati. Nel caso in cui l'applicazione dovesse per qualsiasi motivo usare la stessa registrazione del provider di identità, è possibile eseguire la procedura seguente:

1. Copiare l'ID client e le informazioni di connessione del segreto client per ogni provider usato dall'applicazione.
2. Aggiungere gli endpoint /signin-* del gateway come un URI di reindirizzamento aggiuntive per ciascun provider. 

>[AZURE.NOTE]Alcuni provider, ad esempio Twitter e Account Microsoft, non consentono di specificare più URI di reindirizzamento in domini diversi. Se l'app usa uno di questi provider e stabilisce una dipendenza su ID utente, è consigliabile evitare i provare a eseguire la migrazione in questo momento.

##<a name="updating clients"></a>Aggiornamento dei client
Dopo aver creato un back-end dell'app mobile operativo, è possibile aggiornare l'applicazione client per usare la nuova app mobile. Le app per dispositivi mobili includeranno anche una nuova versione di SDK del client di Servizi mobili, che consentirà agli sviluppatori di sfruttare i vantaggi delle nuove funzionalità di servizio app. Dopo aver creato una versione di app mobile del back-end, è possibile rilasciare una nuova versione dell'applicazione client che sfrutta la nuova versione dell'SDK.

La modifica principale che sarà necessario apportare al codice client riguarda il costruttore. Oltre all'URL del sito del codice dell'app mobile e alla chiave dell'applicazione, è necessario fornire l'URL del gateway del servizio app che ospita le impostazioni di autenticazione:

    public static MobileServiceClient MobileService = new MobileServiceClient(
        "https://contoso-code.azurewebsites.net", //URL of the Mobile App Code
        "https://contosogateway.azurewebsites.net", //URL of the App Service Gateway
        "983682c4-f043-483e-a75b-8a8545fc1846"
    );

Ciò consentirà al client di reindirizzare le richieste ai componenti dell'app mobile. Altre informazioni specifiche per la piattaforma di destinazione sono disponibili nell'argomento [Creare un'app per dispositivi mobili].

Nello stesso aggiornamento sarà necessario modificare qualsiasi chiamata di registrazione di notifica push effettuata. Sono disponibili nuove API che consentono di apportare miglioramenti al processo di registrazione (con Windows come esempio):

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    await MobileService.GetPush().Register(channel.Uri); 

Vedere gli argomenti [Aggiungere notifiche push all'app per dispositivi mobili]e [Inviare notifiche push multipiattaforma] per informazioni dettagliate specifiche per la piattaforma di destinazione.

Una volta che i clienti hanno avuto l'opportunità di ricevere gli aggiornamenti, è possibile eliminare la versione di Servizi mobili dell'app. A questo punto è stata completata la migrazione a un'app mobile del servizio app.

<!-- URLs. -->

[Anteprima del portale di gestione di Azure]: https://portal.azure.com/
[portale di gestione di Azure]: https://manage.windowsazure.com/
[Informazioni sulle app per dispositivi mobili]: app-service-mobile-value-prop-preview.md
[Qual è l'utilità del servizio app per chi usa già i siti Web e i Servizi mobili?]: /it-it/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services-preview
[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Creare un'app per dispositivi mobili]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md
[Aggiungere notifiche push all'app per dispositivi mobili]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview.md
[Aggiungere l'autenticazione all'app per dispositivi mobili]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-users-preview.md
[Utilità di pianificazione di Azure]: /it-it/documentation/services/scheduler/
[processo Web]: ../app-service-web/websites-webjobs-resources.md
[Inviare notifiche push multipiattaforma]: app-service-mobile-dotnet-backend-xamarin-ios-push-notifications-to-user-preview.md
<!--HONumber=54--> 