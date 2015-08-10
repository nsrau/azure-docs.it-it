<properties 
	pageTitle="Uso di dati offline nell'app di Windows universale | Servizi mobili di Azure" 
	description="Informazioni su come usare Servizi mobili di Azure per memorizzare nella cache e sincronizzare i dati offline nell'app di Windows universale." 
	documentationCenter="mobile-services" 
	authors="lindydonna" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/23/2015" 
	ms.author="donnam"/>

# Uso della sincronizzazione dei dati offline in Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-offline](../../includes/mobile-services-selector-offline.md)]

Questa esercitazione illustra come aggiungere il supporto offline a un'app di Windows Store universale mediante Servizi mobili di Azure. Il supporto offline consentirà l'interazione con un database locale quando l'app è in uno scenario offline. Una volta che l'app è online con il database back-end, si sincronizzano le modifiche locali con le funzionalità offline.

Se si preferisce guardare un video, nel clip a destra vengono eseguiti gli stessi passaggi dell'esercitazione.

> [AZURE.VIDEO build-offline-apps-azure-mobile-services]

In questa esercitazione si aggiorna il progetto di app universale creato nell'esercitazione [Introduzione a Servizi mobili] per supportare le funzionalità offline di Servizi mobili di Azure. Consente inoltre di aggiungere i dati in uno scenario offline, eseguire la sincronizzazione degli elementi con il database online e di accedere al portale di gestione di Azure per visualizzare le modifiche apportate durate l'esecuzione dell'app.

>[AZURE.NOTE]Questa esercitazione è stata ideata per illustrare come Servizi mobili consente di usare Azure per archiviare e recuperare i dati di un'app di Windows Store. Se si tratta della prima esperienza con Servizi mobili, è consigliabile iniziare dall'esercitazione [Introduzione a Servizi mobili].
>
>L'esercitazione di Windows Phone 8 meno recente per Visual Studio 2012 è ancora disponibile qui, [Esercitazione di Windows Phone 8 per Visual Studio 2012].

##Prerequisiti 

Per completare questa esercitazione, è necessario disporre di:

* Visual Studio 2013 in esecuzione su Windows 8.1
* Esercitazione [Introduzione a Servizi mobili] completata
* [Azure Mobile Services SDK 1.3.0 o versione successiva][Mobile Services SDK Nuget]
* [Azure Mobile Services SQLite Store 1.0.0 o versione successiva][SQLite store nuget]
* [SQLite per Windows 8.1](www.sqlite.org/downloads)
* Un account Azure. Se non si ha un account, è possibile iscriversi per accedere a una versione di valutazione di Azure e ottenere un massimo di 10 servizi mobili gratuiti che potranno essere usati anche dopo il termine del periodo di valutazione. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28). 

## <a name="enable-offline-app"></a>Aggiornare l'app per supportare le funzionalità offline

Servizi mobili di Azure consente di interagire con un database locale quando si usa un servizio mobile in modalità offline. Per usare queste funzionalità nell'app, inizializzare `MobileServiceClient.SyncContext` in un archivio locale. Quindi, fare riferimento alla tabella tramite l'interfaccia di `IMobileServiceSyncTable`. In questa esercitazione si userà SQLite per l'archivio locale.

>[AZURE.NOTE]È possibile ignorare questa sezione e scaricare semplicemente il progetto di esempio già dotato di supporto offline dall'archivio degli esempi di GitHub per Servizi mobili. Il progetto di esempio con supporto offline abilitato è disponibile qui, [Esempio offline TodoList].

1. Installare il runtime SQLite per Windows 8.1 e Windows Phone 8.1. 

    * **Windows 8.1 Runtime:** installare [SQLite per Windows 8.1].
    * **Windows Phone 8.1:** installare [SQLite per Windows Phone 8.1].

    >[AZURE.NOTE]Quando si fa clic sul collegamento di installazione di SQLite in Internet Explorer, è possibile che venga chiesto di scaricare il file con estensione vsix come zip. Salvare il file in un percorso del disco rigido utilizzando l'estensione .vsix invece di .zip. Quindi, fare doppio clic sul file con estensione vsix in Esplora risorse per eseguire l'installazione.

2. In Visual Studio aprire il progetto completato nell'esercitazione [Introduzione a Servizi mobili]. Installare il pacchetto NuGet **WindowsAzure.MobileServices.SQLiteStore** per i progetti relativi a Windows 8.1 Runtime e Windows Phone 8.1.

    * **Windows 8.1:** in Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto Windows 8.1 e scegliere **Gestisci pacchetti NuGet** per eseguire Gestione pacchetti NuGet. Cercare **SQLiteStore** per installare il pacchetto `WindowsAzure.MobileServices.SQLiteStore`.
    * **Windows Phone 8.1:** fare clic con il pulsante destro del mouse sul progetto Windows Phone 8.1 e scegliere **Gestisci pacchetti NuGet** per eseguire Gestione pacchetti NuGet. Cercare **SQLiteStore** per installare il pacchetto `WindowsAzure.MobileServices.SQLiteStore`.

    >[AZURE.NOTE]Se l'installazione crea un riferimento a una versione precedente di SQLite, è sufficiente eliminare il riferimento duplicato.

    ![][2]

2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul nodo **Riferimenti** per i progetti relativi alle piattaforme Windows 8.1 Runtime e Windows Phone 8.1 e verificare che sia presente un riferimento a SQLite, che si trova nella sezione **Estensioni**.

    ![][1] </br>

    **Windows 8.1 Runtime**

    ![][11] </br>

    **Windows Phone 8.1**

3. SQLite Runtime richiede la modifica dell'architettura del processore per il progetto creato in **x86**, **x64** o **ARM**. **Qualsiasi CPU** non è supportata. In Esplora soluzioni fare clic sulla soluzione nella parte superiore della schermata, quindi nella casella di riepilogo relativa all'architettura del processore selezionare una delle impostazioni supportate da testare.

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

    >[AZURE.NOTE]\* Per rimuovere i record dall'archivio locale del dispositivo quando sono stati eliminati dal database del servizio mobile, è necessario abilitare l'[eliminazione temporanea]. In alternativa, l'app deve periodicamente chiamare `IMobileServiceSyncTable.PurgeAsync()` per ripulire l'archivio locale.

    Si noti che può verificarsi `MobileServicePushFailedException` per un'operazione sia push che pull. Questo avviene perché l'operazione pull esegue internamente un push per garantire che tutte le tabelle e le eventuali relazioni siano coerenti. L'esercitazione successiva, [Gestione dei conflitti con il supporto offline per Servizi mobili], mostra come gestire queste eccezioni relative alla sincronizzazione.

11. In Visual Studio premere **F5** per ricompilare ed eseguire l'app. Il comportamento dell'app non cambierà dopo le modifiche relative alla sincronizzazione offline, poiché esegue un'operazione di sincronizzazione per ogni operazione di inserimento e aggiornamento.

## <a name="update-sync"></a>Aggiornare il comportamento di sincronizzazione dell'app

In questa sezione si procederà alla modifica dell'app in modo che non effettui la sincronizzazione quando sono in corso operazioni di inserimento e aggiornamento, ma solo quando viene premuto il pulsante di **aggiornamento**. Successivamente, verrà interrotta la connessione dell'app al servizio mobile per simulare uno scenario offline. Quando si aggiungono elementi di dati, questi vengono conservati nell'archivio locale, ma non vengono sincronizzati con il servizio mobile.

1. Aprire il file MainPage.cs nel progetto condiviso. Modificare i metodi `InsertTodoItem` e `UpdateCheckedTodoItem` per impostare come commento le chiamate a `SyncAsync`.

2. Modificare il file App.xaml.cs nel progetto condiviso. Impostare come commento l'inizializzazione di **MobileServiceClient** e aggiungere le righe seguenti, che usano un URL del servizio mobile non valido:

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.xxx/",
            "AppKey"
        );

3. In `InitLocalStoreAsync()` impostare come commento la chiamata a `SyncAsync()`, in modo che l'app non esegua la sincronizzazione all'avvio.

4. Premere **F5** per compilare ed eseguire l'app. Immettere nuovi elementi todo e fare clic su **Salva** per ciascuno di essi. I nuovi elementi todo esistono solo nell'archivio locale fino a quando è possibile effettuarne il push al servizio mobile. L'app client si comporta come se fosse connessa al servizio mobile, supportando tutte le operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione).

5. Chiudere l'app e riavviarla per verificare che i nuovi elementi creati siano salvati in modo permanente nell'archivio locale.

## <a name="update-online-app"></a>Aggiornare l'app per la riconnessione al servizio mobile

In questa sezione verrà effettuata la riconnessione dell'app al servizio mobile. Viene simulato il passaggio dell'app dallo stato offline allo stato online con il servizio mobile. Quando si preme il pulsante di aggiornamento, i dati verranno sincronizzati con il servizio mobile.

1. Aprire il file App.xaml.cs nel progetto condiviso. Rimuovere i simboli di commento dalla precedente inizializzazione di `MobileServiceClient` per aggiungere nuovamente l'URL del servizio mobile e la chiave dell'app corretti.

2. Premere **F5** per ricompilare ed eseguire l'app. Si noti che i dati appaiono uguali a quelli dello scenario offline nonostante l'app sia ora connessa al servizio mobile. Questo avviene perché questa app funziona sempre con la `IMobileServiceSyncTable` che fa riferimento all'archivio locale.

3. Accedere al portale di gestione di Microsoft Azure ed esaminare il database relativo al servizio mobile. Se il servizio usa il back-end JavaScript per i servizi mobili, è possibile visualizzare i dati dalla scheda **Data** del servizio mobile.

    Se si usa il back-end .NET per il servizio mobile, in Visual Studio passare a **Esplora server** -> **Azure** -> **Database SQL**. Fare clic con il pulsante destro del mouse sul database e scegliere **Apri in Esplora oggetti di SQL Server**.

    Si noti che i dati non sono stati sincronizzati tra il database e l'archivio locale.

    ![][6]

4. Nell'app premere il pulsante di **aggiornamento**. L'app effettuerà una chiamata a `PushAsync` e `PullAsync`. Questa operazione push invia gli elementi dell'archivio locale al servizio mobile, quindi recupera i nuovi dati dal servizio mobile.

    Un'operazione push viene eseguita da `MobileServiceClient.SyncContext` anziché da `IMobileServicesSyncTable` ed esegue il push delle modifiche in tutte le tabelle associate a tale contesto di sincronizzazione. Questa operazione serve per gli scenari in cui sono presenti relazioni tra tabelle.

    ![][7]

5. Nell'app fare clic sulla casella di controllo accanto ad alcuni elementi da completare nell'archivio locale.

    ![][8]

6. Premere di nuovo il pulsante di **aggiornamento** per chiamare `SyncAsync`. `SyncAsync` chiama sia un'operazione push sia un'operazione pull, ma in questo caso la chiamata a `PushAsync` avrebbe potuto essere rimossa. Questo perché un'operazione **pull effettua sempre un'operazione push all'inizio**. Lo scopo è assicurare che tutte le tabelle nell'archivio locale e le relazioni restino coerenti.

    ![][10]
  

##Riepilogo

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../../includes/mobile-services-offline-summary-csharp.md)]

## Passaggi successivi

* [Gestione dei conflitti con il supporto offline per Servizi mobili]

* [Uso dell'eliminazione temporanea in Servizi mobili][Soft Delete]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your mobile service]: #update-online-app
[Next Steps]: #next-steps

<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-todoitem-data-browse.png
[1]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-reference-sqlite-dialog.png
[2]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-sqlitestore-nuget.png
[6]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse.png
[7]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse2.png
[8]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run2.png
[10]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse3.png
[11]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-wp81-reference-sqlite-dialog.png
[12]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/new-synchandler-class.png
[13]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Gestione dei conflitti con il supporto offline per Servizi mobili]: mobile-services-windows-store-dotnet-handling-conflicts-offline-data.md
[Esempio offline TodoList]: http://go.microsoft.com/fwlink/?LinkId=394777
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Getting Started]: ../mobile-services-dotnet-backend-windows-phone-get-started.md
[Get started with data]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md
[Introduzione a Servizi mobili]: ../mobile-services-windows-store-get-started.md
[SQLite per Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[SQLite per Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkId=397953
[Esercitazione di Windows Phone 8 per Visual Studio 2012]: mobile-services-windows-phone-get-started-offline-data.md
[Soft Delete]: mobile-services-using-soft-delete.md
[eliminazione temporanea]: mobile-services-using-soft-delete.md


[Mobile Services SDK Nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0
[SQLite store nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0
 

<!---HONumber=July15_HO5-->