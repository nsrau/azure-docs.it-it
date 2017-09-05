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
ms.date: 06/14/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: ef2972c077a2b1dd2b2fd6ce53cc6560520ea870
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Eseguire la migrazione delle macchine virtuali IaaS di Azure tra aree di Azure con Azure Site Recovery
## <a name="overview"></a>Overview
Benvenuti in Azure Site Recovery. Consultare questo articolo se si desidera eseguire la migrazione di VM di Azure tra aree di Azure. Prima di iniziare, tenere presente quanto segue:

* Azure offre due diversi modelli di distribuzione per creare e usare le risorse: Azure Resource Manager e la distribuzione classica. Sono anche disponibili il portale di Azure classico, che supporta il modello di distribuzione classica, e il portale di Azure, che supporta entrambi i modelli di distribuzione. I passaggi di base per la migrazione sono gli stessi indipendentemente dal fatto che si stia configurando Site Recovery in Resource Manager o nel modello classico. Tuttavia, le istruzioni e le schermate dell'interfaccia utente presenti in questo articolo sono pertinenti al portale di Azure.
* **Attualmente è possibile eseguire la migrazione solo da un'area a un'altra. Si può eseguire il failover di VM da un'area di Azure a un'altra ma non è possibile eseguire il failback.**

Per inviare commenti o domande, è possibile usare la parte inferiore di questo articolo oppure il [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Prerequisiti
Per la distribuzione è necessario quanto segue:

* **Macchine virtuali IaaS**: le VM di cui si desidera eseguire la migrazione. Nella migrazione queste VM vengono considerate macchine fisiche.

## <a name="deployment-steps"></a>Passaggi di distribuzione
Questa sezione descrive i passaggi di distribuzione nel nuovo portale di Azure.

1. [Creare un insieme di credenziali](site-recovery-vmware-to-azure.md).
2. [Abilitare la replica](site-recovery-vmware-to-azure.md). Abilitare la replica per le VM da migrare, quindi scegliere Azure come origine. 
3. [ Eseguire un failover non pianificato](site-recovery-failover.md). Dopo il completamento della replica iniziale, è possibile eseguire un failover non pianificato da un'area di Azure a un'altra. Facoltativamente, è possibile creare un piano di ripristino ed eseguire un failover non pianificato, per eseguire la migrazione di più macchine virtuali tra regioni. [Ulteriori informazioni](site-recovery-create-recovery-plans.md) sui piani di ripristino.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui vari scenari di replica, vedere [Che cos'è Site Recovery?](site-recovery-overview.md)

