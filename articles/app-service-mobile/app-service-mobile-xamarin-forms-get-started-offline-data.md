---
title: Abilitare la sincronizzazione offline per l&quot;app per dispositivi mobili di Azure (Xamarin.Forms) | Documentazione Microsoft
description: Informazioni su come usare le app per dispositivi mobili del servizio app per memorizzare nella cache e sincronizzare i dati offline in un&quot;applicazione Xamarin.Forms
documentationcenter: xamarin
author: adrianhall
manager: yochayk
editor: 
services: app-service\mobile
ms.assetid: acf0f874-3ea5-4410-bd22-b0e72140f3b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/04/2016
ms.author: adrianha
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 62610da9598077ffd17b97e9427149310c639a1a


---
# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Abilitare la sincronizzazione offline per l'app per dispositivi mobili Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Panoramica
Questa esercitazione descrive la funzionalità di sincronizzazione offline di App per dispositivi mobili di Azure per Xamarin.Forms. La sincronizzazione offline consente agli utenti finali di interagire con un'app, visualizzando, aggiungendo e modificando i dati, anche se non è disponibile una connessione di rete. Le modifiche vengono archiviate in un database locale. Quando il dispositivo torna online, vengono sincronizzate con il servizio remoto.

Questa esercitazione si basa sulla soluzione di avvio rapido per Xamarin.Forms per le app per dispositivi mobili create quando si completa l'esercitazione [Creare un'app per Xamarin.iOS]. La soluzione di avvio rapido per Xamarin.Forms contiene il codice per supportare la sincronizzazione offline, che deve solo essere abilitata. In questa esercitazione viene aggiornata la soluzione di avvio rapido per attivare le funzionalità offline delle app per dispositivi mobili. Viene anche evidenziato il codice specifico per le funzionalità offline nell'app. Se non si usa la soluzione di avvio rapido scaricata, è necessario aggiungere al progetto il pacchetto di estensione per l'accesso ai dati. Per altre informazioni sui pacchetti di estensione server, vedere [Usare l'SDK del server back-end .NET per App per dispositivi mobili di Azure][1].

Per altre informazioni sulla funzionalità di sincronizzazione offline, vedere l'argomento [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure][2].

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>Abilitare la funzionalità di sincronizzazione offline nella soluzione di avvio rapido
Il codice per la sincronizzazione offline viene incluso nel progetto usando le direttive del preprocessore C#. Quando il simbolo **OFFLINE\_SYNC\_ENABLED** viene definito, questi percorsi di codice vengono inclusi nella build. Per le app di Windows, è anche necessario installare la piattaforma SQLite.

1. In Visual Studio fare clic con il pulsante destro del mouse sulla soluzione > **Gestisci pacchetti NuGet per la soluzione**, quindi cercare e installare il pacchetto NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** per tutti i progetti della soluzione.
2. In Esplora soluzioni aprire il file TodoItemManager.cs dal progetto con **portabile** nel nome, ovvero il progetto Libreria di classi portabile, quindi rimuovere il commento dalla direttiva del preprocessore seguente:
   
        #define OFFLINE_SYNC_ENABLED
3. (Facoltativo) Per supportare i dispositivi Windows, installare uno dei pacchetti di runtime SQLite seguenti:
   
   * **Windows 8.1 Runtime:** installare [SQLite per Windows 8.1][3].
   * **Windows Phone 8.1:** installare [SQLite per Windows Phone 8.1][4].
   * **Piattaforma UWP (Universal Windows Platform)** Installare [SQLite per Universal Windows Universal][5].
     
     Anche se la Guida introduttiva non contiene un Windows universale, la piattaforma UWP (Universal Windows Platform) è supportata con Xamarin Forms.
4. (Facoltativo) In ogni progetto app Windows fare clic con il pulsante destro del mouse su **Riferimenti** > **Aggiungi riferimento**, espandere la cartella **Windows** > **Estensioni**.
    Abilitare **SQLite for Windows** SDK appropriato insieme a **Visual C++ 2013 Runtime for Windows** SDK.
    I nomi degli SDK di SQLite sono leggermente diversi per ogni piattaforma Windows.

## <a name="review-the-client-sync-code"></a>Verificare il codice di sincronizzazione del client
Ecco un breve riepilogo degli elementi già inclusi nel codice dell'esercitazione nelle direttive `#if OFFLINE_SYNC_ENABLED` . La funzionalità di sincronizzazione offline è nel file di progetto TodoItemManager.cs nel progetto Libreria di classi portabile. Per una panoramica concettuale della funzionalità, vedere [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure][2].

* Prima di poter eseguire qualsiasi operazione su tabella, è necessario inizializzare l'archivio locale. Il database di archiviazione locale viene inizializzato nel costruttore della classe **TodoItemManager** usando il codice seguente:
  
        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();
  
        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);
  
        this.todoTable = client.GetSyncTable<TodoItem>();
  
    Questo crea un nuovo database SQL locale usando la classe **MobileServiceSQLiteStore**.
  
    Il metodo **DefineTable** crea nell'archivio locale una tabella corrispondente ai campi del tipo specificato.  Il tipo non deve necessariamente includere tutte le colonne presenti nel database remoto. È possibile archiviare un subset di colonne.
* Il campo **todoTable** in **TodoItemManager** è un tipo **IMobileServiceSyncTable** invece di **IMobileServiceTable**. Questa classe usa il database locale per tutte le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD, Create, Read, Update, Delete) sulle tabelle. È possibile decidere quando effettuare il push delle modifiche nel back-end dell'app per dispositivi mobili chiamando **PushAsync** in **IMobileServiceSyncContext**. Questo contesto di sincronizzazione aiuta a mantenere le relazioni tra tabelle rilevando le modifiche apportate da un'app client in tutte le tabelle ed effettuandone il push quando viene chiamato **PushAsync**.
  
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

## <a name="offline-sync-considerations"></a>Considerazioni sulla sincronizzazione offline
Nell'esempio il metodo **SyncAsync** viene chiamato solo all'avvio e quando viene richiesta una sincronizzazione in modo specifico.  Per avviare la sincronizzazione in un'app Android o iOS, trascinare verso il basso nell'elenco di elementi. Per Windows, usare il pulsante **Sincronizza**. In un'applicazione effettiva è anche possibile attivare la sincronizzazione in caso di cambiamento dello stato della rete.

Quando viene effettuato il pull in una tabella con aggiornamenti locali in sospeso rilevati dal contesto, tale operazione attiva automaticamente un'operazione push nel contesto precedente. Quando si aggiornano, aggiungono e completano elementi in questo esempio, è possibile omettere la chiamata **PushAsync** esplicita.

Nel codice fornito viene eseguita una query su tutti i record presenti nella tabella TodoItem remota, ma è anche possibile filtrare i record passando un ID di query e una query a **PushAsync**. Per altre informazioni, vedere la sezione *Sincronizzazione incrementale* in [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure][2].

## <a name="run-the-client-app"></a>Eseguire l'app client
Con la sincronizzazione offline abilitata, è possibile eseguire l'applicazione client almeno una volta in ogni piattaforma per popolare il database di archiviazione locale. Più avanti viene simulato uno scenario offline e vengono modificati i dati nell'archivio locale mentre l'app è offline.

## <a name="update-the-sync-behavior-of-the-client-app"></a>Aggiornare il comportamento di sincronizzazione dell'app client
In questa sezione viene modificato il progetto client per simulare uno scenario offline usando un URL di applicazione non valido per il back-end. In alternativa, è possibile disattivare le connessioni di rete attivando la "Modalità aereo" nel dispositivo.  Quando si aggiungono o si modificano elementi di dati, queste modifiche vengono conservate nell'archivio locale, ma non vengono sincronizzate con l'archivio dati back-end fino a quando non viene ristabilita la connessione.

1. In Esplora soluzioni aprire il file di progetto Constants.cs dal progetto **Portatile** e modificare il valore di `ApplicationURL` in modo che punti a un URL non valido:
   
        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";
2. Aprire il file TodoItemManager.cs dal progetto **Portable**, quindi aggiungere un blocco **catch** aggiuntivo per la classe **Exception** di base al blocco **try...catch** in **SyncAsync**. Questo blocco **catch** scrive il messaggio dell'eccezione nella console, come segue:
   
            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
3. Compilare ed eseguire l'app client.  Aggiungere nuovi elementi. Si noti che un'eccezione viene registrata nella console per ogni tentativo di sincronizzazione con il back-end. I nuovi elementi sono presenti solo nell'archivio locale finché non è possibile effettuarne il push al back-end mobile. L'app client si comporta come se fosse connessa al back-end, supportando tutte le operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione).
4. Chiudere l'app e riavviarla per verificare che i nuovi elementi creati siano salvati in modo permanente nell'archivio locale.
5. (Facoltativo) Usare Visual Studio per visualizzare la tabella di database SQL di Azure per verificare che i dati nel database back-end non siano cambiati.
   
    In Visual Studio aprire **Esplora server**. Passare al database in **Azure**->**Database SQL**. Fare clic con il pulsante destro del mouse sul database e scegliere **Apri in Esplora oggetti di SQL Server**. È ora possibile passare alla tabella di database SQL e al relativo contenuto.

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>Aggiornare l'app client per la riconnessione al back-end mobile
In questa sezione viene riconnessa l'app al back-end mobile, azione che consente di simulare il ritorno dell'app nello stato online. Quando si esegue il movimento di aggiornamento, i dati vengono sincronizzati con il back-end mobile.

1. Riaprire il file Constants.cs. Correggere il valore `applicationURL` in modo che faccia riferimento all'URL corretto.
2. Ricompilare ed eseguire l'app client. L'app prova a eseguire la sincronizzazione con il back-end dell'app per dispositivi mobili dopo l'avvio. Verificare che non vengano registrate eccezioni nella console di debug.
3. (Facoltativo) Visualizzare i dati aggiornati usando Esplora oggetti di SQL Server o uno strumento REST come Fiddler o [Postman][6]. Si noti che i dati sono stati sincronizzati tra il database di back-end e l'archivio locale.
   
    Si noti che i dati sono stati sincronizzati tra il database e l'archivio locale e includono gli elementi aggiunti mentre l'app era disconnessa.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure][2]
* [Procedura per Azure Mobile Apps .NET SDK][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: http://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: http://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: http://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: http://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md



<!--HONumber=Nov16_HO3-->


