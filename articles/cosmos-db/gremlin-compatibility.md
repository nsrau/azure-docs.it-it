---
title: Compatibilità di Azure Cosmos DB Gremlin con le funzionalità TinkerPopAzure Cosmos DB Gremlin compatibility with TinkerPop features
description: Documentazione di riferimento Problemi di compatibilità del motore Graph
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 989a033a843b861c34dc9dbdbced50399f8e5cd7
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81449885"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Compatibilità con Azure Cosmos DB Gremlin
Il motore di Azure Cosmos DB Graph segue da vicino la specifica dei passaggi di attraversamento [Apache TinkerPop,](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) ma esistono differenze.

## <a name="behavior-differences"></a>Differenze di comportamento

* Il motore Azure Cosmos DB Graph esegue un attraversamento ***breadth-first*** mentre TinkerPop Gremlin è il primo approfondimento. Questo comportamento consente di ottenere prestazioni migliori in un sistema scalabile orizzontalmente come Cosmos DB. 

## <a name="unsupported-features"></a>Funzionalità non supportate

* ***[Gremlin Bytecode](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** è una specifica non dipendente da un linguaggio di programmazione per gli attraversamenti grafici. Cosmos DB Graph non lo supporta ancora. Utilizzare `GremlinClient.SubmitAsync()` e passare l'attraversamento come stringa di testo.

* ***`property(set, 'xyz', 1)`*** impostato la cardinalità non è supportato oggi. Usare invece `property(list, 'xyz', 1)`. Per ulteriori informazioni, consultate [Proprietà Vertex con TinkerPop.](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties)

* ***`atch()`*** consente di eseguire query sui grafici utilizzando criteri di ricerca dichiarativi. Questa funzionalità non è disponibile.

* ***Gli oggetti come proprietà*** su vertici o bordi non sono supportati. Le proprietà possono essere solo tipi primitivi o matrici.

* ***L'ordinamento in base alle proprietà*** `order().by(<array property>)` della matrice non è supportato. L'ordinamento è supportato solo per tipi primitivi.

* ***I tipi JSON non primitivi*** non sono supportati. Utilizzare `string` `number`i `true` / `false` tipi , , o . `null`non sono supportati. 

* Il serializzatore ***GraphSONv3*** non è attualmente supportato. Utilizzare `GraphSONv2` le classi Serializer, Reader e Writer nella configurazione della connessione. I risultati restituiti dall'API Azure Cosmos DB Gremlin non hanno lo stesso formato del formato GraphSON. 

* **Le espressioni e** le funzioni lambda non sono attualmente supportate. Sono inclusi `.map{<expression>}`i `.by{<expression>}`funzioni `.filter{<expression>}` , , e . Per ulteriori informazioni e su come riscriverli utilizzando i passaggi di Gremlin, vedere [Una nota sulle lambda](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas).

* ***Le transazioni*** non sono supportate a causa della natura distribuita del sistema.  Configurare il modello di coerenza appropriato nell'account Gremlin per "leggere le proprie scritture" e usare la concorrenza ottimistica per risolvere le scritture in conflitto.

## <a name="next-steps"></a>Passaggi successivi
* Visita la pagina [vocale dell'utente Cosmos DB](https://feedback.azure.com/forums/263030-azure-cosmos-db) per condividere feedback e aiutare il team a concentrarsi sulle funzionalità che sono importanti per te.
