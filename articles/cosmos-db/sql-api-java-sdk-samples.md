---
title: 'API SQL di Azure Cosmos DB: esempi di Java SDK v4'
description: Esempi di Java in GitHub per attività comuni con l'API SQL di Azure Cosmos DB, tra cui operazioni CRUD.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 05/20/2020
ms.author: anfeldma
ms.openlocfilehash: db291b735ed6456a007446ad23373ff25e806aab
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727163"
---
# <a name="azure-cosmos-db-sql-api-java-sdk-v4-examples"></a>API SQL di Azure Cosmos DB: esempi di Java SDK v4

> [!div class="op_single_selector"]
> * [Esempi di .NET V2 SDK](sql-api-dotnet-samples.md)
> * [Esempi di .NET V3 SDK](sql-api-dotnet-v3sdk-samples.md)
> * [Esempi di Java V4 SDK](sql-api-java-sdk-samples.md)
> * [Esempi di Node.js](sql-api-nodejs-samples.md)
> * [Esempi di Python](sql-api-python-samples.md)
> * [Raccolta di esempi di codice di Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

> [!IMPORTANT]  
> Per altre informazioni su Java SDK v4, vedere le [note sulla versione](sql-api-sdk-java-v4.md) di Java SDK v4 per Azure Cosmos DB, il [repository Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos), i [suggerimenti sulle prestazioni ](performance-tips-java-sdk-v4-sql.md) di Java SDK v4 per Azure Cosmos DB e la [guida alla risoluzione dei problemi](troubleshoot-java-sdk-v4-sql.md) di Java SDK v4 per Azure Cosmos DB. Se si usa una versione precedente a v4, vedere l'articolo [Eseguire la migrazione a Java SDK v4 per Azure Cosmos DB](migrate-java-v4-sdk.md) per informazioni sull'aggiornamento a v4.
>

> [!IMPORTANT]  
>[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
>  
>- È possibile [attivare i vantaggi della sottoscrizione Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Con l'abbonamento Visual Studio ogni mese si accumulano crediti che è possibile usare per i servizi di Azure a pagamento.
>
>[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]
>

Le applicazioni di esempio più recenti che eseguono operazioni CRUD e altre operazioni di uso comune su risorse di Azure Cosmos DB sono disponibili nel repository [azure-cosmos-java-sql-api-samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples) di GitHub. Questo articolo include:

* Collegamenti alle attività in ogni file di progetto Java di esempio. 
* Collegamenti al contenuto di riferimento sulle API correlato.

**Prerequisiti**

Per eseguire questa applicazione di esempio, occorre quanto segue:

* Java Development Kit 8
* Java SDK v4 per Azure Cosmos DB

È possibile usare facoltativamente Maven per ottenere i file binari più recenti di Java SDK v4 per Azure Cosmos DB da usare nel progetto. Maven aggiunge automaticamente le dipendenze necessarie. In caso contrario, è possibile scaricare direttamente le dipendenze elencate nel file pom.xml e aggiungerle al percorso di compilazione.

```bash
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-cosmos</artifactId>
    <version>LATEST</version>
</dependency>
```

**Esecuzione delle applicazioni di esempio**

Clonare il repository di esempio:
```bash
$ git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples.git

$ cd azure-cosmos-java-sql-api-samples
```

È possibile eseguire gli esempi tramite un IDE (Eclipse, IntelliJ o VSCODE) o dalla riga di comando usando Maven.

Queste variabili di ambiente devono essere impostate

```
ACCOUNT_HOST=your account hostname;ACCOUNT_KEY=your account master key
```

per concedere all'account l'accesso in lettura/scrittura agli esempi.

Per eseguire un esempio, specificare la relativa classe Main 

```
com.azure.cosmos.examples.sample.synchronicity.MainClass
```

dove *sample.synchronicity.MainClass* può essere
* crudquickstart.sync.SampleCRUDQuickstart
* crudquickstart.async.SampleCRUDQuickstartAsync
* indexmanagement.sync.SampleIndexManagement
* indexmanagement.async.SampleIndexManagementAsync
* storedprocedure.sync.SampleStoredProcedure
* storedprocedure.async.SampleStoredProcedureAsync
* changefeed.SampleChangeFeedProcessor *(Changefeed include solo un esempio asincrono, non sincrono)* e così via.

> [!NOTE]
> Ogni esempio è indipendente e le operazioni di installazione e pulizia sono eseguite automaticamente. Gli esempi eseguono più chiamate per la creazione di `CosmosContainer`. A ogni chiamata, viene addebitata alla sottoscrizione 1 ora di utilizzo per il livello di prestazioni della raccolta creata. 
> 
> 

## <a name="database-examples"></a>Esempi di database
Il file [Database CRUD Samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java) illustra come eseguire le attività seguenti. Per saperne di più sui database di Azure Cosmos prima di passare agli esempi seguenti, vedere l'articolo concettuale [Usare database, contenitori ed elementi](databases-containers-items.md). 

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Creare un database](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L76-L84) | CosmosClient.createDatabaseIfNotExists |
| [Leggere un database in base all'ID](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L86-L94) | CosmosClient.getDatabase |
| [Leggere tutti i database](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L96-L111) | CosmosClient.readAllDatabases |
| [Eliminare un database](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L113-L122) | CosmosDatabase.delete |

## <a name="collection-examples"></a>Esempi di raccolta
Il file [Collection CRUD Samples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) illustra come eseguire le attività seguenti. Per saperne di più sulle raccolte di Azure Cosmos prima di passare agli esempi seguenti, vedere l'articolo concettuale [Usare database, contenitori ed elementi](databases-containers-items.md).

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Creare una raccolta](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L94-106) | CosmosDatabase.createContainerIfNotExists |
| [Modificare le prestazioni configurate di una raccolta](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L108-116) | CosmosContainer.replaceProvisionedThroughput |
| [Ottenere una raccolta in base all'ID](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L118-126) | CosmosDatabase.getContainer |
| [Leggere tutte le raccolte in un database](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L128-143) | CosmosDatabase.readAllContainers |
| [Eliminare una raccolta](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L145-154) | CosmosContainer.delete |

## <a name="autoscale-collection-examples"></a>Esempi di raccolta con scalabilità automatica

Per altre informazioni prima di eseguire questi esempi, vedere queste istruzioni per abilitare la scalabilità automatica nell'[account](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-autoscale/) e nei [database e contenitori](https://docs.microsoft.com/azure/cosmos-db/provision-throughput-autoscale).

Il file [autoscale Database CRUD Samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscaledatabasecrud/sync/AutoscaleDatabaseCRUDQuickstart.java) illustra come eseguire le attività seguenti.

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Creare un database con la velocità effettiva massima a scalabilità automatica specificata](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscaledatabasecrud/sync/AutoscaleDatabaseCRUDQuickstart.java#L76-L87) | CosmosClient.createDatabase<br>ThroughputProperties.createAutoscaledThroughput |

Il file [autoscale Collection CRUD Samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java) illustra come eseguire le attività seguenti. 

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Crea una raccolta con la velocità effettiva massima a scalabilità automatica specificata](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L95-L107) | CosmosDatabase.createContainerIfNotExists |
| [Cambiare la velocità effettiva massima a scalabilità automatica configurata di una raccolta](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L109-L117) | CosmosContainer.replaceThroughput |
| [Leggere la configurazione della velocità effettiva a scalabilità automatica di una raccolta](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L119-L130) | CosmosContainer.readThroughput |

## <a name="analytical-storage-collection-examples"></a>Esempi di raccolta di archiviazione analitica

Il file [Analytical storage Collection CRUD Samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/analyticalcontainercrud/sync/AnalyticalContainerCRUDQuickstart.java) illustra come eseguire le attività seguenti. Per informazioni sulle raccolte di Azure Cosmos prima di eseguire gli esempi seguenti, vedere Azure Cosmos DB Synapse e archivio analitico.

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Creare una raccolta](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/analyticalcontainercrud/sync/AnalyticalContainerCRUDQuickstart.java#L94-L109) | CosmosDatabase.createContainerIfNotExists |

## <a name="document-examples"></a>Esempi di documento
Il file [Document CRUD Samples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java) illustra come eseguire le attività seguenti. Per saperne di più sui documenti di Azure Cosmos prima di passare agli esempi seguenti, vedere l'articolo concettuale [Usare database, contenitori ed elementi](databases-containers-items.md).

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Creare un documento](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L127-L141) | CosmosContainer.createItem |
| [Leggere un documento in base all'ID](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L143-L154) | CosmosContainer.readItem |
| [Leggere tutti i documenti in una raccolta](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L156-171) | CosmosContainer.readAllItems |
| [Eseguire query per documenti](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L173-L187) | CosmosContainer.queryItems |
| [Sostituire un documento](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L189-L204) | CosmosContainer.replaceItem |
| [Upsert per un documento](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L206-L219) | CosmosContainer.upsertItem |
| [Eliminare un documento](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L302-L310) | CosmosContainer.deleteItem |
| [Sostituire documenti con verifica degli ETag condizionale](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L221-L261) | AccessCondition.setType<br>AccessCondition.setCondition |
| [Leggere il documento solo se è stato modificato](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L263-L300) | AccessCondition.setType<br>AccessCondition.setCondition |

## <a name="indexing-examples"></a>Esempi di indicizzazione
Il file [Collection CRUD Samples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) illustra come eseguire le attività seguenti. Per saperne di più sull'indicizzazione in Azure Cosmos DB prima di passare agli esempi seguenti, vedere gli articoli concettuali su [criteri di indicizzazione](index-policy.md), [tipi di indicizzazione](index-types.md) e [percorsi di indicizzazione](index-paths.md). 

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| Escludere un documento dall'indice | ExcludedIndex<br>IndexingPolicy |
| Usare l'indicizzazione differita | IndexingPolicy.IndexingMode |
| [Includere percorsi di documenti specificati nell'indice](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L142-L147) | IndexingPolicy.IncludedPaths |
| [Escludere percorsi di documenti specificati dall'indice](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L149-L154) | IndexingPolicy.ExcludedPaths |
| [Creare un indice composto](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L170-L188) | IndexingPolicy.setCompositeIndexes<br>CompositePath |
| Forzare un'operazione di analisi dell'intervallo su un percorso indicizzato hash | FeedOptions.EnableScanInQuery |
| Usare gli indici di intervallo sulle stringhe | IndexingPolicy.IncludedPaths<br>RangeIndex |
| Eseguire una trasformazione di indice | - |
| [Creare un indice geospaziale](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L156-L168) | IndexingPolicy.setSpatialIndexes<br>SpatialSpec<br>SpatialType |

Per altre informazioni sull'indicizzazione, vedere [Criteri di indicizzazione di Azure Cosmos DB](index-policy.md).

## <a name="query-examples"></a>Esempi di query
Il file [Query Samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java) illustra come eseguire le attività seguenti usando la grammatica di query SQL. Per informazioni sulle query SQL in Azure Cosmos DB prima di passare agli esempi seguenti, vedere [Esempi di query SQL per Azure Cosmos DB](how-to-sql-query.md). 

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Eseguire query per tutti i documenti](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L194-L198) | CosmosContainer.queryItems |
| [Eseguire query relative all'uguaglianza con ==](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L279-183) | CosmosContainer.queryItems |
| [Eseguire query relative alla disuguaglianza con != e NOT](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L285-L293) | CosmosContainer.queryItems |
| [Eseguire query usando operatori di intervallo come >, <, >=, <=](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L295-L300) | CosmosContainer.queryItems |
| [Eseguire query usando operatori di intervallo su stringhe](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L302-L307) | CosmosContainer.queryItems |
| [Eseguire query con ORDER BY](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L309-L314) | CosmosContainer.queryItems |
| [Eseguire query con DISTINCT](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L316-L321) | CosmosContainer.queryItems |
| [Eseguire query con funzioni di aggregazione](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L323-L331) | CosmosContainer.queryItems |
| [Utilizzare i documenti secondari](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L333-L341) | CosmosContainer.queryItems |
| [Eseguire query con join all'interno del documento](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L343-L365) | CosmosContainer.queryItems |
| [Eseguire query con operatori di stringa, matematici e di matrice](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L367-L378) | CosmosContainer.queryItems |
| [seguire query con SQL con parametri mediante SqlQuerySpec](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L380-L409) |CosmosContainer.queryItems |
| [Query con paging esplicito](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L200-L254) | CosmosContainer.queryItems |
| [Raccolte partizionate di query in parallelo](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L256-L277) | CosmosContainer.queryItems |
| Eseguire una query con ORDER BY per le raccolte partizionate | CosmosContainer.queryItems |

## <a name="change-feed-examples"></a>Esempi di feed di modifiche 
Il file [Change Feed Processor Sample](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java) illustra come eseguire le operazioni seguenti. Per informazioni sul feed di modifiche in Azure Cosmos DB prima di passare agli esempi seguenti, vedere [Lettura del feed di modifiche in Azure Cosmos DB](read-change-feed.md) e [Processore dei feed di modifiche](change-feed-processor.md).

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Funzionalità di base del feed di modifiche](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java#L50-L120) |ChangeFeedProcessor.changeFeedProcessorBuilder |
| Leggere il feed di modifiche da un'ora specifica | ChangeFeedProcessor.changeFeedProcessorBuilder |
| [Leggere il feed di modifiche dall'inizio](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java#L50-L120) | - |

## <a name="server-side-programming-examples"></a>Esempi di programmazione lato server

Il file [Stored Procedure Sample](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java) illustra come eseguire le operazioni seguenti. Per informazioni sulla programmazione lato server in Azure Cosmos DB prima di passare agli esempi seguenti, vedere [Stored procedure, trigger e funzioni definite dall'utente](stored-procedures-triggers-udfs.md).

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Creare una stored procedure](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L124-L143) | CosmosScripts.createStoredProcedure |
| [Eseguire una stored procedure](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L160-L174) | CosmosStoredProcedure.execute |
| [Eliminare un stored procedure](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L176-L186) | CosmosStoredProcedure.delete |

## <a name="user-management-examples"></a>Esempi di gestione utenti
Il file User Management Sample illustra come eseguire le attività seguenti:

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| Creare un utente | - |
| Impostare autorizzazioni per una raccolta o un documento | - |
| Ottenere un elenco di autorizzazioni di un utente |- |