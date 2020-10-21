---
title: Trovare l'addebito delle unità richiesta (UR) per una query di API Tabella in Azure Cosmos DB
description: Informazioni su come trovare il costo dell'unità richiesta (UR) per le query API Tabella eseguite su un contenitore di Azure Cosmos. È possibile usare i linguaggi portale di Azure, .NET, Java, Python e Node.js per trovare l'addebito delle UR.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: beca38e46dd2223211fb403d1e1734a5d406075b
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284423"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-table-api"></a>Trovare l'addebito delle unità richiesta per le operazioni eseguite in Azure Cosmos DB API Tabella

Azure Cosmos DB supporta un'ampia gamma di API, come SQL, MongoDB, Cassandra, Gremlin e Tabella. Ogni API ha il proprio set di operazioni di database, da semplici operazioni di lettura e scrittura puntuali a query complesse. Ogni operazione di database utilizza le risorse di sistema a seconda della complessità.

Il costo di tutte le operazioni di database viene normalizzato da Azure Cosmos DB ed è espresso in termini di unità richiesta (o in breve UR). È possibile considerare le UR come una valuta delle prestazioni astraendo le risorse di sistema, ad esempio CPU, IOPS e memoria, necessarie per eseguire le operazioni del database supportate da Azure Cosmos DB. I costi vengono sempre misurati in UR, indipendentemente dall'API usata per interagire con il contenitore Azure Cosmos. Il fatto che l'operazione di database sia una scrittura, un punto di lettura o una query, i costi vengono sempre misurati in ur. Per altre informazioni, vedere l'articolo relativo alle [unità richiesta e alle relative considerazioni](request-units.md) .

Questo articolo illustra i diversi modi in cui è possibile trovare il consumo di [unità richiesta](request-units.md) (UR) per qualsiasi operazione eseguita su un contenitore in Azure Cosmos DB API tabella. Se si usa un'API diversa, vedere gli articoli [API per MongoDB](find-request-unit-charge-mongodb.md), [API Cassandra](find-request-unit-charge-cassandra.md), [API Gremlin](find-request-unit-charge-gremlin.md)e [API SQL](find-request-unit-charge.md) per trovare il costo ur/s.

## <a name="use-the-net-sdk"></a>Usare .NET SDK

Attualmente l'unico SDK che restituisce l'addebito delle UR per le operazioni di tabella è [.NET Standard SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table). L'oggetto `TableResult` espone una proprietà `RequestCharge` che viene popolata dall'SDK quando viene usata con l'API Tabella di Azure Cosmos DB:

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

Per altre informazioni, vedere [Guida introduttiva: creare un'app API tabella usando .NET SDK e Azure Cosmos DB](create-table-dotnet.md).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come ottimizzare il consumo di UR, vedere questi articoli:

* [Velocità effettiva e unità richiesta in Azure Cosmos DB](request-units.md)
* [Ottimizzare il costo della velocità effettiva con provisioning in Azure Cosmos DB](optimize-cost-throughput.md)
* [Ottimizzare il costo delle query in Azure Cosmos DB](optimize-cost-queries.md)