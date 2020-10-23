---
title: Trovare l'addebito delle unità richiesta (UR) per una query di API Cassandra in Azure Cosmos DB
description: Informazioni su come trovare il costo dell'unità richiesta (UR) per le query Cassandra eseguite su un contenitore di Azure Cosmos. È possibile usare i driver portale di Azure, .NET e Java per trovare il costo di ur.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: a0451e76ad8ce65486534bfbc68ae1b71adc9098
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284436"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-cassandra-api"></a>Trovare l'addebito delle unità richiesta per le operazioni eseguite in Azure Cosmos DB API Cassandra

Azure Cosmos DB supporta un'ampia gamma di API, come SQL, MongoDB, Cassandra, Gremlin e Tabella. Ogni API ha il proprio set di operazioni di database, da semplici operazioni di lettura e scrittura puntuali a query complesse. Ogni operazione di database utilizza le risorse di sistema a seconda della complessità.

Il costo di tutte le operazioni di database viene normalizzato da Azure Cosmos DB ed è espresso in termini di unità richiesta (o in breve UR). È possibile considerare le UR come una valuta delle prestazioni astraendo le risorse di sistema, ad esempio CPU, IOPS e memoria, necessarie per eseguire le operazioni del database supportate da Azure Cosmos DB. I costi vengono sempre misurati in UR, indipendentemente dall'API usata per interagire con il contenitore Azure Cosmos. Il fatto che l'operazione di database sia una scrittura, un punto di lettura o una query, i costi vengono sempre misurati in ur. Per altre informazioni, vedere l'articolo relativo alle [unità richiesta e alle relative considerazioni](request-units.md) .

Questo articolo illustra i diversi modi in cui è possibile trovare il consumo di [unità richiesta](request-units.md) (UR) per qualsiasi operazione eseguita su un contenitore in Azure Cosmos DB API Cassandra. Se si usa un'API diversa, vedere l'articolo [relativo all'API per MongoDB](find-request-unit-charge-mongodb.md), l'API [SQL](find-request-unit-charge.md), l' [api Gremlin](find-request-unit-charge-gremlin.md)e gli articoli [API tabella](find-request-unit-charge-table.md) per trovare il costo ur/s.

Quando si eseguono operazioni con l'API Cassandra di Azure Cosmos DB, l'addebito delle UR viene restituito nel payload in ingresso come campo denominato `RequestCharge`. Sono disponibili diverse opzioni per il recupero dell'addebito delle UR.

## <a name="use-the-net-sdk"></a>Usare .NET SDK

Quando si usa [.NET SDK](https://www.nuget.org/packages/CassandraCSharpDriver/), è possibile recuperare il payload in ingresso sotto la proprietà `Info` di un oggetto `RowSet`:

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

Per altre informazioni, vedere [Guida introduttiva: creare un'app Cassandra usando .NET SDK e Azure Cosmos DB](create-cassandra-dotnet.md).

## <a name="use-the-java-sdk"></a>Usare Java SDK

Quando si usa [Java SDK](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core), è possibile recuperare il payload in ingresso chiamando il metodo `getExecutionInfo()` per un oggetto `ResultSet`:

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

Per altre informazioni, vedere [Guida introduttiva: creare un'app Cassandra usando Java SDK e Azure Cosmos DB](create-cassandra-java.md).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come ottimizzare il consumo di UR, vedere questi articoli:

* [Velocità effettiva e unità richiesta in Azure Cosmos DB](request-units.md)
* [Ottimizzare il costo della velocità effettiva con provisioning in Azure Cosmos DB](optimize-cost-throughput.md)
* [Ottimizzare il costo delle query in Azure Cosmos DB](optimize-cost-queries.md)