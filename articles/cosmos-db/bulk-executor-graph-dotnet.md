---
title: Uso della libreria .NET BulkExecutor per i grafi per eseguire operazioni in blocco nell'API Gremlin di Azure Cosmos DB
description: Informazioni su come usare la libreria BulkExecutor per importare volumi elevati di dati dei grafi nel contenitore di API Gremlin di Azure Cosmos DB.
services: cosmos-db
keywords: grafo, gremlin, in blocco, bulkexecutor, migrazione, dati, cosmosdb, cosmos, database, importazione
author: luisbosquez
manager: kfile
editor: cgronlun
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.devlang: na
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: 39abf6d6da8a8035cf486ceb30b9c21186bbb925
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234420"
---
# <a name="using-the-graph-bulkexecutor-net-library-to-perform-bulk-operations-in-azure-cosmos-db-gremlin-api"></a>Uso della libreria .NET BulkExecutor per i grafi per eseguire operazioni in blocco nell'API Gremlin di Azure Cosmos DB

Questa esercitazione presenta le istruzioni sull'uso della libreria .NET BulkExecutor di Azure Cosmos DB per importare e aggiornare oggetti grafo nel contenitore di API Gremlin di Azure Cosmos DB. Questo processo usa la classe Graph nella [libreria BulkExecutor](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-overview) per creare oggetti vertice e arco a livello di codice e quindi inserirne diversi per ogni richiesta di rete. Questo comportamento può essere configurato tramite la libreria BulkExecutor per usare in modo ottimale risorse di database e di memoria locale.

Diversamente dall'invio di query Gremlin a un database, in cui il comando viene valutato e quindi eseguito uno per volta, L'uso della libreria BulkExecutor richiede invece la creazione e la convalida degli oggetti in locale. Dopo aver creato gli oggetti, la libreria permette di inviare oggetti grafo al servizio di database in sequenza. Usando questo metodo, le velocità di inserimento dati possono rivelarsi fino a 100 volte maggiori e di conseguenza questo è un metodo ideale per migrazioni dei dati iniziali o operazioni periodiche di spostamento dati. Altre informazioni sono disponibili nella pagina GitHub dell'[applicazione di esempio Graph BulkExecutor di Azure Cosmos DB](https://aka.ms/graph-bulkexecutor-sample).

## <a name="bulk-operations-with-graph-data"></a>Operazioni in blocco con dati sui grafi

La [raccolta BulkExecutor](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?view=azure-dotnet) contiene uno spazio dei nomi `Microsoft.Azure.CosmosDB.BulkExecutor.Graph` per fornire funzionalità per la creazione e l'importazione di oggetti grafo. 

Il processo seguente descrive come usare la migrazione dei dati per un contenitore di API Gremlin:
1. Recuperare record dall'origine dati.
2. Costruire oggetti `GremlinVertex` e `GremlinEdge` dai record ottenuti e aggiungerli in una struttura di dati `IEnumerable`. In questa parte dell'applicazione deve essere implementata la logica per rilevare e aggiungere relazioni, qualora l'origine dati non sia un database di grafi.
3. Usare il [metodo Graph BulkImportAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph.graphbulkexecutor.bulkimportasync?view=azure-dotnet) per inserire gli oggetti grafo nella raccolta.

Questo meccanismo migliora l'efficienza della migrazione dei dati rispetto all'uso di un client Gremlin. Questo vantaggio è dovuto al fatto che per l'inserimento di dati con Gremlin l'applicazione deve inviare una query per volta e ogni query deve essere convalidata, valutata e quindi eseguita per creare i dati. La libreria BulkExecutor gestirà la convalida nell'applicazione e invierà più oggetti grafo per volta per ogni richiesta di rete.

### <a name="creating-vertices-and-edges"></a>Creazione di vertici e archi

`GraphBulkExecutor` fornisce il metodo `BulkImportAsync` che richiede un elenco `IEnumerable` di oggetti `GremlinVertex` o `GremlinEdge`, entrambi definiti nello spazio dei nomi `Microsoft.Azure.CosmosDB.BulkExecutor.Graph.Element`. Nell'esempio gli archi e i vertici sono stati separati in due diverse attività di importazione di BulkExecutor. Vedere l'esempio seguente:

```csharp

IBulkExecutor graphbulkExecutor = new GraphBulkExecutor(documentClient, targetCollection);

BulkImportResponse vResponse = null;
BulkImportResponse eResponse = null;

try
{
    // Import a list of GremlinVertex objects
    vResponse = await graphbulkExecutor.BulkImportAsync(
            Utils.GenerateVertices(numberOfDocumentsToGenerate),
            enableUpsert: true,
            disableAutomaticIdGeneration: true,
            maxConcurrencyPerPartitionKeyRange: null,
            maxInMemorySortingBatchSize: null,
            cancellationToken: token);

    // Import a list of GremlinEdge objects
    eResponse = await graphbulkExecutor.BulkImportAsync(
            Utils.GenerateEdges(numberOfDocumentsToGenerate),
            enableUpsert: true,
            disableAutomaticIdGeneration: true,
            maxConcurrencyPerPartitionKeyRange: null,
            maxInMemorySortingBatchSize: null,
            cancellationToken: token);
}
catch (DocumentClientException de)
{
    Trace.TraceError("Document client exception: {0}", de);
}
catch (Exception e)
{
    Trace.TraceError("Exception: {0}", e);
}
```

Per altre informazioni sui parametri della libreria BulkExecutor, fare riferimento all'[argomento su BulkImportData per Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-dot-net#bulk-import-data-to-azure-cosmos-db).

Deve essere creata un'istanza del payload in oggetti `GremlinVertex` e `GremlinEdge`. Ecco come possono essere creati questi oggetti:

**Vertici**:
```csharp
// Creating a vertex
GremlinVertex v = new GremlinVertex(
    "vertexId",
    "vertexLabel");

// Adding custom properties to the vertex
v.AddProperty("customProperty", "value");

// Partitioning keys must be specified for all vertices
v.AddProperty("partitioningKey", "value");
```

**Archi**:
```csharp
// Creating an edge
GremlinEdge e = new GremlinEdge(
    "edgeId",
    "edgeLabel",
    "targetVertexId",
    "sourceVertexId",
    "targetVertexLabel",
    "sourceVertexLabel",
    "targetVertexPartitioningKey",
    "sourceVertexPartitioningKey");

// Adding custom properties to the edge
e.AddProperty("customProperty", "value");
```

> [!NOTE]
> L'utilità BulkExecutor non controlla automaticamente la presenza di vertici esistenti prima di aggiungere archi. Questo deve essere convalidato nell'applicazione prima di eseguire le attività BulkImport.

## <a name="sample-application"></a>Applicazione di esempio

### <a name="prerequisites"></a>Prerequisiti
* Visual Studio 2017 con il carico di lavoro di sviluppo di Azure. È possibile iniziare a usare [Visual Studio 2017 Community Edition](https://visualstudio.microsoft.com/downloads/) gratuitamente.
* Una sottoscrizione di Azure. È possibile creare un [account Azure gratuito qui](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cosmos-db). In alternativa, è possibile creare un account di database Cosmos DB nella pagina [Prova gratuitamente Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) senza una sottoscrizione di Azure.
* Database di API Gremlin di Azure Cosmos DB con una **raccolta illimitata**. Questa guida descrive come iniziare a usare l'[API Gremlin di Azure Cosmos DB in .NET](https://docs.microsoft.com/azure/cosmos-db/create-graph-dotnet).
* Git. Per altre informazioni, vedere la [pagina dei download di Git](https://git-scm.com/downloads).

### <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio
In questa esercitazione vengono presentati i passaggi iniziali usando l'[esempio Graph BulkExecutor di Azure Cosmos DB](https://aka.ms/graph-bulkexecutor-sample) ospitato in GitHub. Questa applicazione è costituita da una soluzione .NET che genera casualmente oggetti vertice e arco e quindi esegue inserimenti in blocco nell'account di database di grafi specificato. Per ottenere l'applicazione, eseguire il comando `git clone` seguente:

```bash
git clone https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started.git
```

Questo repository contiene l'esempio GraphBulkExecutor con i file seguenti:

File|DESCRIZIONE
---|---
`App.config`|Contiene i parametri specifici dell'applicazione e del database. Questo file deve essere innanzitutto modificato per connettersi alle raccolte e al database di destinazione.
`Program.cs`| Contiene la logica alla base della creazione della raccolta `DocumentClient`, della gestione delle pulizie e dell'invio delle richieste BulkExecutor.
`Util.cs`| Contiene una classe helper che include la logica alla base della generazione di dati di test e della verifica della presenza o meno del database e delle raccolte.

Nel file `App.config` possono essere specificati i valori di configurazione seguenti:

Impostazione|DESCRIZIONE
---|---
`EndPointUrl`|**Endpoint .NET SDK** che si trova nel pannello Panoramica dell'account di database di API Gremlin di Azure Cosmos DB. Ha questo formato: `https://your-graph-database-account.documents.azure.com:443/`
`AuthorizationKey`|Chiave primaria o secondaria elencata nell'account Azure Cosmos DB. Per altre informazioni, vedere [Protezione dell'accesso ai dati di Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#master-keys)
`DatabaseName`, `CollectionName`|**Nomi della raccolta e del database di destinazione**. Quando `ShouldCleanupOnStart` è impostato su `true`, questi valori, insieme a `CollectionThroughput`, verranno usati per eliminare il database e la raccolta e per crearne di nuovi. Analogamente, se `ShouldCleanupOnFinish` è impostato su `true`, verranno usati per eliminare il database al termine dell'inserimento. La raccolta di destinazione deve essere una **raccolta illimitata**.
`CollectionThroughput`|Impostazione usata per creare una nuova raccolta se l'opzione `ShouldCleanupOnStart` è impostata su `true`.
`ShouldCleanupOnStart`|Elimina l'account di database e le raccolte prima dell'esecuzione del programma e quindi ne crea di nuovi con i valori `DatabaseName`, `CollectionName` e `CollectionThroughput`.
`ShouldCleanupOnFinish`|Elimina l'account di database e le raccolte con i valori di `DatabaseName` e `CollectionName` specificati dopo l'esecuzione del programma.
`NumberOfDocumentsToImport`|Determina il numero di vertici e archi di test generati nell'esempio. Questo numero viene applicato sia ai vertici sia agli archi.
`NumberOfBatches`|Determina il numero di vertici e archi di test generati nell'esempio. Questo numero viene applicato sia ai vertici sia agli archi.
`CollectionPartitionKey`|Impostazione usata per creare i vertici e gli archi di test, in cui questa proprietà viene assegnata automaticamente. Viene usata anche per la ricreazione del database e delle raccolte se l'opzione `ShouldCleanupOnStart` è impostata su `true`.

### <a name="run-the-sample-application"></a>Eseguire l'applicazione di esempio

1. Aggiungere i parametri di configurazione del database specifici in `App.config`. Questo file verrà usato per creare un'istanza DocumentClient. Se il database e il contenitore non sono stati ancora creati, verranno creati automaticamente.
2. Eseguire l'applicazione. Verrà chiamato `BulkImportAsync` due volte, una per importare i vertici e una per importare gli archi. Gli oggetti che generano un errore durante l'inserimento vengono aggiunti a `.\BadVertices.txt` o `.\BadEdges.txt`.
3. Valutare i risultati eseguendo una query sul database di grafi. Se l'opzione `ShouldCleanupOnFinish` è impostata su true, il database viene automaticamente eliminato.

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sul pacchetto Nuget e sulle note sulla versione della libreria .NET dell'executor in blocco, vedere i [dettagli sull'SDK dell'executor in blocco](sql-api-sdk-bulk-executor-dot-net.md). 
* Vedere [Suggerimenti per incrementare le prestazioni](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-dot-net#performance-tips) per ottimizzare ulteriormente l'utilizzo di BulkExecutor.
* Vedere l'[articolo di riferimento su BulkExecutor.Graph](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?view=azure-dotnet) per altre informazioni sulle classi e sui metodi definiti in questo spazio dei nomi.
