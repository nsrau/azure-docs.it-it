---
title: Limiti di Azure Cosmos DB Gremlin
description: Documentazione di riferimento per le limitazioni di runtime del motore GraphReference documentation for runtime limitations of Graph engine
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 10/04/2019
ms.author: lbosq
ms.openlocfilehash: 76ad787990c355d29613c05ca9fce31885a2eccc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72029856"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Limiti di Gremlin in Azure Cosmos DB
Questo articolo illustra i limiti del motore Azure Cosmos DB Gremlin e spiega in che modo possono influire sugli attraversamenti dei clienti.

Cosmos DB Gremlin è costruito sulla base dell'infrastruttura Cosmos DB. Per questo motivo, tutti i limiti illustrati nei [limiti del servizio Database Cosmos](https://docs.microsoft.com/azure/cosmos-db/concepts-limits) di Azure sono ancora validi. 

## <a name="limits"></a>Limiti

Quando viene raggiunto il limite gremlin, l'attraversamento viene annullato con un **x-ms-status-code** 429 che indica un errore di limitazione. Per ulteriori informazioni, vedere [Intestazioni di risposta del server Gremlin.See Gremlin server response headers](gremlin-limits.md) for more information.

**Risorsa**    | **Limite predefinito** | **Spiegazione**
--- | --- | ---
*Lunghezza script* | **64 KB** | Lunghezza massima di uno script di attraversamento Gremlin per ogni richiesta.
*Profondità operatore* | **400** |  Numero totale di passaggi univoci in un attraversamento. ```g.V().out()``` Ad esempio, ha un numero di operatori di ```g.V('label').repeat(out()).times(100)``` 2: V() e out(), ha la ```.times(100)``` profondità ```.repeat()``` dell'operatore di 3: V(), repeat() e out() perché è un parametro per operatore.
*Grado di parallelismo* | **32** | Numero massimo di partizioni di archiviazione di cui viene eseguita una query in un'unica richiesta nel livello di archiviazione. I grafici con centinaia di partizioni saranno interessati da questo limite.
*Limite di ripetizioni* | **32** | Numero massimo di iterazioni eseguibili da un operatore ```.repeat()```. Ogni iterazione del ```.repeat()``` passaggio nella maggior parte dei casi esegue l'attraversamento breadth-first, il che significa che qualsiasi attraversamento è limitato al massimo 32 passaggi tra i vertici.
*Timeout di attraversamento* | **30 secondi** | L'attraversamento verrà annullato quando supererà questo tempo. Graph di Cosmos DB è un database OLTP la cui maggior parte di attraversamenti viene completata nell'arco di millisecondi. Per eseguire query OLAP su Cosmos DB Graph, utilizzare [Apache Spark](https://azure.microsoft.com/services/cosmos-db/) con [Graph Data Frames](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes) e [Cosmos DB Spark Connector](https://github.com/Azure/azure-cosmosdb-spark).
*Timeout connessione inattiva* | **1 ora** | Quantità di tempo in cui il servizio Gremlin manterrà aperte le connessioni websocket inattive. I pacchetti keep-alive TCP o le richieste keep-alive HTTP non estendono la durata della connessione oltre questo limite. Il motore Cosmos DB Graph considera le connessioni websocket inattive se non sono presenti richieste Gremlin attive in esecuzione su di esso.
*Token risorsa all'ora* | **100** | Numero di token di risorsa univoci usati dai client Gremlin per connettersi all'account Gremlin in un'area. Quando l'applicazione supera il limite `"Exceeded allowed resource token limit of 100 that can be used concurrently"` di token univoco orario, verrà restituito alla richiesta di autenticazione successiva.

## <a name="next-steps"></a>Passaggi successivi
* [Intestazioni di risposta di Azure Cosmos DB GremlinAzure Cosmos DB Gremlin response headers](gremlin-headers.md) 
* [Token delle risorse di Azure Cosmos DB con Gremlin](how-to-use-resource-tokens-gremlin.md)
