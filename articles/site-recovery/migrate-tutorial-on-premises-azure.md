---
title: Eseguire la migrazione di computer locali con Azure Site Recovery
description: Questo articolo illustra come eseguire la migrazione di computer locali ad Azure usando Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2020
ms.author: raynew
ms.openlocfilehash: 3c421845d4e15ef13ce98d0de111270159f564fe
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285357"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Eseguire la migrazione di computer locali ad Azure

Questo articolo illustra le opzioni per la migrazione di computer locali in Azure. 

## <a name="migrate-with-azure-migrate"></a>Eseguire la migrazione con Azure Migrate

È consigliabile eseguire la migrazione di computer ad Azure usando il servizio [Azure Migrate](../migrate/migrate-services-overview.md). Azure Migrate offre un hub centralizzato per la valutazione e la migrazione di computer locali in Azure, usando Azure Migrate, altri servizi di Azure e strumenti di terze parti.

Per eseguire la migrazione con Azure Migrate, seguire questi collegamenti:

- [Apprendere](../migrate/server-migrate-overview.md) le opzioni di migrazione per le VM VMware, quindi eseguire la migrazione delle VM ad Azure usando il metodo [senza agente](../migrate/tutorial-migrate-vmware.md) o [basato su agente](../migrate/tutorial-migrate-vmware-agent.md).
- [Eseguire la migrazione di VM Hyper-V](../migrate/tutorial-migrate-hyper-v.md) ad Azure.
- Eseguire la migrazione di [server fisici o altre VM](../migrate/tutorial-migrate-physical-virtual-machines.md), incluse le [istanze AWS](../migrate/tutorial-migrate-aws-virtual-machines.md), ad Azure.

## <a name="migrate-with-site-recovery"></a>Eseguire la migrazione con Site Recovery
Site Recovery deve essere usato solo per il ripristino di emergenza e non per la migrazione.

Se si usa già Azure Site Recovery e si vuole continuare a usarlo per la migrazione, seguire la stessa procedura usata per configurare il ripristino di emergenza.

- Macchine virtuali VMware: [Preparare Azure](tutorial-prepare-azure.md) e [VMware](vmware-azure-tutorial-prepare-on-premises.md), avviare la [replica dei computer](vmware-azure-tutorial.md), [controllare](tutorial-dr-drill-azure.md) che tutto funzioni ed [eseguire un failover](vmware-azure-tutorial-failover-failback.md).
- Macchine virtuali Hyper-V: [Preparare Azure](tutorial-prepare-azure-for-hyperv.md) e [Hyper-V](hyper-v-prepare-on-premises-tutorial.md), avviare la [replica dei computer](hyper-v-azure-tutorial.md), [controllare](tutorial-dr-drill-azure.md) che tutto funzioni ed [eseguire un failover](hyper-v-azure-failover-failback-tutorial.md).
- Server fisici: [Seguire la procedura dettagliata](physical-azure-disaster-recovery.md) per preparare Azure, preparare i computer per il ripristino di emergenza e configurare la replica.

> [!NOTE]
> Quando si esegue un failover per il ripristino di emergenza, come ultimo passaggio è necessario eseguire il commit del failover. Quando si esegue la migrazione di computer locali, l'opzione **Esegui commit** non è pertinente. Selezionare invece l'opzione **Completa la migrazione**. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Vedere le domande frequenti](../migrate/resources-faq.md) su Azure Migrate.

  
