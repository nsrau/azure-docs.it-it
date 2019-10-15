---
title: "Azure Cosmos DB: esempi di .NET (Microsoft.Azure.Cosmos) per l'API SQL"
description: Esempi di C# .NET V3 SDK in GitHub per attività comuni usando l'API SQL di Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2019
ms.author: sngun
ms.openlocfilehash: f6104fba45e64ea6cd47f3b03ae0cde4049f9538
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170813"
---
# <a name="azure-cosmos-dbnet-v3-sdk-microsoftazurecosmos-examples-for-the-sql-api"></a>Azure Cosmos DB: esempi di .NET V3 SDK (Microsoft.Azure.Cosmos) per l'API SQL

> [!div class="op_single_selector"]
> * [Esempi di .NET V2 SDK](sql-api-dotnet-samples.md)
> * [Esempi di .NET V3 SDK](sql-api-dotnet-v3sdk-samples.md)
> * [Esempi di Java](sql-api-java-samples.md)
> * [Esempi di Java Async](sql-api-async-java-samples.md)
> * [Esempi di Node.js](sql-api-nodejs-samples.md)
> * [Esempi di Python](sql-api-python-samples.md)
> * [Raccolta di esempi di codice di Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
>
>

Il repository GitHub [azure-cosmos-dotnet-v3](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage) include le più recenti soluzioni di esempio .NET per eseguire CRUD e altre operazioni comuni sulle risorse di Azure Cosmos DB. Se si ha familiarità con la versione precedente di .NET SDK, i termini raccolta e documento saranno già noti. Poiché Azure Cosmos DB supporta più modelli di API, nella versione 3.0 di .NET SDK vengono usati i termini generici "contenitore" ed "elemento". Un contenitore può essere una raccolta, un grafo o una tabella. Un elemento può essere un documento, un arco/vertice o una riga ed è il contenuto all'interno di un contenitore. Questo articolo include:

* Collegamenti alle attività in ogni file di progetto C# di esempio.
* Collegamenti al contenuto di riferimento sulle API correlato.

## <a name="prerequisites"></a>Prerequisiti

Visual Studio 2019 con il flusso di lavoro di sviluppo di Azure installato

- È possibile scaricare e usare l'edizione **gratuita** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Durante l'installazione di Visual Studio abilitare **Sviluppo di Azure**.

   Il [pacchetto NuGet Microsoft.Azure.cosmos](https://www.nuget.org/packages/Microsoft.Azure.cosmos/)

Una sottoscrizione di Azure o un account di prova gratuito di Cosmos DB

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- È possibile [attivare i vantaggi della sottoscrizione Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Con la sottoscrizione Visual Studio, ogni mese si accumulano crediti che è possibile usare per i servizi di Azure a pagamento.
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

> [!NOTE]
> Gli esempi sono indipendenti ed eseguono autonomamente attività di configurazione e pulizia. Con ogni occorrenza viene addebitata alla sottoscrizione un'ora di utilizzo nel livello di prestazioni del contenitore.
> 

## <a name="database-examples"></a>Esempi di database

Il metodo [RunDatabaseDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L65-L91) del progetto di esempio *DatabaseManagement* illustra come svolgere le attività seguenti. Per informazioni sui database di Azure Cosmos prima di eseguire questi esempi, vedere [Usare database, contenitori ed elementi](databases-containers-items.md).

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Creare un database](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L68) |[CosmosClient.CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync?view=azure-dotnet) |
| [Leggere un database in base all'ID](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L80) |[Database.ReadAsync](/dotnet/api/microsoft.azure.cosmos.database.readasync?view=azure-dotnet) |
| [Leggere tutti i database per un account](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L96) |[CosmosClient.GetDatabaseQueryIterator](/dotnet/api/microsoft.azure.cosmos.cosmosclient.getdatabasequeryiterator?view=azure-dotnet) |
| [Eliminare un database](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L106) |[Database.DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync?view=azure-dotnet) |

## <a name="container-examples"></a>Esempi di contenitore

Il metodo [RunContainerDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L69-L89) del progetto *ContainerManagement* di esempio illustra come eseguire queste attività. Per informazioni sui contenitori di Azure Cosmos prima di eseguire questi esempi, vedere [Usare database, contenitori ed elementi](databases-containers-items.md).

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Creare un contenitore](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L97-L107) |[Database.CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet) |
| [Creare un contenitore con un criterio di indicizzazione personalizzato](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L111-L127) |[Database.CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet) |
| [Modificare le prestazioni configurate di un contenitore](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L149-L171) |[Container.ReplaceThroughputAsync](/dotnet/api/microsoft.azure.cosmos.container.replacethroughputasync?view=azure-dotnet) |
| [Ottenere un contenitore in base all'ID](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L176-L185) |[Container.ReadContainerAsync](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.cosmos.container.readcontainerasync?view=azure-dotnet) |
| [Leggere tutti i contenitori in un database](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L193-L205) |[Database.GetContainerQueryIterator](/dotnet/api/microsoft.azure.cosmos.database.getcontainerqueryiterator?view=azure-dotnet) |
| [Eliminare un contenitore](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L213-L2018) |[Container.DeleteContainerAsync](/dotnet/api/microsoft.azure.cosmos.container.deletecontainerasync?view=azure-dotnet) |

## <a name="item-examples"></a>Esempi di elementi

Il metodo [RunItemsDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L119-L130) del progetto *ItemManagement* di esempio illustra come eseguire queste attività. Per informazioni sugli elementi di Azure Cosmos prima di eseguire questi esempi, vedere [Usare database, contenitori ed elementi](databases-containers-items.md).

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Creare un elemento](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L161-L200) |[Container.CreateItemAsync](/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet) |
| [Leggere un elemento in base all'ID](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L203-L241) |[container.ReadItemAsync](/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet) |
| [Eseguire query per ottenere elementi](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L244-L320) |[container.GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet) |
| [Sostituire un elemento](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L323-L363) |[container.ReplaceItemAsync](/dotnet/api/microsoft.azure.cosmos.container.replaceitemasync?view=azure-dotnet) |
| [Upsert di un elemento](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L366-L411) |[container.UpsertItemAsync](/dotnet/api/microsoft.azure.cosmos.container.upsertitemasync?view=azure-dotnet) |
| [Eliminare un elemento](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L414-L424) |[container.DeleteItemAsync](/dotnet/api/microsoft.azure.cosmos.container.deleteitemasync?view=azure-dotnet) |
| [Sostituire un elemento con il controllo condizionale degli ETag](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L585-L674) |[RequestOptions.IfMatchEtag](/dotnet/api/microsoft.azure.cosmos.requestoptions.ifmatchetag?view=azure-dotnet) |

## <a name="indexing-examples"></a>Esempi di indicizzazione

Il metodo [RunIndexDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L108-L122) del progetto *IndexManagement* di esempio illustra come eseguire queste attività. Per informazioni sull'indicizzazione in Azure Cosmos DB prima di eseguire questi esempi, vedere i [criteri di indicizzazione](index-policy.md), i [tipi di indice](index-types.md) e i [percorsi degli indici](index-paths.md). 

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Escludere un elemento dall'indice](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L130-L186) |[IndexingDirective.Exclude](/dotnet/api/microsoft.azure.cosmos.indexingdirective?view=azure-dotnet) |
| [Usare l'indicizzazione differita](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L198-L220) |[IndexingPolicy.IndexingMode](/dotnet/api/microsoft.azure.cosmos.indexingpolicy.indexingmode?view=azure-dotnet) |
| [Escludere percorsi di elementi specificati dall'indice](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L230-L297) |[IndexingPolicy.ExcludedPaths](/dotnet/api/microsoft.azure.cosmos.indexingpolicy.excludedpaths?view=azure-dotnet) |

## <a name="query-examples"></a>Esempi di query

Il metodo [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L76-L96) del progetto *Queries* di esempio illustra come eseguire queste attività usando la grammatica di query SQL, il provider LINQ con una query e con l'espressione Lambda. Per informazioni sulle query SQL in Azure Cosmos DB prima di passare agli esempi seguenti, vedere [Esempi di query SQL per Azure Cosmos DB](how-to-sql-query.md).

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Eseguire query sugli elementi da una partizione singola](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L154-L186) |[container.GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet) |
| [Eseguire query sugli elementi da più partizioni](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L215-L275) |[container.GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet) |
| [Eseguire query usando un'istruzione SQL](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L189-L212) |[container.GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet) |

## <a name="change-feed-examples"></a>Esempi di feed di modifiche

Il metodo [RunBasicChangeFeed](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L91-L119) del progetto *ChangeFeed* di esempio illustra come eseguire queste attività. Per informazioni sul feed di modifiche in Azure Cosmos DB prima di passare agli esempi seguenti, vedere [Lettura del feed di modifiche in Azure Cosmos DB](read-change-feed.md) e [Processore dei feed di modifiche](change-feed-processor.md).

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Funzionalità di base del feed di modifiche](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L91-L119) |[Container.GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder?view=azure-dotnet) |
| [Leggere il feed di modifiche da un'ora specifica](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L127-L162) |[Container.GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder?view=azure-dotnet) |
| [Leggere il feed di modifiche dall'inizio](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L170-L198) |[Container.GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder?view=azure-dotnet) |
| [Eseguire la migrazione dal processore dei feed di modifiche in V3 SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L256-L333) |[Container.GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder?view=azure-dotnet) |

## <a name="server-side-programming-examples"></a>Esempi di programmazione lato server

Il metodo [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L72-L102) del progetto *ServerSideScripts* di esempio illustra come eseguire queste attività. Per informazioni sulla programmazione lato server in Azure Cosmos DB prima di passare agli esempi seguenti, vedere [Stored procedure, trigger e funzioni definite dall'utente](stored-procedures-triggers-udfs.md).

| Attività | Informazioni di riferimento sulle API |
| --- | --- |
| [Creare una stored procedure](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L116) |[Scripts.CreateStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.createstoredprocedureasync?view=azure-dotnet) |
| [Eseguire una stored procedure](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L135) |[Scripts.ExecuteStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.executestoredprocedureasync?view=azure-dotnet) |
| [Eliminare un stored procedure](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L351) |[Scripts.DeleteStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.deletestoredprocedureasync?view=azure-dotnet) |