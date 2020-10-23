---
title: Trovare l'addebito delle unità richiesta (UR) per le query dell'API Gremlin in Azure Cosmos DB
description: Informazioni su come trovare l'addebito delle unità richiesta (UR) per le query Gremlin eseguite su un contenitore di Azure Cosmos. È possibile usare i driver di portale di Azure, .NET e Java per trovare l'addebito delle UR.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 43685440de236d30698922b3b16a820eb935ae7c
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284439"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-gremlin-api"></a>Trovare l'addebito delle unità richiesta per le operazioni eseguite in Azure Cosmos DB API Gremlin

Azure Cosmos DB supporta un'ampia gamma di API, come SQL, MongoDB, Cassandra, Gremlin e Tabella. Ogni API ha il proprio set di operazioni di database, da semplici operazioni di lettura e scrittura puntuali a query complesse. Ogni operazione di database utilizza le risorse di sistema a seconda della complessità.

Il costo di tutte le operazioni di database viene normalizzato da Azure Cosmos DB ed è espresso in termini di unità richiesta (o in breve UR). È possibile considerare le UR come una valuta delle prestazioni astraendo le risorse di sistema, ad esempio CPU, IOPS e memoria, necessarie per eseguire le operazioni del database supportate da Azure Cosmos DB. I costi vengono sempre misurati in UR, indipendentemente dall'API usata per interagire con il contenitore Azure Cosmos. Il fatto che l'operazione di database sia una scrittura, un punto di lettura o una query, i costi vengono sempre misurati in ur. Per altre informazioni, vedere l'articolo relativo alle [unità richiesta e alle relative considerazioni](request-units.md) .

Questo articolo illustra i diversi modi in cui è possibile trovare il consumo di [unità richiesta](request-units.md) (UR) per qualsiasi operazione eseguita su un contenitore in Azure Cosmos DB API Gremlin. Se si usa un'API diversa, vedere l'articolo [relativo all'API per MongoDB](find-request-unit-charge-mongodb.md), [API Cassandra](find-request-unit-charge-cassandra.md), l' [api SQL](find-request-unit-charge.md)e gli articoli [API tabella](find-request-unit-charge-table.md) per trovare il costo di Ur/s.

Le intestazioni restituite dall'API Gremlin vengono mappate agli attributi di stato personalizzati attualmente esposti da Gremlin .NET e Java SDK. L'addebito della richiesta è disponibile sotto la chiave `x-ms-request-charge`. Quando si usa l'API Gremlin, sono disponibili diverse opzioni per trovare il consumo di UR per un'operazione con un contenitore di Azure Cosmos.

## <a name="use-the-azure-portal"></a>Usare il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account di Azure Cosmos](create-graph-gremlin-console.md#create-a-database-account) e inserirvi dati oppure selezionare un account esistente che contiene già dati.

1. Passare al riquadro **Esplora dati** e quindi selezionare il contenitore da usare.

1. Immettere una query valida, quindi selezionare **Esegui query Gremlin**.

1. Fare clic su **Statistiche query** per visualizzare l'addebito effettivo per la richiesta eseguita.

:::image type="content" source="./media/find-request-unit-charge/portal-gremlin-query.png" alt-text="Screenshot per ottenere un addebito per le richieste di query Gremlin nella portale di Azure":::

## <a name="use-the-net-sdk-driver"></a>Usare il driver .NET SDK

Quando si usa [Gremlin.NET SDK](https://www.nuget.org/packages/Gremlin.Net/), gli attributi di stato sono disponibili sotto la proprietà `StatusAttributes` dell'oggetto `ResultSet<>`:

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

Per altre informazioni, vedere [Guida introduttiva: creare un'applicazione .NET Framework o Core usando un account API Azure Cosmos DB Gremlin](create-graph-dotnet.md).

## <a name="use-the-java-sdk-driver"></a>Usare il driver Java SDK

Quando si usa [Gremlin Java SDK](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver), è possibile recuperare gli attributi di stato chiamando il metodo `statusAttributes()` per l'oggetto `ResultSet`:

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

Per altre informazioni, vedere [Guida introduttiva: creare un database a grafo in Azure Cosmos DB usando Java SDK](create-graph-java.md).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come ottimizzare il consumo di UR, vedere questi articoli:

* [Velocità effettiva e unità richiesta in Azure Cosmos DB](request-units.md)
* [Ottimizzare il costo della velocità effettiva con provisioning in Azure Cosmos DB](optimize-cost-throughput.md)
* [Ottimizzare il costo delle query in Azure Cosmos DB](optimize-cost-queries.md)