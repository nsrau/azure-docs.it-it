---
title: Usare il profilo di esecuzione per valutare le query nell'API di Azure Cosmos DB GremlinUse the execution profile to evaluate queries in Azure Cosmos DB Gremlin API
description: Scopri come risolvere i problemi e migliorare le query Gremlin usando il passaggio del profilo di esecuzione.
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: lbosq
ms.openlocfilehash: 5705ef4fb6aa895009d554617c968543cc3fcd63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441841"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>Come usare il passaggio del profilo di esecuzione per valutare le query Gremlin

Questo articolo fornisce una panoramica dell'utilizzo del passaggio del profilo di esecuzione per i database a grafi dell'API Gremlin in Azure Cosmos DB. Questo passaggio fornisce le informazioni rilevanti per la risoluzione dei problemi e le ottimizzazioni delle query ed è compatibile con tutte le query Gremlin eseguibili in un account dell'API Gremlin di Cosmos DB.

Per utilizzare questo passaggio, è sufficiente aggiungere la `executionProfile()` chiamata di funzione alla fine della query Gremlin. **La query Gremlin verrà eseguita** e il risultato dell'operazione restituirà un oggetto risposta JSON con il profilo di esecuzione della query.

Ad esempio:

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

Dopo aver `executionProfile()` chiamato il passaggio, la risposta sarà un oggetto JSON che include il passaggio Gremlin eseguito, il tempo totale impiegato e una matrice degli operatori di runtime Cosmos DB risultante dall'istruzione.

> [!NOTE]
> Questa implementazione per il profilo di esecuzione non è definita nella specifica Apache Tinkerpop. È specifico per l'implementazione dell'API Cosmos DB Gremlin di Azure.It is specific to Azure Cosmos DB Gremlin API implementation.


## <a name="response-example"></a>Esempio di risposta

Di seguito è riportato un esempio annotato dell'output che verrà restituito:

> [!NOTE]
> Questo esempio è annotato con commenti che spiegano la struttura generale della risposta. Una risposta executionProfile effettiva non conterrà commenti.

```json
[
  {
    // The Gremlin statement that was executed.
    "gremlin": "g.V('mary').out().executionProfile()",

    // Amount of time in milliseconds that the entire operation took.
    "totalTime": 28,

    // An array containing metrics for each of the steps that were executed. 
    // Each Gremlin step will translate to one or more of these steps.
    // This list is sorted in order of execution.
    "metrics": [
      {
        // This operation obtains a set of Vertex objects.
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetVertices",
        "time": 24,
        "annotations": {
          "percentTime": 85.71
        },
        "counts": {
          "resultCount": 2
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 2,
            "size": 696,
            "time": 0.4
          }
        ]
      },
      {
        // This operation obtains a set of Edge objects. 
        // Depending on the query, these might be directly adjacent to a set of vertices, 
        // or separate, in the case of an E() query.
        //
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetEdges",
        "time": 4,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 1,
            "size": 419,
            "time": 0.67
          }
        ]
      },
      {
        // This operation obtains the vertices that a set of edges point at.
        // The metrics include: time, percentTime of total execution time and resultCount.
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        // This operation represents the serialization and preparation for a result from 
        // the preceding graph operations. The metrics include: time, percentTime of total 
        // execution time and resultCount.
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

> [!NOTE]
> Il passaggio executionProfile eseguirà la query Gremlin. Sono inclusi `addV` `addE`i passaggi o, che comporteranno la creazione ed eseguirà il commit delle modifiche specificate nella query. Di conseguenza, verranno addebitate anche le unità di richiesta generate dalla query Gremlin.

## <a name="execution-profile-response-objects"></a>Oggetti risposta del profilo di esecuzione

La risposta di una funzione executionProfile() produrrà una gerarchia di oggetti JSON con la struttura seguente:
  - **Oggetto operazione Gremlin**: rappresenta l'intera operazione Gremlin che è stata eseguita. Contiene le proprietà seguenti.
    - `gremlin`: l'istruzione esplicita Gremlin eseguita.
    - `totalTime`: tempo, in millisecondi, in cui è stata eseguito l'esecuzione del passaggio. 
    - `metrics`: matrice che contiene ognuno degli operatori di runtime Cosmos DB eseguiti per soddisfare la query. Questo elenco è ordinato in base all'esecuzione.
    
  - Operatori di **runtime Cosmos DB**: rappresenta ognuno dei componenti dell'intera operazione Gremlin. Questo elenco è ordinato in base all'esecuzione. Ogni oggetto contiene le seguenti proprietà:
    - `name`: nome dell'operatore. Questo è il tipo di passaggio che è stato valutato ed eseguito. Per saperne di più nella tabella qui sotto.
    - `time`: quantità di tempo, in millisecondi, impiegato da un determinato operatore.
    - `annotations`: contiene informazioni aggiuntive, specifiche dell'operatore eseguito.
    - `annotations.percentTime`: percentuale del tempo totale impiegato per eseguire l'operatore specifico.
    - `counts`: numero di oggetti restituiti dal livello di archiviazione da questo operatore. Questo è contenuto `counts.resultCount` nel valore scalare all'interno.
    - `storeOps`: rappresenta un'operazione di archiviazione che può estendersi su una o più partizioni.
    - `storeOps.fanoutFactor`: rappresenta il numero di partizioni a cui si accede a questa specifica operazione di archiviazione.
    - `storeOps.count`: rappresenta il numero di risultati restituiti da questa operazione di archiviazione.
    - `storeOps.size`: rappresenta la dimensione in byte del risultato di una determinata operazione di archiviazione.

Cosmos DB Gremlin Runtime Operatore|Descrizione
---|---
`GetVertices`| Questo passaggio ottiene un set di oggetti predicato dal livello di persistenza. 
`GetEdges`| Questo passaggio ottiene gli spigoli adiacenti a un insieme di vertici. Questo passaggio può comportare una o più operazioni di archiviazione.
`GetNeighborVertices`| Questo passaggio ottiene i vertici collegati a un insieme di spigoli. I bordi contengono le chiavi di partizione e gli ID dei vertici di origine e di destinazione.
`Coalesce`| Questo passaggio tiene conto della `coalesce()` valutazione di due operazioni ogni volta che viene eseguito il passaggio Gremlin.
`CartesianProductOperator`| Questo passaggio calcola un prodotto cartesiano tra due set di dati. In genere viene eseguito `to()` ogni `from()` volta che vengono utilizzati i predicati o utilizzati.
`ConstantSourceOperator`| Questo passaggio calcola un'espressione per produrre un valore costante come risultato.
`ProjectOperator`| Questo passaggio prepara e serializza una risposta utilizzando il risultato delle operazioni precedenti.
`ProjectAggregation`| Questo passaggio prepara e serializza una risposta per un'operazione di aggregazione.

> [!NOTE]
> Questo elenco continuerà ad essere aggiornato man mano che vengono aggiunti nuovi operatori.

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>Esempi su come analizzare una risposta del profilo di esecuzioneExamples on how to analyze an execution profile response

Di seguito sono riportati esempi di ottimizzazioni comuni che possono essere individuate usando la risposta del profilo di esecuzione:The following are examples of common optimizations that can be spotted using the Execution Profile response:
  - Domanda di fan-out cieca.
  - Query non filtrata.

### <a name="blind-fan-out-query-patterns"></a>Modelli di query di fan-out ciechi

Si supponga la seguente risposta del profilo di esecuzione da un **grafico partizionato:**

```json
[
  {
    "gremlin": "g.V('tt0093640').executionProfile()",
    "totalTime": 46,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 46,
        "annotations": {
          "percentTime": 100
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 5,
            "count": 1,
            "size": 589,
            "time": 75.61
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

Da esso si possono trarre le seguenti conclusioni:
- La query è una ricerca con ID singolo, poiché l'istruzione Gremlin segue il modello `g.V('id')`.
- A giudicare `time` dalla metrica, la latenza di questa query sembra essere elevata poiché è [più di 10 ms per una singola operazione](https://docs.microsoft.com/azure/cosmos-db/introduction#guaranteed-low-latency-at-99th-percentile-worldwide)di lettura dei punti.
- Se esaminiamo `storeOps` l'oggetto, possiamo `fanoutFactor` vedere `5`che il is , il che significa che [5 partizioni](https://docs.microsoft.com/azure/cosmos-db/partition-data) sono state accessibili da questa operazione.

Come conclusione di questa analisi, è possibile determinare che la prima query accede a più partizioni del necessario. Questo problema può essere risolto specificando la chiave di partizionamento nella query come predicato. Ciò comporterà una latenza minore e un costo per query inferiore. Per altre informazioni, vedere l'articolo sul [partizionamento di grafi](graph-partitioning.md). Una query più `g.V('tt0093640').has('partitionKey', 't1001')`ottimale sarebbe .

### <a name="unfiltered-query-patterns"></a>Modelli di query non filtrati

Confrontare le due risposte del profilo di esecuzione seguenti. Per semplicità, questi esempi usano un singolo grafico partizionato.

Questa prima query recupera tutti i `tweet` vertici con l'etichetta e quindi ottiene i vertici adiacenti:

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').out().executionProfile()",
    "totalTime": 42,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 31,
        "annotations": {
          "percentTime": 73.81
        },
        "counts": {
          "resultCount": 30
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 13,
            "size": 6819,
            "time": 1.02
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 6,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 20,
            "size": 7950,
            "time": 1.98
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 11.9
        },
        "counts": {
          "resultCount": 20
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.19
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 20
        }
      }
    ]
  }
]
```

Si noti il profilo della stessa query, `has('lang', 'en')`ma ora con un filtro aggiuntivo, , prima di esplorare i vertici adiacenti:

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').has('lang', 'en').out().executionProfile()",
    "totalTime": 14,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 14,
        "annotations": {
          "percentTime": 58.33
        },
        "counts": {
          "resultCount": 11
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 11,
            "size": 4807,
            "time": 1.27
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 18,
            "size": 7159,
            "time": 1.7
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.01
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 18
        }
      }
    ]
  }
]
```

Queste due query hanno raggiunto lo stesso risultato, tuttavia, la prima richiederà più unità richiesta poiché è necessario scorrere un set di dati iniziale più grande prima di eseguire query sugli elementi adiacenti. Possiamo vedere gli indicatori di questo comportamento quando si confrontano i seguenti parametri da entrambe le risposte:
- Il `metrics[0].time` valore è più alto nella prima risposta, che indica che questo singolo passaggio ha richiesto più tempo per essere risolto.
- Il `metrics[0].counts.resultsCount` valore è più alto anche nella prima risposta, che indica che il set di dati di lavoro iniziale era più grande.

## <a name="next-steps"></a>Passaggi successivi
* Informazioni sulle [funzionalità Gremlin supportate](gremlin-support.md) in Azure Cosmos DB. 
* Altre informazioni [sull'API Gremlin in Azure Cosmos DB](graph-introduction.md).
