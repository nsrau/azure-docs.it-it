---
title: Compatibilità di Azure Cosmos DB Gremlin con le funzionalità di TinkerPop
description: Documentazione di riferimento problemi di compatibilità del motore grafico
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 7257246f618e3028534f3ebd60eaf6f94a3a4720
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87092508"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Compatibilità di Azure Cosmos DB Gremlin
Il motore di Azure Cosmos DB Graph segue strettamente la specifica dei passaggi di attraversamento di [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) , ma esistono differenze nell'implementazione specifiche per Azure Cosmos DB. Per informazioni sull'elenco di passaggi di Gremlin supportati, vedere l'articolo [supporto del protocollo Wire API Gremlin](gremlin-support.md) .

## <a name="behavior-differences"></a>Differenze di comportamento

* Il motore di Azure Cosmos DB Graph esegue l'attraversamento della ***Larghezza-primo*** , mentre TinkerPop Gremlin è la profondità. Questo comportamento consente di ottenere prestazioni migliori in un sistema scalabile orizzontalmente, come Cosmos DB. 

## <a name="unsupported-features"></a>Funzionalità non supportate

* ***[Gremlin Bytecode](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** è una specifica non dipendente da un linguaggio di programmazione per gli attraversamenti grafici. Cosmos DB Graph non lo supporta ancora. Usare `GremlinClient.SubmitAsync()` e passare l'attraversamento come stringa di testo.

* ***`property(set, 'xyz', 1)`*** la cardinalità impostata non è attualmente supportata. In alternativa, utilizzare `property(list, 'xyz', 1)`. Per altre informazioni, vedere [Proprietà Vertex con TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties).

* Il *** `match()` passaggio*** non è attualmente disponibile. Questo passaggio fornisce funzionalità di query dichiarative.

* ***Gli oggetti come proprietà*** su vertici o bordi non sono supportati. Le proprietà possono essere solo tipi primitivi o matrici.

* ***Ordinamento in base alle proprietà*** `order().by(<array property>)` della matrice non è supportato. L'ordinamento è supportato solo per tipi primitivi.

* I ***tipi JSON non primitivi*** non sono supportati. Usare `string` i `number` tipi, o `true` / `false` . `null` i valori non sono supportati. 

* Il serializzatore ***GraphSONv3*** non è attualmente supportato. Utilizzare le `GraphSONv2` classi serializer, Reader e writer nella configurazione della connessione. I risultati restituiti dall'API Azure Cosmos DB Gremlin non hanno lo stesso formato del formato GraphSON. 

* **Le funzioni e le espressioni lambda** non sono attualmente supportate. Sono incluse le `.map{<expression>}` funzioni, `.by{<expression>}` e `.filter{<expression>}` . Per altre informazioni e per informazioni su come riscriverle usando i passaggi Gremlin, vedere [una nota sulle espressioni lambda](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas).

* ***Le transazioni*** non sono supportate a causa della natura distribuita del sistema.  Configurare il modello di coerenza appropriato nell'account Gremlin per "leggere le proprie scritture" e usare la concorrenza ottimistica per risolvere le Scritture in conflitto.

## <a name="known-limitations"></a>Limitazioni note

* **Utilizzo degli indici per le query Gremlin con `.V()` passaggi intermedi**: attualmente, solo la prima `.V()` chiamata di un attraversamento utilizzerà l'indice per risolvere eventuali filtri o predicati collegati. Le chiamate successive non consulteranno l'indice, che può aumentare la latenza e il costo della query.
    
    Presupponendo l'indicizzazione predefinita, una tipica query di lettura Gremlin che inizia con il `.V()` passaggio utilizzerà i parametri nei passaggi di filtro collegati, ad esempio `.has()` o `.where()` per ottimizzare i costi e le prestazioni della query. Ad esempio:

    ```java
    g.V().has('category', 'A')
    ```

    Tuttavia, quando `.V()` nella query Gremlin viene incluso più di un passaggio, la risoluzione dei dati per la query potrebbe non essere ottimale. Eseguire la query seguente come esempio:

    ```java
    g.V().has('category', 'A').as('a').V().has('category', 'B').as('b').select('a', 'b')
    ```

    Questa query restituirà due gruppi di vertici in base alla relativa proprietà denominata `category` . In questo caso, solo la prima chiamata a utilizzerà `g.V().has('category', 'A')` l'indice per risolvere i vertici in base ai valori delle relative proprietà.

    Una soluzione alternativa per questa query consiste nell'usare passaggi di sottoattraversamento, ad esempio `.map()` e `union()` . Questo è esemplificato di seguito:

    ```java
    // Query workaround using .map()
    g.V().has('category', 'A').as('a').map(__.V().has('category', 'B')).as('b').select('a','b')

    // Query workaround using .union()
    g.V().has('category', 'A').fold().union(unfold(), __.V().has('category', 'B'))
    ```

    Per esaminare le prestazioni delle query, è possibile usare il [ `executionProfile()` passaggio Gremlin](graph-execution-profile.md).

## <a name="next-steps"></a>Passaggi successivi
* Visitare [Cosmos DB pagina voce utente](https://feedback.azure.com/forums/263030-azure-cosmos-db) per condividere commenti e suggerimenti e aiutare il team a concentrarsi sulle funzionalità più importanti.
