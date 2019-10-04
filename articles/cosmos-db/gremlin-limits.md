---
title: Limiti di Azure Cosmos DB Gremlin
description: Documentazione di riferimento per le limitazioni di runtime del motore di gestione dei grafi
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: olignat
ms.openlocfilehash: f1fe4cfac22a651f44338986d0a767fe3e1a360b
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910950"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Limiti di Azure Cosmos DB Gremlin
In questo articolo vengono illustrati i limiti di Azure Cosmos DB motore Gremlin e viene spiegato come possono incorrere in un influsso dei clienti.

Cosmos DB Gremlin si basa sull'infrastruttura Cosmos DB per il motivo per cui si applicano tutti i limiti in [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/concepts-limits) . 

## <a name="limits"></a>Limiti

Quando viene raggiunto il limite Gremlin, l'attraversamento viene annullato con **x-ms-status-code** = 429 che indica l'errore di limitazione.

**Risorsa**    | **Limite predefinito** | **Spiegazione**
--- | --- | ---
*Memoria per richiesta* | **2 GB** | Memoria massima utilizzabile da una richiesta durante l'elaborazione. Le richieste che richiedono il calcolo di set di dati di grandi dimensioni utilizzeranno memoria aggiuntiva. Prendere in considerazione l'ambito delle richieste a set di dati più piccoli per evitare di superare questo limite o usare soluzioni OLAP.
*Lunghezza script* | **64 KB** | Lunghezza massima di uno script di attraversamento Gremlin per ogni richiesta.
*Profondità operatore* | **400** |  Numero totale di passaggi univoci in un attraversamento. Ad esempio, ```g.V().out()``` ha un numero di operatori di 2: V () e out () ```g.V('label').repeat(out()).times(100)``` con la profondità dell'operatore di 3: V (), Repeat () e out () perché ```.times(100)``` è un parametro per ```.repeat()``` l'operatore.
*Grado di parallelismo* | **32** | Numero massimo di partizioni di archiviazione di cui viene eseguita una query in un'unica richiesta nel livello di archiviazione. I grafici con centinaia di partizioni saranno interessati da questo limite.
*Limite di ripetizione* | **32** | Numero massimo di iterazioni eseguibili da un operatore ```.repeat()```. Ogni iterazione ```.repeat()``` del passaggio nella maggior parte dei casi esegue l'attraversamento della larghezza-primo, il che significa che tutti gli attraversamenti sono limitati a un massimo di 32 hop tra i vertici.
*Timeout attraversamento* | **30 secondi** | L'attraversamento verrà annullato quando supererà questo intervallo di tempo. Graph di Cosmos DB è un database OLTP la cui maggior parte di attraversamenti viene completata nell'arco di millisecondi. Per eseguire query OLAP su Cosmos DB Graph, usare [Apache Spark](https://azure.microsoft.com/services/cosmos-db/) con [frame di dati Graph](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes) e [Cosmos DB connettore Spark](https://github.com/Azure/azure-cosmosdb-spark).
*Limite predicato* | **20** | Conteggio dei passaggi ```.has()``` o ```.hasNot()``` applicati a un singolo vertice o contorno. Quando questo limite viene raggiunto, l'errore mostrato nell'applicazione è ```The SQL query exceeded the maximum number of joins. The allowed limit is 20```. Si tratta di un inconveniente temporaneo perché il team sta lavorando per sollevare questo limite. 
*Timeout connessione inattiva* | **5 ore** | Quantità di tempo in cui il server Graph manterrà aperta la connessione WebSocket senza traffico. I pacchetti keep-alive TCP o le richieste keep-alive HTTP non estendono la durata della connessione oltre tale limite. Tuttavia, se non vengono inviati, l'infrastruttura di Azure sottostante potrebbe chiudere la connessione ancora prima. Il motore di Cosmos DB Graph considera inattivo se non sono in esecuzione attraversamenti Gremlin.
*Token risorsa all'ora* | **100** | Numero di token di risorsa univoci usati dai client Gremlin per connettersi all'account Gremlin in un'area. Quando l'applicazione supera il limite di token univoco `"Exceeded allowed resource token limit of 100 that can be used concurrently"` orario, viene restituita la richiesta di autenticazione successiva.

## <a name="next-steps"></a>Passaggi successivi
* [Intestazioni di risposta Azure Cosmos DB Gremlin](gremlin-headers.md) 
* [Azure Cosmos DB token di risorsa con Gremlin](how-to-use-resource-tokens-gremlin.md)
