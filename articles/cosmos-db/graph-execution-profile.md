---
title: Usare il profilo di esecuzione per valutare le query in Azure Cosmos DB API Gremlin
description: Informazioni su come risolvere i problemi e migliorare le query Gremlin usando il passaggio del profilo di esecuzione.
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: lbosq
ms.openlocfilehash: 5705ef4fb6aa895009d554617c968543cc3fcd63
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441841"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>Come usare il passaggio del profilo di esecuzione per valutare le query Gremlin

Questo articolo fornisce una panoramica dell'uso del passaggio del profilo di esecuzione per Azure Cosmos DB database Graph dell'API Gremlin. Questo passaggio fornisce le informazioni rilevanti per la risoluzione dei problemi e le ottimizzazioni delle query ed è compatibile con qualsiasi query Gremlin che può essere eseguita su un Cosmos DB account API Gremlin.

Per usare questo passaggio, è sufficiente aggiungere la chiamata di funzione `executionProfile()` alla fine della query Gremlin. La **query Gremlin verrà eseguita** e il risultato dell'operazione restituirà un oggetto risposta JSON con il profilo di esecuzione della query.

Ad esempio:

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

Dopo aver chiamato il passaggio `executionProfile()`, la risposta sarà un oggetto JSON che include il passaggio Gremlin eseguito, il tempo totale impiegato e una matrice degli operatori di Cosmos DB Runtime che l'istruzione ha generato.

> [!NOTE]
> Questa implementazione per il profilo di esecuzione non è definita nella specifica di Apache Tinkerpop. È specifico dell'implementazione dell'API Gremlin Azure Cosmos DB.


## <a name="response-example"></a>Esempio di risposta

Di seguito è riportato un esempio annotato dell'output che verrà restituito:

> [!NOTE]
> Questo esempio viene annotato con commenti che illustrano la struttura generale della risposta. Una risposta executionProfile effettiva non conterrà commenti.

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
> Il passaggio executionProfile eseguirà la query Gremlin. Sono inclusi i passaggi `addV` o `addE`, che comporteranno la creazione e eseguiranno il commit delle modifiche specificate nella query. Di conseguenza, verranno addebitate anche le unità richiesta generate dalla query Gremlin.

## <a name="execution-profile-response-objects"></a>Oggetti di risposta del profilo di esecuzione

La risposta di una funzione executionProfile () produrrà una gerarchia di oggetti JSON con la struttura seguente:
  - **Oggetto dell'operazione Gremlin**: rappresenta l'intera operazione Gremlin eseguita. Contiene le proprietà seguenti.
    - `gremlin`: istruzione Gremlin esplicita che è stata eseguita.
    - `totalTime`: tempo, in millisecondi, durante il quale si è verificata l'esecuzione del passaggio. 
    - `metrics`: matrice che contiene tutti gli operatori di Cosmos DB Runtime eseguiti per completare la query. Questo elenco è ordinato in ordine di esecuzione.
    
  - **Cosmos DB operatori di runtime**: rappresenta tutti i componenti dell'intera operazione Gremlin. Questo elenco è ordinato in ordine di esecuzione. Ogni oggetto contiene le proprietà seguenti:
    - `name`: nome dell'operatore. Si tratta del tipo di passaggio valutato ed eseguito. Per altre informazioni, vedere la tabella seguente.
    - `time`: periodo di tempo, in millisecondi, impiegato da un determinato operatore.
    - `annotations`: contiene informazioni aggiuntive, specifiche per l'operatore che è stato eseguito.
    - `annotations.percentTime`: percentuale del tempo totale impiegato per eseguire l'operatore specifico.
    - `counts`: numero di oggetti restituiti dal livello di archiviazione da questo operatore. Questo oggetto è contenuto nel `counts.resultCount` valore scalare all'interno di.
    - `storeOps`: rappresenta un'operazione di archiviazione che può estendersi a una o più partizioni.
    - `storeOps.fanoutFactor`: rappresenta il numero di partizioni a cui si accede questa operazione di archiviazione specifica.
    - `storeOps.count`: rappresenta il numero di risultati restituiti da questa operazione di archiviazione.
    - `storeOps.size`: rappresenta le dimensioni in byte del risultato di un'operazione di archiviazione specificata.

Operatore di runtime Cosmos DB Gremlin|Description
---|---
`GetVertices`| Questo passaggio consente di ottenere un set predicato di oggetti dal livello di persistenza. 
`GetEdges`| Questo passaggio consente di ottenere i bordi adiacenti a un set di vertici. Questo passaggio può comportare una o più operazioni di archiviazione.
`GetNeighborVertices`| Questo passaggio consente di ottenere i vertici connessi a un set di bordi. I bordi contengono le chiavi di partizione e gli ID dei vertici di origine e di destinazione.
`Coalesce`| Questo passaggio esegue la valutazione di due operazioni ogni volta che viene eseguito il passaggio `coalesce()` Gremlin.
`CartesianProductOperator`| Questo passaggio calcola un prodotto cartesiano tra due set di impostazioni. Vengono in genere eseguiti ogni volta che vengono utilizzati i predicati `to()` o `from()`.
`ConstantSourceOperator`| Questo passaggio calcola un'espressione per produrre un valore costante come risultato.
`ProjectOperator`| Questo passaggio prepara e serializza una risposta usando il risultato delle operazioni precedenti.
`ProjectAggregation`| Questo passaggio prepara e serializza una risposta per un'operazione di aggregazione.

> [!NOTE]
> Questo elenco continuerà a essere aggiornato con l'aggiunta di nuovi operatori.

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>Esempi su come analizzare una risposta del profilo di esecuzione

Di seguito sono riportati alcuni esempi di ottimizzazioni comuni che è possibile individuare utilizzando la risposta del profilo di esecuzione:
  - Query di fan-out cieca.
  - Query non filtrata.

### <a name="blind-fan-out-query-patterns"></a>Modelli di query con ventola cieca

Si supponga che la risposta del profilo di esecuzione seguente da un **grafo partizionato**:

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

È possibile effettuare le seguenti conclusioni:
- La query è una singola ricerca ID, perché l'istruzione Gremlin segue il modello `g.V('id')`.
- A giudicare dalla metrica `time`, la latenza di questa query sembra essere elevata perché è [più di 10 ms per una singola operazione di lettura del punto](https://docs.microsoft.com/azure/cosmos-db/introduction#guaranteed-low-latency-at-99th-percentile-worldwide).
- Se esaminiamo l'oggetto `storeOps`, possiamo notare che la `fanoutFactor` è `5`, il che significa che è stato eseguito l'accesso a [5 partizioni](https://docs.microsoft.com/azure/cosmos-db/partition-data) da questa operazione.

Come conclusione di questa analisi, è possibile determinare che la prima query accede a più partizioni del necessario. È possibile risolvere il problema specificando la chiave di partizionamento nella query come predicato. In questo modo si otterrà una minore latenza e un costo minore per ogni query. Per altre informazioni, vedere l'articolo sul [partizionamento di grafi](graph-partitioning.md). Verrà `g.V('tt0093640').has('partitionKey', 't1001')`una query più ottimale.

### <a name="unfiltered-query-patterns"></a>Modelli di query non filtrati

Confrontare le due risposte del profilo di esecuzione seguenti. Per semplicità, in questi esempi viene usato un solo grafo partizionato.

Questa prima query recupera tutti i vertici con l'etichetta `tweet` e quindi ottiene i vertici adiacenti seguenti:

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

Si noti il profilo della stessa query, ma ora con un filtro aggiuntivo, `has('lang', 'en')`, prima di esplorare i vertici adiacenti:

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

Queste due query hanno raggiunto lo stesso risultato, ma il primo richiede più unità richiesta poiché è necessario eseguire l'iterazione di un set di dati iniziale più grande prima di eseguire una query sugli elementi adiacenti. È possibile visualizzare gli indicatori di questo comportamento quando si confrontano i parametri seguenti da entrambe le risposte:
- Il valore `metrics[0].time` è maggiore nella prima risposta, il che indica che questo singolo passaggio ha richiesto più tempo per la risoluzione.
- Il valore `metrics[0].counts.resultsCount` è anche maggiore nella prima risposta, che indica che il set di dati di lavoro iniziale era più grande.

## <a name="next-steps"></a>Passaggi successivi
* Informazioni sulle [funzionalità di Gremlin supportate](gremlin-support.md) in Azure Cosmos DB. 
* Altre informazioni sull' [API Gremlin sono disponibili in Azure Cosmos DB](graph-introduction.md).
