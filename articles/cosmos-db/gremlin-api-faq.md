---
title: Domande frequenti sull'API Gremlin in Azure Cosmos DB
description: Risposte alle domande frequenti sull'API Gremlin in Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: 416cf4f027b6f1e72641324be39ba0304301db37
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82614499"
---
# <a name="frequently-asked-questions-about-the-gremlin-api-in-azure-cosmos-db"></a>Domande frequenti sull'API Gremlin in Azure Cosmos DB

Questo articolo illustra le risposte ad alcune domande frequenti sull'API Gremlin in Azure Cosmos DB.

## <a name="how-to-evaluate-the-efficiency-of-gremlin-queries"></a>Come valutare l'efficienza delle query Gremlin

Il passaggio di anteprima **executionProfile()** può essere usato per fornire un'analisi del piano di esecuzione della query. Questo passaggio deve essere aggiunto alla fine di qualsiasi query di Gremlin, come illustrato dall'esempio seguente:

**Esempio di query**

```
g.V('mary').out('knows').executionProfile()
```

**Output di esempio**

```json
[
  {
    "gremlin": "g.V('mary').out('knows').executionProfile()",
    "totalTime": 8,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 3,
        "annotations": {
          "percentTime": 37.5
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 62.5
        },
        "counts": {
          "resultCount": 0
        },
        "storeOps": [
          {
            "count": 0,
            "size": 0,
            "time": 0.6
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      }
    ]
  }
]
```

L'output del profilo precedente Mostra la quantità di tempo impiegato per ottenere gli oggetti vertex, gli oggetti Edge e le dimensioni del set di dati di lavoro. Queste informazioni sono correlate alle misure dei costi standard per le query di Azure Cosmos DB.

## <a name="other-frequently-asked-questions"></a>Altre domande frequenti

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>In che modo vengono addebitate le unità richiesta al secondo quando si eseguono query in un database a grafi?

Tutti gli oggetti, i vertici e gli archi dei grafi vengono rappresentati come documenti JSON nel back-end. Poiché una query Gremlin può modificare uno o più oggetti del grafo alla volta, il costo associato a tale operazione è direttamente correlato agli oggetti e agli archi elaborati dalla query. Si tratta dello stesso processo usato da Azure Cosmos DB per tutte le altre API. Per altre informazioni, vedere [unità richiesta in Azure Cosmos DB](request-units.md).

L'addebito per unità richiesta viene basato sul set di dati in uso dell'attraversamento e non sul set di risultati. Ad esempio, se una query ha come obiettivo il recupero di un singolo vertice come risultato ma deve attraversare altri oggetti per ottenere tale risultato, il costo sarà basato su tutti gli oggetti del grafo necessari per il calcolo del vertice specifico.

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>Qual è la scalabilità massima consentita per un database Graph in Azure Cosmos DB API Gremlin?

Azure Cosmos DB usa il [partizionamento orizzontale](partition-data.md) per gestire automaticamente gli incrementi nei requisiti relativi a risorse di archiviazione e velocità effettiva. La velocità effettiva massima e la capacità di archiviazione di un carico di lavoro sono determinate dal numero di partizioni associate a un determinato contenitore. Tuttavia, un contenitore dell'API Gremlin dispone di un set specifico di linee guida per garantire un'esperienza di prestazioni adeguata su larga scala. Per altre informazioni sul partizionamento e sulle procedure consigliate, vedere l'articolo relativo al [partizionamento in Azure Cosmos DB](partition-data.md).

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>Per lo sviluppo C#/.NET è necessario usare il pacchetto Microsoft.Azure.Graphs o Gremlin.NET?

L'API Gremlin di Azure Cosmos DB sfrutta i vantaggi dei driver open source come connettori principali per il servizio. L'opzione consigliata consiste quindi nell'usare [driver supportati da Apache Tinkerpop](https://tinkerpop.apache.org/).

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Come ci si può proteggere da attacchi di tipo injection con driver Gremlin?

La maggior parte dei driver di Apache Tinkerpop Gremlin nativi consente l'opzione per fornire un dizionario di parametri per l'esecuzione di query. Ecco un esempio di come ottenere questo risultato in [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) e in [Gremlin-Javascript](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/master/app.js).

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>Perché viene ricevuto l'errore "errore di compilazione della query Gremlin: Impossibile trovare un metodo"?

L'API Gremlin di Azure Cosmos DB implementa un sottoinsieme di funzionalità definite nella superficie di attacco di Gremlin. Per le procedure supportate e altre informazioni, vedere l'articolo [Supporto Gremlin](gremlin-support.md).

La soluzione alternativa migliore consiste nel riscrivere la procedura Gremlin necessaria con le funzionalità supportate, poiché tutti i passaggi Gremlin essenziali sono supportati da Azure Cosmos DB.

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>Perché viene visualizzato il messaggio "WebSocketException: il server ha restituito il codice di stato" 200 "quando è previsto il codice di stato" 101 "?

Questo errore viene probabilmente generato quando viene usato un endpoint non corretto. L'endpoint che genera questo errore ha il criterio seguente:

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

Endpoint dei documenti per il database a grafi.  L'endpoint corretto da usare è l'endpoint Gremlin, che ha il formato seguente:

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>Perché viene ricevuto l'errore "RequestRateIsTooLarge"?

Questo errore indica che le unità richiesta allocate al secondo non sono sufficienti per la query. L'errore viene in genere visualizzato quando si esegue una query che ottiene tutti i vertici:

```
// Query example:
g.V()
```

La query proverà a recuperare tutti i vertici dal grafo. Il costo di questa query in termini di unità richiesta sarà uguale almeno al numero di vertici. È necessario modificare l'impostazione UR/s in modo specifico per questa query.

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>Perché le connessioni del driver Gremlin vengono interrotte?

Una connessione Gremlin viene effettuata tramite una connessione WebSocket. Benché le connessioni WebSocket non abbiano una durata specifica, l'API Gremlin di Azure Cosmos DB interromperà le connessioni inattive dopo 30 minuti di inattività.

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>Perché non è possibile usare le chiamate API Fluent nei driver Gremlin nativi?

Le chiamate API Fluent non sono ancora supportate dall'API Gremlin di Azure Cosmos DB. Le chiamate API Fluent richiedono una funzionalità di formattazione interna denominata supporto per bytecode che non è attualmente supportata dall'API Gremlin di Azure Cosmos DB. A causa dello stesso motivo, non è attualmente supportato il driver Gremlin-JavaScript più recente.

## <a name="next-steps"></a>Passaggi successivi

* [Supporto del protocollo wire di Azure Cosmos DB Gremlin](gremlin-support.md)
* Creare, eseguire query e attraversare un database di Azure Cosmos DB Graph usando la [console Gremlin](create-graph-gremlin-console.md)
