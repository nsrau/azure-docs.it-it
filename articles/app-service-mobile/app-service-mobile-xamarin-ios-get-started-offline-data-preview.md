<properties
    pageTitle="Abilitare la sincronizzazione offline per l'app mobile (Xamarin iOS)"
    description="Informazioni su come usare le app per dispositivi mobili del servizio app per memorizzare nella cache e sincronizzare i dati offline in un'applicazione Xamarin iOS"
    documentationCenter="xamarin"
    authors="lindydonna"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
	ms.date="07/01/2015"
    ms.author="donnam"/>

# Abilitare la sincronizzazione offline per l'app mobile per Xamarin iOS

[AZURE.INCLUDE [app-service-mobile-selector-offline-preview](../../includes/app-service-mobile-selector-offline-preview.md)]

Questa esercitazione descrive la funzionalità di sincronizzazione offline delle app per dispositivi mobili per iOS. La sincronizzazione offline consente agli utenti finali di interagire con un'app, visualizzando, aggiungendo e modificando i dati, anche se non è disponibile una connessione di rete. Le modifiche vengono archiviate in un database locale. Quando il dispositivo torna online, vengono sincronizzate con il servizio remoto.

La sincronizzazione offline ha diversi usi potenziali:

* Migliorare la velocità di risposta dell'app memorizzando i dati del server nella cache locale del dispositivo
* Rendere le app resilienti rispetto alla connettività di rete intermittente
* Permettere agli utenti finali di creare e modificare i dati anche in mancanza di accesso di rete, supportando scenari con connettività scarsa o assente
* Sincronizzare i dati tra più dispositivi e rilevare i conflitti quando lo stesso record viene modificato da due dispositivi

Se questa è la prima esperienza con App per dispositivi mobili, è consigliabile completare prima l'esercitazione [Creare un'app Xamarin iOS].

Per completare questa esercitazione, è necessario disporre di:

* Visual Studio 2013
* [Estensione Xamarin] per Visual Studio **oppure** [Xamarin Studio] in OS X

## Esaminare il codice di sincronizzazione nell'app mobile

La sincronizzazione offline del servizio per app per dispositivi mobili consente agli utenti finali di interagire con un database locale quando la rete non è accessibile. Per usare queste funzionalità nell'app, inizializzare `MobileServiceClient.SyncContext` in un archivio locale. Quindi, fare riferimento alla tabella tramite l'interfaccia di `IMobileServiceSyncTable`. Questa sezione illustra il codice correlato alla sincronizzazione offline in `QSTodoService.cs`.

1. In Visual Studio aprire il progetto completato nell'esercitazione [Introduzione al servizio per app per dispositivi mobili]. Aprire il file `QSTodoService.cs`.

2. Notare che il tipo del membro `todoTable` è `IMobileServiceSyncTable`. La sincronizzazione offline usa questa interfaccia della tabella di sincronizzazione anziché `IMobileServiceTable`. Quando si usa una tabella di sincronizzazione, tutte le operazioni vengono inviate all'archivio locale e vengono sincronizzate con il servizio remoto solo mediante operazioni push e pull esplicite.

    Per ottenere un riferimento a una tabella di sincronizzazione, viene usato il metodo `GetSyncTable()`. Per rimuovere la funzionalità di sincronizzazione offline, usare invece `GetTable()`.

3. Prima di poter eseguire qualsiasi operazione su tabella, è necessario inizializzare l'archivio locale. Questa operazione viene effettuata nel metodo `InitializeStoreAsync`:

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }

    Verrà creato un archivio locale usando la classe `MobileServiceSQLiteStore`, disponibile in Mobile App SDK. È anche possibile fornire un'implementazione di archivio locale diversa, implementando `IMobileServiceLocalStore`.

    Il metodo `DefineTable` crea nell'archivio locale una tabella corrispondente ai campi del tipo specificato, `ToDoItem` in questo caso. Il tipo non deve includere tutte le colonne presenti nel database remoto, è possibile anche archiviare solo un subset di colonne.

<!--     This overload of `InitializeAsync` uses the default conflict handler, which fails whenever there is a conflict. To provide a custom conflict handler, see the tutorial [Handling conflicts with offline support for Mobile Services].
 -->
4. Il metodo `SyncAsync` attiva l'effettiva operazione di sincronizzazione:

        public async Task SyncAsync()
        {
            try
            {
                await client.SyncContext.PushAsync();
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery()); // query ID is used for incremental sync
            }

            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
        }

    Prima di tutto, viene eseguita una chiamata a `IMobileServiceSyncContext.PushAsync()`. Questo metodo fa parte di `IMobileServicesSyncContext` invece che della tabella di sincronizzazione perché effettuerà il push delle modifiche in tutte le tabelle. Solo i record che sono stati in qualche modo modificati localmente (tramite le operazioni CUD) verranno inviati al server.

    Successivamente, il metodo chiama `IMobileServiceSyncTable.PullAsync()` per eseguire il pull dei dati da una tabella del server all'app. Si noti che se nel contesto di sincronizzazione sono presenti modifiche in sospeso, un'operazione pull effettua sempre un'operazione push all'inizio. Lo scopo è assicurare che tutte le tabelle nell'archivio locale e le relazioni siano coerenti. In questo caso, è stata effettuata una chiamate esplicita a push.

    In questo esempio vengono recuperati tutti i record presenti nella tabella `TodoItem` remota, ma è anche possibile filtrare i record passando una query. Il primo parametro di `PullAsync()` è un ID di query usato per la sincronizzazione incrementale, che usa il timestamp `UpdatedAt` per ottenere i soli record modificati dopo l'ultima sincronizzazione. L'ID di query deve essere una stringa descrittiva univoca per ogni query logica presente nell'app. Per rifiutare esplicitamente la sincronizzazione incrementale, passare `null` come ID di query. In ogni operazione pull verranno recuperati tutti i record e questo potrebbe creare inefficienze.

<!--     >[AZURE.NOTE] To remove records from the device local store when they have been deleted in your mobile service database, you should enable [Soft Delete]. Otherwise, your app should periodically call `IMobileServiceSyncTable.PurgeAsync()` to purge the local store.

    Note that the `MobileServicePushFailedException` can occur for both a push and a pull operation. The next tutorial, [Handling conflicts with offline support for Mobile Services], shows how to handle these sync related exceptions.
-->

5. Nella classe `QSTodoService` il metodo `SyncAsync()` viene chiamato dopo le operazioni che modificano i dati, `InsertTodoItemAsync()` e `CompleteItemAsync`. Viene anche chiamato da `RefreshDataAsync()`, in modo che l'utente ottenga i dati più recenti ogni volta che esegue il movimento di aggiornamento. L'app esegue anche una sincronizzazione all'avvio, in quanto `QSTodoListViewController.ViewDidLoad()` chiama `RefreshDataAsync()`.

    Poiché ogni volta che vengono modificati i dati viene chiamato `SyncAsync()`, quest'app presuppone che l'utente sia online quando modifica i dati. Nella sezione successiva, l'app verrà aggiornata in modo che gli utenti possano apportare modifiche anche offline.

## Aggiornare il comportamento di sincronizzazione dell'app

In questa sezione si procederà alla modifica dell'app in modo che non effettui la sincronizzazione all'avvio né quando vengono eseguite operazioni di inserimento e aggiornamento, ma solo quando viene eseguito il movimento di aggiornamento. Successivamente, verrà interrotta la connessione dell'app al back-end mobile per simulare uno scenario offline. Quando si aggiungono elementi di dati, questi vengono conservati nell'archivio locale, ma non immediatamente sincronizzati con l'archivio dati del back-end mobile.

1. Aprire `QSTodoService.cs`. Impostare come commento le chiamate a `SyncAsync()` nei metodi seguenti:

    - `InsertTodoItemAsync`
    - `CompleteItemAsync`
    - `RefreshAsync`

    Ora, `RefreshAsync()` caricherà i dati solo dall'archivio locale, ma non effettuerà alcuna connessione al back-end dell'app.

2. In `QSTodoService.cs` modificare la definizione di `applicationURL` per puntare a un URI di app per dispositivi mobili non valido:

        const string applicationURL = @"https://your-service.azurewebsites.xxx/"; // invalid URI

3. Per assicurarsi che i dati siano sincronizzati quando viene eseguito il movimento di aggiornamento, modificare il metodo `QSTodoListViewController.RefreshAsync()`. Aggiungere una chiamata a `SyncAsync()` prima della chiamata a `RefreshDataAsync()`:

        private async Task RefreshAsync ()
        {
            RefreshControl.BeginRefreshing ();

            await todoService.SyncAsync();
            await todoService.RefreshDataAsync (); // add this line

            RefreshControl.EndRefreshing ();

            TableView.ReloadData ();
        }

4. Compilare ed eseguire l'app. Aggiungere nuovi elementi todo. I nuovi elementi esistono solo nell'archivio locale finché non è possibile effettuarne il push al back-end mobile. L'app client si comporta come se fosse connessa al back-end, supportando tutte le operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione).

5. Chiudere l'app e riavviarla per verificare che i nuovi elementi creati siano salvati in modo permanente nell'archivio locale.

## Aggiornare l'app per la riconnessione al back-end mobile

In questa sezione viene riconnessa l'app al back-end mobile, azione che consente di simulare il ritorno dell'app nello stato online. Quando si esegue il movimento di aggiornamento, i dati verranno sincronizzati con il back-end mobile.

1. Aprire `QSTodoService.cs`. Rimuovere l'URL dell'app mobile non valido e aggiungere nuovamente l'URL e la chiave dell'app corretti.

2. Ricompilare ed eseguire l'app. Si noti che i dati non sono cambiati, nonostante l'app sia ora connessa al back-end mobile. Il motivo è che l'app usa sempre l'interfaccia `IMobileServiceSyncTable` che fa riferimento all'archivio locale.

3. Connettere il back-end del database SQL per visualizzare i dati che sono stati archiviati. In Visual Studio passare a **Esplora server** -> **Azure** -> **Database SQL**. Fare clic con il pulsante destro del mouse sul database e scegliere **Apri in Esplora oggetti di SQL Server**.

    Si noti che i dati *non* sono stati sincronizzati tra il database e l'archivio locale.

4. Nell'app eseguire il movimento di aggiornamento spostando verso il basso l'elenco di elementi. L'app effettuerà una chiamata a`RefreshDataAsync()`, che ha sua volta effettua una chiamata a `SyncAsync()`. Verranno eseguite le operazioni push e pull, prima inviando gli elementi dell'archivio locale al back-end mobile, quindi recuperando i nuovi dati dal back-end.

5. Aggiornare la vista di database e verificare che le modifiche siano state sincronizzate.

<!-- ##Summary

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../../includes/mobile-services-offline-summary-csharp.md)]

## Next steps

* [Handling conflicts with offline support for Mobile Services]

* [How to use the Xamarin Component client for Azure Mobile Services]
 -->

<!-- Images -->

<!-- URLs. -->
[Creare un'app Xamarin iOS]: ../app-service-mobile-dotnet-backend-xamarin-ios-get-started.md

[How to use the Xamarin Component client for Azure Mobile Services]: ../partner-xamarin-mobile-services-how-to-use-client-library.md

[Xamarin Studio]: http://xamarin.com/download
[Estensione Xamarin]: http://xamarin.com/visual-studio
 

<!---HONumber=July15_HO3-->