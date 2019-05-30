---
title: Livelli di coerenza e API di Azure Cosmos DB
description: Riconoscimento dei livelli di coerenza nelle API in Azure Cosmos DB.
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/22/2019
ms.reviewer: sngun
ms.openlocfilehash: 1129152c1823fbffb3d6c9ec918d7b8cb4426bbd
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235618"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Livelli di coerenza e API di Azure Cosmos DB

Azure Cosmos DB offre supporto nativo per rete compatibile con protocollo API per i database più diffusi. Ad esempio archiviazione MongoDB, Apache Cassandra, Gremlin e tabelle di Azure. Questi database non sono disponibile con precisione i modelli di coerenza definiti o garanzie supportato dal contratto per i livelli di coerenza. In genere forniscono solo un subset dei cinque modelli di coerenza offerti da Cosmos DB. 

Quando si usa l'API SQL, API Gremlin e API di tabella, viene utilizzato il livello di coerenza predefinito configurato per l'account Azure Cosmos. 

Quando si usa l'API Cassandra o Azure Cosmos DB API per MongoDB, applicazioni di ottenere un set completo di livelli di coerenza offerti da Apache Cassandra e MongoDB, rispettivamente, con ancora più robusta garanzia su coerenza e durabilità. Questo documento illustra i livelli di coerenza di Azure Cosmos DB corrispondenti per Apache Cassandra e MongoDB i livelli di coerenza.


## <a id="cassandra-mapping"></a>Eseguire il mapping tra i livelli di coerenza di Azure Cosmos DB e Apache Cassandra

A differenza di DB AzureCosmos, Apache Cassandra non fornisce in modo nativo con precisione garanzie di coerenza definiti.  Invece, Apache Cassandra fornisce un livello di coerenza di scrittura e un livello di coerenza di lettura, per abilitare i compromessi di disponibilità, coerenza e latenza elevati. Quando si usa l'API Cassandra di Azure Cosmos DB: 

* Il livello di coerenza di scrittura di Apache Cassandra è mappato a livello di coerenza predefinito configurato per l'account Azure Cosmos. 

* Azure Cosmos DB esegue il mapping in modo dinamico il livello di coerenza di lettura specificata da driver client Cassandra su uno dei livelli di coerenza di Azure Cosmos DB configurati in modo dinamico in una richiesta di lettura. 

Nella tabella seguente viene illustrato come i livelli di coerenza Cassandra nativi vengono eseguito il mapping ai livelli di coerenza di Azure Cosmos DB quando si usa l'API Cassandra:  

[![Mapping del modello di coerenza Cassandra](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png)](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

## <a id="mongo-mapping"></a>Il mapping tra i livelli di coerenza di MongoDB e Azure Cosmos DB

A differenza di Azure Cosmos DB, MongoDB nativo non offre garanzie di coerenza definiti in modo preciso. Al contrario, MongoDB nativo consente agli utenti di configurare le seguenti garanzie di coerenza: un problema di scrittura, un problema di lettura e la direttiva isMaster - per indirizzare le operazioni di lettura nelle repliche primarie o secondarie per ottenere il livello di coerenza desiderato. 

Quando si usa l'API di Azure Cosmos DB per MongoDB, il driver MongoDB considera l'area di scrittura come la replica primaria e tutte le altre aree vengono letti replica. È possibile scegliere quale area associata al proprio account Azure Cosmos come una replica primaria. 

Quando si usa l'API di Azure Cosmos DB per MongoDB:

* La preoccupazione di scrittura viene mappata a livello di coerenza predefinito configurato per l'account Azure Cosmos.
 
* Azure Cosmos DB esegue il mapping in modo dinamico la preoccupazione di lettura specificata dal driver del client MongoDB su uno dei livelli di coerenza di Azure Cosmos DB che è configurato in modo dinamico in una richiesta di lettura. 

* È possibile annotare un'area specifica associata all'account Azure Cosmos come "Master", rendendo l'area come prima area accessibile in scrittura. 

La tabella seguente illustra come MongoDB nativo lettura/scrittura problemi vengono mappati a livelli di coerenza Cosmos Azure quando si usa l'API di Azure Cosmos DB per MongoDB:

[![Mapping del modello MongoDB coerenza](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png)](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

Leggere altre informazioni sui livelli di coerenza e la compatibilità tra le API di Azure Cosmos DB e le API open source. Vedere gli articoli seguenti:

* [Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza](consistency-levels-tradeoffs.md)
* [Funzionalità di MongoDB supportate dall'API Azure Cosmos DB per MongoDB](mongodb-feature-support.md)
* [Funzionalità di Apache Cassandra supportate dall'API Cassandra di Azure Cosmos DB](cassandra-support.md)