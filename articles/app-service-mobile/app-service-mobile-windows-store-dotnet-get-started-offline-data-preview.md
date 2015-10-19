<properties
	pageTitle="Abilitare la sincronizzazione offline per l'app per dispositivi mobili di Azure (Windows 8.1) | Microsoft Azure"
	description="Informazioni su come usare App mobili di Azure per memorizzare nella cache e sincronizzare i dati offline nell'applicazione per Windows Store"
	documentationCenter="windows"
	authors="wesmc7777"
	manager="dwrede"
	editor=""
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/27/2015"
	ms.author="wesmc"/>

# Abilitare la sincronizzazione offline per l'app di Windows

[AZURE.INCLUDE [app-service-mobile-selector-offline-preview](../../includes/app-service-mobile-selector-offline-preview.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

## Panoramica

Questa esercitazione illustra come aggiungere il supporto offline a un'app di Windows 8.1 Store o di Windows Phone usando un back-end dell'app per dispositivi mobili di Azure. La sincronizzazione offline consente agli utenti finali di interagire con un'app, visualizzando, aggiungendo e modificando i dati, anche se non è disponibile una connessione di rete. Le modifiche vengono archiviate in un database locale. Quando il dispositivo torna online, vengono sincronizzate con il back-end remoto.

In questa esercitazione si aggiornerà il progetto app di Windows 8.1 creato nell'esercitazione [Creare un'app di Windows] per supportare le funzionalità offline di App per dispositivi mobili di Azure. Se non si usa il progetto server di avvio rapido scaricato, è necessario aggiungere al progetto il pacchetto di estensione per l'accesso ai dati. Per altre informazioni sui pacchetti di estensione server, vedere l'articolo relativo all'[utilizzo dell'SDK del server back-end .NET per app per dispositivi mobili di Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Per altre informazioni sulla funzionalità di sincronizzazione offline, vedere l'argomento [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure].

## Requisiti

Per completare questa esercitazione, è necessario disporre di:

* Visual Studio 2013 in esecuzione su Windows 8.1.
* Completamento dell'esercitazione [Creare un'app Windows][create a windows app].
* [SQLite Store di Servizi mobili di Azure versione 2.0.0-beta][sqlite store nuget]
* [SQLite per Windows 8.1](http://www.sqlite.org/downloads)

## Aggiornare l'app client per supportare le funzionalità offline

Le funzionalità offline delle app per dispositivi mobili di Azure consentono di interagire con un database locale in uno scenario offline. Per usare queste funzionalità nell'app, inizializzare `MobileServiceClient.SyncContext` in un archivio locale. Quindi, fare riferimento alla tabella tramite l'interfaccia di `IMobileServiceSyncTable`. In questa esercitazione si userà SQLite per l'archivio locale.

1. Installare il runtime SQLite per Windows 8.1 e Windows Phone 8.1.

    * **Windows 8.1 Runtime:** installare [SQLite per Windows 8.1].
    * **Windows Phone 8.1:** installare [SQLite per Windows Phone 8.1].

    >[AZURE.NOTE]Quando si fa clic sul collegamento di installazione di SQLite in Internet Explorer, è possibile che venga chiesto di scaricare il file con estensione vsix come zip. Salvare il file in un percorso del disco rigido utilizzando l'estensione .vsix invece di .zip. Quindi, fare doppio clic sul file con estensione vsix in Esplora risorse per eseguire l'installazione.

2. In Visual Studio aprire il progetto completato nell'esercitazione [Creare un'app Windows]. Installare il pacchetto NuGet **WindowsAzure.MobileServices.SQLiteStore** per i progetti relativi a Windows 8.1 Runtime e Windows Phone 8.1.

    In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Gestisci pacchetti NuGet per la soluzione** per eseguire Gestione pacchetti NuGet. Nella scheda "Online" selezionare l'opzione "Includi versione preliminare" nell'elenco a discesa nella parte superiore. Cercare **SQLiteStore** per installare la versione 2.0.0-beta di `WindowsAzure.MobileServices.SQLiteStore`.

    Aggiungere quindi il riferimento a NuGet nei progetti Windows Store 8.1 e Windows Phone 8.1.

    >[AZURE.NOTE]Se l'installazione crea un riferimento aggiuntivo a una versione diversa di SQLite rispetto a quella installata, verrà generato un errore di compilazione. Risolvere l'errore rimuovendo il duplicato nel nodo **Riferimenti** dei progetti.

3. In Esplora soluzioni fare clic con il pulsante destro del mouse sul nodo **Riferimenti** per i progetti relativi alle piattaforme Windows 8.1 Runtime e Windows Phone 8.1 e verificare che sia presente un riferimento a SQLite, che si trova nella sezione **Estensioni**.

    ![][1] </br>

    **Windows 8.1 Runtime**

    ![][11] </br>

    **Windows Phone 8.1**

4. SQLite è una libreria nativa e richiede la scelta di un'architettura specifica della piattaforma, ad esempio **x86**, **x64** o **ARM**. **Qualsiasi CPU** non è supportata. In Esplora soluzioni fare clic sulla soluzione nella parte superiore della schermata, quindi nella casella di riepilogo relativa all'architettura del processore selezionare una delle impostazioni supportate da testare.

    ![][13]

5. In Esplora soluzioni, nel progetto condiviso, aprire il file MainPage.cs. Rimuovere i simboli di commento dalle istruzioni using seguenti all'inizio del file:

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;  // offline sync
        using Microsoft.WindowsAzure.MobileServices.Sync;         // offline sync

6. In MainPage.cs commentare la riga di codice che inizializza `todoTable` come `IMobileServiceTable`. Rimuovere il commento dalla riga di codice che inizializza `todoTable` come `IMobileServiceSyncTable`:

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>(); // offline sync

7. In MainPage.cs, nell'area contrassegnata come `Offline sync`, rimuovere i simboli di commento dai metodi `InitLocalStoreAsync` e `SyncAsync`. Il metodo `InitLocalStoreAsync` inizializza il contesto di sincronizzazione del client con un archivio SQLite. In Visual Studio è possibile selezionare tutte le righe commentate e usare i tasti di scelta rapida **CTRL**+**K**+**U** per rimuovere i commenti.

	Si noti che in `SyncAsync` viene eseguita un'operazione push da `MobileServiceClient.SyncContext` anziché da `IMobileServicesSyncTable`. Questo avviene perché il contesto tiene traccia delle modifiche apportate dal client per tutte le tabelle. Questa operazione serve per gli scenari in cui sono presenti relazioni tra tabelle. Per altre informazioni su questo comportamento, vedere [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure].

        private async Task InitLocalStoreAsync()
        {
            if (!App.MobileService.SyncContext.IsInitialized)
            {
                var store = new MobileServiceSQLiteStore("localstore.db");
                store.DefineTable<TodoItem>();
                await App.MobileService.SyncContext.InitializeAsync(store);
            }

            await SyncAsync();
        }

        private async Task SyncAsync()
        {
            await App.MobileService.SyncContext.PushAsync();
            await todoTable.PullAsync("todoItems", todoTable.CreateQuery());
        }

8. Nel gestore eventi `OnNavigatedTo` rimuovere i simboli di commento dalla chiamata a `InitLocalStoreAsync`:

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await InitLocalStoreAsync(); // offline sync
            await RefreshTodoItems();
        }

9. Rimuovere i simboli di commento dalle tre chiamate a `SyncAsync` nei metodi `InsertTodoItem`, `UpdateCheckedTodoItem` e `ButtonRefresh_Click`:

        private async Task InsertTodoItem(TodoItem todoItem)
        {
            await todoTable.InsertAsync(todoItem);
            items.Add(todoItem);

            await SyncAsync(); // offline sync
        }

        private async Task UpdateCheckedTodoItem(TodoItem item)
        {
            await todoTable.UpdateAsync(item);
            items.Remove(item);
            ListItems.Focus(Windows.UI.Xaml.FocusState.Unfocused);

            await SyncAsync(); // offline sync
        }

        private async void ButtonRefresh_Click(object sender, RoutedEventArgs e)
        {
            ButtonRefresh.IsEnabled = false;

            await SyncAsync(); // offline sync
            await RefreshTodoItems();

            ButtonRefresh.IsEnabled = true;
        }

10. Aggiungere gestori di eccezioni nel metodo `SyncAsync`. In una situazione offline viene generata un'eccezione `MobileServicePushFailedException` con `PushResult.Status == CancelledByNetworkError`.

        private async Task SyncAsync()
        {
            String errorString = null;

            try
            {
                await App.MobileService.SyncContext.PushAsync();
                await todoTable.PullAsync("todoItems", todoTable.CreateQuery()); // first param is query ID, used for incremental sync
            }

            catch (MobileServicePushFailedException ex)
            {
                errorString = "Push failed because of sync errors. You may be offine.\nMessage: " +
                  ex.Message + "\nPushResult.Status: " + ex.PushResult.Status.ToString();
            }
            catch (Exception ex)
            {
                errorString = "Pull failed: " + ex.Message +
                  "\n\nIf you are still in an offline scenario, " +
                  "you can try your Pull again when connected with your Mobile Serice.";
            }

            if (errorString != null)
            {
                MessageDialog d = new MessageDialog(errorString);
                await d.ShowAsync();
            }
        }

    In questo esempio `PullAsync` vengono recuperati tutti i record presenti nella `todoTable` remota, ma è anche possibile filtrare i record passando una query. Il primo parametro di `PullAsync` è un ID di query usato per la sincronizzazione incrementale, che usa il timestamp `UpdatedAt` per ottenere i soli record modificati dopo l'ultima sincronizzazione. L'ID di query deve essere una stringa descrittiva univoca per ogni query logica nell'applicazione client. Per rifiutare esplicitamente la sincronizzazione incrementale, passare `null` come ID di query. In ogni operazione pull verranno recuperati tutti i record e questo potrebbe creare inefficienze.

    Si noti che può verificarsi `MobileServicePushFailedException` per un'operazione sia push che pull. Questo avviene perché l'operazione pull esegue internamente un push per garantire che tutte le tabelle e le eventuali relazioni siano coerenti.

11. In Visual Studio premere **F5** per ricompilare ed eseguire l'app client. Il comportamento dell'app non cambierà dopo le modifiche relative alla sincronizzazione offline, poiché esegue un'operazione di sincronizzazione per ogni operazione di inserimento e aggiornamento. Tuttavia, verrà popolato un database locale che può essere usato in uno scenario offline. Verrà simulato uno scenario offline nella sezione successiva ora che il database locale è stato popolato.

## <a name="update-sync"></a>Aggiornare il comportamento di sincronizzazione dell'app client

In questa sezione si modificherà l'app client per simulare uno scenario offline interrompendo la connessione al back-end dell'app per dispositivi mobili di Azure. Quando si aggiungono elementi di dati, il gestore di eccezioni informa che l'app funziona in modalità offline con `PushResult.Status == CancelledByNetworkError`. Gli elementi aggiunti verranno mantenuti nell'archivio locale, ma non verranno sincronizzati con il back-end dell'app per dispositivi mobili fino a quando non si torna di nuovo online e si esegue un'operazione push al back-end dell'app per dispositivi mobili di Azure.

1. Modificare il file App.xaml.cs nel progetto condiviso. Impostare come commento l'inizializzazione di **MobileServiceClient** e aggiungere le righe seguenti, che usano un URL dell'app per dispositivi mobili non valido:

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-service.azurewebsites.fail",
            "https://your-gateway.azurewebsites.fail",
            ""
        );

2. Premere **F5** per compilare ed eseguire l'app. Si noti l'errore di sincronizzazione al momento dell'aggiornamento quando l'app viene avviata.
3. Immettere nuovi elementi todo e fare clic su **Salva** per ciascuno di essi. L'operazione push non riesce per ogni elemento con un oggetto `PushResult.Status=CancelledByNetworkError`. I nuovi elementi todo sono presenti solo nell'archivio locale fino a quando non è possibile effettuarne il push al back-end di App per dispositivi mobili. 
 
	Sarebbe possibile eliminare la finestra di dialogo dell'eccezione per `PushResult.Status=CancelledByNetworkError` e l'app client si comporterebbe come se fosse connessa al back-end dell'app per dispositivi mobili, supportando senza problemi tutte le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD, Create, Read, Update, Delete).

4. Chiudere l'app e riavviarla per verificare che i nuovi elementi creati siano salvati in modo permanente nell'archivio locale.

5. (Facoltativo) Usare Visual Studio per visualizzare la tabella di database SQL di Azure per verificare che i dati nel database back-end non siano cambiati.

   In Visual Studio aprire **Esplora server**. Passare al database in **Azure**->**Database SQL**. Fare clic con il pulsante destro del mouse sul database e scegliere **Apri in Esplora oggetti di SQL Server**. È ora possibile passare alla tabella di database SQL e al relativo contenuto.

6. (Facoltativo) Usare uno strumento REST come Fiddler o Postman per eseguire una query sul back-end mobile, usando una query GET nel formato `https://your-mobile-app-backend-name.azurewebsites.net/tables/TodoItem`. 

## <a name="update-online-app"></a>Aggiornare l'app per la riconnessione al back-end dell'app per dispositivi mobili

In questa sezione verrà effettuata la riconnessione dell'app al back-end dell'app per dispositivi mobili. Viene simulato il passaggio dell'app dallo stato offline allo stato online con il back-end dell'app per dispositivi mobili. Alla prima esecuzione dell'applicazione, il gestore eventi `OnNavigatedTo` chiama `InitLocalStoreAsync`. Viene quindi eseguita una chiamata a `SyncAsync` per sincronizzare l'archivio locale con il database back-end. L'app prova quindi a eseguire la sincronizzazione all'avvio.

1. Aprire il file App.xaml.cs nel progetto condiviso. Rimuovere il commento dalla precedente inizializzazione di `MobileServiceClient` per usare L'URL dell'app per dispositivi mobili e l'URL del gateway corretti.

2. Premere **F5** per ricompilare ed eseguire l'app. L'app sincronizza le modifiche locali con il back-end dell'app per dispositivi mobili di Azure usando operazioni push e pull non appena il gestore eventi `OnNavigatedTo` viene eseguito.

3. (Facoltativo) Visualizzare i dati aggiornati usando Esplora oggetti di SQL Server o uno strumento REST come Fiddler. Si noti che i dati sono stati sincronizzati tra il database back-end dell'app per dispositivi mobili di Azure e l'archivio locale.

4. Nell'app fare clic sulla casella di controllo accanto ad alcuni elementi da completare nell'archivio locale.

  `UpdateCheckedTodoItem` chiama `SyncAsync` per sincronizzare ogni elemento con il back-end dell'app per dispositivi mobili. `SyncAsync` chiama operazioni sia push sia pull. Si noti tuttavia che **ogni volta che si esegue un'operazione pull in una tabella in cui il client ha apportato modifiche, viene sempre eseguita prima un'operazione push automatica nel contesto di sincronizzazione del client**. Lo scopo è quello di assicurare che tutte le tabelle nell'archivio locale e le relazioni restino coerenti. In questo caso, quindi, sarebbe stato possibile rimuovere la chiamata a `PushAsync`, perché viene eseguita automaticamente quando si esegue un'operazione pull. Qualora non si sia consapevoli di questo comportamento, si potrebbe interpretare come un'operazione push non prevista. Per altre informazioni su questo comportamento, vedere [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure].


##Riepilogo

Per supportare le funzionalità offline di Servizi mobili è stata usata l'interfaccia `IMobileServiceSyncTable` ed è stato inizializzato `MobileServiceClient.SyncContext` in un archivio locale. In questo caso l'archivio locale era un database SQLite.

Le normali operazioni CRUD per Servizi mobili funzionano come se l'app fosse ancora connessa, ma tutte le operazioni si verificano nell'archivio locale.

Per sincronizzare l'archivio locale con il server sono stati usati i metodi `IMobileServiceSyncTable.PullAsync` e `MobileServiceClient.SyncContext.PushAsync`.

*  Per eseguire il push delle modifiche al server, è stata effettuata la chiamata a `IMobileServiceSyncContext.PushAsync()`. Questo metodo fa parte di `IMobileServicesSyncContext` invece che della tabella di sincronizzazione perché effettuerà il push delle modifiche in tutte le tabelle.

    Solo i record che sono stati in qualche modo modificati localmente (tramite le operazioni CUD) verranno inviati al server.

* Per eseguire il pull dei dati da una tabella del server all'app, è stata effettuata la chiamata a `IMobileServiceSyncTable.PullAsync`.

    Un'operazione pull comporta sempre l'esecuzione, prima del pull, di un'operazione push sul contesto di sincronizzazione se il contesto di sincronizzazione client ha rilevato modifiche nella tabella. Lo scopo è assicurare che tutte le tabelle nell'archivio locale e le relazioni restino coerenti.

    Sono inoltre presenti overload di `PullAsync()` che consentono di specificare una query in modo da filtrare i dati archiviati nel client. Se non viene passata una query, `PullAsync()` effettuerà il pull di tutte le righe nella tabella (o query) corrispondente. È possibile passare la query per filtrare solo le modifiche necessarie per la sincronizzazione dell'app.

* Per abilitare la sincronizzazione incrementale, passare un ID di query a `PullAsync()`. L'ID di query viene usato per archiviare il timestamp dell'ultimo aggiornamento dai risultati dell'ultima operazione di pull. L'ID di query deve essere una stringa descrittiva univoca per ogni query logica presente nell'app. Se la query ha un parametro, lo stesso valore di parametro può far parte dell'ID di query.

    Ad esempio, se si applica un filtro in base all'ID utente, l'ID di query univoco potrebbe essere:

        await PullAsync("todoItems" + userid, syncTable.Where(u => u.UserId = userid));

    Se si intende rifiutare esplicitamente la sincronizzazione incrementale, passare `null` come ID di query. In questo caso, verranno recuperati tutti i record in ogni chiamata a `PullAsync`, potenzialmente inefficace.

* L'app deve chiamare periodicamente `IMobileServiceSyncTable.PurgeAsync()` per ripulire l'archivio locale.


## Risorse aggiuntive

* [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure]

* [Cloud Cover: Sincronizzazione offline in Servizi mobili di Azure]  \(nota: il video è relativo ai Servizi mobili, ma il funzionamento della sincronizzazione offline è simile nelle app per dispositivi mobili di Azure\)

* [Azure Friday: App con supporto offline in Servizi mobili di Azure]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]: #next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data-preview/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data-preview/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data-preview/cpu-architecture.png


<!-- URLs. -->
[Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure]: ../app-service-mobile-offline-data-sync-preview.md
[create a windows app]: ../app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md
[Creare un'app Windows]: ../app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md
[Creare un'app di Windows]: ../app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md
[SQLite per Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[SQLite per Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkId=397953

[azure mobile app sdk nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/2.0.0-beta
[sqlite store nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/2.0.0-beta
 
[Cloud Cover: Sincronizzazione offline in Servizi mobili di Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: App con supporto offline in Servizi mobili di Azure]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

<!------HONumber=Sept15_HO1-->