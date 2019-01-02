---
title: Livelli di coerenza e API di Azure Cosmos DB
description: Riconoscimento dei livelli di coerenza nelle API in Azure Cosmos DB.
keywords: coerenza, azure cosmos db, azure, modelli, mongodb, cassandra, grafico, tabella, Microsoft azure
services: cosmos-db
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.openlocfilehash: 277a064d93e2ebcea82f3909b3fd16328a775105
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52832497"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Livelli di coerenza e API di Azure Cosmos DB

I cinque modelli di coerenza offerti da Azure Cosmos DB sono supportati in modo nativo dall'API SQL di Cosmos DB. Quando si usa Azure Cosmos DB, l'API SQL è quella predefinita. 

Azure Cosmos DB fornisce anche il supporto nativo per le API compatibili con protocollo di rete per i database più diffusi. I database includono l'archiviazione MongoDB, Apache Cassandra, Gremlin e Azure Table. Questi database non offrono né modelli di coerenza definiti in modo preciso né le garanzie supportate da contratti di servizio per i livelli di coerenza. In genere forniscono solo un subset dei cinque modelli di coerenza offerti da Cosmos DB. Per l'API SQL, l'API Gremlin e l'API Tabella, viene utilizzato il livello di coerenza predefinito configurato per l'account Azure Cosmos DB. 

Le sezioni seguenti illustrano il mapping tra la coerenza dei dati richiesta da un driver del client OSS per Apache Cassandra 4.x e MongoDB 3.4. Questo documento illustra anche i livelli di coerenza di Azure Cosmos DB corrispondenti per Apache Cassandra e MongoDB.

## <a id="cassandra-mapping"></a>Eseguire il mapping tra i livelli di coerenza di Azure Cosmos DB e Apache Cassandra

La tabella illustra il mapping per la "coerenza in lettura" tra il client di Apache Cassandra 4.x e il livello di coerenza predefinito in Azure Cosmos DB. La tabella mostra le distribuzioni multiarea e in una singola area.

| **Apache Cassandra 4.x** | **Azure Cosmos DB (multiarea)** | **Azure Cosmos DB (area singola)** |
| - | - | - |
| ONE TWO THREE | Prefisso coerente | Prefisso coerente |
| LOCAL_ONE | Prefisso coerente | Prefisso coerente |
| QUORUM, ALL, SERIAL | L'impostazione predefinita è il decadimento ristretto. Assoluta è in anteprima privata. | Assoluta |
| LOCAL_QUORUM | Obsolescenza associata | Assoluta |
| LOCAL_SERIAL | Obsolescenza associata | Assoluta |

## <a id="mongo-mapping"></a>Eseguire il mapping tra i livelli di coerenza MongoDB 3.4 e Azure Cosmos DB

La tabella seguente illustra il mapping per i "problemi di lettura" tra MongoDB 3.4 e il livello di coerenza predefinito in Azure Cosmos DB. La tabella mostra le distribuzioni multiarea e in una singola area.

| **MongoDB 3.4** | **Azure Cosmos DB (multiarea)** | **Azure Cosmos DB (area singola)** |
| - | - | - |
| Linearizzabile | Assoluta | Assoluta |
| Maggioranza | Obsolescenza associata | Assoluta |
| Local | Prefisso coerente | Prefisso coerente |

## <a name="next-steps"></a>Passaggi successivi

Leggere altre informazioni sui livelli di coerenza e la compatibilità tra le API di Azure Cosmos DB e le API open source. Vedere gli articoli seguenti:

* [Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza](consistency-levels-tradeoffs.md)
* [Funzionalità di MongoDB supportate dall'API Azure Cosmos DB MongoDB](mongodb-feature-support.md)
* [Funzionalità di Apache Cassandra supportate dall'API Cassandra di Azure Cosmos DB](cassandra-support.md)