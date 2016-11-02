<properties
    pageTitle="Abilitare la sincronizzazione offline per l'app per dispositivi mobili di Azure (Xamarin.Forms) | Microsoft Azure"
    description="Informazioni su come usare le app per dispositivi mobili del servizio app per memorizzare nella cache e sincronizzare i dati offline in un'applicazione Xamarin.Forms"
    documentationCenter="xamarin"
    authors="ggailey777"
    manager="erikre"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
	ms.date="06/18/2016"
    ms.author="glenga"/>

# Abilitare la sincronizzazione offline per l'app per dispositivi mobili Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## Panoramica
Questa esercitazione descrive la funzionalità di sincronizzazione offline di App per dispositivi mobili di Azure per Xamarin.Forms. La sincronizzazione offline consente agli utenti finali di interagire con un'app, visualizzando, aggiungendo e modificando i dati, anche se non è disponibile una connessione di rete. Le modifiche vengono archiviate in un database locale. Quando il dispositivo torna online, vengono sincronizzate con il servizio remoto.

Questa esercitazione si basa sulla soluzione di avvio rapido per Xamarin.Forms per le app per dispositivi mobili create quando si completa l'esercitazione [Creare un'app per Xamarin.iOS]. La soluzione di avvio rapido per Xamarin.Forms contiene il codice per supportare la sincronizzazione offline, che deve solo essere abilitata. In questa esercitazione si aggiornerà la soluzione di avvio rapido per attivare le funzionalità offline delle app per dispositivi mobili. Verrà anche evidenziato il codice specifico per le funzionalità offline nell'app. Se non si usa la soluzione di avvio rapido scaricata, è necessario aggiungere al progetto il pacchetto di estensione per l'accesso ai dati. Per altre informazioni sui pacchetti di estensione server, vedere l'articolo relativo all'[utilizzo dell'SDK del server back-end .NET per app per dispositivi mobili di Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Per altre informazioni sulla funzionalità di sincronizzazione offline, vedere l'argomento [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure].

## Abilitare la funzionalità di sincronizzazione offline nella soluzione di avvio rapido

Il codice per la sincronizzazione offline viene incluso nel progetto usando le direttive del preprocessore C#. Quando il simbolo **OFFLINE\_SYNC\_ENABLED** viene definito, questi percorsi di codice vengono inclusi nella build. Per le app di Windows, è anche necessario installare la piattaforma SQLite.

1. In Visual Studio fare clic con il pulsante destro del mouse sulla soluzione > **Gestisci pacchetti NuGet per la soluzione**, quindi cercare e installare il pacchetto NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** per tutti i progetti della soluzione.

2. In Esplora soluzioni aprire il file TodoItemManager.cs dal progetto con **portabile** nel nome, ovvero il progetto Libreria di classi portabile, quindi rimuovere il commento dalla direttiva del preprocessore seguente:

		#define OFFLINE_SYNC_ENABLED

3. In Esplora soluzioni aprire il file TodoList.xaml.cs dal progetto **Portatile**, trovare il metodo **OnAppearing** e verificare che venga passato `true` per *syncItems* quando si chiama **RefreshItems**, come segue:

		await RefreshItems(true, syncItems: true);
	Ciò significa che l'app cercherà di eseguire la sincronizzazione con il back-end all'avvio.
 
4. (Facoltativo) Se si supportano dispositivi iOS, aprire il file AppDelegate.cs dal progetto **iOS** e rimuovere il commento dalla riga seguente di codice nel metodo **FinishedLaunching**:

		SQLitePCL.CurrentPlatform.Init();

	Questo inizializza l'archivio SQLite nei dispositivi iOS. Le attività rimanenti sono necessarie solo per supportare i dispositivi Windows.

5. (Facoltativo) Per supportare i dispositivi Windows, installare uno dei pacchetti di runtime SQLite seguenti:

	* **Windows 8.1 Runtime:** installare [SQLite per Windows 8.1](http://go.microsoft.com/fwlink/p/?LinkID=716919).
    * **Windows Phone 8.1:** installare [SQLite per Windows Phone 8.1](http://go.microsoft.com/fwlink/p/?LinkID=716920).
    * **Piattaforma UWP (Universal Windows Platform)** Installare [SQLite per la piattaforma UWP (Universal Windows Platform)](http://sqlite.org/2016/sqlite-uwp-3120200.vsix). La soluzione di avvio rapido attualmente non include un progetto per la piattaforma UWP (Universal Windows Platform).

6. (Facoltativo) In ogni progetto di app di Windows fare clic con il pulsante destro del mouse su **Riferimenti** > **Aggiungi riferimento**, espandere la cartella **Windows** > **Estensioni**, quindi abilitare **SQLite for Windows Runtime** SDK con **Visual C++ 2013 Runtime for Windows** SDK. Si noti che i nomi degli SDK di SQLite sono leggermente diversi per ogni piattaforma Windows Per un progetto UWP, installare **Visual C++ 2015 Runtime for Universal Windows Platform apps** SDK.


## Verificare il codice di sincronizzazione del client

Ecco un breve riepilogo degli elementi già inclusi nel codice dell'esercitazione nelle direttive `#if OFFLINE_SYNC_ENABLED`. Si noti che la funzionalità di sincronizzazione offline è nel file di progetto TodoItemManager.cs nel progetto Libreria di classi portabile. Per una panoramica concettuale della funzionalità, vedere [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure].

* Prima di poter eseguire qualsiasi operazione su tabella, è necessario inizializzare l'archivio locale. Il database di archiviazione locale viene inizializzato nel costruttore della classe **TodoItemManager** usando il codice seguente:

	    var store = new MobileServiceSQLiteStore("localstore.db");
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

	Questo crea un nuovo database SQL locale usando la classe **MobileServiceSQLiteStore**. Il metodo **DefineTable** crea nell'archivio locale una tabella corrispondente ai campi del tipo specificato, `ToDoItem` in questo caso. Il tipo non deve necessariamente includere tutte le colonne presenti nel database remoto. È possibile archiviare solo un subset di colonne.

* Il campo **todoTable** in **TodoItemManager** è un tipo **IMobileServiceSyncTable** invece che **IMobileServiceTable**. Questa classe usa il database locale per tutte le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD, Create, Read, Update, Delete) sulle tabelle. È possibile decidere quando effettuare il push delle modifiche nel back-end dell'app per dispositivi mobili chiamando **PushAsync** sull'oggetto **IMobileServiceSyncContext** usato dal client. Questo contesto di sincronizzazione aiuta a mantenere le relazioni tra tabelle rilevando le modifiche apportate da un'app client in tutte le tabelle ed effettuandone il push quando viene chiamato **PushAsync**.

	Il metodo **SyncAsync** seguente viene chiamato per la sincronizzazione con il back-end dell'app per dispositivi mobili:

		public async Task SyncAsync()
        {
            ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

            try
            {
                await this.client.SyncContext.PushAsync();

                await this.todoTable.PullAsync(
                    "allTodoItems",
                    this.todoTable.CreateQuery());
            }
            catch (MobileServicePushFailedException exc)
            {
                if (exc.PushResult != null)
                {
                    syncErrors = exc.PushResult.Errors;
                }
            }

            // Simple error/conflict handling. 
            if (syncErrors != null)
            {
                foreach (var error in syncErrors)
                {
                    if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
                    {
                        //Update failed, reverting to server's copy.
                        await error.CancelAndUpdateItemAsync(error.Result);
                    }
                    else
                    {
                        // Discard local change.
                        await error.CancelAndDiscardItemAsync();
                    }

                    Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.",
						error.TableName, error.Item["id"]);
                }
            }
        }

	Questo esempio usa la gestione degli errori semplice con il gestore di sincronizzazione predefinito. Una vera applicazione gestirà i diversi errori come condizioni di rete, conflitti del server e altro usando un'implementazione **IMobileServiceSyncHandler** personalizzata.

##Considerazioni sulla sincronizzazione offline

Nell'esempio il metodo **SyncAsync** viene chiamato solo all'avvio e quando viene richiesta una sincronizzazione in modo specifico. Per avviare la sincronizzazione in un'app Android o iOS, trascinare verso il basso nell'elenco di elementi. Per Windows, usare il pulsante **Sincronizza**. In un'applicazione vera e propria è anche possibile attivare questa funzionalità di sincronizzazione quando lo stato della rete cambia.

Quando viene effettuato il pull in una tabella con aggiornamenti locali in sospeso rilevati dal contesto, tale operazione attiva automaticamente un'operazione push nel contesto precedente. Quando si aggiornano, aggiungono e completano elementi in questo esempio, è possibile omettere la chiamata **PushAsync** esplicita, perché è ridondante.

Nel codice fornito viene eseguita una query su tutti i record presenti nella tabella TodoItem remota, ma è anche possibile filtrare i record passando un ID di query e una query a **PushAsync**. Per altre informazioni, vedere la sezione *Sincronizzazione incrementale* in [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure].


## Eseguire l'app client

Con la sincronizzazione offline abilitata, ora è possibile eseguire l'applicazione client almeno una volta in ogni piattaforma per popolare il database di archiviazione locale. Più avanti verrà simulato uno scenario offline e verranno modificati i dati nell'archivio locale mentre l'app è offline.

## Aggiornare il comportamento di sincronizzazione dell'app client

In questa sezione si modificherà il progetto client per simulare uno scenario offline usando un URL di applicazione non valido per il back-end. Quando si aggiungono o si modificano elementi di dati, queste modifiche vengono conservate nell'archivio locale, ma non vengono sincronizzate con l'archivio dati back-end fino a quando non viene ristabilita la connessione.

2. In Esplora soluzioni aprire il file di progetto Constants.cs dal progetto **Portatile** e modificare il valore di `ApplicationURL` in modo che punti a un URL non valido, come di seguito:

        publis static string ApplicationURL = @"https://your-service.azurewebsites.xxx/";


2. Aprire il file TodoItemManager.cs dal progetto **Portatile**, quindi aggiungere un blocco **catch** aggiuntivo per la classe **Exception** di base al blocco **try...catch** in **SyncAsync**. Questo blocco **catch** scrive il messaggio dell'eccezione nella console, come segue:

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }

3. Compilare ed eseguire l'app client, aggiungere alcuni nuovi elementi e notare che un'eccezione viene registrata nella console per ogni tentativo di sincronizzazione con il back-end. I nuovi elementi sono presenti solo nell'archivio locale finché non è possibile effettuarne il push al back-end mobile. L'app client si comporta come se fosse connessa al back-end, supportando tutte le operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione).

4. Chiudere l'app e riavviarla per verificare che i nuovi elementi creati siano salvati in modo permanente nell'archivio locale.

5. (Facoltativo) Usare Visual Studio per visualizzare la tabella di database SQL di Azure per verificare che i dati nel database back-end non siano cambiati.

	In Visual Studio aprire **Esplora server**. Passare al database in **Azure**->**Database SQL**. Fare clic con il pulsante destro del mouse sul database e scegliere **Apri in Esplora oggetti di SQL Server**. È ora possibile passare alla tabella di database SQL e al relativo contenuto.


## Aggiornare l'app client per la riconnessione al back-end mobile

In questa sezione viene riconnessa l'app al back-end mobile, azione che consente di simulare il ritorno dell'app nello stato online. Quando si esegue il movimento di aggiornamento, i dati verranno sincronizzati con il back-end mobile.

1. Riaprire Constants.cs, quindi correggere `applicationURL` in modo che punti all'URL corretto.

2. Ricompilare ed eseguire l'app client. L'app prova a eseguire la sincronizzazione con il back-end dell'app per dispositivi mobili dopo l'avvio. Verificare che non vengano registrate eccezioni nella console di debug.

3. (Facoltativo) Visualizzare i dati aggiornati usando Esplora oggetti di SQL Server o uno strumento REST come Fiddler. Si noti che i dati sono stati sincronizzati tra il database di back-end e l'archivio locale.

    Si noti che i dati sono stati sincronizzati tra il database e l'archivio locale e includono gli elementi aggiunti mentre l'app era disconnessa.

## Risorse aggiuntive

* [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure]

* [Cloud Cover: sincronizzazione offline in Servizi mobili di Azure] \(nota: il video è relativo ai Servizi mobili, ma il funzionamento della sincronizzazione offline è simile nelle app per dispositivi mobili di Azure)

<!-- ##Summary

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../../includes/mobile-services-offline-summary-csharp.md)]

## Next steps

* [Handling conflicts with offline support for Mobile Services]

* [How to use the Xamarin Component client for Azure Mobile Services]
 -->

<!-- Images -->

<!-- URLs. -->
[Creare un'app per Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md
[Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure]: app-service-mobile-offline-data-sync.md
[How to use the Xamarin Component client for Azure Mobile Services]: partner-xamarin-mobile-services-how-to-use-client-library.md
[Cloud Cover: sincronizzazione offline in Servizi mobili di Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri

<!---HONumber=AcomDC_0629_2016-->