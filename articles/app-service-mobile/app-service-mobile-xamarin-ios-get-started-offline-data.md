---
title: Abilitare la sincronizzazione offline per l'app per dispositivi mobili di Azure (Xamarin iOS)
description: Informazioni su come usare le app per dispositivi mobili del servizio app per memorizzare nella cache e sincronizzare i dati offline in un'applicazione Xamarin iOS
documentationcenter: xamarin
author: ggailey777
manager: cfowler
editor: 
services: app-service\mobile
ms.assetid: 828a287c-5d58-4540-9527-1309ebb0f32b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 06e16033435ed0a37d5688055743875827d3aec2
ms.openlocfilehash: d609231d6d9913b0f40b6e311aeedeb9a2391c7c
ms.contentlocale: it-it
ms.lasthandoff: 03/01/2017

---
# <a name="enable-offline-sync-for-your-xamarinios-mobile-app"></a>Abilitare la sincronizzazione offline per l'app per dispositivi mobili Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Panoramica
Questa esercitazione descrive la funzionalità di sincronizzazione offline di App per dispositivi mobili di Azure per Xamarin.iOS. La sincronizzazione offline consente agli utenti finali di interagire con un'app, visualizzando, aggiungendo e modificando i dati, anche se non è disponibile una connessione di rete. Le modifiche vengono archiviate in un database locale. Quando il dispositivo torna online, vengono sincronizzate con il servizio remoto.

In questa esercitazione viene aggiornato il progetto app Xamarin.iOS dell'esercitazione [Creare un'app Xamarin iOS] per supportare le funzionalità offline delle app per dispositivi mobili di Azure. Se non si usa il progetto server di avvio rapido scaricato, è necessario aggiungere al progetto il pacchetto di estensione per l'accesso ai dati. Per altre informazioni sui pacchetti di estensione server, vedere l'articolo relativo all' [utilizzo dell'SDK del server back-end .NET per app per dispositivi mobili di Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Per altre informazioni sulla funzionalità di sincronizzazione offline, vedere l'argomento [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure].

## <a name="update-the-client-app-to-support-offline-features"></a>Aggiornare l'app client per supportare le funzionalità offline
Le funzionalità offline delle app per dispositivi mobili di Azure consentono di interagire con un database locale in uno scenario offline. Per usare queste funzionalità nell'app, inizializzare [SyncContext] in un archivio locale. Fare riferimento alla tabella tramite l'interfaccia di [IMobileServiceSyncTable]. SQLite viene usato come archivio locale nel dispositivo.

1. Aprire Gestione pacchetti NuGet nel progetto completato nell'esercitazione [Creare un'app Xamarin iOS], quindi cercare e installare il pacchetto NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore**.
2. Aprire il file QSTodoService.cs e rimuovere i simboli di commento dalla definizione `#define OFFLINE_SYNC_ENABLED`.
3. Ricompilare ed eseguire l'app client. L'app funziona esattamente come prima di attivare la sincronizzazione offline. Tuttavia, il database locale è ora popolato con dati che possono essere usati in uno scenario offline.

## <a name="update-sync"></a>Aggiornare l'app per disconnetterla dal back-end
In questa sezione verrà interrotta la connessione al back-end dell'app per dispositivi mobili per simulare una situazione offline. Quando si aggiungono elementi di dati, il gestore di eccezioni informa che l'app è in modalità offline. In questo stato, nuovi elementi verranno aggiunti all'archivio locale che verranno sincronizzati con il back-end dell'app per dispositivi mobili la prima volta che verrà eseguita un'operazione di push in presenza di uno stato di connessione.

1. Modificare il file QSToDoService.cs nel progetto condiviso. Modificare il valore **applicationURL** in modo che faccia riferimento a un URL non valido:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    È anche possibile illustrare il comportamento offline disabilitando le reti Wi-Fi e cellulare sul dispositivo o impostando la modalità aereo.
2. Compilare ed eseguire l'app. Si noti l'errore di sincronizzazione al momento dell'aggiornamento quando l'app viene avviata.
3. Immettere nuovi elementi. Si noti che il push ha esito negativo con stato [CancelledByNetworkError] ogni volta che si fa clic su **Salva**. I nuovi elementi todo, tuttavia, sono presenti nell'archivio locale fino a quando non è possibile effettuarne il push al back-end dell'app per dispositivi mobili.  In un'app di produzione, se si eliminano queste eccezioni, l'app client si comporta come se fosse ancora connessa al back-end dell'app per dispositivi mobili.
4. Chiudere l'app e riavviarla per verificare che i nuovi elementi creati siano salvati in modo permanente nell'archivio locale.
5. (Facoltativo) Se Visual Studio è installato in un computer, aprire **Esplora server**. Passare al database in **Azure**-> **Database SQL**. Fare clic con il pulsante destro del mouse sul database e scegliere **Apri in Esplora oggetti di SQL Server**. È ora possibile passare alla tabella di database SQL e al relativo contenuto. Verificare che i dati nel database back-end non siano stati modificati.
6. (Facoltativo) Usare uno strumento REST come Fiddler o Postman per eseguire una query sul back-end mobile, usando una query GET nel formato `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Aggiornare l'app per la riconnessione al back-end dell'app per dispositivi mobili
In questa sezione viene effettuata la riconnessione dell'app al back-end dell'app per dispositivi mobili. Viene simulato il passaggio dell'app dallo stato offline allo stato online con il back-end dell'app per dispositivi mobili.   Se l'interruzione della rete è stata simulata disattivando la connettività di rete, non sono necessarie modifiche al codice.
Riattivare la rete.  Alla prima esecuzione dell'applicazione viene chiamato il metodo `RefreshDataAsync`, che sua volta chiama `SyncAsync` per sincronizzare l'archivio locale con il database back-end.

1. Aprire il file QSToDoService.cs nel progetto condiviso e annullare la modifica della proprietà **applicationURL**.
2. Ricompilare ed eseguire l'app. L'app sincronizza le modifiche locali con il back-end dell'app per dispositivi mobili di Azure usando operazioni push e pull quando viene eseguito il metodo `OnRefreshItemsSelected`.
3. (Facoltativo) Visualizzare i dati aggiornati usando Esplora oggetti di SQL Server o uno strumento REST come Fiddler. Si noti che i dati sono stati sincronizzati tra il database back-end dell'app per dispositivi mobili di Azure e l'archivio locale.
4. Nell'app fare clic sulla casella di controllo accanto ad alcuni elementi da completare nell'archivio locale.

   `CompleteItemAsync` chiama `SyncAsync` per sincronizzare ogni elemento completato con il back-end dell'app per dispositivi mobili. `SyncAsync` chiama operazioni sia push che pull.
   **Ogni volta che si esegue un'operazione pull in una tabella in cui il client ha apportato modifiche, viene sempre eseguita prima un'operazione push automatica nel contesto di sincronizzazione del client**. Il push implicito assicura che tutte le tabelle nell'archivio locale e le relazioni restino coerenti. Per altre informazioni su questo comportamento, vedere [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure].

## <a name="review-the-client-sync-code"></a>Verificare il codice di sincronizzazione del client
Il progetto client Xamarin scaricato dopo aver completato l'esercitazione [Creare un'app Xamarin iOS] contiene già il codice che supporta la sincronizzazione offline mediante un database SQLite locale. Ecco un breve riepilogo degli elementi già inclusi nel codice dell'esercitazione. Per una panoramica concettuale della funzionalità, vedere [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure].

* Prima di poter eseguire qualsiasi operazione su tabella, è necessario inizializzare l'archivio locale. Il database di archiviazione locale viene inizializzato quando `QSTodoListViewController.ViewDidLoad()` esegue `QSTodoService.InitializeStoreAsync()`. Questo metodo comporta la creazione di un nuovo database SQLite locale usando la classe `MobileServiceSQLiteStore` fornita dall'SDK del client dell'app per dispositivi mobili di Azure.

    Il metodo `DefineTable` crea nell'archivio locale una tabella corrispondente ai campi del tipo specificato, `ToDoItem` in questo caso. Il tipo non deve necessariamente includere tutte le colonne presenti nel database remoto. È possibile archiviare solo un subset di colonne.

        // QSTodoService.cs

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }
* Il membro `todoTable` di `QSTodoService` è di tipo `IMobileServiceSyncTable` invece che `IMobileServiceTable`. IMobileServiceSyncTable indirizza tutte le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD, Create, Read, Update, Delete) sulle tabelle al database di archiviazione locale.

    Decidere quando viene eseguito il push delle modifiche al back-end delle app per dispositivi mobili di Azure chiamando `IMobileServiceSyncContext.PushAsync()`. Il contesto di sincronizzazione aiuta a mantenere le relazioni tra tabelle rilevando le modifiche apportate da un'app client in tutte le tabelle ed eseguendone il push quando viene chiamato `PushAsync` .

    Il codice fornito chiama `QSTodoService.SyncAsync()` per eseguire la sincronizzazione ogni volta che l'elenco todoitem viene aggiornato o un oggetto todoitem viene aggiunto o completato. L'app viene sincronizzata dopo ogni modifica locale. Se viene effettuato il pull in una tabella con aggiornamenti locali in sospeso rilevati dal contesto, tale operazione attiva prima di tutto automaticamente un'operazione push nel contesto.

    Nel codice fornito viene eseguita una query su tutti i record presenti nella tabella `TodoItem` remota, ma è anche possibile filtrare i record passando un ID di query e una query a `PushAsync`. Per altre informazioni, vedere la sezione *Sincronizzazione incrementale* in [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure].

        // QSTodoService.cs
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

## <a name="additional-resources"></a>Risorse aggiuntive
* [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure]
* [Procedura per .NET SDK di App per dispositivi mobili di Azure][8]

<!-- Images -->

<!-- URLs. -->
[Creare un'app Xamarin iOS]: app-service-mobile-xamarin-ios-get-started.md
[Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure]: app-service-mobile-offline-data-sync.md
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md

