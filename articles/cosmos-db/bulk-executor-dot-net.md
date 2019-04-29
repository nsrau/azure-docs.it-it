---
title: Uso della libreria .NET dell'executor bulk per eseguire operazioni di importazione e aggiornamento in Azure Cosmos DB
description: Importazione e aggiornamento bulk di documenti di Azure Cosmos DB con la libreria .NET dell'executor bulk.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: cfb90dc31635001291b1661f31ec2ee1fc378404
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60894442"
---
# <a name="use-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Usare la libreria .NET dell'executor bulk per eseguire operazioni in blocco in Azure Cosmos DB

Questa esercitazione fornisce le istruzioni per importare e aggiornare i documenti nei contenitori di Azure Cosmos DB usando la libreria .NET dell'executor bulk di Azure Cosmos DB. Per informazioni sulla libreria dell'executor bulk e su come consente di sfruttare il livello elevatissimo di velocità effettiva e archiviazione, vedere l'articolo [Panoramica della libreria dell'executor bulk](bulk-executor-overview.md). In questa esercitazione si esaminerà in dettaglio un'applicazione .NET di esempio che importa in blocco i documenti generati in modo casuale in un contenitore di Azure Cosmos DB. Dopo l'importazione illustra come è possibile aggiornare in blocco i dati importati specificando le patch come operazioni da eseguire su campi di documenti specifici. 

Attualmente la libreria dell'executor bulk è supportata solo dagli account delle API SQL e Gremlin di Azure Cosmos DB. Questo articolo descrive come usare la libreria .NET dell'executor bulk con account API SQL. Per informazioni sull'uso della libreria .NET dell'executor bulk con l'API Gremlin, vedere [Eseguire operazioni bulk nell'API Gremlin di Azure Cosmos DB](bulk-executor-graph-dotnet.md). 

## <a name="prerequisites"></a>Prerequisiti

* Se Visual Studio 2017 non è ancora installato, è possibile scaricare e usare la versione [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Durante l'installazione di Visual Studio abilitare Sviluppo di Azure.

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare. 

* È possibile [provare Microsoft Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) senza una sottoscrizione di Azure, gratuitamente e senza impegno. In alternativa è possibile usare l'[emulatore di Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/local-emulator) con l'endpoint `https://localhost:8081`. La chiave primaria viene fornita in [Autenticazione delle richieste](local-emulator.md#authenticating-requests).

* Creare un account API SQL di Azure Cosmos DB tramite la procedura descritta nella sezione [Creare un account di database](create-sql-api-dotnet.md#create-account) dell'articolo sulla guida introduttiva per .NET. 

## <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

A questo punto è possibile iniziare a usare il codice scaricando alcune applicazioni .NET di esempio da GitHub. Queste applicazioni eseguono operazioni in blocco sui dati di Azure Cosmos DB. Per clonare le applicazioni, aprire un prompt dei comandi, passare alla directory in cui si vuole copiare le applicazioni ed eseguire il comando seguente:

```
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

Il repository clonato contiene due esempi: "BulkImportSample" e "BulkUpdateSample". È possibile aprire una delle applicazioni di esempio, aggiornare le stringhe di connessione nel file App.config con le stringhe di connessione dell'account Azure Cosmos DB, compilare la soluzione ed eseguirla. 

L'applicazione "BulkImportSample" genera documenti casuali e li importa in blocco in Azure Cosmos DB. L'applicazione "BulkUpdateSample" aggiorna in blocco i documenti importati, specificando le patch come operazioni da eseguire su campi di documenti specifici. Nelle sezioni successive verrà esaminato il codice in ognuna di queste app di esempio.

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Importare in blocco i dati in Azure Cosmos DB

1. Passare alla cartella "BulkImportSample" e aprire il file "BulkImportSample.sln".  

2. Le stringhe di connessione di Azure Cosmos DB vengono recuperate dal file App.config, come illustrato nel codice seguente:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   L'utilità di importazione in blocco crea un nuovo database e una nuova raccolta con il nome del database, il nome della raccolta e i valori di velocità effettiva specificati nel file App.config. 

3. L'oggetto DocumentClient viene quindi inizializzato con la modalità di connessione TCP diretto:  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. L'oggetto di BulkExecutor viene inizializzata con un valore elevato di ripetizione dei tentativi per tempo di attesa e le richieste limitate. Questi vengono quindi impostati su 0 per passare il controllo della congestione a BulkExecutor per la relativa durata.  

   ```csharp
   // Set retry options high during initialization (default values).
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 30;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 9;

   IBulkExecutor bulkExecutor = new BulkExecutor(client, dataCollection);
   await bulkExecutor.InitializeAsync();

   // Set retries to 0 to pass complete control to bulk executor.
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 0;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 0;
   ```

5. L'applicazione richiama l'API BulkImportAsync. La libreria .NET fornisce due overload dell'API di importazione in blocco: una che accetta un elenco di documenti JSON serializzati e una che accetta un elenco di documenti POCO deserializzati. Per informazioni sulle definizioni di ognuno di questi metodi di overload, vedere la [documentazione dell'API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet).

   ```csharp
   BulkImportResponse bulkImportResponse = await bulkExecutor.BulkImportAsync(
     documents: documentsToImportInBatch,
     enableUpsert: true,
     disableAutomaticIdGeneration: true,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```
   **Di seguito sono illustrati i parametri accettati dal metodo BulkImportAsync**.
   
   |**Parametro**  |**Descrizione** |
   |---------|---------|
   |enableUpsert    |   Flag per abilitare esegue l'Upsert dei documenti. Se esiste già un documento con l'ID specificato, questo viene aggiornato. Per impostazione predefinita, il valore è impostato su false.      |
   |disableAutomaticIdGeneration    |    Flag per disabilitare la generazione automatica dell'ID. Per impostazione predefinita, è impostato su true.     |
   |maxConcurrencyPerPartitionKeyRange    | Il grado massimo di concorrenza per ogni intervallo di chiavi di partizione; impostando il valore null la libreria userà il valore predefinito 20. |
   |maxInMemorySortingBatchSize     |  Il numero massimo di documenti di cui è stato eseguito il pull dall'enumeratore di documenti che viene passato alla chiamata API in ogni fase.  Per la fase di ordinamento di pre-elaborazione in memoria che precede l'importazione in blocco, impostando il valore null la libreria userà il valore predefinito min(documents.count, 1000000).       |
   |cancellationToken    |    Il token di annullamento per uscire dall'importazione in blocco in modo graduale.     |

   **Definizione dell'oggetto risposta di importazione in blocco** Il risultato della chiamata API di importazione in blocco contiene gli attributi seguenti:

   |**Parametro**  |**Descrizione**  |
   |---------|---------|
   |NumberOfDocumentsImported (long)   |  Il numero totale di documenti importati correttamente rispetto ai documenti forniti alla chiamata API di importazione in blocco.       |
   |TotalRequestUnitsConsumed (double)   |   Le unità richiesta (UR) totali usate dalla chiamata API di importazione in blocco.      |
   |TotalTimeTaken (TimeSpan)    |   Il tempo totale impiegato dalla chiamata API di importazione in blocco per completare l'esecuzione.      |
   |BadInputDocuments (elenco\<oggetto >)   |     L'elenco di documenti con formato non valido che non sono stati importati correttamente nella chiamata API di importazione in blocco. L'utente deve correggere il problema nei documenti restituiti e ripetere l'importazione. I documenti con formato non valido includono i documenti con un valore ID diverso da stringa, ad esempio con un valore null o con qualsiasi altro tipo di dati.    |

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Aggiornare in blocco i dati in Azure Cosmos DB

È possibile aggiornare i documenti esistenti tramite l'API BulkUpdateAsync. In questo esempio impostare il campo del nome su un nuovo valore e rimuovere il campo della descrizione dai documenti esistenti. Per il set completo di operazioni di aggiornamento di campi supportate, vedere la [documentazione dell'API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet). 

1. Passare alla cartella "BulkImportSample" e aprire il file "BulkImportSample.sln".  

2. Definire gli elementi di aggiornamento insieme alle operazioni di aggiornamento dei campi corrispondenti. In questo esempio si userà SetUpdateOperation per aggiornare il campo del nome e UnsetUpdateOperation per rimuovere il campo della descrizione da tutti i documenti. È possibile anche eseguire altre operazioni, ad esempio incrementare un campo del documento per un valore specifico, eseguire il push di valori specifici in un campo di matrice o rimuovere un valore specifico da un campo di matrice. Per informazioni sui diversi metodi forniti dall'API di aggiornamento in blocco, vedere la [documentazione dell'API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet).

   ```csharp
   SetUpdateOperation<string> nameUpdate = new SetUpdateOperation<string>("Name", "UpdatedDoc");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   List<UpdateOperation> updateOperations = new List<UpdateOperation>();
   updateOperations.Add(nameUpdate);
   updateOperations.Add(descriptionUpdate);

   List<UpdateItem> updateItems = new List<UpdateItem>();
   for (int i = 0; i < 10; i++)
   {
    updateItems.Add(new UpdateItem(i.ToString(), i.ToString(), updateOperations));
   }
   ```

3. L'applicazione richiama l'API BulkUpdateAsync. Per altre informazioni sulla definizione del metodo BulkUpdateAsync, vedere la [documentazione dell'API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync?view=azure-dotnet).  

   ```csharp
   BulkUpdateResponse bulkUpdateResponse = await bulkExecutor.BulkUpdateAsync(
     updateItems: updateItems,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```  
   **Di seguito sono illustrati i parametri accettati dal metodo BulkUpdateAsync**.

   |**Parametro**  |**Descrizione** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   Il grado massimo di concorrenza per ogni intervallo di chiavi di partizione; impostando il valore null la libreria userà il valore predefinito 20.   |
   |maxInMemorySortingBatchSize    |    Il numero massimo di elementi di aggiornamento di cui è stato eseguito il pull dall'enumeratore di elementi di aggiornamento passato alla chiamata API in ogni fase per la fase di ordinamento in pre-elaborazione in memoria prima dell'aggiornamento in blocco; impostando il valore null la libreria userà il valore predefinito min(updateItems.count, 1000000).     |
   | cancellationToken|Il token di annullamento per uscire dall'aggiornamento in blocco in modo graduale. |

   **Definizione dell'oggetto risposta di aggiornamento in blocco** Il risultato della chiamata API di aggiornamento in blocco contiene gli attributi seguenti:

   |**Parametro**  |**Descrizione** |
   |---------|---------|
   |NumberOfDocumentsUpdated (long)    |   Il numero totale di documenti aggiornati correttamente rispetto ai documenti forniti alla chiamata API di aggiornamento in blocco.      |
   |TotalRequestUnitsConsumed (double)   |    Le unità richiesta (UR) totali usate dalla chiamata API di aggiornamento in blocco.    |
   |TotalTimeTaken (TimeSpan)   | Il tempo totale impiegato dalla chiamata API di aggiornamento in blocco per completare l'esecuzione. |
    
## <a name="performance-tips"></a>Suggerimenti per incrementare le prestazioni 

Per ottenere prestazioni migliori, quando si usa la libreria dell'executor bulk tenere presente quanto segue:

* Per prestazioni ottimali eseguire l'applicazione da una macchina virtuale Azure che si trovi nella stessa area in cui si trova l'area di scrittura dell'account Cosmos DB.  

* Si consiglia di creare un'istanza di un singolo oggetto BulkExecutor per l'intera applicazione all'interno di una singola macchina virtuale corrispondente a uno specifico contenitore di Cosmos DB.  

* Dato che l'esecuzione di una singola API con un'operazione in blocco usa un blocco di grandi dimensioni della CPU e dell'I/O di rete del computer client generando internamente più attività, evitare di generare più attività simultanee all'interno del processo dell'applicazione, di cui ognuna esegue chiamate API di operazioni in blocco. Se una chiamata di operazione API bulk singolo in esecuzione in una singola macchina virtuale è in grado di utilizzare la velocità effettiva del contenitore intero (se. la velocità effettiva > 1 del contenitore milioni di UR/s), è preferibile per creare macchine virtuali separate da eseguire simultaneamente chiamate API di operazioni bulk.  

* Verificare che InitializeAsync() venga richiamato dopo la creazione dell'istanza di un oggetto BulkExecutor per recuperare la mappa di partizioni del contenitore di Cosmos DB di destinazione.  

* In App.Config dell'applicazione verificare che **gcServer** sia abilitato per assicurare prestazioni migliori.
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* La libreria genera tracce che possono essere raccolte in un file di log o nella console. Per abilitare entrambi, aggiungere quanto segue al file App.Config dell'applicazione.

  ```xml
  <system.diagnostics>
    <trace autoflush="false" indentsize="4">
      <listeners>
        <add name="logListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="application.log" />
        <add name="consoleListener" type="System.Diagnostics.ConsoleTraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
  ```

## <a name="next-steps"></a>Passaggi successivi
* Per ulteriori informazioni sui dettagli del pacchetto Nuget e note della libreria .NET di executor bulk, vedere[bulk dettagli SDK executor](sql-api-sdk-bulk-executor-dot-net.md). 
