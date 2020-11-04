---
title: Mapping dei livelli di coerenza per l'API Azure Cosmos DB per MongoDB
description: Mapping dei livelli di coerenza per l'API Azure Cosmos DB per MongoDB.
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: e8ac3e376c8d67e82def3a57910707c6b1433912
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93333155"
---
# <a name="consistency-levels-for-azure-cosmos-db-and-the-api-for-mongodb"></a>Livelli di coerenza per Azure Cosmos DB e l'API per MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

A differenza Azure Cosmos DB, MongoDB nativo non fornisce garanzie di coerenza definite in modo accurato. Al contrario, MongoDB nativo consente agli utenti di configurare le garanzie di coerenza seguenti: un problema di scrittura, un problema di lettura e la direttiva Master per indirizzare le operazioni di lettura alle repliche primarie o secondarie per ottenere il livello di coerenza desiderato.

Quando si usa l'API di Azure Cosmos DB per MongoDB, il Driver MongoDB considera l'area di scrittura come replica primaria e tutte le altre aree sono in lettura replica. È possibile scegliere l'area associata all'account Azure Cosmos come replica primaria.

> [!NOTE]
> Il modello di coerenza predefinito per Azure Cosmos DB è Session. Session è un modello di coerenza incentrato sul client che non è supportato in modo nativo da Cassandra o MongoDB. Per ulteriori informazioni sul modello di coerenza da scegliere, vedere [livelli di coerenza in Azure Cosmos DB](consistency-levels.md)

Quando si usa l'API di Azure Cosmos DB per MongoDB:

* Il problema di scrittura viene mappato al livello di coerenza predefinito configurato nell'account Azure Cosmos.

* Azure Cosmos DB eseguirà il mapping dinamico del problema di lettura specificato dal driver client MongoDB a uno dei livelli di coerenza Azure Cosmos DB configurati in modo dinamico in una richiesta di lettura.  

* È possibile aggiungere annotazioni a un'area specifica associata all'account Azure Cosmos come "primaria", rendendo la regione come prima area scrivibile. 

## <a name="mapping-consistency-levels"></a>Mapping dei livelli di coerenza

La tabella seguente illustra come viene eseguito il mapping dei problemi di scrittura/lettura MongoDB nativi ai livelli di coerenza di Azure Cosmos quando si usa l'API di Azure Cosmos DB per MongoDB:

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png" alt-text="Mapping del modello di coerenza MongoDB" lightbox= "./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png":::

Se l'account Azure Cosmos è configurato con un livello di coerenza diverso da quello della coerenza assoluta, è possibile determinare la probabilità che i client ottengano letture solide e coerenti per i carichi di lavoro esaminando la metrica di *obsolescenza* (PBS) con decadimento ristretto. Questa metrica viene esposta nel portale di Azure. Per altre informazioni, vedere [Monitorare la metrica del decadimento ristretto probabilistico (Probabilistic Bounded Staleness, PBS)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Il decadimento ristretto probabilistico mostra il livello di finalità della coerenza finale. Questa metrica fornisce informazioni dettagliate sulla frequenza con cui è possibile ottenere una coerenza più avanzata rispetto al livello di coerenza attualmente configurato nell'account Azure Cosmos. In altre parole, è possibile visualizzare la probabilità (misurata in millisecondi) di ottenere letture con coerenza assoluta per una combinazione di aree di scrittura e lettura.

## <a name="next-steps"></a>Passaggi successivi

Scopri di più sulla distribuzione globale e i livelli di coerenza per Azure Cosmos DB:

* [Panoramica della distribuzione globale](distribute-data-globally.md)
* [Panoramica del livello di coerenza](consistency-levels.md)
* [Disponibilità elevata](high-availability.md)
