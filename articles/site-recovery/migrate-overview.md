---
title: Migrazione di server e macchine virtuali in Azure con Azure Site Recovery
description: Viene descritto come eseguire la migrazione di macchine virtuali IaaS e locali in Azure usando il servizio Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: raynew
ms.openlocfilehash: aaf01dcd63c21e4741456f4f7fccaf22b8fbfffc
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87281311"
---
# <a name="about-migration"></a>Informazioni sulla migrazione

Usare il servizio Azure Migrate per eseguire la migrazione di VM e server in Azure, anziché Azure Site Recovery servizio. [Altre](../migrate/migrate-services-overview.md) informazioni su Azure migrate.


## <a name="why-use-azure-migrate"></a>Vantaggi di Azure Migrate

L'uso di Azure Migrate per la migrazione offre diversi vantaggi:
 
 
- Azure Migrate offre un hub centralizzato per l'individuazione, la valutazione e la migrazione ad Azure.
- L'uso di Azure Migrate garantisce l'interoperabilità e l'estendibilità futura con strumenti Azure Migrate, altri servizi di Azure e strumenti di terze parti.
- Lo strumento di migrazione Azure Migrate: Server è progettato per la migrazione del server in Azure. È ottimizzato per la migrazione. Non è necessario apprendere i concetti e gli scenari che non sono direttamente rilevanti per la migrazione. 
- Non sono previsti addebiti per l'utilizzo degli strumenti per la migrazione per 180 giorni, dal momento in cui viene avviata la replica per una macchina virtuale. Questa operazione consente di completare la migrazione. Paghi solo per le risorse di archiviazione e di rete utilizzate per la replica e per i costi di calcolo utilizzati durante le migrazioni di test.
- Per le macchine virtuali VMware, Azure Migrate consente la migrazione senza agenti, oltre alla migrazione basata su agenti.
- Le nuove funzionalità di migrazione vengono classificate in ordine di priorità per lo strumento di migrazione Azure Migrate: Server.

## <a name="when-to-use-site-recovery"></a>Quando usare Site Recovery?

Usare Site Recovery:

- Per il ripristino di emergenza di computer locali in Azure.
- Per il ripristino di emergenza di macchine virtuali di Azure tra aree di Azure.

Sebbene sia consigliabile usare Azure Migrate per eseguire la migrazione di server locali ad Azure, se è già stato avviato il percorso di migrazione con Site Recovery, è possibile continuare a usarlo per completare la migrazione.  

## <a name="next-steps"></a>Passaggi successivi

> [Esaminare le domande frequenti](../migrate/resources-faq.md) su Azure migrate.
