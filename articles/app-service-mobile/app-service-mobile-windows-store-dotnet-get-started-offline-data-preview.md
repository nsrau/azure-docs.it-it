<properties
	pageTitle="Uso di dati offline con App mobili di Azure (Windows Store) | Dev Center di Servizi mobili"
	description="Informazioni su come usare App mobili di Azure per memorizzare nella cache e sincronizzare i dati offline nell'applicazione per Windows Store"
	documentationCenter="windows"
	authors="christopheranderson"
	manager="dwrede"
	editor=""
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/22/2015"
	ms.author="chrande"/>

# Uso della sincronizzazione dei dati offline in App mobili di Azure

[AZURE.INCLUDE [app-service-mobile-selector-offline-preview](../../includes/app-service-mobile-selector-offline-preview.md)]


Questa esercitazione illustra come aggiungere il supporto offline a un'app di Windows Store universale mediante un back-end di App mobili di Azure. Il supporto offline consentirà l'interazione con un database locale quando l'app è in uno scenario offline. Una volta che l'app è online con il database back-end, si sincronizzano le modifiche locali con le funzionalità offline.



In questa esercitazione si aggiornerà il progetto di app universale creato nell'esercitazione [Creare un'app Windows] per supportare le funzionalità offline delle app per dispositivi mobili di Azure. Consente inoltre di aggiungere i dati in uno scenario offline, eseguire la sincronizzazione degli elementi con il database online e di accedere al portale di gestione di Azure per visualizzare le modifiche apportate durate l'esecuzione dell'app.

Questa esercitazione descrive le operazioni di base seguenti:

1. [Aggiornare l'app per supportare le funzionalità offline]
2. [Aggiornare il comportamento di sincronizzazione dell'app]
3. [Aggiornare l'app per la riconnessione al back-end delle app per dispositivi mobili]

Per completare questa esercitazione, è necessario disporre di:

* Visual Studio 2013 in esecuzione su Windows 8.1.
* Completamento dell'esercitazione [Creare un'app Windows][create a windows app].
* [Azure Mobile Services SDK 2.0.0 o versione successiva][azure mobile app sdk nuget]
* [Azure Mobile Services SQLite Store 1.0.2 o versione successiva][sqlite store nuget]
* [SQLite per Windows 8.1](www.sqlite.org/downloads)

>[AZURE.NOTE]Per completare l'esercitazione, è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">versione di valutazione gratuita di Azure</a>.

##<a name="review"></a>Verificare la configurazione del progetto server (facoltativo)

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-enable-offline-preview](../../includes/app-service-mobile-dotnet-backend-enable-offline-preview.md)]

## <a name="enable-offline-app"></a>Aggiornare l'app per supportare le funzionalità offline

Le funzionalità offline di App mobili di Azure consentono di interagire con un database locale quando si usa un servizio mobile in modalità offline. Per usare queste funzionalità nell'app, inizializzare `MobileServiceClient.SyncContext` in un archivio locale. Quindi, fare riferimento alla tabella tramite l'interfaccia di `IMobileServiceSyncTable`. In questa esercitazione si userà SQLite per l'archivio locale.

1. Installare il runtime SQLite per Windows 8.1 e Windows Phone 8.1.

    * **Windows 8.1 Runtime:** installare [SQLite per Windows 8.1].
    * **Windows Phone 8.1:** installare [SQLite per Windows Phone 8.1].

    >[AZURE.NOTE]Quando si fa clic sul collegamento di installazione di SQLite in Internet Explorer, è possibile che venga chiesto di scaricare il file con estensione vsix come zip. Salvare il file in un percorso del disco rigido utilizzando l'estensione .vsix invece di .zip. Quindi, fare doppio clic sul file con estensione vsix in Esplora risorse per eseguire l'installazione.

2. In Visual Studio aprire il progetto completato nell'esercitazione [Creare un'app Windows]. Installare il pacchetto NuGet **WindowsAzure.MobileServices.SQLiteStore** per i progetti relativi a Windows 8.1 Runtime e Windows Phone 8.1.

    * **Windows 8.1:** in Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto Windows 8.1 e scegliere **Gestisci pacchetti NuGet** per eseguire Gestione pacchetti NuGet. Cercare **SQLiteStore** per installare il pacchetto `WindowsAzure.MobileServices.SQLiteStore`.
    * **Windows Phone 8.1:** fare clic con il pulsante destro del mouse sul progetto Windows Phone 8.1 e scegliere **Gestisci pacchetti NuGet** per eseguire Gestione pacchetti NuGet. Cercare **SQLiteStore** per installare il pacchetto `WindowsAzure.MobileServices.SQLiteStore`.

    >[AZURE.NOTE]Se l'installazione crea un riferimento a una versione precedente di SQLite, è sufficiente eliminare il riferimento duplicato.

3. In Esplora soluzioni fare clic con il pulsante destro del mouse sul nodo **Riferimenti** per i progetti relativi alle piattaforme Windows 8.1 Runtime e Windows Phone 8.1 e verificare che sia presente un riferimento a SQLite, che si trova nella sezione **Estensioni**.

    ![][1] </br>

    **Windows 8.1 Runtime**

    ![][11] </br>

    **Windows Phone 8.1**

4. SQLite Runtime richiede la modifica dell'architettura del processore per il progetto creato in **x86**, **x64** o **ARM**. **Qualsiasi CPU** non è supportata. In Esplora soluzioni fare clic sulla soluzione nella parte superiore della schermata, quindi nella casella di riepilogo relativa all'architettura del processore selezionare una delle impostazioni supportate da testare.

    ![][13]

5. In Esplora soluzioni, nel progetto condiviso, aprire il file MainPage.cs. Rimuovere i simboli di commento dalle istruzioni using seguenti all'inizio del file:

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;  // offline sync
        using Microsoft.WindowsAzure.MobileServices.Sync;         // offline sync

6. In MainPage.cs impostare come commento la definizione di `todoTable` e rimuovere i simboli di commento dalla definizione nella riga successiva, che chiama `MobileServicesClient.GetSyncTable()`:

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>(); // offline sync

7. In MainPage.cs, nell'area contrassegnata come `Offline sync`, rimuovere i simboli di commento dai metodi `InitLocalStoreAsync` e `SyncAsync`. Il metodo `InitLocalStoreAsync` inizializza il contesto di sincronizzazione del client con un archivio SQLite.

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

10. Aggiungere gestori di eccezioni nel metodo `SyncAsync`:

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
                errorString = "Push failed because of sync errors: " +
                  ex.PushResult.Errors.Count + " errors, message: " + ex.Message;
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

    In questo esempio vengono recuperati tutti i record presenti nella tabella `todoTable` remota, ma è anche possibile filtrare i record passando una query. Il primo parametro di `PullAsync` è un ID di query usato per la sincronizzazione incrementale, che usa il timestamp `UpdatedAt` per ottenere i soli record modificati dopo l'ultima sincronizzazione. L'ID di query deve essere una stringa descrittiva univoca per ogni query logica presente nell'app. Per rifiutare esplicitamente la sincronizzazione incrementale, passare `null` come ID di query. In ogni operazione pull verranno recuperati tutti i record e questo potrebbe creare inefficienze.

    Si noti che può verificarsi `MobileServicePushFailedException` per un'operazione sia push che pull. Questo avviene perché l'operazione pull esegue internamente un push per garantire che tutte le tabelle e le eventuali relazioni siano coerenti.

11. In Visual Studio premere **F5** per ricompilare ed eseguire l'app. Il comportamento dell'app non cambierà dopo le modifiche relative alla sincronizzazione offline, poiché esegue un'operazione di sincronizzazione per ogni operazione di inserimento e aggiornamento.

## <a name="update-sync"></a>Aggiornare il comportamento di sincronizzazione dell'app

In questa sezione si procederà alla modifica dell'app in modo che non effettui la sincronizzazione quando sono in corso operazioni di inserimento e aggiornamento, ma solo quando viene premuto il pulsante di **aggiornamento**. Successivamente, verrà interrotta la connessione dell'app al back-end di App mobili per simulare uno scenario offline. Quando si aggiungono elementi di dati, questi vengono conservati nell'archivio locale, ma non vengono sincronizzati con il back-end di App mobili.

1. Aprire il file App.xaml.cs nel progetto condiviso. Modificare i metodi `InsertTodoItem` e `UpdateCheckedTodoItem` per impostare come commento le chiamate a `SyncAsync`.

2. Modificare il file App.xaml.cs nel progetto condiviso. Impostare come commento l'inizializzazione di **MobileServiceClient** e aggiungere le righe seguenti, che usano un URL dell'app per dispositivi mobili non valido:

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://<your-mobile-service>-code.azurewebsites.fail",
            "https://<your-mobile-service>gateway.azurewebsites.fail",
            "AppKey"
        );

3. In `InitLocalStoreAsync()` impostare come commento la chiamata a `SyncAsync()`, in modo che l'app non esegua la sincronizzazione all'avvio.

4. Premere **F5** per compilare ed eseguire l'app. Immettere nuovi elementi todo e fare clic su **Salva** per ciascuno di essi. I nuovi elementi todo esistono solo nell'archivio locale fino a quando è possibile effettuarne il push al back-end di App mobili. L'app client si comporta come se fosse connessa al back-end di App mobili, supportando tutte le operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione).

5. Chiudere l'app e riavviarla per verificare che i nuovi elementi creati siano salvati in modo permanente nell'archivio locale.

## <a name="update-online-app"></a>Aggiornare l'app per la riconnessione al back-end dell'app per dispositivi mobili

In questa sezione verrà effettuata la riconnessione dell'app al back-end di App mobili. Viene simulato il passaggio dell'app dallo stato offline allo stato online con il back-end di App mobili. Quando si preme il pulsante di aggiornamento, i dati verranno sincronizzati con il back-end di App mobili.

1. Aprire il file App.xaml.cs nel progetto condiviso. Rimuovere il commento della precedente inizializzazione di `MobileServiceClient` per aggiungere nuovamente l'URL, l'URL gateway e la chiave dell'app corretti per il back-end del servizio app per dispositivi mobili.

2. Premere **F5** per ricompilare ed eseguire l'app. Si noti che i dati appaiono uguali a quelli dello scenario offline nonostante l'app sia ora connessa al back-end di App mobili. Questo avviene perché questa app funziona sempre con la `IMobileServiceSyncTable` che fa riferimento all'archivio locale.

3. Accedere al portale di Azure ed esaminare il database del back-end di App mobili.

    In Visual Studio passare a **Esplora server** -> **Azure** -> **Database SQL**. Fare clic con il pulsante destro del mouse sul database e scegliere **Apri in Esplora oggetti di SQL Server**.

    Si noti che i dati non sono stati sincronizzati tra il database e l'archivio locale.

4. Nell'app premere il pulsante di **aggiornamento**. L'app effettuerà una chiamata a `PushAsync` e `PullAsync`. Questa operazione push invia gli elementi dell'archivio locale al servizio mobile, quindi recupera i nuovi dati dal servizio mobile.

    Un'operazione push viene eseguita da `MobileServiceClient.SyncContext` anziché da `IMobileServicesSyncTable` ed esegue il push delle modifiche in tutte le tabelle associate a tale contesto di sincronizzazione. Questa operazione serve per gli scenari in cui sono presenti relazioni tra tabelle.

5. Nell'app fare clic sulla casella di controllo accanto ad alcuni elementi da completare nell'archivio locale.

6. Premere di nuovo il pulsante di **aggiornamento** per chiamare `SyncAsync`. `SyncAsync` chiama sia un'operazione push sia un'operazione pull, ma in questo caso la chiamata a `PushAsync` avrebbe potuto essere rimossa. Questo perché un'operazione **pull effettua sempre un'operazione push all'inizio**. Lo scopo è assicurare che tutte le tabelle nell'archivio locale e le relazioni restino coerenti.


##Riepilogo

Per supportare le funzionalità offline di Servizi mobili è stata usata l'interfaccia `IMobileServiceSyncTable` ed è stato inizializzato `MobileServiceClient.SyncContext` in un archivio locale. In questo caso l'archivio locale era un database SQLite.

Le normali operazioni CRUD per Servizi mobili funzionano come se l'app fosse ancora connessa, ma tutte le operazioni si verificano nell'archivio locale.

Per sincronizzare l'archivio locale con il server sono stati usati i metodi `IMobileServiceSyncTable.PullAsync` e `MobileServiceClient.SyncContext.PushAsync`.

*  Per eseguire il push delle modifiche al server, è stata effettuata la chiamata a `IMobileServiceSyncContext.PushAsync()`. Questo metodo fa parte di `IMobileServicesSyncContext` invece che della tabella di sincronizzazione perché effettuerà il push delle modifiche in tutte le tabelle.

    Solo i record che sono stati in qualche modo modificati localmente (tramite le operazioni CUD) verranno inviati al server.

* Per eseguire il pull dei dati da una tabella del server all'app, è stata effettuata la chiamata a `IMobileServiceSyncTable.PullAsync`.

    Un'operazione pull effettua sempre un'operazione push all'inizio. Lo scopo è assicurare che tutte le tabelle nell'archivio locale e le relazioni restino coerenti.

    Sono inoltre presenti overload di `PullAsync()` che consentono di specificare una query in modo da filtrare i dati archiviati nel client. Se non viene passata una query, `PullAsync()` effettuerà il pull di tutte le righe nella tabella (o query) corrispondente. È possibile passare la query per filtrare solo le modifiche necessarie per la sincronizzazione dell'app.

* Per abilitare la sincronizzazione incrementale, passare un ID di query a `PullAsync()`. L'ID di query viene usato per archiviare il timestamp dell'ultimo aggiornamento dai risultati dell'ultima operazione di pull. L'ID di query deve essere una stringa descrittiva univoca per ogni query logica presente nell'app. Se la query include un parametro, è necessario l'ID di query deve includere lo stesso valore di parametro.

    Ad esempio, se si applica un filtro in base all'ID utente, è necessario che questo faccia parte dell'ID di query:

        await PullAsync("todoItems" + userid, syncTable.Where(u => u.UserId = userid));

    Se si intende rifiutare esplicitamente la sincronizzazione incrementale, passare `null` come ID di query. In questo caso, verranno recuperati tutti i record in ogni chiamata a `PullAsync`, potenzialmente inefficace.

* L'app deve chiamare periodicamente `IMobileServiceSyncTable.PurgeAsync()` per ripulire l'archivio locale.


<!-- Anchors. -->
[Aggiornare l'app per supportare le funzionalità offline]: #enable-offline-app
[Aggiornare il comportamento di sincronizzazione dell'app]: #update-sync
[Aggiornare l'app per la riconnessione al back-end delle app per dispositivi mobili]: #update-online-app
[Next Steps]: #next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data-preview/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data-preview/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data-preview/cpu-architecture.png


<!-- URLs. -->
[todolist offline sample]: http://go.microsoft.com/fwlink/?LinkId=394777
[create a windows app]: /documentation/articles/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview/
[Creare un'app Windows]: /documentation/articles/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview/
[SQLite per Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[SQLite per Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkId=397953

[azure mobile app sdk nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/2.0.0-beta
[sqlite store nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.2
 

<!---HONumber=August15_HO6-->