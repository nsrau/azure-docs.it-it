---
title: Presenza a livello di area con Azure Cosmos DB
description: Questo articolo include informazioni sulla presenza a livello di area di Azure Cosmos DB e sui diversi ambienti cloud.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.custom: seodec18
ms.openlocfilehash: 95b9a9804fe1a9473d226912f178284f701a1547
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72753219"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Presenza a livello di area con Azure Cosmos DB

Azure Cosmos DB è un servizio di base in Azure e, per impostazione predefinita, è sempre disponibile in tutte le aree geografiche, dove è disponibile Azure.Azure Cosmos DB is a foundational service in Azure, and, by default, is always available in all regions, where Azure is available. Azure è attualmente disponibile in [54 aree](https://azure.microsoft.com/global-infrastructure/regions/) in tutto il mondo. 

[![Aree in cui è disponibile Azure Cosmos DBRegions where Azure Cosmos DB is available](./media/regional-presence/regional-presence.png)](./media/regional-presence/regional-presence.png#lightbox)

Cosmos DB è disponibile in tutti i cinque ambienti cloud distinti di Azure disponibili per i clienti:

* Cloud **Azure pubblico**, disponibile a livello globale.

* **Azure China 21Vianet** è disponibile tramite una partnership unica tra Microsoft e 21Vianet, uno dei più grandi provider Internet del paese in Cina.

* **Azure Germania** fornisce i servizi sulla base di un modello di trustee per i dati, che assicura che i dati dei clienti rimangano in Germania sotto il controllo di T-Systems International GmbH, una filiale di Deutsche Telecom, che funge da trustee per i dati tedesco.

* **Azure per enti pubblici** è disponibile in quattro aree negli Stati Uniti per enti pubblici degli Stati Uniti e i rispettivi partner. 

* Azure per enti pubblici per il **reparto della difesa (DoD)** è disponibile in due aree negli Stati Uniti per il Reparto della difesa degli Stati Uniti.

## <a name="regional-presence-with-global-distribution"></a>Presenza a livello di area con distribuzione globale

Tutte le API esposte da Azure Cosmos DB (inclusi SQL, MongoDB, Cassandra, Gremlin e Table) sono disponibili in tutte le aree di Azure per impostazione predefinita. Ad esempio, è possibile fare in modo che le API MongoDB e Cassandra siano esposte da Azure Cosmos DB non solo in tutte le aree di Azure globali, ma anche in cloud sovrani come aree Cina, Germania, Governo e Department of Defense (DoD).

Azure Cosmos DB è un servizio di database [distribuito a livello globale.](distribute-data-globally.md) È possibile associare un numero qualsiasi di aree di Azure al proprio account Azure Cosmos e i dati vengono replicati automaticamente e in modo trasparente. È possibile aggiungere o rimuovere un'area all'account Azure Cosmos in qualsiasi momento. Con la funzionalità di distribuzione globale chiavi in mano e il protocollo di replica multimaster, Azure Cosmos DB offre latenza inferiore a 10 ms per la lettura e la scrittura al 99° percentile, una disponibilità del 99,999% per lettura e scrittura e la capacità di scalabilità elastica della velocità effettiva di cui è stato effettuato il provisioning per le letture e le scritture in tutte le aree associate all'account Azure Cosmos. Azure Cosmos DB offre anche cinque modelli di coerenza ben definiti ed è possibile scegliere di applicare un modello di coerenza specifico ai dati. Infine, Azure Cosmos DB è l'unico servizio di database nel settore che fornisce un contratto di servizio [(SLA)](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) completo che include velocità effettiva con provisioning, latenza al 99esimo percentile, disponibilità elevata e coerenza. Le funzionalità precedenti sono disponibili in tutti i cloud di Azure.The above capabilities are available in all Azure clouds.

## <a name="next-steps"></a>Passaggi successivi

È ora possibile ottenere informazioni sui concetti di base di Azure Cosmos DB con gli articoli seguenti:You can now learn about core concepts of Azure Cosmos DB with the following articles:

* [Distribuire i dati a livello globale](distribute-data-globally.md)
* [Come gestire un account Azure Cosmos DB](manage-account.md)
* [Effettuare il provisioning della velocità effettiva per contenitori e database di Azure Cosmos](set-throughput.md)
* [Contratto di servizio Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
