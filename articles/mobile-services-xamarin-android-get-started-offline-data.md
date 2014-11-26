<properties linkid="develop-mobile-tutorials-get-started-offline-data-android" urlDisplayName="Getting Started with Offline Data" pageTitle="Get started with offline data in Mobile Services (Xamarin Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to cache and sync offline data in your Xamarin Android application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with offline data in Mobile Services" authors="donnam,wesmc" editor="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam,wesmc"/>

# Introduzione alla sincronizzazione dei dati offline in Servizi mobili

<div class="dev-center-tutorial-selector sublanding">
<a href="/it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data" title="Windows Store C#">Windows Store C#</a>
<a href="/it-it/documentation/articles/mobile-services-windows-phone-get-started-offline-data" title="Windows Phone">Windows Phone</a>
<a href="/it-it/documentation/articles/mobile-services-ios-get-started-offline-data" title="iOS">iOS</a>
<a href="/it-it/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/it-it/documentation/articles/mobile-services-xamarin-android-get-started-offline-data" title="Xamarin.Android" class="current">Xamarin.Android</a>
</div>

In questo argomento viene illustrato come usare le funzionalità offline di Servizi mobili di Azure. Queste funzionalità consentono l'interazione con un database locale quando ci si trova in uno scenario offline con il proprio servizio mobile. Le funzionalità offline consentono di sincronizzare le modifiche locali con il servizio mobile usato quando si torna online.

In questa esercitazione verrà aggiornata l'app creata nell'esercitazione [Introduzione a Servizi mobili][Introduzione a Servizi mobili] o [Introduzione ai dati][Introduzione ai dati] per supportare le funzionalità offline di Servizi mobili di Azure. Quindi, verranno aggiunti dati in uno scenario offline, verrà effettuata la sincronizzazione degli elementi con il database online, quindi si effettuerà l'accesso al portale di gestione di Azure per visualizzare le modifiche apportate durate l'esecuzione dell'app.

> [WACOM.NOTE] In questa esercitazione viene descritto come usare Servizi mobili di Azure per archiviare e recuperare i dati da un'app per Windows Store. e vengono pertanto riproposte molte delle procedure già completate nella guida introduttiva a Servizi mobili. Se si tratta della prima esperienza con Servizi mobili, è consigliabile iniziare dall'esercitazione [Introduzione a Servizi mobili][Introduzione a Servizi mobili].

> [WACOM.NOTE] Per completare l'esercitazione, è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][versione di valutazione gratuita di Azure].

In questa esercitazione vengono descritte le operazioni di base seguenti:

1.  [Aggiornamento dell'app per supportare le funzionalità offline][Aggiornamento dell'app per supportare le funzionalità offline]
2.  [Test dell'app connessa al servizio mobile][Test dell'app connessa al servizio mobile]

Per completare questa esercitazione, è necessario:

-   Visual Studio con [estensione Xamarin][estensione Xamarin] **o** [Xamarin Studio][Xamarin Studio]
-   Avere completato l'esercitazione [Introduzione a Servizi mobili][Introduzione a Servizi mobili] o [Introduzione ai dati][Introduzione ai dati]
-   [Mobile Services SDK di Azure versione 1.3.0-alpha3][Mobile Services SDK di Azure versione 1.3.0-alpha3]
-   [SQLite Store di Servizi mobili di Azure versione 1.0.0-alpha2][SQLite Store di Servizi mobili di Azure versione 1.0.0-alpha2]

> [WACOM.NOTE] Le istruzioni seguenti presuppongono che si stia usando Visual Studio 2012 o versioni successive con l'estensione Xamarin. Se si usa Xamarin Studio, la maggior parte delle istruzioni risulta uguale, ma sarà necessario installare anche il [componente aggiuntivo NuGet per Xamarin][componente aggiuntivo NuGet per Xamarin], in modo da poter aggiungere con facilità i pacchetti NuGet preliminari per Servizi mobili al progetto.

## <a name="enable-offline-app"></a>Aggiornamento dell'app per supportare le funzionalità offline

Servizi mobili di Azure consente di interagire con un database locale quando si usa un servizio mobile in modalità offline. Per usare queste funzionalità nell'app, inizializzare `MobileServiceClient.SyncContext` in un archivio locale. Quindi, fare riferimento alla tabella tramite l'interfaccia di `IMobileServiceSyncTable`.

1.  In Visual Studio aprire il progetto completato nell'esercitazione [Introduzione a Servizi mobili][Introduzione a Servizi mobili] o [Introduzione ai dati][Introduzione ai dati]. In Esplora soluzioni rimuovere il riferimento a **Mobile Services SDK di Azure** in **Componenti**.

2.  Installare il pacchetto preliminare di SQLiteStore di Servizi mobili usando il comando seguente nella console di Gestione pacchetti:

        install-package WindowsAzure.MobileServices.SQLiteStore -Pre

    Verranno installare anche tutte le dipendenze necessarie.

3.  Nel nodo dei riferimenti, rimuovere i riferimenti a `System.IO`, `System.Runtime` e `System.Threading.Tasks`.

### Modificare ToDoActivity.cs

-   Aggiungere le dichiarazioni

        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
        using System.IO;

-   Cambiare il tipo del membro `ToDoActivity.toDoTable` da `IMobileServiceTable<>` a `IMobileServiceSyncTable<>`

-   Nel metodo `OnCreate(Bundle)`, dopo la riga che inizializza il membro `client`, aggiungere il codice seguente:

        // existing initializer
        client = new MobileServiceClient (applicationURL, applicationKey, progressHandler);

        // new code to initialize the SQLite store
        string path = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.Personal), "test1.db");

        if (!File.Exists(path))
        {
            File.Create(path).Dispose();
        }

        var store = new MobileServiceSQLiteStore(path);
        store.DefineTable<ToDoItem>();

        await client.SyncContext.InitializeAsync(store, new TodoSyncHandler(this));

-   Nello stesso metodo, modificare la riga che inizializza `toDoTable` per usare il metodo `GetSyncTable<>` invece di `GetTable<>`:

        toDoTable = client.GetSyncTable <ToDoItem> ();

-   Modificare il metodo `OnRefreshItemsSelected` per aggiungere chiamate a `PushAsync` e `PullAsync`:

        async void OnRefreshItemsSelected ()
        {
            await client.SyncContext.PushAsync();
            await toDoTable.PullAsync();
            await RefreshItemsFromTableAsync();
        }

### Modificare ToDoItem.cs

-   Aggiungere l'istruzione using:

        using Microsoft.WindowsAzure.MobileServices; 

-   Aggiungere i membri seguenti alla classe `ToDoItem`:

        [Version]
        public string Version { get; set; }


        public override string ToString()
        {
            return "Text: " + Text + "\nComplete: " + Complete + "\n";
        }

## <a name="test-online-app"></a>Test dell'app

In questa sezione verrà effettuato il test del metodo `SyncAsync` che sincronizza l'archivio locale con il database del servizio mobile.

1.  In Visual Studio fare clic su **Esegui** per compilare il progetto e avviare l'app nell'emulatore iPhone, che è l'impostazione predefinita per questo progetto.

2.  Si noti che l'elenco di elementi nell'app è vuoto. Come risultato delle modifiche al codice nella sezione precedente, l'app non legge più gli elementi dal servizio mobile, ma li legge dall'archivio locale.

3.  Aggiungere elementi all'elenco di attività da eseguire.

    ![](./media/mobile-services-xamarin-android-get-started-offline-data/mobile-quickstart-startup-android.png)

4.  Accedere al portale di gestione di Microsoft Azure ed esaminare il database relativo al servizio mobile. Se il servizio usa il back-end JavaScript per i servizi mobili, è possibile visualizzare i dati dalla scheda **Data** del servizio mobile. Se si usa il back-end .NET per il servizio mobile, è possibile fare clic sul pulsante **Manage** per il database nell'estensione SQL Azure per eseguire una query sulla tabella.

    Si noti che i dati non sono stati sincronizzati tra il database e l'archivio locale.

5.  Nell'app, premere il pulsante per l'**aggiornamento**. L'app effettuerà la chiamata a `MobileServiceClient.SyncContext.PushAsync` e `IMobileServiceSyncTable.PullAsync()`, quindi a `RefreshTodoItems` in modo da effettuare l'aggiornamento con gli elementi dell'archivio locale.

    Con questa operazione push il database del servizio mobile riceve i dati dall'archivio. L'operazione viene eseguita da `MobileServiceClient.SyncContext` invece che da `IMobileServicesSyncTable` ed esegue il push delle modifiche a tutte le tabelle associate a tale contesto di sincronizzazione. Questa operazione serve per gli scenari in cui sono presenti relazioni tra tabelle.

    L'operazione pull, invece, recupera i record solo dalla tabella specificata. Se nel contesto di sincronizzazione sono presenti operazioni in sospeso per questa tabella, un'operazione `PushAsync` verrà chiamata in modo implicito da Mobile Services SDK di Azure.

    ![][1]

    ![][2]

## Riepilogo

Per supportare le funzionalità offline di Servizi mobili è stata usata l'interfaccia `IMobileServiceSyncTable` e inizializzato `MobileServiceClient.SyncContext` in un archivio locale. In questo caso l'archivio locale era un database SQLite.

Le normali operazioni CRUD per Servizi mobili funzionano come se l'app fosse ancora connessa, ma tutte le operazioni si verificano nell'archivio locale.

Per sincronizzare l'archivio locale con il server sono stati usati i metodi `IMobileServiceSyncTable.PullAsync` e `MobileServiceClient.SyncContext.PushAsync`.

-   Per eseguire il push delle modifiche al server, è stata effettuata la chiamata a `IMobileServiceSyncContext.PushAsync()`. Questo metodo fa parte di `IMobileServicesSyncContext` invece che della tabella di sincronizzazione perché effettuerà il push delle modifiche in tutte le tabelle:

    Solo i record che sono stati in qualche modo modificati localmente (tramite le operazioni CUD) verranno inviati al server.

-   Per eseguire il pull dei dati da una tabella del server all'app, è stata effettuata la chiamata a `IMobileServiceSyncTable.PullAsync`.

    Un'operazione pull effettua sempre un'operazione push all'inizio.

    Esistono inoltre overload di **PullAsync()** che consentono di specificare una query. Si noti che nella versione preliminare del supporto offline per Servizi mobili, **PullAsync** leggerà tutte le righe nella tabella (o query) corrispondente; non effettua il tentativo di leggere solo righe più recenti rispetto, ad esempio, all'ultima sincronizzazione. Se le righe esistono già nella tabella di sincronizzazione locale, resteranno invariate.

## Passaggi successivi

<!--* [Handling conflicts with offline support for Mobile Services] -->

-   [Come usare il componente client Xamarin per Servizi mobili di Azure][Come usare il componente client Xamarin per Servizi mobili di Azure]


<!-- Images --> 


  [Introduzione a Servizi mobili]: /it-it/documentation/articles/partner-xamarin-mobile-services-android-get-started/
  [Introduzione ai dati]: /it-it/documentation/articles/partner-xamarin-mobile-services-android-get-started-data/
  [versione di valutazione gratuita di Azure]: http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=AE564AB28
  [Aggiornamento dell'app per supportare le funzionalità offline]: #enable-offline-app
  [Test dell'app connessa al servizio mobile]: #test-online-app
  [estensione Xamarin]: http://xamarin.com/visual-studio
  [Xamarin Studio]: http://xamarin.com/download
  [Mobile Services SDK di Azure versione 1.3.0-alpha3]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0-alpha3
  [SQLite Store di Servizi mobili di Azure versione 1.0.0-alpha2]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0-alpha2
  [componente aggiuntivo NuGet per Xamarin]: https://github.com/mrward/monodevelop-nuget-addin
   
  [1]: ./media/mobile-services-xamarin-android-get-started-offline-data/mobile-quickstart-completed-android.png
  [2]: ./media/mobile-services-xamarin-android-get-started-offline-data/mobile-data-browse.png
  [Come usare il componente client Xamarin per Servizi mobili di Azure]: /it-it/documentation/articles/partner-xamarin-mobile-services-how-to-use-client-library/
