---
title: Livelli di coerenza e API in Azure Cosmos DB | Microsoft Docs
description: Riconoscimento dei livelli di coerenza nelle API in Azure Cosmos DB.
keywords: coerenza, azure cosmos db, azure, modelli, mongodb, cassandra, grafico, tabella, Microsoft azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: mjbrown
ms.openlocfilehash: ed08b90b9e216ee8713bfe445e98144bf2ba02d4
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50243996"
---
# <a name="consistency-levels-and-cosmos-db-apis"></a>Livelli di coerenza e API di Cosmos DB

I cinque modelli di coerenza sono supportati in modo nativo dall'API SQL, ovvero l'API predefinita quando si usa Cosmos DB. Oltre all'API SQL, Cosmos DB offre anche supporto nativo per le API compatibili con il protocollo di trasmissione per i database più diffusi, ad esempio MongoDB, Apache Cassandra, Gremlin e le tabelle di Azure. Questi database non offrono nè modelli di coerenza definiti in modo preciso né le garanzie supportate da contratti di servizio per i livelli di coerenza e fornisce in genere solo un subset dei cinque modelli di coerenza offerti da Cosmos DB. Per API SQL, API Gremlin e API Tabella, viene utilizzato il livello di coerenza predefinito configurato per l'account Cosmos.

La tabella seguente illustra il mapping tra la coerenza dei dati richiesto da un driver del client OSS per Apache Cassandra 4.x e MongoDB 3.4, quando si usa l'API Cassandra e l'API MongoDB, rispettivamente, e i livelli di coerenza di Cosmos DB corrispondenti.

## <a id="cassandra-mapping"></a>Eseguire il mapping dei livelli di coerenza di Cosmos DB e Apache Cassandra

La tabella seguente illustra il mapping per la coerenza in lettura tra il client di Apache Cassandra 4.x e il livello di coerenza "Predefinito" di Cosmos DB per una distribuzione in più aree e in una singola area.

| **Apache Cassandra 4.x** | **COSMOS DB (multiarea)** | **COSMOS DB (area singola)** |
| - | - | - |
| ONE TWO THREE | Prefisso coerente | Prefisso coerente |
| LOCAL_ONE | Prefisso coerente | Prefisso coerente |
| QUORUM, ALL, SERIAL | Decadimento ristretto (impostazione predefinita) o complesso (in anteprima privata) | Assoluta |
| LOCAL_QUORUM | Decadimento ristretto | Assoluta |
| LOCAL_SERIAL | Decadimento ristretto | Assoluta |

## <a id="mongo-mapping"></a>Eseguire il mapping tra i livelli di coerenza MongoDB 3.4 e Cosmos DB

La tabella seguente illustra il mapping per il livello di coerenza "Predefinito" per i "problemi di lettura" di MongoDB 3.4.x e Cosmos DB per una distribuzione in più aree e in una singola area.

| **MongoDB 3.4** | **COSMOS DB (multiarea)** | **COSMOS DB (area singola)** |
| - | - | - |
| Linearizzabile | Assoluta | Assoluta |
| Maggioranza | Decadimento ristretto | Assoluta |
| Local | Prefisso coerente | Prefisso coerente |

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui livelli di coerenza e la compatibilità tra le API di Cosmos DB e le API open source sono disponibili negli articoli seguenti:

* [Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza](consistency-levels-tradeoffs.md)
* [Funzionalità di MongoDB supportate dall'API MongoDB di Cosmos DB](mongodb-feature-support.md)
* [Funzionalità di Apache Cassandra supportate dall'API Cassandra di Azure Cosmos DB](cassandra-support.md)