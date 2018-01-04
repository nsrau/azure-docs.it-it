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
ms.date: 08/31/2017
ms.author: raynew
ms.openlocfilehash: 02f68b68491250f89e8b0e3057f2363b177ab32e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Eseguire la migrazione delle macchine virtuali IaaS di Azure tra aree di Azure con Azure Site Recovery
## <a name="overview"></a>Panoramica
Benvenuti in Azure Site Recovery. Consultare questo articolo se si desidera eseguire la migrazione di VM di Azure tra aree di Azure.
>[!NOTE]
>
> Per la replica di macchine virtuali di Azure in un'altra area per esigenze di migrazione e ripristino di emergenza, fare riferimento a [questo documento](site-recovery-azure-to-azure.md). La replica di Site Recovery per le macchine virtuali di Azure è attualmente in anteprima.

Prima di iniziare, tenere presente quanto segue:

* Azure offre due diversi modelli di distribuzione per creare e usare le risorse: Azure Resource Manager e la distribuzione classica. Il portale di Azure supporta entrambi i modelli di distribuzione. I passaggi di base per la migrazione sono gli stessi indipendentemente dal fatto che si stia configurando Site Recovery in Resource Manager o nel modello classico. 

Per inviare commenti o domande è possibile usare la parte inferiore di questo articolo oppure il [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Prerequisiti
Per la distribuzione è necessario quanto segue:

* **Macchine virtuali IaaS**: le VM di cui si desidera eseguire la migrazione. Nella migrazione queste VM vengono considerate macchine fisiche.

## <a name="deployment-steps"></a>Passaggi di distribuzione
In questa sezione vengono descritti i passaggi di distribuzione nel portale di Azure.

1. [Creare un insieme di credenziali](site-recovery-azure-to-azure.md#create-a-recovery-services-vault).
2. [Abilitare la replica](site-recovery-azure-to-azure.md) per le macchine virtuali da migrare, quindi scegliere Azure come origine. La replica nativa di macchine virtuali di Azure mediante dischi gestiti non è attualmente supportata. È possibile usare l'opzione "Physical to Azure" in [questo documento](site-recovery-vmware-to-azure.md) per eseguire la migrazione di macchine virtuali con dischi gestiti.
1. [Eseguire un failover](site-recovery-failover.md). Dopo il completamento della replica iniziale, è possibile eseguire un failover da un'area di Azure a un'altra. Facoltativamente, è possibile creare un piano di ripristino ed eseguire un failover per eseguire la migrazione di più macchine virtuali tra regioni. [Ulteriori informazioni](site-recovery-create-recovery-plans.md) sui piani di ripristino.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui vari scenari di replica, vedere [Che cos'è Site Recovery?](site-recovery-overview.md)
