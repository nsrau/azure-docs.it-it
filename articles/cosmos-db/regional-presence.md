---
title: Presenza a livello di area con Azure Cosmos DB
description: Questo articolo include informazioni sulla presenza a livello di area di Azure Cosmos DB e sui diversi ambienti cloud.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: rimman
ms.custom: seodec18
ms.openlocfilehash: 787bcc8f0db60868008ec93fcacdec1283946d2f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66243727"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Presenza a livello di area con Azure Cosmos DB

Azure Cosmos DB è un servizio di base in Azure e, per impostazione predefinita, è sempre disponibile in tutte le aree, in cui Azure è disponibile. Azure è attualmente disponibile in [54 aree](https://azure.microsoft.com/global-infrastructure/regions/) in tutto il mondo. 

[![Aree in cui è disponibile Azure Cosmos DB](./media/regional-presence/regional-presence.png)](./media/regional-presence/regional-presence.png#lightbox)

Cosmos DB è disponibile in tutti i cinque ambienti cloud distinti di Azure disponibili per i clienti:

* Cloud **Azure pubblico**, disponibile a livello globale.

* **Azure Cina 21Vianet** è disponibile tramite una collaborazione unica tra Microsoft e 21Vianet, uno dei principali provider internet del paese in Cina.

* **Azure Germania** fornisce i servizi sulla base di un modello di trustee per i dati, che assicura che i dati dei clienti rimangano in Germania sotto il controllo di T-Systems International GmbH, una filiale di Deutsche Telecom, che funge da trustee per i dati tedesco.

* **Azure per enti pubblici** è disponibile in quattro aree negli Stati Uniti per enti pubblici degli Stati Uniti e i rispettivi partner. 

* **Azure per enti pubblici per Department of Defense (DoD)** è disponibile in due aree negli Stati Uniti per il dipartimento della difesa degli Stati Uniti.

## <a name="regional-presence-with-global-distribution"></a>Presenza a livello di area con distribuzione globale

Tutte le API esposte da Azure Cosmos DB (inclusi SQL, MongoDB, Cassandra, Gremlin e tabella) sono disponibili in tutte le aree di Azure per impostazione predefinita. Ad esempio, è possibile avere MongoDB e Cassandra API esposta da Azure Cosmos DB non solo in tutte le aree di Azure globale, ma anche nel cloud con sovranità, ad esempio Cina, Germania, per enti pubblici e Department of Defense aree (DoD).

Azure Cosmos DB è un [distribuite a livello globale](distribute-data-globally.md) servizio di database. È possibile associare un numero qualsiasi di aree di Azure al proprio account Azure Cosmos e i dati vengono replicati automaticamente e in modo trasparente. È possibile aggiungere o rimuovere un'area all'account Azure Cosmos in qualsiasi momento. Con la funzionalità di distribuzione globale chiavi in mano e il protocollo di replica multimaster, Azure Cosmos DB offre latenza inferiore a 10 ms per la lettura e la scrittura al 99° percentile, una disponibilità del 99,999% per lettura e scrittura e la capacità di scalabilità elastica della velocità effettiva di cui è stato effettuato il provisioning per le letture e le scritture in tutte le aree associate all'account Azure Cosmos. Azure Cosmos DB offre anche cinque modelli di coerenza ben definiti ed è possibile scegliere di applicare un modello di coerenza specifico ai dati. Infine, Azure Cosmos DB è l'unico servizio di database del settore che offre una serie completa [contratto di servizio (SLA)](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) inclusiva velocità effettiva con provisioning, latenza al 99 ° percentile, disponibilità elevata, e coerenza. Le funzionalità di versioni successive sono disponibili in tutte le aree di Azure.

## <a name="next-steps"></a>Passaggi successivi

Sono ora disponibili sui concetti principali di Azure Cosmos DB con gli articoli seguenti:

* [Distribuire i dati a livello globale](distribute-data-globally.md)
* [Come gestire un account Azure Cosmos DB](manage-account.md)
* [Effettuare il provisioning della velocità effettiva per contenitori e database di Azure Cosmos](set-throughput.md)
* [Contratto di servizio Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
