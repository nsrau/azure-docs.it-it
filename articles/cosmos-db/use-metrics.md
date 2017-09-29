---
title: Eseguire il monitoraggio e il debug con le metriche in Azure Cosmos DB | Microsoft Docs
description: Usare le metriche in Azure Cosmos DB per eseguire il debug di problemi comuni e monitorare il database.
keywords: Metriche
services: cosmos-db
author: gnot
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: govindk
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: e6399831fe7c6cc727e92b13719df3b69e9981bf
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="monitoring-and-debugging-with-metrics-in-azure-cosmos-db"></a>Eseguire il monitoraggio e il debug con le metriche in Azure Cosmos DB

Azure Cosmos DB offre metriche per velocità effettiva, archiviazione, coerenza, disponibilità e latenza. Il [portale di Azure](https://portal.azure.com) offre una vista aggregata di tali metriche; per metriche più granulari sono disponibili l'SDK del client e [i log di diagnostica](./logging.md).

Per ottenere una panoramica delle nuove metriche e apprendere come trovare partizioni critiche nel database, guardare il video seguente di Azure Friday:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Cosmos-DB-Get-the-Most-Out-of-Provisioned-Throughput/player]
> 

Questo articolo illustra dettagliatamente i casi d'uso comuni e come usare le metriche di Azure Cosmos DB per analizzare ed eseguire il debug di questi problemi. Le metriche vengono raccolte ogni cinque minuti e vengono conservate per sette giorni.

## <a name="understanding-how-many-requests-are-succeeding-or-causing-errors"></a>Scoprire il numero di richieste che riesce o causa errori

Per iniziare, accedere al [portale di Azure](https://portal.azure.com) e passare al pannello **Metriche**. Nel pannello trovare il grafico **Numero di richieste che hanno superato la capacità per 1 minuto**. Questo grafico mostra un minuto delle richieste totali per minuto segmentate in base al codice di stato. Per altre informazioni sui codici di stato HTTP, vedere [HTTP Status Codes for Azure Cosmos DB](https://docs.microsoft.com/rest/api/documentdb/http-status-codes-for-documentdb) (Codici di stato HTTP per Azure Cosmos DB).

Il codice di stato di errore più comune è 429 relativo ai limiti, che indica che le richieste inviate ad Azure Cosmos DB superano la velocità effettiva di provisioning. La soluzione più comune consiste nell'[aumentare il numero di UR](./set-throughput.md) per la raccolta specificata.

![Numero di richieste al minuto](media/use-metrics/metrics-12.png)

## <a name="determining-the-throughput-distribution-across-partitions"></a>Determinare la distribuzione della velocità effettiva tra le partizioni

Avere una buona cardinalità delle chiavi di partizione è essenziale per qualsiasi applicazione scalabile. Per determinare la distribuzione della velocità effettiva di una raccolta partizionata suddivisa dalle partizioni, passare al **pannello Metriche** nel [portale di Azure](https://portal.azure.com). Nella scheda **Velocità effettiva** la scomposizione di archiviazione viene mostrata nel grafico **Numero massimo di unità richiesta al secondo utilizzate da ogni partizione fisica**. Il grafico seguente illustra un esempio di distribuzione ridotta dei dati come evidenziato dalla partizione decrescente all'estrema sinistra. 

![Partizione singola che mostra un uso intenso alle 15:05](media/use-metrics/metrics-17.png)

Una distribuzione non uniforme della velocità effettiva può generare partizioni *critiche*, che possono ridurre le richieste e potrebbero richiedere una nuova ripartizione. Per altre informazioni sul partizionamento in Azure Cosmos DB, vedere [Partizionamento e ridimensionamento in Azure Cosmos DB](./partition-data.md).

## <a name="determining-the-storage-distribution-across-partitions"></a>Determinare la distribuzione dell'archiviazione tra le partizioni

Avere una buona cardinalità della partizione è essenziale per qualsiasi applicazione scalabile. Per determinare la distribuzione della velocità effettiva di una raccolta partizionata suddivisa dalle partizioni, passare al pannello Metriche nel [portale di Azure](https://portal.azure.com). Nella scheda Velocità effettiva la scomposizione di archiviazione viene mostrata nel grafico Numero massimo di unità richiesta al secondo usate da ogni partizione fisica. Il grafico seguente illustra una distribuzione ridotta dei dati come evidenziato dalla partizione decrescente all'estrema sinistra. 

![Esempio di distribuzione dei dati ridotta](media/use-metrics/metrics-07.png)

È possibile capire quale chiave di partizione riduce la distribuzione facendo clic sulla partizione nel grafico. 

![Chiave di partizione che riduce la distribuzione](media/use-metrics/metrics-05.png)

Dopo aver identificato la chiave di partizione che riduce la distribuzione, è possibile che sia necessario ripartizionare la raccolta con una chiave di partizione più distribuita. Per altre informazioni sul partizionamento in Azure Cosmos DB, vedere [Partizionamento e ridimensionamento in Azure Cosmos DB](./partition-data.md).

## <a name="comparing-data-size-against-index-size"></a>Confronto tra dimensioni dei dati e dimensioni dell'indice

In Azure Cosmos DB lo spazio di archiviazione totale usato risulta dalla combinazione delle dimensioni dei dati e delle dimensioni dell'indice. In genere, le dimensioni dell'indice sono una frazione delle dimensioni dei dati. Nel pannello Metriche nel [portale di Azure](https://portal.azure.com) la scheda Archiviazione mostra la suddivisione dell'uso dello spazio di archiviazione in base ai dati e all'indice. Immagine (forse) In alternativa, nell'SDK è possibile trovare l'uso corrente della memoria tramite una lettura della raccolta.
```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll")); 
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
``` 
Se si desidera risparmiare spazio degli indici, è possibile modificare i [criteri di indicizzazione](./indexing-policies.md).

## <a name="debugging-why-queries-are-running-slow"></a>Eseguire il degug sul motivo per cui l'esecuzione delle query risulta rallentata

Nell'SDK dell'API di DocumentDB Azure Cosmos DB presenta le statistiche di esecuzione delle query. 

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
 “SELECT * FROM c WHERE c.city = ‘Seattle’”, 
 new FeedOptions 
 { 
 PopulateQueryMetrics = true, 
 MaxItemCount = -1, 
 MaxDegreeOfParallelism = -1, 
 EnableCrossPartitionQuery = true 
 }).AsDocumentQuery();
FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id 
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;
```

*QueryMetrics* offre informazioni dettagliate sulla durata dell'esecuzione di ogni componente della query. La causa più comune di un'esecuzione di lunga durata delle query è l'analisi, poiché la query non è riuscita a usare gli indici. Questa può essere risolta migliorando la condizione di filtro.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come monitorare ed eseguire il debug dei problemi con le metriche presenti nel portale di Azure, è possibile avere altre informazioni su come migliorare le prestazioni del database leggendo gli articoli seguenti:

* [Test delle prestazioni e della scalabilità con Azure Cosmos DB](performance-testing.md)
* [Suggerimenti sulle prestazioni per Azure Cosmos DB](performance-tips.md)

