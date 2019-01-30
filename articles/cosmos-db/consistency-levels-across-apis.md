---
title: Livelli di coerenza e API di Azure Cosmos DB
description: Riconoscimento dei livelli di coerenza nelle API in Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.reviewer: sngun
ms.openlocfilehash: 4d2994ea6ab6d6472ec56f0f2e378062590c8920
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54806998"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Livelli di coerenza e API di Azure Cosmos DB

I cinque modelli di coerenza offerti da Azure Cosmos DB sono supportati in modo nativo dall'API SQL di Cosmos DB. Quando si usa Azure Cosmos DB, l'API SQL è quella predefinita. 

Azure Cosmos DB fornisce anche il supporto nativo per le API compatibili con protocollo di rete per i database più diffusi. I database includono l'archiviazione MongoDB, Apache Cassandra, Gremlin e Azure Table. Questi database non offrono né modelli di coerenza definiti in modo preciso né le garanzie supportate da contratti di servizio per i livelli di coerenza. In genere forniscono solo un subset dei cinque modelli di coerenza offerti da Cosmos DB. Per l'API SQL, l'API Gremlin e l'API Tabella, viene utilizzato il livello di coerenza predefinito configurato per l'account Azure Cosmos DB. 

Le sezioni seguenti illustrano il mapping tra la coerenza dei dati richiesta da un driver del client OSS per Apache Cassandra 4.x e MongoDB 3.4. Questo documento illustra anche i livelli di coerenza di Azure Cosmos DB corrispondenti per Apache Cassandra e MongoDB.

## <a id="cassandra-mapping"></a>Eseguire il mapping tra i livelli di coerenza di Azure Cosmos DB e Apache Cassandra

La tabella seguente illustra il mapping per la coerenza tra Apache Cassandra e i livelli di coerenza in Azure Cosmos DB. Per ognuno dei livelli di coerenza in lettura e scrittura di Cassandra, il livello di coerenza di Cosmos DB corrispondente offre garanzie più solide, ossia più rigorose.

La tabella seguente illustra il **mapping per la coerenza in scrittura** tra Azure Cosmos DB e Cassandra:

| Cassandra | Azure Cosmos DB | Garanzia |
| - | - | - |
|ALL|Assoluta  | Linearità |
| EACH_QUORUM   | Assoluta    | Linearità | 
| QUORUM, SERIAL |  Assoluta |    Linearità |
| LOCAL_QUORUM, THREE, TWO, ONE, LOCAL_ONE, ANY | Prefisso coerente |Prefisso coerente globale |
| EACH_QUORUM   | Assoluta    | Linearità |
| QUORUM, SERIAL |  Assoluta |    Linearità |
| LOCAL_QUORUM, THREE, TWO, ONE, LOCAL_ONE, ANY | Prefisso coerente | Prefisso coerente globale |
| QUORUM, SERIAL | Assoluta   | Linearità |
| LOCAL_QUORUM, THREE, TWO, ONE, LOCAL_ONE, ANY | Prefisso coerente | Prefisso coerente globale |
| LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE    | Decadimento ristretto | <ul><li>Decadimento ristretto.</li><li>Al massimo 1000 versioni o tempo inferiore al tempo t.</li><li>Lettura dell'ultimo valore di cui è stato eseguito il commit nell'area.</li></ul> |
| ONE, LOCAL_ONE, ANY   | Prefisso coerente | Prefisso coerente per area |

La tabella seguente illustra il **mapping per la coerenza in lettura** tra Azure Cosmos DB e Cassandra:

| Cassandra | Azure Cosmos DB | Garanzia |
| - | - | - |
| ALL, QUORUM, SERIAL, LOCAL_QUORUM, LOCAL_SERIAL, THREE, TWO, ONE, LOCAL_ONE | Assoluta  | Linearità|
| ALL, QUORUM, SERIAL, LOCAL_QUORUM, LOCAL_SERIAL, THREE, TWO   |Assoluta |   Linearità |
|LOCAL_ONE, ONE | Prefisso coerente | Prefisso coerente globale |
| ALL, QUORUM, SERIAL   | Assoluta    | Linearità |
| LOCAL_ONE, ONE, LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE |  Prefisso coerente   | Prefisso coerente globale |
| LOCAL_ONE, ONE, TWO, THREE, LOCAL_QUORUM, QUORUM |    Prefisso coerente   | Prefisso coerente globale |
| ALL, QUORUM, SERIAL, LOCAL_QUORUM, LOCAL_SERIAL, THREE, TWO   |Assoluta |   Linearità |
| LOCAL_ONE, ONE    | Prefisso coerente | Prefisso coerente globale|
| ALL, QUORUM, SERIAL   Assoluta  Linearità
LOCAL_ONE, ONE, LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE  |Prefisso coerente  | Prefisso coerente globale |
|ALL    |Assoluta |Linearità |
| LOCAL_ONE, ONE, TWO, THREE, LOCAL_QUORUM, QUORUM  |Prefisso coerente  |Prefisso coerente globale|
|ALL, QUORUM, SERIAL    Assoluta  Linearità
LOCAL_ONE, ONE, LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE  |Prefisso coerente  |Prefisso coerente globale |
|ALL    |Assoluta | Linearità |
| LOCAL_ONE, ONE, TWO, THREE, LOCAL_QUORUM, QUORUM  | Prefisso coerente | Prefisso coerente globale |
| QUORUM, LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE |  Decadimento ristretto   | <ul><li>Decadimento ristretto.</li><li>Al massimo 1000 versioni o tempo inferiore al tempo t. </li><li>Lettura dell'ultimo valore di cui è stato eseguito il commit nell'area.</li></ul>
| LOCAL_ONE, ONE |Prefisso coerente | Prefisso coerente per area |
| LOCAL_ONE, ONE, TWO, THREE, LOCAL_QUORUM, QUORUM  | Prefisso coerente | Prefisso coerente per area |


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
* [Funzionalità di MongoDB supportate dall'API Azure Cosmos DB per MongoDB](mongodb-feature-support.md)
* [Funzionalità di Apache Cassandra supportate dall'API Cassandra di Azure Cosmos DB](cassandra-support.md)