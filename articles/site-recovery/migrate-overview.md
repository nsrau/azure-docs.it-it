---
title: Eseguire la migrazione di computer locali e macchine virtuali di Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo illustra come eseguire la migrazione di computer locali e di macchine virtuali dell'infrastruttura distribuita come servizio di Azure usando il servizio Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: raynew
ms.openlocfilehash: 856d03b1ecc1c7a3bd527eb265061f9a305d8f50
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58315634"
---
# <a name="about-migration"></a>Informazioni sulla migrazione

Leggere questo articolo per una rapida panoramica del modo in cui il servizio [Azure Site Recovery](site-recovery-overview.md) consenta di eseguire la migrazione dei computer. 

Di seguito sono riportate le operazioni di migrazione che è possibile eseguire con Site Recovery:

- **Eseguire la migrazione da locale ad Azure**: eseguire la migrazione da server fisici e macchine virtuali Hyper-V e VMware locali ad Azure. Al termine della migrazione, i carichi di lavoro in esecuzione nei computer locali verranno eseguiti in VM di Azure. 
- **Eseguire la migrazione all'interno di Azure**: eseguire la migrazione di machine virtuali di Azure tra aree di Azure. 
- **Eseguire la migrazione da AWS**: eseguire la migrazione di istanze di Windows per Amazon Web Services a macchine virtuali IaaS di Azure. 


## <a name="what-do-we-mean-by-migration"></a>Informazioni sulla migrazione

Oltre a usare Site Recovery per il ripristino di emergenza di VM di Azure e locali è possibile usare il servizio Site Recovery per eseguirne la migrazione. Qual è la differenza?

- Per il ripristino di emergenza, replicare le macchine a intervalli regolari in Azure. Quando si verifica un'interruzione, effettuare quindi il failover dei computer dal sito primario al sito secondario di Azure e accedere ai computer da Azure. Quando il sito primario è di nuovo disponibile, effettuare il failback da Azure.
- Per la migrazione eseguire la replica dei computer locali in Azure o delle VM di Azure in un'area secondaria. Eseguire quindi il failover della VM dal sito primario al secondario e completare il processo di migrazione. Non viene eseguito il failback.  


## <a name="migration-scenarios"></a>Scenari di migrazione

**Scenario** | **Dettagli**
--- | ---
**Eseguire la migrazione da locale ad Azure** | Eseguire la migrazione da server fisici e VM Hyper-V e VMware locali ad Azure. A tale scopo, effettuare una procedura simile a quella che si esegue per un ripristino di emergenza completo. Semplicemente non si esegue il failback dei computer da Azure al sito locale.
**Eseguire la migrazione tra aree di Azure** | È possibile eseguire la migrazione delle VM di Azure da un'area di Azure a un'altra. Una volta completata la migrazione, è ora possibile configurare il ripristino di emergenza per le VM di Azure nell'area secondaria in cui è eseguita la migrazione.
**Eseguire la migrazione da AWS ad Azure** | È possibile eseguire la migrazione di istanze AWS a VM di Azure. Ai fini della migrazione, Site Recovery gestisce le istanze AWS come i server fisici. 

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire la migrazione di computer locali ad Azure](migrate-tutorial-on-premises-azure.md)
- [Eseguire la migrazione di VM da un'area di Azure a un'altra](azure-to-azure-tutorial-migrate.md)
- [Eseguire la migrazione da AWS ad Azure](migrate-tutorial-aws-azure.md)
