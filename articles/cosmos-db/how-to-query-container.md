---
title: Eseguire query sui contenitori in Azure Cosmos DB
description: Informazioni su come eseguire query sui contenitori in Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: f7536b5d0815351d2e6cb67705060d2e1046c970
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55857873"
---
# <a name="query-an-azure-cosmos-container"></a>Eseguire una query su un contenitore in Azure Cosmos

Questo articolo illustra come eseguire una query su un contenitore (raccolta, grafo o tabella) in Azure Cosmos DB.

## <a name="in-partition-query"></a>Query in partizioni

Una query eseguita sui dati dei contenitori, per cui è specificato un filtro della chiave di partizione, viene gestita automaticamente da Azure Cosmos DB. La query viene instradata alle partizioni corrispondenti ai valori della chiave di partizione specificati nel filtro. Ad esempio, la query seguente viene instradata alla partizione `DeviceId`, che contiene tutti i documenti corrispondenti al valore chiave di partizione `XMS-0001`.

```csharp
// Query using partition key into a class called, DeviceReading
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```

## <a name="cross-partition-query"></a>Query tra partizioni

La query seguente non ha un filtro per la chiave di partizione (`DeviceId`) e ne viene effettuato il fan-out a tutte le partizioni in cui viene eseguita a fronte dell'indice della partizione. Per eseguire una query tra partizioni, impostare `EnableCrossPartitionQuery` su true (o `x-ms-documentdb-query-enablecrosspartition` nell'API REST).

```csharp
// Query across partition keys into a class called, DeviceReading
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

Azure Cosmos DB supporta l'uso delle funzioni di aggregazione COUNT, MIN, MAX e AVG con i contenitori tramite SQL. L'uso delle funzioni di aggregazione con i contenitori è previsto a partire dalla versione dell'SDK 1.12.0 e successive. Le query devono includere un unico operatore di aggregazione e un singolo valore nella proiezione.

## <a name="parallel-cross-partition-query"></a>Query tra partizioni in parallelo

Le versioni degli SDK di Azure Cosmos DB 1.9.0 e successive supportano le opzioni di esecuzione di query in parallelo. Le query tra partizioni in parallelo consentono di eseguire query tra partizioni a bassa latenza. Ad esempio, la query seguente è configurata in modo da essere eseguita in parallelo tra le partizioni.

```csharp
// Cross-partition Order By Query with parallel execution
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),  
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```

È possibile gestire l'esecuzione di query in parallelo, ottimizzando i parametri seguenti:

- **MaxDegreeOfParallelism**: imposta il numero massimo di connessioni di rete simultanee alle partizioni del contenitore. Se si imposta questa proprietà su -1, il grado di parallelismo viene gestito dall'SDK. Se  `MaxDegreeOfParallelism`  non è specificato o è impostato su 0, ovvero il valore predefinito, esisterà una sola connessione di rete alle partizioni del contenitore.

- **MaxBufferedItemCount**: bilancia la latenza delle query rispetto all'utilizzo della memoria sul lato client. Se questa opzione viene omessa o è impostata su -1, il numero di elementi memorizzati nel buffer durante l'esecuzione di query in parallelo viene gestito dall'SDK.

Con lo stesso stato della raccolta, una query in parallelo restituisce i risultati nello stesso ordine di un'esecuzione seriale. Quando si esegue una query tra partizioni che include operatori di ordinamento (ORDER BY, TOP), l'SDK di Azure Cosmos DB invia la query in parallelo tra le partizioni. Unisce i risultati parzialmente ordinati nel lato client per produrre risultati ordinati globalmente.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti per altre informazioni sul partizionamento in Azure Cosmos DB:

- [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Partizionamento in Azure Cosmos DB)
- [Synthetic partition keys in Azure Cosmos DB](synthetic-partition-keys.md) (Chiavi di partizione sintetiche in Azure Cosmos DB)
