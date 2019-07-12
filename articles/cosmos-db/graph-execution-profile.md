---
title: Valutare le query con la funzione del profilo di esecuzione per l'API di Azure Cosmos DB Gremlin
description: Informazioni su come risolvere i problemi e migliorare le query Gremlin usando il passaggio di profilo di esecuzione.
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: lbosq
ms.openlocfilehash: 4964f485f5e781b7fe0a0f09486512fe6a5b9035
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592486"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>Come usare il passaggio di profilo di esecuzione per valutare le query Gremlin

Questo articolo offre una panoramica di come usare il passaggio di profilo di esecuzione per i database di grafi Gremlin API di Azure Cosmos DB. Questo passaggio vengono fornite informazioni rilevanti per la risoluzione dei problemi e ottimizzazione delle query ed è compatibile con le query Gremlin che possono essere eseguite su un account API di Cosmos DB Gremlin.

Per usare questo passaggio, è sufficiente aggiungere il `executionProfile()` funzione chiamata alla fine della query Gremlin. **Verrà eseguita la query di Gremlin** e il risultato dell'operazione restituirà un oggetto di risposta JSON con il profilo di esecuzione di query.

Ad esempio:

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

Dopo la chiamata di `executionProfile()` passaggio, la risposta sarà un oggetto JSON che include il passaggio di Gremlin eseguito, il tempo totale impiegato e una matrice degli operatori di runtime di Cosmos DB che ha comportato l'istruzione.

> [!NOTE]
> Questa implementazione per il profilo di esecuzione non è definita nella specifica di Apache Tinkerpop. È specifico all'implementazione dell'API di Azure Cosmos DB Gremlin.


## <a name="response-example"></a>Esempio di risposta

Di seguito è riportato un esempio con annotazione di output che verrà restituito:

> [!NOTE]
> In questo esempio è annotato con commenti che spiegano la struttura generale della risposta. Una risposta executionProfile effettiva non verrà inoltre contenere commenti.

```json
[
  {
    // The Gremlin statement that was executed.
    "gremlin": "g.V('mary').out().executionProfile()",

    // Amount of time in milliseconds that the entire operation took.
    "totalTime": 28,

    // An array containing metrics for each of the steps that were executed. Each Gremlin step will translate to one or more of these steps.
    // This list is sorted in order of execution.
    "metrics": [
      {
        // This operation obtains a set of Vertex objects.
        // The metrics include: time, percentTime of total execution time, resultCount, fanoutFactor, count, size (in bytes) and time.
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
        // This operation obtains a set of Edge objects. Depending on the query, these might be directly adjacent to a set of vertices, or separate, in the case of an E() query.
        // The metrics include: time, percentTime of total execution time, resultCount, fanoutFactor, count, size (in bytes) and time.
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
        // This operation represents the serialization and preparation for a result from the preceding graph operations.
        // The metrics include: time, percentTime of total execution time and resultCount.
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
> Il passaggio executionProfile eseguirà la query Gremlin. Ciò include la `addV` o `addE`passaggi che comporteranno la creazione e verranno eseguito il commit di modifiche specificate nella query. Di conseguenza, l'unità di richiesta generato dalla query Gremlin viene inoltre addebitata.

## <a name="execution-profile-response-objects"></a>Oggetti di risposta del profilo di esecuzione

La risposta di una funzione executionProfile() produrrà una gerarchia di oggetti JSON con la struttura seguente:
  - **Oggetto dell'operazione di gremlin**: Rappresenta l'intera operazione Gremlin è stata eseguita. Contiene le proprietà seguenti.
    - `gremlin`: L'istruzione Gremlin esplicita che è stata eseguita.
    - `totalTime`: Il tempo, espresso in millisecondi, sostenuta per l'esecuzione del passaggio. 
    - `metrics`: Matrice che contiene ciascuno degli operatori di runtime di Cosmos DB che sono stati eseguiti per soddisfare la query. Questo elenco viene ordinato in ordine di esecuzione.
    
  - **Gli operatori di runtime di COSMOS DB**: Rappresenta ogni componente dell'intera operazione Gremlin. Questo elenco viene ordinato in ordine di esecuzione. Ogni oggetto contiene le proprietà seguenti:
    - `name`: Nome dell'operatore. Questo è il tipo di passaggio che è stata valutata ed eseguita. Per altre informazioni nella tabella seguente.
    - `time`: Quantità di tempo, espresso in millisecondi, impiegato un determinato operatore.
    - `annotations`: Contiene informazioni aggiuntive, specifiche per l'operatore che è stata eseguita.
    - `annotations.percentTime`: Percentuale di tempo totale impiegato per l'esecuzione dell'operatore specifico.
    - `counts`: Numero di oggetti che sono stati restituiti dal livello di archiviazione da questo operatore. Questo è contenuta nel `counts.resultCount` valore scalare all'interno.
    - `storeOps`: Rappresenta un'operazione di archiviazione che può estendersi su una o più partizioni.
    - `storeOps.fanoutFactor`: Rappresenta il numero di partizioni a cui accede questa operazione di archiviazione specifico.
    - `storeOps.count`: Rappresenta il numero di risultati che questa operazione di archiviazione restituita.
    - `storeOps.size`: Rappresenta la dimensione in byte del risultato di un'operazione di archiviazione specificato.

Operatore di Runtime di COSMOS DB Gremlin|DESCRIZIONE
---|---
`GetVertices`| Questo passaggio Ottiene un set di oggetti predicato ha dal livello di persistenza. 
`GetEdges`| Questa operazione Ottiene i bordi adiacenti a un set di vertici. Questo passaggio può comportare uno o più operazioni di archiviazione.
`GetNeighborVertices`| Questo passaggio consente di ottenere i vertici che sono connessi a un set dei bordi. I bordi contengono la partizione di chiavi e gli ID dei vertici di loro origine e di destinazione.
`Coalesce`| Account di questo passaggio per la valutazione di due operazioni ogni volta che il `coalesce()` viene eseguito il passaggio di Gremlin.
`CartesianProductOperator`| Questo passaggio consente di calcolare un prodotto cartesiano tra due set di dati. In genere eseguiti ogni volta che i predicati `to()` o `from()` vengono usati.
`ConstantSourceOperator`| Questo passaggio consente di calcolare un'espressione e produce come risultato un valore costante.
`ProjectOperator`| Questo passaggio prepara e serializza una risposta utilizzando il risultato di operazioni precedenti.
`ProjectAggregation`| Questo passaggio prepara e serializza una risposta per un'operazione di aggregazione.

> [!NOTE]
> Questo elenco continuerà a essere aggiornato man mano che vengono aggiunti nuovi operatori.

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>Esempi su come analizzare una risposta di profilo di esecuzione

Di seguito è riportati esempi di ottimizzazioni comuni che possono essere rilevati utilizzando la risposta di profilo di esecuzione:
  - Query di tipo fan-out nascosta.
  - Query senza filtri.

### <a name="blind-fan-out-query-patterns"></a>Modelli di query di tipo fan-out non vedenti

Si supponga la seguente risposta di profilo di esecuzione da un **grafo partizionato**:

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

Da quest'ultimo, è possono effettuare le seguenti conclusioni:
- La query è una singola ricerca di ID, poiché l'istruzione Gremlin segue il modello `g.V('id')`.
- A giudicare dal `time` metrica, la latenza di questa query potrebbe essere elevata poiché si tratta [più di 10 ms per una singola operazione di lettura punto](https://docs.microsoft.com/azure/cosmos-db/introduction#guaranteed-low-latency-at-99th-percentile-worldwide).
- Se vengono esaminati i `storeOps` dell'oggetto, è possibile osservare che il `fanoutFactor` viene `5`, vale a dire che [5 partizioni](https://docs.microsoft.com/azure/cosmos-db/partition-data) erano accessibili tramite questa operazione.

Come un termine di questa analisi, è possibile determinare che la prima query accede a più partizioni rispetto al necessario. Questo problema può essere risolto specificando la chiave di partizionamento della query come predicato. Ciò causerà minore latenza e meno costi per ogni query. Per altre informazioni, vedere l'articolo sul [partizionamento di grafi](graph-partitioning.md). Una query più ottimale sarebbe `g.V('tt0093640').has('partitionKey', 't1001')`.

### <a name="unfiltered-query-patterns"></a>Modelli di query senza filtri

Confrontare le risposte di profilo di due esecuzione seguenti. Per semplicità, questi esempi utilizzano un grafo partizionato singolo.

Prima di questa query recupera tutti i vertici con l'etichetta `tweet` e quindi Ottiene i vertici in adiacenti:

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

Si noti che il profilo della stessa query, ma ora con un filtro supplementare, `has('lang', 'en')`, prima di esaminare i vertici adiacenti:

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

Queste due query raggiunto lo stesso risultato, tuttavia, prima sarà necessario più unità di richiesta perché necessaria per eseguire l'iterazione di un set di dati più grande iniziale prima dell'esecuzione di query su elementi adiacenti. È possibile osservare gli indicatori di questo comportamento quando si confrontano i parametri da entrambe le risposte seguenti:
- Il `metrics[0].time` valore è superiore nella prima risposta, che indica che questo singolo passaggio ha impiegato più tempo per risolvere.
- Il `metrics[0].counts.resultsCount` valore è più in alto anche nella risposta prima, che indica che il set di dati di lavoro iniziale non è più grande.

## <a name="next-steps"></a>Passaggi successivi
* Scopri le [funzionalità di Gremlin supportati](gremlin-support.md) in Azure Cosmos DB. 
* Altre informazioni sul [API di Gremlin in Azure Cosmos DB](graph-introduction.md).
