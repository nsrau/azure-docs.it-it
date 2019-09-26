---
title: Compatibilità di Azure Cosmos DB Gremlin con le funzionalità di TinkerPop
description: Documentazione di riferimento problemi di compatibilità del motore grafico
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: olignat
ms.openlocfilehash: d58d1ea50251c3568161ce008db2672ff0fd6372
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910958"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Compatibilità di Azure Cosmos DB Gremlin
Il motore di Azure Cosmos DB Graph segue strettamente la specifica della procedura di attraversamento di [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) , ma esistono differenze.

## <a name="behavior-differences"></a>Differenze di comportamento

* Il motore di Azure Cosmos DB Graph esegue l'attraversamento della ***Larghezza-primo*** , mentre TinkerPop Gremlin è la profondità. Questo comportamento consente di ottenere prestazioni migliori in un sistema scalabile orizzontalmente, come Cosmos DB. 

## <a name="unsupported-features"></a>Funzionalità non supportate

* ***[Gremlin Bytecode](http://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** è una specifica non dipendente da un linguaggio di programmazione per gli attraversamenti grafici. Cosmos DB Graph non lo supporta ancora. Usare ```GremlinClient.SubmitAsync()``` e passare l'attraversamento come stringa di testo.

* ***```property(set, 'xyz', 1)```*** la cardinalità impostata non è attualmente supportata. In alternativa, utilizzare ```property(list, 'xyz', 1)```.

* ***```match()```*** consente di eseguire query sui grafici usando criteri di ricerca dichiarativi. Questa funzionalità non è disponibile.

* ***Gli oggetti come proprietà*** su vertici o bordi non sono supportati. Le proprietà possono essere solo tipi primitivi o matrici.

* ***Ordinamento in base alle proprietà della matrice*** ```.order().by(<array property>)``` non è supportato. L'ordinamento è supportato solo per tipi primitivi.

* I ***tipi JSON non primitivi*** non sono supportati. Usare ```string```i ```number```tipi, ```true``` o ./ ```false``` ```null```i valori non sono supportati. 

* Il serializzatore ***GraphSONv3*** non è attualmente disponibile.

* ***Le transazioni*** non sono supportate a causa della natura distribuita del sistema.  Configurare il modello di coerenza appropriato nell'account Gremlin per "leggere le proprie scritture" e usare la concorrenza ottimistica per risolvere le Scritture in conflitto.

## <a name="next-steps"></a>Passaggi successivi
* Visitare [Cosmos DB pagina voce utente](https://feedback.azure.com/forums/263030-azure-cosmos-db) per condividere commenti e suggerimenti e aiutare il team a concentrarsi sulle funzionalità più importanti.
