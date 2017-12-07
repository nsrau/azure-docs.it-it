---
title: Eseguire la migrazione di macchine virtuali IaaS di Azure tra aree di Azure | Documentazione Microsoft
description: "Utilizzare Site Recovery per la migrazione di macchine virtuali IaaS di Azure da un’area di Azure a un’altra."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8a29e0d9-0010-4739-972f-02b8bdf360f6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: raynew
ms.openlocfilehash: f645150aa7b93ebbd98899cc8fdd495dfeaf3076
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/29/2017
---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Eseguire la migrazione delle macchine virtuali IaaS di Azure tra aree di Azure con Azure Site Recovery

Benvenuti in Azure Site Recovery. Consultare questo articolo se si desidera eseguire la migrazione di VM di Azure tra aree di Azure.
>[!NOTE]
>
> Per la replica di macchine virtuali di Azure in un'altra area per esigenze di migrazione e ripristino di emergenza, fare riferimento a [questo documento](../site-recovery-azure-to-azure.md). La replica di Site Recovery per le macchine virtuali di Azure è attualmente in anteprima.


## <a name="prerequisites"></a>Prerequisiti
Per la distribuzione è necessario quanto segue:

* **Macchine virtuali IaaS**: le VM di cui si desidera eseguire la migrazione. Nella migrazione queste VM vengono considerate macchine fisiche.

## <a name="deployment-steps"></a>Passaggi di distribuzione
Questa sezione descrive i passaggi di distribuzione nel nuovo portale di Azure.

1. [Creare un insieme di credenziali](../site-recovery-azure-to-azure.md#create-a-recovery-services-vault).
2. [Abilitare la replica]../(site-recovery-azure-to-azure.md) per le macchine virtuali di cui eseguire la migrazione e scegliere Azure come origine.
  >[!NOTE]
  >
  > La replica nativa di macchine virtuali di Azure mediante dischi gestiti non è attualmente supportata. È possibile usare l'opzione "Physical to Azure" in [questo documento](../site-recovery-vmware-to-azure.md) per eseguire la migrazione di macchine virtuali con dischi gestiti.
3. [Eseguire un failover](../site-recovery-failover.md). Dopo il completamento della replica iniziale, è possibile eseguire un failover da un'area di Azure a un'altra. Facoltativamente, è possibile creare un piano di ripristino ed eseguire un failover per eseguire la migrazione di più macchine virtuali tra regioni. [Ulteriori informazioni](../site-recovery-create-recovery-plans.md) sui piani di ripristino.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui vari scenari di replica, vedere [Che cos'è Site Recovery?](../site-recovery-overview.md)
