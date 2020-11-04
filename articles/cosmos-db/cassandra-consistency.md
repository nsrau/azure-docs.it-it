---
title: Livelli di coerenza di Apache Cassandra e Azure Cosmos DB
description: I livelli di coerenza di Apache Cassandra e Azure Cosmos DB.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: a02076c09d038b02c0ab846440ad14e799271733
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339955"
---
# <a name="apache-cassandra-and-azure-cosmos-db-consistency-levels"></a>Livelli di coerenza di Apache Cassandra e Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Diversamente da Azure Cosmos DB, Apache Cassandra non fornisce in modo nativo garanzie di coerenza definite in modo nativo. Apache Cassandra fornisce invece un livello di coerenza di scrittura e un livello di coerenza di lettura per consentire i compromessi di disponibilità elevata, coerenza e latenza. Quando si usa API Cassandra di Azure Cosmos DB:

* Il livello di coerenza di scrittura di Apache Cassandra viene mappato al livello di coerenza predefinito configurato nell'account Azure Cosmos. La coerenza per un'operazione di scrittura (CL) non può essere modificata in base alle singole richieste.

* Azure Cosmos DB eseguirà il mapping dinamico del livello di coerenza di lettura specificato dal driver del client Cassandra a uno dei livelli di coerenza Azure Cosmos DB configurati in modo dinamico in una richiesta di lettura.

## <a name="mapping-consistency-levels"></a>Mapping dei livelli di coerenza

La tabella seguente illustra come viene eseguito il mapping dei livelli di coerenza di Cassandra nativi ai livelli di coerenza del Azure Cosmos DB quando si usa API Cassandra:  

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" alt-text="Mapping del modello di coerenza Cassandra" lightbox="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" :::

Se l'account Azure Cosmos è configurato con un livello di coerenza diverso da quello della coerenza assoluta, è possibile determinare la probabilità che i client ottengano letture solide e coerenti per i carichi di lavoro esaminando la metrica di *obsolescenza* (PBS) con decadimento ristretto. Questa metrica viene esposta nel portale di Azure. Per altre informazioni, vedere [Monitorare la metrica del decadimento ristretto probabilistico (Probabilistic Bounded Staleness, PBS)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Il decadimento ristretto probabilistico mostra il livello di finalità della coerenza finale. Questa metrica fornisce informazioni dettagliate sulla frequenza con cui è possibile ottenere una coerenza più avanzata rispetto al livello di coerenza attualmente configurato nell'account Azure Cosmos. In altre parole, è possibile visualizzare la probabilità (misurata in millisecondi) di ottenere letture con coerenza assoluta per una combinazione di aree di scrittura e lettura.

## <a name="next-steps"></a>Passaggi successivi

Scopri di più sulla distribuzione globale e i livelli di coerenza per Azure Cosmos DB:

* [Panoramica della distribuzione globale](distribute-data-globally.md)
* [Panoramica del livello di coerenza](consistency-levels.md)
* [Disponibilità elevata](high-availability.md)
