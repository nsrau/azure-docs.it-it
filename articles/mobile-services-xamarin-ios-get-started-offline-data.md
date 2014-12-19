<properties urlDisplayName="Using Offline Data" pageTitle="Uso di dati offline in Servizi mobili (Xamarin iOS) | Mobile Developer Center" metaKeywords="" description="Learn how to use Azure Mobile Services to cache and sync offline data in your Xamarin iOS application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Using offline data in Mobile Services" authors="donnam" editor="wesmc" manager="dwrede"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="donnam" />

# Uso della sincronizzazione dei dati offline in Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]

In questo argomento viene illustrato come usare le funzionalità offline di Servizi mobili di Azure. Queste funzionalità consentono l'interazione con un database locale quando ci si trova in uno scenario offline con il proprio servizio mobile. Le funzionalità offline consentono di sincronizzare le modifiche locali con il servizio mobile usato quando si torna online. 

In questa esercitazione verrà aggiornata l'app creata nell'esercitazione [Introduzione a Servizi mobili] o [Introduzione ai dati] per supportare le funzionalità offline di Servizi mobili di Azure. Quindi, verranno aggiunti dati in uno scenario offline, verrà effettuata la sincronizzazione degli elementi con il database online, quindi si effettuerà l'accesso al portale di gestione di Azure per visualizzare le modifiche apportate durate l'esecuzione dell'app.

>[WACOM.NOTE] In questa esercitazione viene descritto come è possibile usare Servizi mobili di Azure per archiviare e recuperare i dati da un'app di Windows Store e vengono pertanto riproposte molte delle procedure già completate nella guida introduttiva a Servizi mobili. Se si tratta della prima esperienza con Servizi mobili, è consigliabile iniziare dall'esercitazione [Introduzione a Servizi mobili].
>
>Per completare l'esercitazione, è necessario un account Azure. Se non si ha un account, è possibile iscriversi per accedere a una versione di valutazione di Azure e ottenere un massimo di 10 servizi mobili gratuiti che potranno essere usati anche dopo il termine del periodo di valutazione. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">versione di valutazione gratuita di Azure</a>. 

In questa esercitazione vengono descritte le operazioni di base seguenti:

1. [Aggiornare l'app per supportare le funzionalità offline]
2. [Testare l'app connessa al servizio mobile]

Per completare questa esercitazione, è necessario disporre di:

* XCode 4.5 e iOS 6.0 (o versioni successive) 
* Visual Studio con [estensione Xamarin] **o** [Xamarin Studio] in OS X
* Avere completato l'esercitazione [Introduzione a Servizi mobili] o [Introduzione ai dati]
* [Azure Mobile Services SDK versione 1.3.0-beta2 (o successiva)][Mobile Services SDK Nuget]
* [Azure Mobile Services SQLite Store versione 1.0.0-beta2 (o successiva)][SQLite store nuget]

>[WACOM.NOTE] Le istruzioni seguenti presuppongono che si stia usando Visual Studio 2012 o versioni successive con l'estensione Xamarin. Se si sta usando Xamarin Studio in OS X, usare il supporto di gestione pacchetti NuGet predefinito.

## <a name="enable-offline-app"></a>Aggiornare l'app per supportare le funzionalità offline

La sincronizzazione offline di Servizi mobili di Azure consente agli utenti finali di interagire con un database locale quando la rete non è accessibile. Per usare queste funzionalità nell'app, inizializzare `MobileServiceClient.SyncContext` in un archivio locale. Quindi, fare riferimento alla tabella tramite l'interfaccia di `IMobileServiceSyncTable`.

Un progetto con lo stato completato di questa esercitazione è disponibile [qui](https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/Xamarin.iOS).

1. In Visual Studio aprire il progetto completato nell'esercitazione [Introduzione a Servizi mobili] o [Introduzione ai dati]. In Esplora soluzioni rimuovere il riferimento a **Mobile Services SDK di Azure** in **Componenti**.

2. Installare il pacchetto preliminare di SQLiteStore di Servizi mobili usando il comando seguente nella console di Gestione pacchetti: 
    
        install-package WindowsAzure.MobileServices.SQLiteStore -Pre

    Verranno installare anche tutte le dipendenze necessarie.
    
3. Nel nodo dei riferimenti rimuovere i riferimenti a `System.IO`, `System.Runtime` e `System.Threading.Tasks`.

### Modificare il file QSTodoService.cs 

Modificare la classe `QSTodoService` per permettere l'uso delle funzionalità offline di Servizi mobili con un archivio SQLite locale.

1. Aggiungere le istruzioni using seguenti all'inizio del file:

		using Microsoft.WindowsAzure.MobileServices; 
		using Microsoft.WindowsAzure.MobileServices.Sync; 
		using Microsoft.WindowsAzure.MobileServices.SQLiteStore;

2. Cambiare il tipo del membro `todoTable` da `IMobileServiceTable` a `IMobileServicesSyncTable`

		IMobileServiceSyncTable<ToDoItem> todoTable; 

3. Nel costruttore per `QSTodoService` cambiare l'inizializzatore per `todoTable`:

        todoTable = client.GetSyncTable <ToDoItem> ();

4. Nel costruttore per `QSTodoService` aggiungere una chiamata a `SQLitePCL.CurrentPlatform.Init()` come seconda riga di codice:

		QSTodoService ()
		{
			CurrentPlatform.Init ();
            SQLitePCL.CurrentPlatform.Init(); // add this line

			// Initialize the Mobile Service client with your URL and key
			client = new MobileServiceClient (applicationURL, applicationKey, this);

			// Create an MSTable instance to allow us to work with the TodoItem table
			todoTable = client.GetSyncTable <ToDoItem> ();
		}
 
5. Nella classe `QSTodoService` definire un nuovo metodo `InitializeAsync`:
 
		public async Task InitializeStoreAsync()
		{
		    string path = "syncstore.db";
		    var store = new MobileServiceSQLiteStore(path);
		    store.DefineTable<ToDoItem>();
		    await client.SyncContext.InitializeAsync(store);
		}

6. Nella classe `QSTodoService` definire un nuovo metodo `SyncAsync`:
 
		public async Task SyncAsync()
		{
		    try
		    {
		        await this.client.SyncContext.PushAsync();
                await this.todoTable.PullAsync("todoItems", todoTable.CreateQuery());
		    }
		    catch (MobileServiceInvalidOperationException e)
		    {
		        Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
		    }
		}

### Modificare ToDoItem.cs 

1. Aggiungere l'istruzione using: 

        using Microsoft.WindowsAzure.MobileServices; 


2. Aggiungere i membri seguenti alla classe `ToDoItem`:
 
		[Version]
		public string Version { get; set; }
		
		
		public override string ToString()
		{
		    return "Text: " + Text + "\nComplete: " + Complete + "\n";
		}

### Modificare QSTodoListViewController.cs 

Modificare `QSTodoListViewController` per chiamare il nuovo metodo `SyncAsync` quando l'utente esegue il movimento di aggiornamento.
 
1. Aggiungere una chiamata a `InitializeStoreAsync` in `ViewDidLoad()` dopo l'inizializzazione di `todoService`:

		public override async void ViewDidLoad ()
		{
		    base.ViewDidLoad ();
		
		    todoService = QSTodoService.DefaultService;
			await todoService.InitializeStoreAsync();
			
			...    // the rest of the code in the method is unchanged
		}

2. Modificare il metodo `AddRefreshControl` per poter chiamare `SyncAsync` prima della chiamata a `RefreshAsync`:


		RefreshControl.ValueChanged += async (sender, e) => {
            await todoService.SyncAsync();
			await RefreshAsync();
		}; 

<!-- 
DM: commenting this out because this tutorial doesn't show OC conflict handling
### Edit ToDoItem.cs 

Modify the strongly-type data class to add a version field

1. In the top of the file, add the using statement: 

        using Microsoft.WindowsAzure.MobileServices; 

2. Add the following members to the class `ToDoItem`:
 
		[Version]
		public string Version { get; set; }
		
		public override string ToString()
		{
		    return "Text: " + Text + "\nComplete: " + Complete + "\n";
		}

-->

## <a name="test-online-app"></a>Testare l'app 

In questa sezione verrà effettuato il test del metodo `SyncAsync` che sincronizza l'archivio locale con il database del servizio mobile.

1. In Visual Studio fare clic su **Esegui** per compilare il progetto e avviare l'app nell'emulatore iPhone, che è l'impostazione predefinita per questo progetto.

2. Si noti che l'elenco di elementi nell'app è vuoto. Come risultato delle modifiche al codice nella sezione precedente, l'app non legge più gli elementi dal servizio mobile, ma li legge dall'archivio locale. 

3. Aggiungere elementi all'elenco di attività da eseguire.

    ![][1]


4. Accedere al portale di gestione di Microsoft Azure ed esaminare il database relativo al servizio mobile. Se il servizio utilizza il back-end JavaScript per i servizi mobili, è possibile visualizzare i dati dalla scheda **Dati** del servizio mobile. Se si usa il back-end .NET per il servizio mobile, è possibile fare clic sul pulsante **Manage** per il database nell'estensione SQL Azure per eseguire una query sulla tabella.

    Si noti che i dati non sono stati sincronizzati tra il database e l'archivio locale.

5. Nell'app eseguire il movimento di aggiornamento spostando verso il basso l'elenco di elementi. L'app effettuerà la chiamata a `MobileServiceClient.SyncContext.PushAsync` e `IMobileServiceSyncTable.PullAsync()`, quindi a `RefreshTodoItems` per poter effettuare l'aggiornamento con gli elementi dell'archivio locale. 

    Con questa operazione push il database del servizio mobile riceve i dati dall'archivio. L'operazione viene eseguita da `MobileServiceClient.SyncContext` invece che da `IMobileServicesSyncTable` ed effettua il push delle modifiche a tutte le tabelle associate a tale contesto di sincronizzazione. Questa operazione serve per gli scenari in cui sono presenti relazioni tra tabelle.
    
    L'operazione pull, invece, recupera i record solo dalla tabella specificata. Se nel contesto di sincronizzazione sono presenti operazioni in sospeso per questa tabella, un'operazione `PushAsync` verrà chiamata in modo implicito da Mobile Services SDK.
        
    ![][3] 



    ![][2]


  

##Riepilogo

Per supportare le funzionalità offline di Servizi mobili è stata usata l'interfaccia `IMobileServiceSyncTable` e inizializzato `MobileServiceClient.SyncContext` in un archivio locale. In questo caso l'archivio locale era un database SQLite.

Le normali operazioni CRUD per Servizi mobili funzionano come se l'app fosse ancora connessa, ma tutte le operazioni si verificano nell'archivio locale.

Per sincronizzare l'archivio locale con il server sono stati usati i metodi `IMobileServiceSyncTable.PullAsync` e `MobileServiceClient.SyncContext.PushAsync`.

*  Per effettuare il push delle modifiche al server, è stata effettuata la chiamata a `IMobileServiceSyncContext.PushAsync()`. Questo metodo fa parte di `IMobileServicesSyncContext` invece che della tabella di sincronizzazione perché effettuerà il push delle modifiche in tutte le tabelle:

    Solo i record che sono stati in qualche modo modificati localmente (tramite le operazioni CUD) verranno inviati al server.
   
* Per effettuare il pull dei dati da una tabella del server all'app, è stata effettuata la chiamata a `IMobileServiceSyncTable.PullAsync`.

    Un'operazione pull effettua sempre un'operazione push all'inizio.  

    Questo esempio usa un overload di **PullAsync()** che consente di specificare una chiave di query e una query. La chiave di query viene usata per la sincronizzazione incrementale. Mobile Services SDK tiene traccia dell'ultimo timestamp aggiornato al termine di ogni operazione pull. Al pull successivo, verranno recuperati solo i nuovi record. Se non viene specificata alcuna chiave di query, verrà eseguita una sincronizzazione completa per la tabella di sincronizzazione.

## Passaggi successivi

È possibile scaricare la versione completa di questa esercitazione dall'[archivio di esempi GitHub](https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/Xamarin.iOS).


<!--* [Handling conflicts with offline support for Mobile Services]
-->
* [Come usare il componente client Xamarin per Servizi mobili di Azure]

<!-- Anchors. -->
[Aggiornare l'app per supportare le funzionalità offline]: #enable-offline-app
[Testare l'app connessa al servizio mobile]: #test-online-app
[Passaggi successivi]:#next-steps

<!-- Images -->
[1]: ./media/mobile-services-xamarin-ios-get-started-offline-data/mobile-quickstart-startup-ios.png
[2]: ./media/mobile-services-xamarin-ios-get-started-offline-data/mobile-data-browse.png
[3]: ./media/mobile-services-xamarin-ios-get-started-offline-data/mobile-quickstart-completed-ios.png



<!-- URLs. -->
[Gestione dei conflitti con il supporto offline per Servizi mobili]: /it-it/documentation/articles/mobile-services-xamarin-ios-handling-conflicts-offline-data/ 
[Introduzione ai dati]: /it-it/documentation/articles/partner-xamarin-mobile-services-ios-get-started-data/
[Introduzione a Servizi mobili]: /it-it/documentation/articles/partner-xamarin-mobile-services-ios-get-started/
[Come usare il componente client Xamarin per Servizi mobili di Azure]: /it-it/documentation/articles/partner-xamarin-mobile-services-how-to-use-client-library/

[Mobile Services SDK Nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0-beta2
[SQLite store nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0-beta2
[Xamarin Studio]: http://xamarin.com/download
[Estensione per Xamarin]: http://xamarin.com/visual-studio
[Componente aggiuntivo NuGet per Xamarin]: https://github.com/mrward/monodevelop-nuget-addin
