---
title: Risolvere i problemi di query quando si usa l'API Azure Cosmos DB per MongoDB
description: Informazioni su come identificare, diagnosticare e risolvere i problemi relativi alle query dell'API Azure Cosmos DB per MongoDB.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 10/12/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 615bd423296fb9ed2ee28cab9e362873a30ee7b9
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284186"
---
# <a name="troubleshoot-query-issues-when-using-the-azure-cosmos-db-api-for-mongodb"></a>Risolvere i problemi di query quando si usa l'API Azure Cosmos DB per MongoDB

Questo articolo illustra un approccio generale consigliato per la risoluzione dei problemi relativi alle query in Azure Cosmos DB. I passaggi descritti in questo articolo non rappresentano una soluzione completa ai potenziali problemi di query, ma offrono suggerimenti per la soluzione degli errori più comuni relativi alle prestazioni. Usare questo articolo come punto di partenza per la risoluzione dei problemi relativi alle query lente o costose nell'API Azure Cosmos DB per MongoDB. Se si usa l'API Azure Cosmos DB Core (SQL), vedere l'articolo [Guida alla risoluzione dei problemi delle query dell'API SQL](troubleshoot-query-performance.md) .

Le ottimizzazioni delle query in Azure Cosmos DB sono categorizzate in modo estensivo come segue:

- Ottimizzazioni che consentono di ridurre l'addebito dell'unità richiesta (UR) della query
- Ottimizzazioni che consentono di ridurre solo la latenza

Se si riduce il costo delle UR di una query, in genere si diminuisce anche la latenza.

Questo articolo fornisce esempi che è possibile ricreare usando il set di [dati nutrizionale](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json).

> [!NOTE] 
> Questo articolo presuppone che si usi la versione 3,6 dell'API Azure Cosmos DB "s per MongoDB. Alcune query con prestazioni scarse nella versione 3,2 presentano miglioramenti significativi nella versione 3,6. Eseguire l'aggiornamento alla versione 3,6 inviando una [richiesta di supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="use-explain-command-to-get-metrics"></a>Usare $explain comando per ottenere le metriche

Quando si ottimizza una query in Azure Cosmos DB, il primo passaggio consiste sempre nell' [ottenere l'addebito](find-request-unit-charge-mongodb.md) per le UR per la query. Come linea guida approssimativa, è consigliabile esplorare i modi per abbassare l'addebito delle unità richiesta per le query con costi maggiori di 50 UR. 

Oltre a ottenere l'addebito delle UR, è necessario usare il `$explain` comando per ottenere le metriche di utilizzo di query e indici. Di seguito è riportato un esempio che esegue una query e usa il `$explain` comando per visualizzare le metriche di utilizzo di query e indici:

**comando $explain:**

```
db.coll.find({foodGroup: "Baby Foods"}).explain({"executionStatistics": true })
```

**Output:**

```json
{
    "stages" : [ 
        {
            "stage" : "$query",
            "timeInclusiveMS" : 905.2888,
            "timeExclusiveMS" : 905.2888,
            "in" : 362,
            "out" : 362,
            "details" : {
                "database" : "db-test",
                "collection" : "collection-test",
                "query" : {
                    "foodGroup" : {
                        "$eq" : "Baby Foods"
                    }
                },
                "pathsIndexed" : [],
                "pathsNotIndexed" : [ 
                    "foodGroup"
                ],
                "shardInformation" : [ 
                    {
                        "activityId" : "e68e6bdd-5e89-4ec5-b053-3dbbc2428140",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 788.5867,
                        "preemptions" : 1,
                        "outputDocumentCount" : 362,
                        "retrievedDocumentCount" : 8618
                    }
                ],
                "queryMetrics" : {
                    "retrievedDocumentCount" : 8618,
                    "retrievedDocumentSizeBytes" : 104963042,
                    "outputDocumentCount" : 362,
                    "outputDocumentSizeBytes" : 2553535,
                    "indexHitRatio" : 0.0016802042237178,
                    "totalQueryExecutionTimeMS" : 777.72,
                    "queryPreparationTimes" : {
                        "queryCompilationTimeMS" : 0.19,
                        "logicalPlanBuildTimeMS" : 0.14,
                        "physicalPlanBuildTimeMS" : 0.09,
                        "queryOptimizationTimeMS" : 0.03
                    },
                    "indexLookupTimeMS" : 0,
                    "documentLoadTimeMS" : 687.22,
                    "vmExecutionTimeMS" : 774.09,
                    "runtimeExecutionTimes" : {
                        "queryEngineExecutionTimeMS" : 37.45,
                        "systemFunctionExecutionTimeMS" : 10.82,
                        "userDefinedFunctionExecutionTimeMS" : 0
                    },
                    "documentWriteTimeMS" : 49.42
                }
            }
        }
    ],
    "estimatedDelayFromRateLimitingInMilliseconds" : 0.0,
    "continuation" : {
        "hasMore" : false
    },
    "ok" : 1.0
}
```

L' `$explain` output del comando è lungo e contiene informazioni dettagliate sull'esecuzione delle query. In generale, tuttavia, sono disponibili alcune sezioni in cui è necessario concentrarsi sull'ottimizzazione delle prestazioni delle query:

| Metrica | Descrizione | 
| ------ | ----------- |
| `timeInclusiveMS` | Latenza query back-end |
| `pathsIndexed` | Mostra gli indici utilizzati dalla query | 
| `pathsNotIndexed` | Mostra gli indici che potrebbero essere stati utilizzati dalla query, se disponibili | 
| `shardInformation` | Riepilogo delle prestazioni delle query per una determinata [partizione fisica](partition-data.md#physical-partitions) | 
| `retrievedDocumentCount` | Numero di documenti caricati dal motore di query | 
| `outputDocumentCount` | Numero di documenti restituiti nei risultati della query | 
| `estimatedDelayFromRateLimitingInMilliseconds` | Latenza di query aggiuntive stimata a causa della limitazione della frequenza | 

Dopo avere ottenuto la metrica della query, confrontare `retrievedDocumentCount` con per la `outputDocumentCount` query. Usare questo confronto per identificare le sezioni rilevanti da esaminare in questo articolo. `retrievedDocumentCount`È il numero di documenti che il motore di query deve caricare. `outputDocumentCount`È il numero di documenti necessari per i risultati della query. Se `retrievedDocumentCount`  è significativamente superiore a `outputDocumentCount` , era presente almeno una parte della query che non è in grado di utilizzare un indice ed è necessario eseguire un'analisi.

Per informazioni sull'ottimizzazione delle query relative a ogni scenario, vedere le sezioni che seguono.

### <a name="querys-ru-charge-is-too-high"></a>L'addebito UR della query è troppo elevato

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>Il conteggio dei documenti recuperati è significativamente superiore al conteggio dei documenti di output

- [Includere gli indici necessari.](#include-necessary-indexes)

- [Informazioni sulle operazioni di aggregazione che utilizzano l'indice.](#understand-which-aggregation-operations-use-the-index)

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>Il conteggio documenti recuperati è approssimativamente uguale al conteggio documenti di output

- [Ridurre al minimo le query su più partizioni.](#minimize-cross-partition-queries)

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>L'addebito UR della query è accettabile, ma la latenza è ancora troppo elevata

- [Migliorare la prossimità.](#improve-proximity)

- [Aumentare la velocità effettiva sottoposta a provisioning.](#increase-provisioned-throughput)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Query in cui il numero di documenti recuperati supera il numero di documenti di output

 `retrievedDocumentCount`È il numero di documenti necessari per il caricamento del motore di query. `outputDocumentCount`È il numero di documenti restituiti dalla query. Se `retrievedDocumentCount` è significativamente superiore a `outputDocumentCount` , era presente almeno una parte della query che non è in grado di utilizzare un indice ed è necessario eseguire un'analisi.

Di seguito è riportato un esempio di una query di analisi che non è stata interamente gestita dall'indice:

**comando $explain:**

```
db.coll.find(
  {
    $and : [
            { "foodGroup" : "Cereal Grains and Pasta"}, 
            { "description" : "Oat bran, cooked"}
        ]
  }
).explain({"executionStatistics": true })
```

**Output:**

```json
{
    "stages" : [ 
        {
            "stage" : "$query",
            "timeInclusiveMS" : 436.5716,
            "timeExclusiveMS" : 436.5716,
            "in" : 1,
            "out" : 1,
            "details" : {
                "database" : "db-test",
                "collection" : "indexing-test",
                "query" : {
                    "$and" : [ 
                        {
                            "foodGroup" : {
                                "$eq" : "Cereal Grains and Pasta"
                            }
                        }, 
                        {
                            "description" : {
                                "$eq" : "Oat bran, cooked"
                            }
                        }
                    ]
                },
                "pathsIndexed" : [],
                "pathsNotIndexed" : [ 
                    "foodGroup", 
                    "description"
                ],
                "shardInformation" : [ 
                    {
                        "activityId" : "13a5977e-a10a-4329-b68e-87e4f0081cac",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 435.4867,
                        "preemptions" : 1,
                        "outputDocumentCount" : 1,
                        "retrievedDocumentCount" : 8618
                    }
                ],
                "queryMetrics" : {
                    "retrievedDocumentCount" : 8618,
                    "retrievedDocumentSizeBytes" : 104963042,
                    "outputDocumentCount" : 1,
                    "outputDocumentSizeBytes" : 6064,
                    "indexHitRatio" : 0.0,
                    "totalQueryExecutionTimeMS" : 433.64,
                    "queryPreparationTimes" : {
                        "queryCompilationTimeMS" : 0.12,
                        "logicalPlanBuildTimeMS" : 0.09,
                        "physicalPlanBuildTimeMS" : 0.1,
                        "queryOptimizationTimeMS" : 0.02
                    },
                    "indexLookupTimeMS" : 0,
                    "documentLoadTimeMS" : 387.44,
                    "vmExecutionTimeMS" : 432.93,
                    "runtimeExecutionTimes" : {
                        "queryEngineExecutionTimeMS" : 45.36,
                        "systemFunctionExecutionTimeMS" : 16.86,
                        "userDefinedFunctionExecutionTimeMS" : 0
                    },
                    "documentWriteTimeMS" : 0.13
                }
            }
        }
    ],
    "estimatedDelayFromRateLimitingInMilliseconds" : 0.0,
    "continuation" : {
        "hasMore" : false
    },
    "ok" : 1.0
}
```

`retrievedDocumentCount`(8618) è significativamente superiore `outputDocumentCount` a (1), a indicare che questa query richiede un'analisi del documento. 

### <a name="include-necessary-indexes"></a>Includi indici necessari

È necessario controllare la `pathsNotIndexed` matrice e aggiungere questi indici. In questo esempio, i percorsi `foodGroup` e `description` devono essere indicizzati.

```json
"pathsNotIndexed" : [ 
                    "foodGroup", 
                    "description"
                ]
```

Le procedure consigliate per l'indicizzazione nell'API Azure Cosmos DB per MongoDB sono diverse da MongoDB. Nell'API di Azure Cosmos DB per MongoDB, gli indici composti vengono usati solo nelle query che devono essere ordinati in modo efficiente in base a più proprietà. Se si dispone di query con filtri su più proprietà, è necessario creare indici di campi singoli per ognuna di queste proprietà. I predicati di query possono usare più indici di campo singolo.

Gli [indici jolly](mongodb-indexing.md#wildcard-indexes) possono semplificare l'indicizzazione. Diversamente da MongoDB, gli indici con caratteri jolly possono supportare più campi nei predicati di query. Non vi sarà alcuna differenza nelle prestazioni delle query se si usa un singolo indice con caratteri jolly anziché creare un indice separato per ogni proprietà. L'aggiunta di un indice con caratteri jolly per tutte le proprietà rappresenta il modo più semplice per ottimizzare tutte le query.

È possibile aggiungere nuovi indici in qualsiasi momento, senza alcun effetto sulla disponibilità di scrittura o lettura. È possibile [tenere traccia dell'avanzamento della trasformazione dell'indice](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-net-sdk-v3).

### <a name="understand-which-aggregation-operations-use-the-index"></a>Informazioni sulle operazioni di aggregazione che utilizzano l'indice

Nella maggior parte dei casi, le operazioni di aggregazione nell'API Azure Cosmos DB per MongoDB utilizzeranno parzialmente gli indici. Il motore di query, in genere, applica prima i filtri di uguaglianza e di intervallo e usa gli indici. Dopo aver applicato questi filtri, il motore di query può valutare filtri aggiuntivi e ricorrere al caricamento dei documenti rimanenti per calcolare l'aggregazione, se necessario. 

Ecco un esempio:

```
db.coll.aggregate( [
   { $match: { foodGroup: 'Fruits and Fruit Juices' } },
   {
     $group: {
        _id: "$foodGroup",
        total: { $max: "$version" }
     }
   }
] )
```

In questo caso, gli indici possono ottimizzare la `$match` fase. L'aggiunta di un indice per `foodGroup` aumenterà significativamente le prestazioni di esecuzione delle query. Analogamente a MongoDB, è consigliabile inserire `$match` il prima possibile nella pipeline di aggregazione per ottimizzare l'utilizzo degli indici.

Nell'API di Azure Cosmos DB per MongoDB gli indici non vengono usati per l'aggregazione effettiva, che in questo caso è `$max` . L'aggiunta di un indice in `version` non consentirà di migliorare le prestazioni delle query.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Query in cui il numero di documenti recuperati è uguale al numero di documenti di output

Se `retrievedDocumentCount` è approssimativamente uguale a `outputDocumentCount` , il motore di query non ha dovuto analizzare molti documenti superflui.

### <a name="minimize-cross-partition-queries"></a>Ridurre al minimo le query su più partizioni

Azure Cosmos DB usa il [partizionamento](partitioning-overview.md) per la scalabilità dei singoli contenitori quando si riscontra un aumento dell'UR e delle risorse di archiviazione dati necessarie. Ogni partizione fisica dispone di un indice distinto e indipendente. Se la query include un filtro di uguaglianza che corrisponde alla chiave di partizione del contenitore, è necessario controllare solo l'indice della partizione pertinente. Questa ottimizzazione consente di ridurre il numero totale di UR necessarie per la query. [Altre informazioni sulle differenze tra le query in-Partition e le query tra partizioni](how-to-query-container.md).

Se è presente un numero elevato di UR di cui è stato effettuato il provisioning (più di 30.000) o di una quantità elevata di dati archiviati (circa oltre 100 GB), è probabile che si disponga di un contenitore sufficientemente grande da consentire una riduzione significativa degli addebiti UR. 

È possibile controllare la `shardInformation` matrice per comprendere la metrica della query per ogni singola partizione fisica. Il numero di valori univoci `shardKeyRangeId` è il numero di partizioni fisiche in cui è necessario eseguire la query. In questo esempio la query è stata eseguita su quattro partizioni fisiche. È importante comprendere che l'esecuzione è completamente indipendente dall'utilizzo degli indici. In altre parole, le query tra partizioni possono comunque usare gli indici.

```json
  "shardInformation" : [ 
                    {
                        "activityId" : "42f670a8-a201-4c58-8023-363ac18d9e18",
                        "shardKeyRangeId" : "5",
                        "durationMS" : 24.3859,
                        "preemptions" : 1,
                        "outputDocumentCount" : 463,
                        "retrievedDocumentCount" : 463
                    }, 
                    {
                        "activityId" : "a8bf762a-37b9-4c07-8ed4-ae49961373c0",
                        "shardKeyRangeId" : "2",
                        "durationMS" : 35.8328,
                        "preemptions" : 1,
                        "outputDocumentCount" : 905,
                        "retrievedDocumentCount" : 905
                    }, 
                    {
                        "activityId" : "3754e36b-4258-49a6-8d4d-010555628395",
                        "shardKeyRangeId" : "1",
                        "durationMS" : 67.3969,
                        "preemptions" : 1,
                        "outputDocumentCount" : 1479,
                        "retrievedDocumentCount" : 1479
                    }, 
                    {
                        "activityId" : "a69a44ee-db97-4fe9-b489-3791f3d52878",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 185.1523,
                        "preemptions" : 1,
                        "outputDocumentCount" : 867,
                        "retrievedDocumentCount" : 867
                    }
                ]
```

## <a name="optimizations-that-reduce-query-latency"></a>Ottimizzazioni che consentono di ridurre la latenza della query

In molti casi, l'addebito UR potrebbe essere accettabile quando la latenza della query è ancora troppo elevata. Le sezioni seguenti forniscono una panoramica dei suggerimenti per la riduzione della latenza delle query. Se si esegue la stessa query più volte nello stesso set di dati, in genere lo stesso addebito viene addebitato ogni volta. La latenza delle query potrebbe tuttavia variare tra le esecuzioni della query.

### <a name="improve-proximity"></a>Migliorare la prossimità

Le query eseguite da un'area diversa da quella dell'account di Azure Cosmos DB avranno una latenza superiore rispetto a quando vengono eseguite all'interno della stessa area. Se, ad esempio, se si esegue il codice sul computer desktop, è prevedibile che la latenza sia superiore di decine o centinaia di millisecondi (o più) rispetto a una query eseguita su una macchina virtuale all'interno della stessa area di Azure come Azure Cosmos DB. La [distribuzione a livello globale dei dati in Azure Cosmos DB](tutorial-global-distribution-mongodb.md) è un'operazione semplice che consente di migliorare la prossimità dei dati all'app.

### <a name="increase-provisioned-throughput"></a>Aumentare la velocità effettiva sottoposta a provisioning

In Azure Cosmos DB la velocità effettiva con provisioning viene misurata in Unità Richieste (UR). Si supponga di disporre di una query che utilizza 5 UR di velocità effettiva. Se, ad esempio, si esegue il provisioning di 1.000 UR, sarà possibile eseguire la query 200 volte al secondo. Se si è tentato di eseguire la query quando la velocità effettiva disponibile non è sufficiente, Azure Cosmos DB consentirà di limitare il numero di richieste. L'API di Azure Cosmos DB per MongoDB tenterà automaticamente di eseguire la query dopo un'attesa per un breve periodo di tempo. Le richieste limitate richiedono più tempo, quindi l'aumento della velocità effettiva con provisioning può diminuire la latenza delle query.

Il valore `estimatedDelayFromRateLimitingInMilliseconds` offre un'idea dei vantaggi della latenza potenziale se si aumenta la velocità effettiva.

## <a name="next-steps"></a>Passaggi successivi

* [Risolvere i problemi relativi alle prestazioni delle query (API SQL)](troubleshoot-query-performance.md)
* [Gestire l'indicizzazione nell'API Azure Cosmos DB per MongoDB](mongodb-indexing.md)
