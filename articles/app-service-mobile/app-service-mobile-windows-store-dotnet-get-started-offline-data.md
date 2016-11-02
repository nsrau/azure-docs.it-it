<properties
	pageTitle="Abilitare la sincronizzazione offline per l'app UWP (Universal Windows Platform) con App per dispositivi mobili| Servizio App di Azure"
	description="Informazioni su come usare un'app per dispositivi mobili di Azure per memorizzare nella cache e sincronizzare i dati offline nell'app UWP (Universal Windows Platform)."
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
	ms.date="08/19/2016"
	ms.author="wesmc"/>

# Abilitare la sincronizzazione offline per l'app di Windows

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## Panoramica

Questa esercitazione illustra come aggiungere il supporto offline a un'app UWP (Universal Windows Platform) tramite un back-end di App per dispositivi mobili di Azure. La sincronizzazione offline consente agli utenti finali di interagire con un'app, visualizzando, aggiungendo e modificando i dati, anche se non è disponibile una connessione di rete. Le modifiche vengono archiviate in un database locale. Quando il dispositivo torna online, vengono sincronizzate con il back-end remoto.

In questa esercitazione verrà aggiornato il progetto dell'app UWP creato nell'esercitazione [Creare un'app Windows] in modo da supportare le funzionalità offline di App per dispositivi mobili di Azure. Se non si usa il progetto server di avvio rapido scaricato, è necessario aggiungere al progetto il pacchetto di estensione per l'accesso ai dati. Per altre informazioni sui pacchetti di estensione server, vedere l'articolo relativo all'[utilizzo dell'SDK del server back-end .NET per app per dispositivi mobili di Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Per altre informazioni sulla funzionalità di sincronizzazione offline, vedere l'argomento [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure].

## Requisiti

Per completare questa esercitazione, è necessario disporre di:

* Visual Studio 2013 in esecuzione su Windows 8.1.
* Completamento dell'esercitazione [Creare un'app Windows][create a windows app].
* [Azure Mobile Services SQLite Store][sqlite store nuget]
* [SQLite for Universal Windows Platform](http://www.sqlite.org/downloads)

## Aggiornare l'app client per supportare le funzionalità offline

Le funzionalità offline delle app per dispositivi mobili di Azure consentono di interagire con un database locale in uno scenario offline. Per usare queste funzionalità nell'app, inizializzare [SyncContext][synccontext] in un archivio locale. Quindi, fare riferimento alla tabella tramite l'interfaccia di [IMobileServiceSyncTable][IMobileServiceSyncTable]. SQLite viene usato come archivio locale nel dispositivo.

1. Installare il [runtime di SQLite per la piattaforma UWP (Universal Windows Platform)](http://sqlite.org/2016/sqlite-uwp-3120200.vsix).

2. In Visual Studio aprire Gestione pacchetti NuGet per il progetto dell'app UWP completato nell'esercitazione [Creare un'app Windows], quindi cercare e installare il pacchetto NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore**.

4. In Esplora soluzioni fare clic con il pulsante destro del mouse su **Riferimenti** > **Aggiungi riferimento** > **UWP** > **Estensioni**, quindi abilitare sia **SQLite for Universal Windows Platform** che **Visual C++ 2015 Runtime for Universal Windows Platform apps**.

    ![Aggiungere un riferimento UWP SQLite][1]

5. Aprire il file MainPage.xaml.cs e rimuovere i commenti dalle istruzioni `using` seguenti all'inizio del file:

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;  
        using Microsoft.WindowsAzure.MobileServices.Sync;         

6. Impostare come commento la riga di codice che inizializza `todoTable` come **IMobileServiceTable**, quindi rimuovere il commento dalla riga di codice che inizializza `todoTable` come **IMobileServiceSyncTable**:

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>(); 

7. Nell'area `Offline sync` di MainPage.xaml.cs rimuovere il commento dai metodi seguenti:

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

	In `SyncAsync` viene avviata un'operazione push da [SyncContext][synccontext] e quindi una sincronizzazione incrementale. Il contesto di sincronizzazione tiene traccia delle modifiche apportate dal client a tutte le tabelle. Per altre informazioni su questo comportamento, vedere [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure].

8. Nel gestore eventi `OnNavigatedTo` rimuovere il commento dalla chiamata a `InitLocalStoreAsync`. Se è già stata completata l'esercitazione [Aggiungere l'autenticazione all'app Windows](app-service-mobile-windows-store-dotnet-get-started-users.md), è necessario eseguire questa operazione nel metodo `AuthenticateAsync`.

9. Rimuovere il commento dalle chiamate a [PushAsync] nei metodi `InsertTodoItem` e `UpdateCheckedTodoItem`, quindi rimuovere il commento dalla chiamata a `SyncAsync` nel metodo `ButtonRefresh_Click`.

10. Nel metodo `SyncAsync` aggiungere i gestori di eccezioni seguenti:

        private async Task SyncAsync()
        {
            String errorString = null;

            try
            {
                await App.MobileService.SyncContext.PushAsync();
                await todoTable.PullAsync("todoItems", todoTable.CreateQuery()); 
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
                  "you can try your pull again when connected with your backend.";
            }

            if (errorString != null)
            {
                MessageDialog d = new MessageDialog(errorString);
                await d.ShowAsync();
            }
        }

	In una situazione offline, [PullAsync][PullAsync] può causare un'eccezione [MobileServicePushFailedException][MobileServicePushFailedException] con [PushResult.Status][Status] corrispondente a [CancelledByNetworkError][CancelledByNetworkError]. Questo si verifica quando un'operazione di push implicito tenta di effettuare il push di aggiornamenti in sospeso prima dell'operazione di pull e ha esito negativo. Per altre informazioni, vedere [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure].

11. In Visual Studio premere **F5** per ricompilare ed eseguire l'app client. L'app funziona esattamente come prima di attivare la sincronizzazione offline. Tuttavia, il database locale è ora popolato con dati che possono essere usati in uno scenario offline. Verrà quindi creato uno scenario offline e verranno usati dati archiviati localmente per avviare l'app.

## <a name="update-sync"></a>Aggiornare l'app per disconnetterla dal back-end

In questa sezione verrà interrotta la connessione al back-end dell'app per dispositivi mobili per simulare una situazione offline. Quando si aggiungono elementi di dati, il gestore di eccezioni informa che l'app è in modalità offline. In questo stato, nuovi elementi verranno aggiunti all'archivio locale che verranno sincronizzati con il back-end dell'app per dispositivi mobili la prima volta che verrà eseguita un'operazione di push in presenza di uno stato di connessione.

1. Modificare il file App.xaml.cs nel progetto condiviso. Impostare come commento l'inizializzazione di **MobileServiceClient** e aggiungere le righe seguenti, che usano un URL dell'app per dispositivi mobili non valido:

         public static MobileServiceClient MobileService =
				new MobileServiceClient("https://your-service.azurewebsites.fail");

	Se l'app usa anche una procedura di autenticazione, non sarà possibile eseguire l'accesso. È anche possibile illustrare il comportamento offline disabilitando le reti Wi-Fi e cellulare sul dispositivo o impostando la modalità aereo.

2. Premere **F5** per compilare ed eseguire l'app. Si noti l'errore di sincronizzazione al momento dell'aggiornamento quando l'app viene avviata.
 
3. Immettere nuovi elementi. Si noti che il push ha esito negativo con stato [CancelledByNetworkError] ogni volta che si fa clic su **Salva**. I nuovi elementi todo, tuttavia, sono presenti nell'archivio locale fino a quando non è possibile effettuarne il push al back-end dell'app per dispositivi mobili.

	In un'app di produzione, se si eliminano queste eccezioni, l'app client si comporta come se fosse ancora connessa al back-end dell'app per dispositivi mobili.

4. Chiudere l'app e riavviarla per verificare che i nuovi elementi creati siano salvati in modo permanente nell'archivio locale.

5. (Facoltativo) In Visual Studio aprire **Esplora server**. Passare al database in **Azure**->**Database SQL**. Fare clic con il pulsante destro del mouse sul database e scegliere **Apri in Esplora oggetti di SQL Server**. È ora possibile passare alla tabella di database SQL e al relativo contenuto. Verificare che i dati nel database back-end non siano stati modificati.

6. (Facoltativo) Usare uno strumento REST come Fiddler o Postman per eseguire una query sul back-end mobile, usando una query GET nel formato `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Aggiornare l'app per la riconnessione al back-end dell'app per dispositivi mobili

In questa sezione verrà effettuata la riconnessione dell'app al back-end dell'app per dispositivi mobili. Viene simulato il passaggio dell'app dallo stato offline allo stato online con il back-end dell'app per dispositivi mobili. Alla prima esecuzione dell'applicazione, il gestore eventi `OnNavigatedTo` chiama `InitLocalStoreAsync`. Viene quindi eseguita una chiamata a `SyncAsync` per sincronizzare l'archivio locale con il database back-end. L'app prova quindi a eseguire la sincronizzazione all'avvio.

1. Aprire App.xaml.cs nel progetto condiviso e rimuovere il commento dall'inizializzazione precedente di `MobileServiceClient` per l'URL corretto dell'app per dispositivi mobili.

2. Premere **F5** per ricompilare ed eseguire l'app. L'app sincronizza le modifiche locali con il back-end dell'app per dispositivi mobili di Azure usando operazioni push e pull non appena il gestore eventi `OnNavigatedTo` viene eseguito.

3. (Facoltativo) Visualizzare i dati aggiornati usando Esplora oggetti di SQL Server o uno strumento REST come Fiddler. Si noti che i dati sono stati sincronizzati tra il database back-end dell'app per dispositivi mobili di Azure e l'archivio locale.

4. Nell'app fare clic sulla casella di controllo accanto ad alcuni elementi da completare nell'archivio locale.

  `UpdateCheckedTodoItem` chiama `SyncAsync` per sincronizzare ogni elemento completato con il back-end dell'app per dispositivi mobili. `SyncAsync` chiama operazioni sia push che pull. Si noti tuttavia che **ogni volta che si esegue un'operazione pull in una tabella in cui il client ha apportato modifiche, viene sempre eseguita prima un'operazione push automatica nel contesto di sincronizzazione del client**. Lo scopo è quello di assicurare che tutte le tabelle nell'archivio locale e le relazioni restino coerenti. In questo caso, quindi, sarebbe stato possibile rimuovere la chiamata a `PushAsync`, poiché viene effettuata automaticamente ogni volta che si esegue un'operazione pull. Qualora non si sia consapevoli di questo comportamento, si potrebbe interpretare come un'operazione push non prevista. Per altre informazioni su questo comportamento, vedere [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure].


##Riepilogo dell'API

Per supportare le funzionalità offline di Servizi mobili, è stata usata l'interfaccia [IMobileServiceSyncTable] ed è stato inizializzato [MobileServiceClient.SyncContext][synccontext] con un database SQLite locale. In una situazione offline le normali operazioni CRUD per App per dispositivi mobili funzionano come se l'app fosse ancora connessa, mentre tutte le operazioni interessano l'archivio locale. Per sincronizzare l'archivio locale con il server vengono usati i metodi seguenti:

*  **[PushAsync]**  
   Poiché questo metodo è un membro di [IMobileServicesSyncContext], viene effettuato il push al back-end delle modifiche in tutte le tabelle. Solo i record con modifiche locali vengono inviati al server.

* **[PullAsync]**   
   un pull viene avviato da [IMobileServiceSyncTable]. Se nella tabella sono presenti modifiche di cui si è tenuta traccia, viene eseguito un push implicito per assicurarsi che tutte le tabelle nell'archivio locale e le relazioni corrispondenti siano ancora coerenti. Il parametro *pushOtherTables* determina se viene effettuato il push implicito di altre tabelle nel contesto. Il parametro *query* riceve una stringa [IMobileServiceTableQuery&lt;U&gt;][IMobileServiceTableQuery] o una stringa di query OData per filtrare i dati restituiti. Il parametro *queryId* viene usato per definire la sincronizzazione incrementale. Per altre informazioni, vedere [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure](app-service-mobile-offline-data-sync.md#how-sync-works).

* **[PurgeAsync]**  
   L'app deve chiamare periodicamente questo metodo per ripulire l'archivio locale dai dati non aggiornati. Usare il parametro *force* quando è necessario ripulire tutte le modifiche non ancora sincronizzate.

Per altre informazioni su questi concetti, vedere [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure](app-service-mobile-offline-data-sync.md#how-sync-works).

## Altre informazioni

Gli argomenti seguenti forniscono altre informazioni in background sulla funzionalità di sincronizzazione offline delle app per dispositivi mobili:

* [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure]
* [Cloud Cover: Offline Sync in Azure Mobile Services] \(Cloud Cover: sincronizzazione offline nei servizi mobili di Azure). Si noti che il video è relativo ai servizi mobili, ma il funzionamento della sincronizzazione offline è simile nelle app per dispositivi mobili di Azure.
* [Azure Friday: App con supporto offline in Servizi mobili di Azure]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]: #next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure]: app-service-mobile-offline-data-sync.md
[create a windows app]: app-service-mobile-windows-store-dotnet-get-started.md
[Creare un'app Windows]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: http://go.microsoft.com/fwlink/?LinkID=716921
[synccontext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[IMobileServiceSyncTable]: https://msdn.microsoft.com/library/azure/mt691742(v=azure.10).aspx
[IMobileServiceTableQuery]: https://msdn.microsoft.com/library/azure/dn250631(v=azure.10).aspx
[IMobileServicesSyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynccontext(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushfailedexception(v=azure.10).aspx
[Status]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushcompletionresult.status(v=azure.10).aspx
[CancelledByNetworkError]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[Cloud Cover: Offline Sync in Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: App con supporto offline in Servizi mobili di Azure]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

<!---HONumber=AcomDC_0824_2016-->