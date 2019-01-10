---
title: Presenza a livello di area con Azure Cosmos DB
description: Questo articolo include informazioni sulla presenza a livello di area di Azure Cosmos DB e sui diversi ambienti cloud.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.custom: seodec18
ms.openlocfilehash: 5a4523405f7a9182bb5123ebacaebc9a9e5ae9a9
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54038664"
---
# <a name="regional-presence-of-azure-cosmos-db"></a>Presenza a livello di area di Azure Cosmos DB

Azure è attualmente disponibile in [54 aree](https://azure.microsoft.com/global-infrastructure/regions/) in tutto il mondo. Azure Cosmos DB è un servizio fondamentale in Azure ed è disponibile in tutte le aree in cui è disponibile Azure.

[![Aree in cui è disponibile Azure Cosmos DB](./media/regional-presence/regional-presence.png)](./media/regional-presence/regional-presence.png#lightbox)

Cosmos DB è disponibile in tutti i cinque ambienti cloud distinti di Azure disponibili per i clienti:

* Cloud **Azure pubblico**, disponibile a livello globale.

* **Azure Cina** è disponibile tramite una speciale collaborazione tra Microsoft e 21Vianet, uno dei principali provider Internet in Cina.

* **Azure Germania** fornisce i servizi sulla base di un modello di trustee per i dati, che assicura che i dati dei clienti rimangano in Germania sotto il controllo di T-Systems International GmbH, una filiale di Deutsche Telecom, che funge da trustee per i dati tedesco.

* **Azure per enti pubblici** è disponibile in quattro aree negli Stati Uniti per enti pubblici degli Stati Uniti e i rispettivi partner. 

* **Azure per enti pubblici per DOD (Department of Defense)** è disponibile in due aree negli Stati Uniti per il dipartimento della difesa.

## <a name="regional-presence-with-global-distribution"></a>Presenza a livello di area con distribuzione globale

Diverse API esposte da Azure Cosmos DB (inclusi SQL, MongoDB, Cassandra, Gremlin e Archiviazione tabelle di Azure) sono disponibili in tutte le aree di Azure. Ad esempio, è possibile avere le API MongoDB e Cassandra esposte da Azure Cosmos DB non solo in tutte le aree di Azure globale, ma anche nelle aree sovrane come Azure Cina, Azure Germania, Azure per enti pubblici e DOD (Department of Defense).

Azure Cosmos DB è un [database distribuito a livello globale](distribute-data-globally.md). È possibile associare un numero qualsiasi di aree di Azure al proprio account Azure Cosmos e i dati vengono replicati automaticamente e in modo trasparente. È possibile aggiungere o rimuovere un'area all'account Azure Cosmos in qualsiasi momento. Con la funzionalità di distribuzione globale chiavi in mano e il protocollo di replica multimaster, Azure Cosmos DB offre latenza inferiore a 10 ms per la lettura e la scrittura al 99° percentile, una disponibilità del 99,999% per lettura e scrittura e la capacità di scalabilità elastica della velocità effettiva di cui è stato effettuato il provisioning per le letture e le scritture in tutte le aree associate all'account Azure Cosmos. Azure Cosmos DB offre anche cinque modelli di coerenza ben definiti ed è possibile scegliere di applicare un modello di coerenza specifico ai dati. Infine, Azure Cosmos DB è l'unico servizio di database del settore che fornisce un contratto di servizio (SLA) completo che include velocità effettiva di cui è stato effettuato il provisioning, latenza al 99° percentile, disponibilità elevata e coerenza.

## <a name="next-steps"></a>Passaggi successivi

Negli articoli seguenti sono disponibili informazioni su vari concetti di Azure Cosmos DB:

* [Distribuire i dati a livello globale](distribute-data-globally.md)
* [Come gestire un account Azure Cosmos DB](manage-account.md)
* [Effettuare il provisioning della velocità effettiva per contenitori e database di Azure Cosmos](set-throughput.md)
* [Contratto di servizio di Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
