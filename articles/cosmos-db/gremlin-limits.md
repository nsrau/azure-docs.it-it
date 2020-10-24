---
title: Limiti di Azure Cosmos DB Gremlin
description: Documentazione di riferimento per le limitazioni di runtime del motore di gestione dei grafi
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 10/04/2019
ms.author: sngun
ms.openlocfilehash: 23a265f1ae6e925440e2804056348cb705b09da5
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490561"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Limiti di Gremlin in Azure Cosmos DB
In questo articolo vengono illustrati i limiti di Azure Cosmos DB motore Gremlin e viene spiegato come possono incorrere in un influsso dei clienti.

Cosmos DB Gremlin si basa sull'infrastruttura Cosmos DB. Per questo motivo, vengono comunque applicati tutti i limiti descritti in [Azure Cosmos DB limiti del servizio](./concepts-limits.md) .

## <a name="limits"></a>Limiti

Quando viene raggiunto il limite Gremlin, l'attraversamento viene annullato con un valore **x-ms-status-code** di 429 che indica un errore di limitazione. Per ulteriori informazioni, vedere [intestazioni di risposta del server Gremlin](gremlin-limits.md) .

**Risorsa**    | **Limite predefinito** | **Spiegazione**
--- | --- | ---
*Lunghezza script* | **64 KB** | Lunghezza massima di uno script di attraversamento Gremlin per ogni richiesta.
*Profondità operatore* | **400** |  Numero totale di passaggi univoci in un attraversamento. Ad esempio, ```g.V().out()``` ha un numero di operatori pari a 2: v () e out (), ```g.V('label').repeat(out()).times(100)``` con la profondità dell'operatore 3: v (), Repeat () e out () perché ```.times(100)``` è un parametro per l' ```.repeat()``` operatore.
*Grado di parallelismo* | **32** | Numero massimo di partizioni di archiviazione di cui viene eseguita una query in un'unica richiesta nel livello di archiviazione. I grafici con centinaia di partizioni saranno interessati da questo limite.
*Limite di ripetizioni* | **32** | Numero massimo di iterazioni eseguibili da un operatore ```.repeat()```. Ogni iterazione del ```.repeat()``` passaggio nella maggior parte dei casi esegue l'attraversamento della larghezza-primo, il che significa che tutti gli attraversamenti sono limitati a un massimo di 32 hop tra i vertici.
*Timeout di attraversamento* | **30 secondi** | L'attraversamento verrà annullato quando supererà questo intervallo di tempo. Graph di Cosmos DB è un database OLTP la cui maggior parte di attraversamenti viene completata nell'arco di millisecondi. Per eseguire query OLAP su Cosmos DB Graph, usare [Apache Spark](https://azure.microsoft.com/services/cosmos-db/) con [frame di dati Graph](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes) e [Cosmos DB connettore Spark](https://github.com/Azure/azure-cosmosdb-spark).
*Timeout connessione inattiva* | **1 ora** | Periodo di tempo durante il quale il servizio Gremlin manterrà aperte le connessioni WebSocket inattive. I pacchetti keep-alive TCP o le richieste keep-alive HTTP non estendono la durata della connessione oltre questo limite. Il motore di Cosmos DB Graph considera inattive le connessioni WebSocket se non sono in esecuzione richieste di Gremlin attive.
*Token risorsa all'ora* | **100** | Numero di token di risorsa univoci usati dai client Gremlin per connettersi all'account Gremlin in un'area. Quando l'applicazione supera il limite di token univoco orario, `"Exceeded allowed resource token limit of 100 that can be used concurrently"` verrà restituita alla successiva richiesta di autenticazione.

## <a name="next-steps"></a>Passaggi successivi
* [Intestazioni di risposta Azure Cosmos DB Gremlin](gremlin-headers.md)
* [Token delle risorse di Azure Cosmos DB con Gremlin](how-to-use-resource-tokens-gremlin.md)