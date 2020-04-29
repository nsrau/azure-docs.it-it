---
title: Compatibilità di Azure Cosmos DB Gremlin con le funzionalità di TinkerPop
description: Documentazione di riferimento problemi di compatibilità del motore grafico
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 989a033a843b861c34dc9dbdbced50399f8e5cd7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81449885"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Compatibilità di Azure Cosmos DB Gremlin
Il motore di Azure Cosmos DB Graph segue strettamente la specifica della procedura di attraversamento di [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) , ma esistono differenze.

## <a name="behavior-differences"></a>Differenze di comportamento

* Il motore di Azure Cosmos DB Graph esegue l'attraversamento della ***Larghezza-primo*** , mentre TinkerPop Gremlin è la profondità. Questo comportamento consente di ottenere prestazioni migliori in un sistema scalabile orizzontalmente, come Cosmos DB. 

## <a name="unsupported-features"></a>Funzionalità non supportate

* ***[Gremlin Bytecode](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** è una specifica non dipendente da un linguaggio di programmazione per gli attraversamenti grafici. Cosmos DB Graph non lo supporta ancora. Usare `GremlinClient.SubmitAsync()` e passare l'attraversamento come stringa di testo.

* ***`property(set, 'xyz', 1)`*** la cardinalità impostata non è attualmente supportata. Usare invece `property(list, 'xyz', 1)`. Per altre informazioni, vedere [Proprietà Vertex con TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties).

* ***`atch()`*** consente di eseguire query sui grafici usando criteri di ricerca dichiarativi. Questa funzionalità non è disponibile.

* ***Gli oggetti come proprietà*** su vertici o bordi non sono supportati. Le proprietà possono essere solo tipi primitivi o matrici.

* L' ***ordinamento in base alle proprietà*** `order().by(<array property>)` della matrice non è supportato. L'ordinamento è supportato solo per tipi primitivi.

* I ***tipi JSON non primitivi*** non sono supportati. Usare `string`i `number`tipi, `true` / `false` o. `null`i valori non sono supportati. 

* Il serializzatore ***GraphSONv3*** non è attualmente supportato. Utilizzare `GraphSONv2` le classi serializer, Reader e writer nella configurazione della connessione. I risultati restituiti dall'API Azure Cosmos DB Gremlin non hanno lo stesso formato del formato GraphSON. 

* **Le funzioni e le espressioni lambda** non sono attualmente supportate. Sono incluse le `.map{<expression>}` `.filter{<expression>}` funzioni, `.by{<expression>}`e. Per altre informazioni e per informazioni su come riscriverle usando i passaggi Gremlin, vedere [una nota sulle espressioni lambda](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas).

* ***Le transazioni*** non sono supportate a causa della natura distribuita del sistema.  Configurare il modello di coerenza appropriato nell'account Gremlin per "leggere le proprie scritture" e usare la concorrenza ottimistica per risolvere le Scritture in conflitto.

## <a name="next-steps"></a>Passaggi successivi
* Visitare [Cosmos DB pagina voce utente](https://feedback.azure.com/forums/263030-azure-cosmos-db) per condividere commenti e suggerimenti e aiutare il team a concentrarsi sulle funzionalità più importanti.
