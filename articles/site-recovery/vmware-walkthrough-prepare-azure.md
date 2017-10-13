---
title: Preparare le risorse di Azure per replicare le macchine virtuali VMware locali in Azure usando Azure Site Recovery | Microsoft Docs
description: Descrive gli elementi necessari in Azure prima di iniziare la replica di macchine virtuali VMware locali in Azure usando Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 4e320d9b-8bb8-46bb-ba21-77c5d16748ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 40abff72278c9f8d9f701023fd473fe52c17b421
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="step-5-prepare-azure-resources-for-vmware-replication-to-azure"></a>Passaggio 5: preparare le risorse di Azure per la replica VMWare in Azure


Usare le istruzioni in questo articolo per preparare le risorse di Azure in modo che sia possibile replicare le macchine locali in Azure usando il servizio [Azure Site Recovery](site-recovery-overview.md).

Inserire commenti e domande nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="before-you-start"></a>Prima di iniziare

Assicurarsi di aver letto i [prerequisiti](vmware-walkthrough-prerequisites.md)

## <a name="set-up-an-azure-account"></a>Configurare un account Azure

- Ottenere un [account Microsoft Azure](http://azure.microsoft.com/).
- È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Consultare le informazioni sulla disponibilità a livello geografico e sulle aree supportate nella pagina relativa ai [dettagli sui prezzi per Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
- Leggere le informazioni sui [prezzi di Site Recovery](site-recovery-faq.md#pricing) e ottenere i [dettagli dei prezzi](https://azure.microsoft.com/pricing/details/site-recovery/).



## <a name="set-up-an-azure-network"></a>Configurare una rete di Azure

- Configurare una rete di Azure. Le VM di Azure create dopo il failover verranno inserite in questa rete.
- Site Recovery nel portale di Azure può usare reti configurate in [Gestione risorse](../resource-manager-deployment-model.md) o in modalità classica.
- La rete deve trovarsi nella stessa area dell'insieme di credenziali di Servizi di ripristino
- Leggere le informazioni sui [prezzi per la rete virtuale](https://azure.microsoft.com/pricing/details/virtual-network/).
- Altre informazioni sulla [connettività della macchina virtuale di Azure](site-recovery-network-design.md) dopo il failover.


## <a name="set-up-an-azure-storage-account"></a>Configurare un account di archiviazione di Azure

- Site Recovery replica le macchine locali in Archiviazione di Azure. Le macchine virtuali di Azure vengono create dalla risorsa di archiviazione dopo che si verifica il failover.
- Configurare un [account di archiviazione di Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account) per i dati replicati.
- Site Recovery nel portale di Azure può usare gli account di archiviazione configurati in Gestione risorse o in modalità classica.
- L'account di archiviazione può essere Standard o [Premium](../storage/common/storage-premium-storage.md).
- Se si configura un account Premium, sarà necessario anche un altro account Standard per i dati di log.


## <a name="next-steps"></a>Passaggi successivi

Andare al [Passaggio 6: preparare le risorse VMware](vmware-walkthrough-prepare-vmware.md)
