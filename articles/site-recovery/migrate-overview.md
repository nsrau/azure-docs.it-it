---
title: Confrontare Azure Migrate e Site Recovery per la migrazione ad Azure
description: Riepiloga i vantaggi derivanti dall'utilizzo di Azure Migrate per la migrazione, anziché Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: raynew
ms.openlocfilehash: 358efaa1493aa08fb76c9bb83e0e4289950e0969
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87844321"
---
# <a name="migrating-to-azure"></a>Migrazione ad Azure

Per la migrazione, è consigliabile usare il servizio Azure Migrate per eseguire la migrazione di macchine virtuali e server in Azure, anziché il servizio Azure Site Recovery. [Altre](../migrate/migrate-services-overview.md) informazioni su Azure migrate.


## <a name="why-use-azure-migrate"></a>Vantaggi di Azure Migrate

L'uso di Azure Migrate per la migrazione offre diversi vantaggi:
 
 
- Azure Migrate offre un hub centralizzato per l'individuazione, la valutazione e la migrazione ad Azure.
- L'uso di Azure Migrate garantisce l'interoperabilità e l'estendibilità futura con strumenti Azure Migrate, altri servizi di Azure e strumenti di terze parti.
- Lo strumento di migrazione Azure Migrate: Server è progettato per la migrazione del server in Azure. È ottimizzato per la migrazione. Non è necessario apprendere i concetti e gli scenari che non sono direttamente rilevanti per la migrazione. 
- Non sono previsti addebiti per l'utilizzo degli strumenti per la migrazione per 180 giorni, dal momento in cui viene avviata la replica per una macchina virtuale. Questa operazione consente di completare la migrazione. Paghi solo per le risorse di archiviazione e di rete utilizzate per la replica e per i costi di calcolo utilizzati durante le migrazioni di test.
- Azure Migrate supporta tutti gli scenari di migrazione supportati da Site Recovery. Inoltre, per le macchine virtuali VMware, Azure Migrate fornisce un'opzione di migrazione senza agenti.
- Le nuove funzionalità di migrazione vengono classificate in ordine di priorità per lo strumento di migrazione Azure Migrate: Server. Queste funzionalità non sono destinate a Site Recovery.

## <a name="when-to-use-site-recovery"></a>Quando usare Site Recovery?

Usare Site Recovery:

- Per il ripristino di emergenza di computer locali in Azure.
- Per il ripristino di emergenza di macchine virtuali di Azure tra aree di Azure.

Sebbene sia consigliabile usare Azure Migrate per eseguire la migrazione di server locali ad Azure, se è già stato avviato il percorso di migrazione con Site Recovery, è possibile continuare a usarlo per completare la migrazione.  

## <a name="next-steps"></a>Passaggi successivi

> [Esaminare le domande comuni](../migrate/resources-faq.md) su Azure Migrate.
