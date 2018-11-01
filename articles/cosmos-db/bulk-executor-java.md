---
title: Uso della libreria Java dell'executor bulk per eseguire operazioni di importazione e aggiornamento in Azure Cosmos DB | Microsoft Docs
description: Importazione e aggiornamento bulk di documenti di Azure Cosmos DB con la libreria Java dell'executor bulk.
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: ramkris
ms.openlocfilehash: 44477f77547c9373bd4a3394c80e217d419bc8e0
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50420048"
---
# <a name="use-bulk-executor-java-library-to-perform-bulk-operations-on-azure-cosmos-db-data"></a>Usare la libreria Java dell'executor bulk per eseguire operazioni in blocco sui dati di Azure Cosmos DB

Questa esercitazione fornisce le istruzioni per importare e aggiornare i documenti in Azure Cosmos DB usando la libreria Java BulkExecutor di Azure Cosmos DB. Per informazioni sulla libreria dell'executor bulk e su come consente di sfruttare il livello elevatissimo di velocità effettiva e archiviazione, vedere l'articolo [Panoramica della libreria dell'executor bulk](bulk-executor-overview.md). In questa esercitazione si compilerà un'applicazione Java che genera documenti casuali che verranno importati in blocco in un contenitore di Azure Cosmos DB. Dopo l'importazione alcune proprietà di un documento verranno aggiornate in blocco. 

Attualmente la libreria dell'executor bulk è supportata solo dagli account delle API SQL e Gremlin di Azure Cosmos DB. Questo articolo descrive come usare la libreria .Net dell'executor bulk con account API SQL. Per informazioni sull'uso della libreria .NET dell'executor bulk con l'API Gremlin, vedere [Eseguire operazioni bulk nell'API Gremlin di Azure Cosmos DB](bulk-executor-graph-dotnet.md).

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.  

* È possibile [provare Microsoft Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) senza una sottoscrizione di Azure, gratuitamente e senza impegno. In alternativa è possibile usare l'[emulatore di Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/local-emulator) con l'URI `https://localhost:8081`. La chiave primaria viene fornita in [Autenticazione delle richieste](local-emulator.md#authenticating-requests).  

* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)  
  - In Ubuntu eseguire `apt-get install default-jdk` per installare JDK.  

  - Assicurarsi di impostare la variabile di ambiente JAVA_HOME in modo che faccia riferimento alla cartella di installazione di JDK.

* [Scaricare](http://maven.apache.org/download.cgi) e [installare](http://maven.apache.org/install.html) un archivio binario [Maven](http://maven.apache.org/)  
  
  - In Ubuntu è possibile eseguire `apt-get install maven` per installare Maven.

* Creare un account API SQL Azure Cosmos DB seguendo la procedura descritta nella sezione [Creare un account di database](create-sql-api-java.md#create-a-database-account) dell'articolo sulla guida introduttiva per Java.

## <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

A questo punto è possibile iniziare a usare il codice scaricando un'applicazione Java di esempio da GitHub. Questa applicazione esegue operazioni in blocco sui dati di Azure Cosmos DB. Per clonare l'applicazione, aprire un prompt dei comandi, passare alla directory in cui si vuole copiare l'applicazione ed eseguire il comando seguente:

```
 git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started.git 
```

Il repository clonato contiene due esempi "bulkimport" e "bulkupdate" relativi alla cartella "\azure-cosmosdb-bulkexecutor-java-getting-started\samples\bulkexecutor-sample\src\main\java\com\microsoft\azure\cosmosdb\bulkexecutor". L'applicazione "bulkimport" genera documenti casuali e li importa in Azure Cosmos DB. L'applicazione "bulkupdate" aggiorna alcuni documenti in Azure Cosmos DB. Nelle sezioni successive verrà esaminato il codice in ognuna di queste app di esempio. 

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Importare in blocco i dati in Azure Cosmos DB

1. Le stringhe di connessione di Azure Cosmos DB vengono lette come argomenti e assegnate alle variabili definite nel file CmdLineConfiguration.java.  

2. L'oggetto DocumentClient viene quindi inizializzato usando le istruzioni seguenti:  

   ```java
   ConnectionPolicy connectionPolicy = new ConnectionPolicy();
   connectionPolicy.setMaxPoolSize(1000);
   DocumentClient client = new DocumentClient(
      HOST,
      MASTER_KEY, 
      connectionPolicy,
      ConsistencyLevel.Session)
   ```

3. L'oggetto DocumentBulkExecutor viene inizializzato con valori di ripetizione elevati per il tempo di attesa e le richieste con limitazione. I valori vengono quindi impostati su 0 per passare il controllo della congestione a DocumentBulkExecutor per la relativa durata.  

   ```java
   // Set client's retry options high for initialization
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(30);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(9);

   // Builder pattern
   Builder bulkExecutorBuilder = DocumentBulkExecutor.builder().from(
     client,
     DATABASE_NAME,
     COLLECTION_NAME,
     collection.getPartitionKey(),
     offerThroughput) // throughput you want to allocate for bulk import out of the container's total throughput

   // Instantiate DocumentBulkExecutor
   DocumentBulkExecutor bulkExecutor = bulkExecutorBuilder.build()

   // Set retries to 0 to pass complete control to bulk executor
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(0);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(0);
```

4. Chiamare l'API importAll che genera documenti casuali da importare in blocco in un contenitore di Azure Cosmos DB. È possibile specificare le configurazioni da riga di comando all'interno del file CmdLineConfiguration.java.

   ```java
   BulkImportResponse bulkImportResponse = bulkExecutor.importAll(documents, false, true, null);
```
   L'API di importazione in blocco accetta una raccolta di documenti serializzati con JSON e ha la sintassi riportata di seguito; per altre informazioni, vedere la [documentazione dell'API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor):

   ```java
   public BulkImportResponse importAll(
        Collection<String> documents,
        boolean isUpsert,
        boolean disableAutomaticIdGeneration,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;   
   ```

   Di seguito sono illustrati i parametri accettati dal metodo importAll.
 
   |**Parametro**  |**Descrizione**  |
   |---------|---------|
   |isUpsert    |   Flag per consentire l'upsert dei documenti. Se esiste già un documento con l'ID specificato, questo viene aggiornato.  |
   |disableAutomaticIdGeneration     |   Flag per disabilitare la generazione automatica dell'ID. Per impostazione predefinita, è impostato su true.   |
   |maxConcurrencyPerPartitionRange    |  Grado massimo di concorrenza per ogni intervallo di chiavi di partizione. Il valore predefinito è 20.  |

   **Definizione dell'oggetto risposta di importazione in blocco** Il risultato della chiamata API di importazione in blocco contiene i metodi get seguenti:

   |**Parametro**  |**Descrizione**  |
   |---------|---------|
   |int getNumberOfDocumentsImported()  |   Il numero totale di documenti importati correttamente rispetto ai documenti forniti alla chiamata API di importazione in blocco.      |
   |double getTotalRequestUnitsConsumed()   |  Le unità richiesta (UR) totali usate dalla chiamata API di importazione in blocco.       |
   |Duration getTotalTimeTaken()   |    Il tempo totale impiegato dalla chiamata API di importazione in blocco per completare l'esecuzione.     |
   |List<Exception> getErrors() |  L'elenco di errori se alcuni documenti del batch fornito alla chiamata API di importazione in blocco non vengono inseriti.       |
   |List<Object> getBadInputDocuments()  |    L'elenco di documenti con formato non valido che non sono stati importati correttamente nella chiamata API di importazione in blocco. L'utente deve correggere il problema nei documenti restituiti e ripetere l'importazione. I documenti con formato non valido includono i documenti con un valore ID diverso da stringa, ad esempio con un valore null o con qualsiasi altro tipo di dati.     |

5. Dopo avere preparato l'applicazione di importazione in blocco, compilare lo strumento da riga di comando dall'origine usando il comando "mvn clean package". Questo comando genera un file jar nella cartella di destinazione:  

   ```java
   mvn clean package
   ```

6. Dopo avere generato le dipendenze di destinazione, è possibile richiamare l'applicazione di importazione in blocco usando il comando seguente:  

   ```java
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint *<Fill in your Azure Cosmos DB’s endpoint URI>*  -masterKey *<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkImportDb -collectionId bulkImportColl -operation import -shouldCreateCollection -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

   L'utilità di importazione in blocco crea un nuovo database e una nuova raccolta con il nome del database, il nome della raccolta e i valori di velocità effettiva specificati nel file App.config. 

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Aggiornare in blocco i dati in Azure Cosmos DB

È possibile aggiornare i documenti esistenti tramite l'API BulkUpdateAsync. In questo esempio impostare il campo del nome su un nuovo valore e rimuovere il campo della descrizione dai documenti esistenti. Per il set completo di operazioni di aggiornamento dei campi supportate, vedere la [documentazione dell'API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor). 

1. Definisce gli elementi di aggiornamento insieme alle operazioni di aggiornamento dei campi corrispondenti. In questo esempio si userà SetUpdateOperation per aggiornare il campo del nome e UnsetUpdateOperation per rimuovere il campo della descrizione da tutti i documenti. È possibile anche eseguire altre operazioni, ad esempio incrementare un campo del documento per un valore specifico, eseguire il push di valori specifici in un campo di matrice o rimuovere un valore specifico da un campo di matrice. Per informazioni sui diversi metodi forniti dall'API di aggiornamento in blocco, vedere la [documentazione dell'API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor).  

   ```java
   SetUpdateOperation<String> nameUpdate = new SetUpdateOperation<>("Name","UpdatedDocValue");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   ArrayList<UpdateOperationBase> updateOperations = new ArrayList<>();
   updateOperations.add(nameUpdate);
   updateOperations.add(descriptionUpdate);

   List<UpdateItem> updateItems = new ArrayList<>(cfg.getNumberOfDocumentsForEachCheckpoint());
   IntStream.range(0, cfg.getNumberOfDocumentsForEachCheckpoint()).mapToObj(j -> {                      
    return new UpdateItem(Long.toString(prefix + j), Long.toString(prefix + j), updateOperations);
    }).collect(Collectors.toCollection(() -> updateItems));
   ```

2. Chiamare l'API updateAll che genera documenti casuali da importare successivamente in blocco in un contenitore di Azure Cosmos DB. È possibile specificare le configurazioni da riga di comando da passare al file CmdLineConfiguration.java.

   ```java
   BulkUpdateResponse bulkUpdateResponse = bulkExecutor.updateAll(updateItems, null)
   ```

   L'API di aggiornamento in blocco accetta una raccolta di elementi da aggiornare. Ogni elemento di aggiornamento specifica l'elenco di operazioni di aggiornamento dei campi da eseguire su un documento identificato da un ID e da un valore di chiave di partizione. Per altri dettagli, vedere la [documentazione dell'API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor):

   ```java
   public BulkUpdateResponse updateAll(
        Collection<UpdateItem> updateItems,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;
   ```

   Di seguito sono illustrati i parametri accettati dal metodo updateAll.

   |**Parametro** |**Descrizione** |
   |---------|---------|
   |maxConcurrencyPerPartitionRange   |  Grado massimo di concorrenza per ogni intervallo di chiavi di partizione. Il valore predefinito è 20.  |
 
   **Definizione dell'oggetto risposta di importazione in blocco** Il risultato della chiamata API di importazione in blocco contiene i metodi get seguenti:

   |**Parametro** |**Descrizione**  |
   |---------|---------|
   |int getNumberOfDocumentsUpdated()  |   Il numero totale di documenti aggiornati correttamente rispetto ai documenti forniti alla chiamata API di aggiornamento in blocco.      |
   |double getTotalRequestUnitsConsumed() |  Le unità richiesta (UR) totali usate dalla chiamata API di aggiornamento in blocco.       |
   |Duration getTotalTimeTaken()  |   Il tempo totale impiegato dalla chiamata API di aggiornamento in blocco per completare l'esecuzione.      |
   |List<Exception> getErrors()   |     L'elenco di errori se alcuni documenti del batch fornito alla chiamata API di aggiornamento in blocco non vengono inseriti.      |

3. Dopo avere preparato l'applicazione di aggiornamento in blocco, compilare lo strumento da riga di comando dall'origine usando il comando "mvn clean package". Questo comando genera un file jar nella cartella di destinazione:  

   ```
   mvn clean package
   ```

4. Dopo avere generato le dipendenze di destinazione, è possibile richiamare l'applicazione di aggiornamento in blocco usando il comando seguente:

   ```
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint **<Fill in your Azure Cosmos DB’s endpoint URI>* -masterKey **<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkUpdateDb -collectionId bulkUpdateColl -operation update -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

## <a name="performance-tips"></a>Suggerimenti per incrementare le prestazioni 

Per ottenere prestazioni migliori, quando si usa la libreria dell'executor bulk tenere presente quanto segue:

* Per prestazioni ottimali eseguire l'applicazione da una macchina virtuale Azure nella stessa area in cui si trova l'area di scrittura dell'account Cosmos DB.  
* Per ottenere una velocità effettiva più elevata:  

   * Impostare la dimensione dell'heap della JVM su un numero sufficientemente elevato per evitare problemi di memoria quando si gestisce un numero elevato di documenti. Dimensione heap suggerita: max (3GB, 3 * sizeof (tutti i documenti passati all'API di importazione in blocco in un unico batch)).  
   * Il tempo di pre-elaborazione previsto consente di ottenere una velocità effettiva maggiore quando si eseguono operazioni in blocco con un numero elevato di documenti. Per importare 10.000.000 documenti, è preferibile eseguire l'importazione in blocco 10 volte di 10 blocchi di documenti, ognuno con dimensione pari a 1.000.000, anziché importare in blocco 100 volte 100 blocchi di documenti, ognuno con dimensione pari a 100.000.  

* È consigliabile creare l'istanza di un singolo oggetto DocumentBulkExecutor per l'intera applicazione all'interno di una singola macchina virtuale corrispondente a uno specifico contenitore di Azure Cosmos DB.  

* Dato che l'esecuzione di una singola API con un'operazione in blocco usa un blocco di grandi dimensioni della CPU e dell'I/O di rete del computer client generando internamente più attività, evitare di generare più attività simultanee all'interno del processo dell'applicazione, di cui ognuna esegue chiamate API di operazioni in blocco. Se una singola chiamata API di un'operazione in blocco in esecuzione su una singola macchina virtuale non è in grado di usare la velocità effettiva dell'intero contenitore (se la velocità effettiva del contenitore > 1 milione di UR/s), è preferibile creare macchine virtuali separate per eseguire simultaneamente le chiamate API di operazione in blocco.

    
## <a name="next-steps"></a>Passaggi successivi
* Per i dettagli del pacchetto Maven e le note sulla versione della libreria Java dell'executor bulk, vedere i [dettagli sull'SDK dell'executor bulk](sql-api-sdk-bulk-executor-java.md).


