---
title: Abilitare la sincronizzazione offline per l'app per dispositivi mobili di Azure (Cordova) | Documentazione Microsoft
description: Informazioni su come usare le app mobili del servizio app per memorizzare nella cache e sincronizzare i dati offline in un'applicazione Cordova
documentationcenter: cordova
author: conceptdev
manager: crdun
editor: 
services: app-service\mobile
ms.assetid: 1a3f685d-f79d-4f8b-ae11-ff96e79e9de9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-cordova-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: c12328a441a8cc438fa3e974863cc8adf8651b50
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2018
---
# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>Abilitare la sincronizzazione offline per l'app per dispositivi mobili Cordova
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

Questa esercitazione descrive la funzionalità di sincronizzazione offline delle app per dispositivi mobili di Azure per Cordova. La sincronizzazione offline consente agli utenti finali di interagire con un'app&mdash;visualizzando, aggiungendo e modificando i dati&mdash;anche se non è disponibile una connessione di rete. Le modifiche vengono archiviate in un database locale.  Quando il dispositivo torna online, vengono sincronizzate con il servizio remoto.

Questa esercitazione si basa sulla soluzione di avvio rapido Cordova per le app per dispositivi mobili create quando si completa l'esercitazione sull' [avvio rapido di Apache Cordova]. In questa esercitazione viene aggiornata la soluzione di avvio rapido per aggiungere le funzionalità offline delle app per dispositivi mobili di Azure.  Viene anche evidenziato il codice specifico per le funzionalità offline nell'app.

Per altre informazioni sulla funzionalità di sincronizzazione offline, vedere l'argomento [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure]. Per informazioni dettagliate sull'utilizzo delle API, vedere la [documentazione per le API](https://azure.github.io/azure-mobile-apps-js-client).

## <a name="add-offline-sync-to-the-quickstart-solution"></a>Aggiungere la sincronizzazione offline alla soluzione di avvio rapido
È necessario aggiungere dall'app il codice di sincronizzazione offline. La sincronizzazione offline richiede il plug-in cordova-sqlite-storage, che viene aggiunto automaticamente all'app quando il plug-in App per dispositivi mobili di Azure è incluso nel progetto. Il progetto di avvio rapido include entrambi questi plug-in.

1. In Esplora soluzioni di Visual Studio,aprire index.js e sostituire il codice seguente

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable;      // Reference to a table endpoint on backend

    con questo codice:

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable,      // Reference to a table endpoint on backend
           syncContext;        // Reference to offline data sync context

2. Sostituire quindi il codice seguente:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

    con questo codice:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');
        var store = new WindowsAzure.MobileServiceSqliteStore('store.db');

        store.defineTable({
          name: 'todoitem',
          columnDefinitions: {
              id: 'string',
              text: 'string',
              complete: 'boolean',
              version: 'string'
          }
        });

        // Get the sync context from the client
        syncContext = client.getSyncContext();

    Le aggiunte di codice precedenti inizializzano l'archivio locale e definiscono una tabella locale che corrisponde ai valori di colonna usati nel back-end di Azure. Non è necessario includere tutti i valori delle colonne in questo codice.  Il campo `version` viene gestito dal back-end per dispositivi mobili e viene usato per la risoluzione dei conflitti.

    Per ottenere un riferimento al contesto di sincronizzazione, chiamare **getSyncContext**. Il contesto di sincronizzazione aiuta a mantenere le relazioni tra tabelle rilevando le modifiche apportate da un'app client in tutte le tabelle ed eseguendone il push quando viene chiamato `.push()` .

3. Aggiornare l'URL dell'applicazione con l'URL dell'applicazione App per dispositivi mobili.

4. Quindi, sostituire il codice:

        todoItemTable = client.getTable('todoitem'); // todoitem is the table name

    con questo codice:

        // Initialize the sync context with the store
        syncContext.initialize(store).then(function () {

        // Get the local table reference.
        todoItemTable = client.getSyncTable('todoitem');

        syncContext.pushHandler = {
            onConflict: function (pushError) {
                // Handle the conflict.
                console.log("Sync conflict! " + pushError.getError().message);
                // Update failed, revert to server's copy.
                pushError.cancelAndDiscard();
              },
              onError: function (pushError) {
                  // Handle the error
                  // In the simulated offline state, you get "Sync error! Unexpected connection failure."
                  console.log("Sync error! " + pushError.getError().message);
              }
        };

        // Call a function to perform the actual sync
        syncBackend();

        // Refresh the todoItems
        refreshDisplay();

        // Wire up the UI Event Handler for the Add Item
        $('#add-item').submit(addItemHandler);
        $('#refresh').on('click', refreshDisplay);

    Il codice precedente inizializza il contesto di sincronizzazione e quindi chiama getSyncTable (invece di getTable) per ottenere un riferimento alla tabella locale.

    Questo codice usa il database locale per tutte le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD, Create, Read, Update, Delete) sulle tabelle.

    In questo esempio si esegue una semplice gestione degli errori nei conflitti di sincronizzazione. Una vera applicazione gestirà i diversi errori come condizioni di rete, conflitti di server e altro. Per esempi di codice, vedere l' [esempio di sincronizzazione offline].

5. Successivamente, aggiungere questa funzione per eseguire la sincronizzazione effettiva.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    Decidere quando effettuare il push delle modifiche nel back-end dell'app per dispositivi mobili chiamando **syncContext.push()**. Ad esempio, è possibile chiamare **syncBackend** in un gestore eventi associato a un pulsante di sincronizzazione.

## <a name="offline-sync-considerations"></a>Considerazioni sulla sincronizzazione offline

Nell'esempio, il metodo **push** di **syncContext** viene chiamato solo all'avvio dell'app nella funzione di callback per l'accesso.  In un'applicazione vera e propria è anche possibile attivare questa funzionalità di sincronizzazione manualmente o quando lo stato della rete cambia.

Quando viene effettuato il pull in una tabella con aggiornamenti locali in sospeso rilevati dal contesto, tale operazione attiva automaticamente un'operazione push. Quando si aggiornano, aggiungono e completano elementi in questo esempio, è possibile omettere la chiamata **push** esplicita, perché potrebbe essere ridondante.

Nel codice fornito viene eseguita una query su tutti i record presenti nella tabella TodoItem remota, ma è anche possibile filtrare i record passando un ID query e una query a **push**. Per altre informazioni, vedere la sezione *Sincronizzazione incrementale* in [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure].

## <a name="optional-disable-authentication"></a>(Facoltativo) Disabilitare l'autenticazione

Se non si vuole configurare l'autenticazione prima di testare la sincronizzazione offline, impostare come commento la funzione di callback per l'accesso, ma lasciare il codice all'interno della funzione di callback in cui sono stati rimossi i commenti.  Dopo l'impostazione come commento delle righe di accesso, il codice sarà come segue:

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

A questo punto, l'app viene sincronizzata con il back-end di Azure quando si esegue l'app.

## <a name="run-the-client-app"></a>Eseguire l'app client
Con la sincronizzazione offline abilitata, è possibile eseguire l'applicazione client almeno una volta in ogni piattaforma per popolare il database di archiviazione locale. Più avanti viene simulato uno scenario offline e vengono modificati i dati nell'archivio locale mentre l'app è offline.

## <a name="optional-test-the-sync-behavior"></a>(Facoltativo) Testare il comportamento di sincronizzazione
In questa sezione viene modificato il progetto client per simulare uno scenario offline usando un URL di applicazione non valido per il back-end. Quando si aggiungono o si modificano elementi di dati, queste modifiche vengono conservate nell'archivio locale, ma non vengono sincronizzate con l'archivio dati back-end fino a quando non viene ristabilita la connessione.

1. In Esplora soluzioni aprire il file di progetto index.js e modificare l'URL dell'applicazione, in modo che punti a un URL non valido, come nel codice seguente:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. Nel file Index. HTML aggiornare l'elemento `<meta>` CSP con lo stesso URL non valido.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. Compilare ed eseguire l'app client e notare che un'eccezione viene registrata nella console quando l'app prova a sincronizzarsi con il back-end dopo l'accesso. Gli eventuali nuovi elementi aggiunti esistono solo nell'archivio locale fino a quando non ne viene effettuato il push nel back-end dell'app per dispositivi mobili. L'app client si comporta come se fosse connessa al back-end.

4. Chiudere l'app e riavviarla per verificare che i nuovi elementi creati siano salvati in modo permanente nell'archivio locale.

5. (Facoltativo) Usare Visual Studio per visualizzare la tabella di database SQL di Azure per verificare che i dati nel database back-end non siano cambiati.

    In Visual Studio aprire **Esplora server**. Passare al database in **Azure**->**Database SQL**. Fare clic con il pulsante destro del mouse sul database e scegliere **Apri in Esplora oggetti di SQL Server**. È ora possibile passare alla tabella di database SQL e al relativo contenuto.

## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>(Facoltativo) Testare la riconnessione al back-end mobile

In questa sezione, l'app viene riconnessa al back-end del'app per dispositivi mobili, azione che simula il ritorno dell'app allo stato online. Quando si esegue l'accesso, i dati verranno sincronizzati con il back-end dell'app per dispositivi mobili.

1. Riaprire index. js e ripristinare l'URL dell'applicazione.
2. Riaprire index.htnl e correggere l'URL dell'applicazione nell'elemento `<meta>` CSP.
3. Ricompilare ed eseguire l'app client. L'app prova a eseguire la sincronizzazione con il back-end dell'app per dispositivi mobili dopo l'accesso. Verificare che non vengano registrate eccezioni nella console di debug.
4. (Facoltativo) Visualizzare i dati aggiornati usando Esplora oggetti di SQL Server o uno strumento REST come Fiddler. Si noti che i dati sono stati sincronizzati tra il database di back-end e l'archivio locale.

    Si noti che i dati sono stati sincronizzati tra il database e l'archivio locale e includono gli elementi aggiunti mentre l'app era disconnessa.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure]
* [Visual Studio Tools per Apache Cordova]

## <a name="next-steps"></a>Passaggi successivi
* Nell'[esempio di sincronizzazione offline] sono disponibili informazioni sulle funzionalità di sincronizzazione offline più avanzate, ad esempio la risoluzione dei conflitti.
* Vedere le informazioni di riferimento sull'API di sincronizzazione offline nella [documentazione per l'API](https://azure.github.io/azure-mobile-apps-js-client).

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[avvio rapido di Apache Cordova]: app-service-mobile-cordova-get-started.md
[esempio di sincronizzazione offline]: https://github.com/Azure-Samples/app-service-mobile-cordova-client-conflict-handling
[Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure]: app-service-mobile-offline-data-sync.md
[Cloud Cover: Offline Sync in Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Visual Studio Tools per Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
