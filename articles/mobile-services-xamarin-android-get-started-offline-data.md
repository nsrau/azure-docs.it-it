<properties urlDisplayName="Using Offline Data" pageTitle="Uso dei dati offline in Servizi mobili (Xamarin per Android) | Mobile Dev Center" metaKeywords="" description="Informazioni su come usare Servizi mobili di Azure per memorizzare nella cache e sincronizzare dati offline nell'applicazione Android Xamarin." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Using offline data in Mobile Services" authors="donnam" editor="wesmc" manager="dwrede"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="donnam" />

# Uso della sincronizzazione dei dati offline in Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]

In questo argomento viene illustrato come usare le funzionalità offline di Servizi mobili di Azure. Queste funzionalità consentono l'interazione con un database locale quando ci si trova in uno scenario offline con il proprio servizio mobile. Le funzionalità offline consentono di sincronizzare le modifiche locali con il servizio mobile usato quando si torna online. 

In questa esercitazione si aggiornerà l'app creata nell'esercitazione [Introduzione a Servizi mobili] o [Introduzione ai dati] per supportare le funzionalità offline di Servizi mobili di Azure. Quindi, verranno aggiunti dati in uno scenario offline, verrà effettuata la sincronizzazione degli elementi con il database online, quindi si effettuerà l'accesso al portale di gestione di Azure per visualizzare le modifiche apportate durate l'esecuzione dell'app.

>[WACOM.NOTE] Questa esercitazione è stata progettata per illustrare come Servizi mobili consente di usare Azure per archiviare e recuperare i dati di un'app di Windows Store. e vengono pertanto riproposte molte delle procedure già completate nella guida introduttiva a Servizi mobili. Se questa è la prima esperienza con Servizi mobili, è consigliabile iniziare dall'esercitazione [Introduzione a Servizi mobili].
>
> Per completare l'esercitazione, è necessario un account Azure. Se non si ha un account, è possibile iscriversi per accedere a una versione di valutazione di Azure e ottenere un massimo di 10 servizi mobili gratuiti che potranno essere usati anche dopo il termine del periodo di valutazione. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">versione di valutazione gratuita di Azure</a>. 

Un progetto completato di questa esercitazione è disponibile [qui](https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/Xamarin.Android).

In questa esercitazione vengono descritte le operazioni di base seguenti:

1. [Aggiornamento dell'app per supportare le funzionalità offline]
2. [Test dell'app connessa al servizio mobile]

Per completare questa esercitazione, è necessario disporre di:

* Visual Studio con l'[estensione Xamarin] **o** [Xamarin Studio] 
* Esercitazione [Introduzione a Servizi mobili] o [Introduzione ai dati] completata
* [Azure Mobile Services SDK versione 1.3.0][Mobile Services SDK Nuget]
* [Azure Mobile Services SQLite Store versione 1.0.0][SQLite store nuget]

>[WACOM.NOTE] Le istruzioni seguenti presuppongono che si stia usando Visual Studio 2012 o versione successiva con l'estensione Xamarin. Se si esegue Xamarin Studio, usare il supporto di Gestione pacchetti NuGet integrato.

## <a name="enable-offline-app"></a>Aggiornamento dell'app per supportare le funzionalità offline

La sincronizzazione offline di Servizi mobili di Azure consente agli utenti finali di interagire con un database locale quando la rete non è accessibile. Per usare queste funzionalità nell'app, inizializzare `MobileServiceClient.SyncContext` in un archivio locale. Quindi, fare riferimento alla tabella tramite l'interfaccia `IMobileServiceSyncTable`.

1. In Visual Studio aprire il progetto completato nell'esercitazione [Introduzione a Servizi mobili] o [Introduzione ai dati]. In Esplora soluzioni rimuovere il riferimento a **Azure Mobile Services SDK** in **Componenti**.

2. Installare il pacchetto preliminare di SQLiteStore di Servizi mobili usando il comando seguente nella console di Gestione pacchetti: 
    
        install-package WindowsAzure.MobileServices.SQLiteStore -Pre

    Verranno installare anche tutte le dipendenze necessarie.
    
3. Nel nodo dei riferimenti rimuovere i riferimenti a `System.IO`, `System.Runtime` e `System.Threading.Tasks`.

### Modificare ToDoActivity.cs

1. Aggiungere le dichiarazioni

		using Microsoft.WindowsAzure.MobileServices.Sync;
		using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
		using System.IO;

2. Cambiare il tipo del membro `ToDoActivity.toDoTable` da `IMobileServiceTable<>` a `IMobileServiceSyncTable<>`

3. Nel metodo `OnCreate(Bundle)`, dopo la riga che inizializza il membro `client`, aggiungere il codice seguente:

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

4. Nello stesso metodo modificare la riga che inizializza `toDoTable` in modo da usare il metodo `GetSyncTable<>` anziché `GetTable<>`:

		toDoTable = client.GetSyncTable <ToDoItem> ();

5. Modificare il metodo `OnRefreshItemsSelected` per aggiungere chiamate a `PushAsync` e `PullAsync`:

		async void OnRefreshItemsSelected ()
		{
		    await client.SyncContext.PushAsync();
			await this.todoTable.PullAsync("todoItems", todoTable.CreateQuery());
		    await RefreshItemsFromTableAsync();
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

## <a name="test-online-app"></a>Test dell'app 

In questa sezione si eseguirà il test del metodo `SyncAsync` che sincronizza l'archivio locale con il database del servizio mobile.

1. In Visual Studio scegliere il pulsante **Esegui** per compilare il progetto e avviare l'app nell'emulatore iPhone, che è l'impostazione predefinita per questo progetto.

2. Si noti che l'elenco di elementi nell'app è vuoto. Come risultato delle modifiche al codice nella sezione precedente, l'app non legge più gli elementi dal servizio mobile, ma li legge dall'archivio locale. 

3. Aggiungere elementi all'elenco di attività da eseguire.

    ![][1]


4. Accedere al portale di gestione di Microsoft Azure ed esaminare il database relativo al servizio mobile. Se il servizio usa il back-end JavaScript per i servizi mobili, è possibile visualizzare i dati dalla scheda **Dati** del servizio mobile. Se si usa il back-end .NET per il servizio mobile, è possibile fare clic sul pulsante **Gestisci** relativo al database nell'estensione SQL Azure per eseguire una query sulla tabella.

    Si noti che i dati non sono stati sincronizzati tra il database e l'archivio locale.

5. Nell'app scegliere il pulsante **Refresh**. L'app effettuerà una chiamata a `MobileServiceClient.SyncContext.PushAsync` e `IMobileServiceSyncTable.PullAsync()` e quindi a `RefreshTodoItems` in modo da aggiornare i dati con gli elementi dell'archivio locale. 

    Con questa operazione push il database del servizio mobile riceve i dati dall'archivio. L'operazione viene eseguita da `MobileServiceClient.SyncContext` anziché da `IMobileServicesSyncTable` ed esegue il push delle modifiche in tutte le tabelle associate a tale contesto di sincronizzazione. Questa operazione serve per gli scenari in cui sono presenti relazioni tra tabelle.
    
    L'operazione pull, invece, recupera i record solo dalla tabella specificata. Se nel contesto di sincronizzazione sono presenti operazioni in sospeso per questa tabella, un'operazione `PushAsync` verrà chiamata in modo implicito da Mobile Services SDK.
        
    ![][3] 



    ![][2]


  

##Riepilogo

Per supportare le funzionalità offline di Servizi mobili è stata usata l'interfaccia `IMobileServiceSyncTable` ed è stato inizializzato `MobileServiceClient.SyncContext` con un archivio locale. In questo caso l'archivio locale era un database SQLite.

Le normali operazioni CRUD per Servizi mobili funzionano come se l'app fosse ancora connessa, ma tutte le operazioni si verificano nell'archivio locale.

Per sincronizzare l'archivio locale con il server sono stati usati i metodi `IMobileServiceSyncTable.PullAsync` e `MobileServiceClient.SyncContext.PushAsync`.

*  Per eseguire il push delle modifiche nel server, è stata effettuata una chiamata a `IMobileServiceSyncContext.PushAsync()`. Questo metodo è un membro di `IMobileServicesSyncContext` anziché della tabella di sincronizzazione perché effettua il push delle modifiche in tutte le tabelle:

    Solo i record che sono stati in qualche modo modificati localmente (tramite le operazioni CUD) verranno inviati al server.
   
* Per eseguire il pull dei dati da una tabella del server all'app, è stata effettuata una chiamata a `IMobileServiceSyncTable.PullAsync`.

    Un'operazione pull effettua sempre un'operazione push all'inizio.  

    Il metodo **PullAsync()** richiede un ID di query e una query. L'ID di query viene usato per la sincronizzazione incrementale ed è consigliabile usare un ID di query diverso per ogni query univoca nell'app. Mobile Services SDK tiene traccia del timestamp dell'ultimo aggiornamento dopo ogni operazione di pull con esito positivo. All'operazione di pull successiva vengono recuperati solo i record più recenti. Se come ID di query è specificato Null, viene eseguita una sincronizzazione completa per la tabella di sincronizzazione.


## Passaggi successivi

È possibile scaricare la versione completa di questa esercitazione nell'[archivio degli esempi GitHub](https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/Xamarin.Android).

<!--* [Handling conflicts with offline support for Mobile Services]
-->
* [Come usare il componente client Xamarin per Servizi mobili di Azure]

<!-- Anchors. -->
[Aggiornamento dell'app per supportare le funzionalità offline]: #enable-offline-app
[Test dell'app connessa al servizio mobile]: #test-online-app
[Passaggi successivi]:#next-steps

<!-- Images -->
[1]: ./media/mobile-services-xamarin-android-get-started-offline-data/mobile-quickstart-startup-android.png
[2]: ./media/mobile-services-xamarin-android-get-started-offline-data/mobile-data-browse.png
[3]: ./media/mobile-services-xamarin-android-get-started-offline-data/mobile-quickstart-completed-android.png



<!-- URLs. -->
[Gestione dei conflitti con il supporto offline per Servizi mobili]: /it-it/documentation/articles/mobile-services-xamarin-android-handling-conflicts-offline-data/ 
[Introduzione ai dati]: /it-it/documentation/articles/partner-xamarin-mobile-services-android-get-started-data/
[Introduzione a Servizi mobili]: /it-it/documentation/articles/partner-xamarin-mobile-services-android-get-started/
[Come usare il componente client Xamarin per Servizi mobili di Azure]: /it-it/documentation/articles/partner-xamarin-mobile-services-how-to-use-client-library/

[Pacchetto NuGet Mobile Services SDK]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0
[Pacchetto NuGet SQLite Store]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0
[Xamarin Studio]: http://xamarin.com/download
[Estensione Xamarin]: http://xamarin.com/visual-studio
[Componente aggiuntivo NuGet per Xamarin]: https://github.com/mrward/monodevelop-nuget-addin

<!--HONumber=35.2-->
