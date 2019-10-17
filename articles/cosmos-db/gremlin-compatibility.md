---
title: Compatibilità di Azure Cosmos DB Gremlin con le funzionalità di TinkerPop
description: Documentazione di riferimento problemi di compatibilità del motore grafico
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 0ed5824859b8463919a809861993f9f98a4f9251
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72327022"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Compatibilità di Azure Cosmos DB Gremlin
Il motore di Azure Cosmos DB Graph segue strettamente la specifica della procedura di attraversamento di [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) , ma esistono differenze.

## <a name="behavior-differences"></a>Differenze di comportamento

* Il motore di Azure Cosmos DB Graph esegue l'attraversamento della ***Larghezza-primo*** , mentre TinkerPop Gremlin è la profondità. Questo comportamento consente di ottenere prestazioni migliori in un sistema scalabile orizzontalmente, come Cosmos DB. 

## <a name="unsupported-features"></a>Funzionalità non supportate

* ***[Gremlin Bytecode](http://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** è una specifica non dipendente da un linguaggio di programmazione per gli attraversamenti grafici. Cosmos DB Graph non lo supporta ancora. Usare ```GremlinClient.SubmitAsync()``` e passare l'attraversamento come stringa di testo.

* la cardinalità set ***```property(set, 'xyz', 1)```*** non è attualmente supportata. Usare invece ```property(list, 'xyz', 1)```.

* ***```match()``` consente di*** eseguire query sui grafici usando criteri di ricerca dichiarativi. Questa funzionalità non è disponibile.

* ***Gli oggetti come proprietà*** su vertici o bordi non sono supportati. Le proprietà possono essere solo tipi primitivi o matrici.

* L' ***ordinamento in base alle proprietà di matrice*** ```.order().by(<array property>)``` non è supportato. L'ordinamento è supportato solo per tipi primitivi.

* I ***tipi JSON non primitivi*** non sono supportati. Usare i tipi ```string```, ```number``` o ```true``` @ no__t-3 @ no__t-4. i valori @no__t 0 non sono supportati. 

* Il serializzatore ***GraphSONv3*** non è attualmente disponibile.

* ***Le transazioni*** non sono supportate a causa della natura distribuita del sistema.  Configurare il modello di coerenza appropriato nell'account Gremlin per "leggere le proprie scritture" e usare la concorrenza ottimistica per risolvere le Scritture in conflitto.

## <a name="next-steps"></a>Passaggi successivi
* Visitare [Cosmos DB pagina voce utente](https://feedback.azure.com/forums/263030-azure-cosmos-db) per condividere commenti e suggerimenti e aiutare il team a concentrarsi sulle funzionalità più importanti.
