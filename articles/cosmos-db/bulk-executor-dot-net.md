---
title: Usare la libreria .NET dell'executor bulk nel database Cosmos di Azure per le operazioni di importazione e aggiornamento in bloccoUse bulk executor .NET library in Azure Cosmos DB for bulk import and update operations
description: Importare e aggiornare in blocco i documenti del database Cosmos di Azure usando la libreria .NET dell'esecutore in blocco.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: d7600267dcd196a9a5c06c29774ea21d582cd7ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246877"
---
# <a name="use-the-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Usare la libreria .NET dell'esecutore bulk per eseguire operazioni bulk in Azure Cosmos DBUse the bulk executor .NET library to perform bulk operations in Azure Cosmos DB

Questa esercitazione fornisce istruzioni sull'uso della libreria .NET dell'esecutore bulk per importare e aggiornare documenti in un contenitore Cosmos di Azure.This tutorial provides instructions on using the bulk executor .NET library to import and update documents to an Azure Cosmos container. Per informazioni sulla libreria dell'executor bulk e su come consente di sfruttare la velocità effettiva e l'archiviazione di massa, vedere l'articolo panoramica della [libreria dell'executor bulk.](bulk-executor-overview.md) In questa esercitazione verrà visualizzata un'applicazione .NET di esempio che importa in blocco i documenti generati casualmente in un contenitore Cosmos di Azure.In this tutorial, you will see a sample .NET application that bulk generated randomly generated documents into an Azure Cosmos container. Dopo l'importazione illustra come è possibile aggiornare in blocco i dati importati specificando le patch come operazioni da eseguire su campi di documenti specifici.

Attualmente, la libreria dell'executor bulk è supportata solo dagli account API SQL del database di Azure Cosmos e Gremlin. In questo articolo viene descritto come utilizzare la libreria .NET dell'esecutore bulk con gli account dell'API SQL. Per informazioni sull'uso della libreria .NET dell'esecutore bulk con gli account API Gremlin, vedere [Eseguire operazioni in blocco nell'API Gremlin di Azure Cosmos DB](bulk-executor-graph-dotnet.md).

## <a name="prerequisites"></a>Prerequisiti

* Se Visual Studio 2019 non è già installato, è possibile scaricare e utilizzare [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Assicurarsi di abilitare lo "sviluppo di Azure" durante l'installazione di Visual Studio.Make sure that you enable "Azure development" during the Visual Studio setup.

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.

* È possibile [provare Microsoft Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) senza una sottoscrizione di Azure, gratuitamente e senza impegno. In alternativa, è possibile usare l'emulatore [di database Cosmos](https://docs.microsoft.com/azure/cosmos-db/local-emulator) di Azure con l'endpoint. `https://localhost:8081` La chiave primaria viene fornita in [Autenticazione delle richieste](local-emulator.md#authenticating-requests).

* Creare un account API SQL di Azure Cosmos DB tramite la procedura descritta nella sezione [Creare un account di database](create-sql-api-dotnet.md#create-account) dell'articolo sulla guida introduttiva per .NET.

## <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

Passiamo ora all'utilizzo del codice scaricando un'applicazione .NET di esempio da GitHub.Now let's switch to working with code by downloading a sample .NET application from GitHub. Questa applicazione esegue operazioni bulk sui dati archiviati nell'account Cosmos di Azure.This application performs bulk operations on the data stored in your Azure Cosmos account. Per clonare l'applicazione, aprire un prompt dei comandi, passare alla directory in cui si desidera copiarla ed eseguire il comando seguente:

```
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

Il repository clonato contiene due esempi "BulkImportSample" e "BulkUpdateSample". È possibile aprire una delle applicazioni di esempio, aggiornare le stringhe di connessione nel file App.config con le stringhe di connessione dell'account Azure Cosmos DB, compilare la soluzione ed eseguirla.

L'applicazione "BulkImportSample" genera documenti casuali e li importa in blocco nell'account Azure Cosmos.The "BulkImportSample" application generates random documents and bulk imports to your Azure Cosmos account. L'applicazione "BulkUpdateSample" aggiorna in blocco i documenti importati, specificando le patch come operazioni da eseguire su campi di documenti specifici. Nelle sezioni successive verrà esaminato il codice in ognuna di queste app di esempio.

## <a name="bulk-import-data-to-an-azure-cosmos-account"></a>Importazione in blocco di dati in un account Cosmos di AzureBulk import data to an Azure Cosmos account

1. Passare alla cartella "BulkImportSample" e aprire il file "BulkImportSample.sln".  

2. Le stringhe di connessione di Azure Cosmos DB vengono recuperate dal file App.config, come illustrato nel codice seguente:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   L'utilità di importazione bulk crea un nuovo database e un contenitore con il nome del database, il nome del contenitore e i valori di velocità effettiva specificati nel file App.config.

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

4. L'oggetto BulkExecutor viene inizializzato con un valore di ripetizione dei tentativi elevato per il tempo di attesa e le richieste limitate. Questi vengono quindi impostati su 0 per passare il controllo della congestione a BulkExecutor per la relativa durata.  

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

5. L'applicazione richiama l'API BulkImportAsync. La libreria .NET fornisce due overload dell'API di importazione bulk, uno che accetta un elenco di documenti JSON serializzati e l'altro che accetta un elenco di documenti POCO deserializzati. Per ulteriori informazioni sulle definizioni di ognuno di questi metodi di overload, fare riferimento alla [documentazione dell'API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet).

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
   |enableUpsert    |   Un flag per abilitare le operazioni di upsert sui documenti. Se esiste già un documento con l'ID specificato, viene aggiornato. Per impostazione predefinita, il valore è impostato su false.      |
   |disableAutomaticIdGeneration    |    Flag per disabilitare la generazione automatica dell'ID. Per impostazione predefinita, è impostato su true.     |
   |maxConcurrencyPerPartitionKeyRange    | Il grado massimo di concorrenza per ogni intervallo di chiavi di partizione; impostando il valore null la libreria userà il valore predefinito 20. |
   |maxInMemorySortingBatchSize     |  Numero massimo di documenti estratti dall'enumeratore del documento, che viene passato alla chiamata API in ogni fase. Per la fase di ordinamento in memoria che si verifica prima dell'importazione in blocco, l'impostazione di questo parametro su null farà sì che la libreria utilizzi il valore minimo predefinito (documents.count, 1000000).       |
   |cancellationToken    |    Token di annullamento per uscire normalmente dall'operazione di importazione in blocco.     |

   **Definizione dell'oggetto risposta di importazione in blocco** Il risultato della chiamata API di importazione in blocco contiene gli attributi seguenti:

   |**Parametro**  |**Descrizione**  |
   |---------|---------|
   |NumberOfDocumentsImported (long)   |  Numero totale di documenti importati correttamente dai documenti totali forniti alla chiamata API di importazione in blocco.       |
   |TotalRequestUnitsConsumed (double)   |   Le unità richiesta (UR) totali usate dalla chiamata API di importazione in blocco.      |
   |TotalTimeTaken (TimeSpan)    |   Tempo totale impiegato dalla chiamata all'API di importazione in blocco per completare l'esecuzione.      |
   |BadInputDocuments (oggetto List\<>)   |     L'elenco di documenti con formato non valido che non sono stati importati correttamente nella chiamata API di importazione in blocco. Correggere i documenti restituiti e riprovare l'importazione. I documenti con formato non valido includono i documenti con un valore ID diverso da stringa, ad esempio con un valore null o con qualsiasi altro tipo di dati.    |

## <a name="bulk-update-data-in-your-azure-cosmos-account"></a>Aggiornamento in blocco dei dati nell'account Cosmos di Azure

È possibile aggiornare i documenti esistenti tramite l'API BulkUpdateAsync. In questo esempio, si `Name` imposterà il campo `Description` su un nuovo valore e si rimuoverà il campo dai documenti esistenti. Per il set completo di operazioni di aggiornamento supportate, fare riferimento alla [documentazione dell'API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet).

1. Passare alla cartella "BulkImportSample" e aprire il file "BulkImportSample.sln".  

2. Definire gli elementi di aggiornamento insieme alle operazioni di aggiornamento dei campi corrispondenti. In questo esempio verrà `SetUpdateOperation` utilizzato `Name` per `UnsetUpdateOperation` aggiornare `Description` il campo e rimuovere il campo da tutti i documenti. È possibile anche eseguire altre operazioni, ad esempio incrementare un campo del documento per un valore specifico, eseguire il push di valori specifici in un campo di matrice o rimuovere un valore specifico da un campo di matrice. Per informazioni sui diversi metodi forniti dall'API di aggiornamento in blocco, vedere la [documentazione dell'API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet).

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

3. L'applicazione richiama l'API BulkUpdateAsync. Per informazioni sulla definizione del metodo BulkUpdateAsync , fare riferimento alla [documentazione dell'API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync?view=azure-dotnet).  

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
   |maxConcurrencyPerPartitionKeyRange    |   Il grado massimo di concorrenza per intervallo di chiavi di partizione, impostando questo parametro su null, la libreria verrà utilizzata per l'utilizzo del valore predefinito(20).   |
   |maxInMemorySortingBatchSize    |    Numero massimo di elementi di aggiornamento estratti dall'enumeratore degli elementi di aggiornamento passato alla chiamata API in ogni fase. Per la fase di ordinamento in memoria che si verifica prima dell'aggiornamento bulk, l'impostazione di questo parametro su null causerà l'utilizzo del valore minimo predefinito nella libreria updateItems.count, 1000000.     |
   | cancellationToken|Token di annullamento per uscire normalmente dall'operazione di aggiornamento in blocco. |

   **Definizione dell'oggetto risposta di aggiornamento in blocco** Il risultato della chiamata API di aggiornamento in blocco contiene gli attributi seguenti:

   |**Parametro**  |**Descrizione** |
   |---------|---------|
   |NumberOfDocumentsUpdated (long)    |   Numero di documenti aggiornati correttamente rispetto al totale dei documenti forniti alla chiamata API di aggiornamento in blocco.      |
   |TotalRequestUnitsConsumed (double)   |    Unità di richiesta (RU) totali utilizzate dalla chiamata ALL'API di aggiornamento in blocco.    |
   |TotalTimeTaken (TimeSpan)   | Tempo totale impiegato dalla chiamata all'API di aggiornamento in blocco per completare l'esecuzione. |
    
## <a name="performance-tips"></a>Suggerimenti per incrementare le prestazioni 

Considerare i punti seguenti per migliorare le prestazioni quando si utilizza la libreria dell'executor bulk:Consider the following points for better performance when using the bulk executor library:

* Per ottenere prestazioni ottimali, eseguire l'applicazione da una macchina virtuale di Azure che si trova nella stessa area dell'area di scrittura dell'account Azure Cosmos.For best performance, run your application from an Azure virtual machine that is in the same region as your Azure Cosmos account's write region.  

* È consigliabile creare un'istanza di un singolo `BulkExecutor` oggetto per l'intera applicazione all'interno di una singola macchina virtuale che corrisponde a un contenitore Cosmos di Azure specifico.  

* Poiché l'esecuzione di una singola operazione in blocco dell'API consuma una grande parte della CPU e dell'I/O di rete del computer client (ciò avviene generando più attività internamente). Evitare la generazione di più attività simultanee all'interno del processo dell'applicazione che eseguono chiamate API di operazioni in blocco. Se una singola chiamata API di operazione in blocco in esecuzione su una singola macchina virtuale non è in grado di utilizzare la velocità effettiva dell'intero contenitore (se la velocità effettiva del contenitore > 1 milione di RU/s), è preferibile creare macchine virtuali separate per eseguire contemporaneamente le chiamate API dell'operazione in blocco.  

* Assicurarsi `InitializeAsync()` che il metodo venga richiamato dopo la creazione di un'istanza di un oggetto BulkExecutor per recuperare la mappa di partizione del contenitore Cosmos di destinazione.  

* In App.Config dell'applicazione verificare che **gcServer** sia abilitato per assicurare prestazioni migliori.
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* La libreria genera tracce che possono essere raccolte in un file di log o nella console. Per abilitare entrambi, aggiungere il codice seguente al file App.Config dell'applicazione.

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

* Per informazioni sui dettagli del pacchetto Nuget e sulle note sulla versione, vedere i [dettagli dell'SDK dell'esecutore in blocco.](sql-api-sdk-bulk-executor-dot-net.md)
