---
title: Preparare le risorse di Azure per la replica di VM Hyper-V (senza System Center VMM) in Azure usando Azure Site Recovery| Microsoft Docs
description: Vengono descritti gli elementi necessari in Azure prima di iniziare la replica di VM Hyper-V (senza VMM) in Azure usando Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 28fa722c-675e-4637-98eb-7ccbf3806d69
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/21/2017
ms.author: raynew
ms.openlocfilehash: 1a30cadaab7e053184f0be133f1da5bfddc1fd91
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="step-5-prepare-azure-resources-for-hyper-v-replication-to-azure"></a>Passaggio 5: Preparare le risorse di Azure per la replica Hyper-V in Azure

Usare le istruzioni in questo articolo per preparare le risorse di Azure in modo che sia possibile eseguire la replica delle VM Hyper-V locali (senza System Center VMM) in Azure usando il servizio [Azure Site Recovery](site-recovery-overview.md).

Dopo la lettura di questo articolo, è possibile inserire commenti nella parte inferiore oppure porre domande tecniche nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="before-you-start"></a>Prima di iniziare

Assicurarsi di aver letto i [prerequisiti](hyper-v-site-walkthrough-prerequisites.md)

## <a name="set-up-an-azure-account"></a>Configurare un account Azure

- Ottenere un [account Microsoft Azure](http://azure.microsoft.com/).
- È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Consultare le informazioni sulla disponibilità a livello geografico e sulle aree supportate nella pagina relativa ai [dettagli sui prezzi per Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
- Leggere le informazioni sui [prezzi di Site Recovery](site-recovery-faq.md#pricing) e ottenere i [dettagli dei prezzi](https://azure.microsoft.com/pricing/details/site-recovery/).


## <a name="set-up-an-azure-network"></a>Configurare una rete di Azure

- Configurare una rete di Azure. Le VM di Azure create dopo il failover verranno inserite in questa rete.
- La rete deve trovarsi nella stessa area dell'insieme di credenziali di Servizi di ripristino.
- Site Recovery nel portale di Azure può usare reti configurate in [Gestione risorse](../resource-manager-deployment-model.md) o in modalità classica.
- È consigliabile configurare una rete prima di iniziare. In caso contrario sarà necessario eseguire l'operazione durante la distribuzione di Site Recovery.
- Leggere le informazioni sui [prezzi per la rete virtuale](https://azure.microsoft.com/pricing/details/virtual-network/).


## <a name="set-up-an-azure-storage-account"></a>Configurare un account di archiviazione di Azure

- Site Recovery replica le macchine locali in Archiviazione di Azure. Le VM di Azure vengono create dalla risorsa di archiviazione dopo il failover.
- Configurare un [account di archiviazione di Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account) Standard/Premium in cui inserire i dati replicati in Azure.
- [Archiviazione Premium](../storage/common/storage-premium-storage.md) si usa in genere per le macchine virtuali che richiedono un livello di prestazioni di I/O costantemente elevato e bassa latenza per ospitare carichi di lavoro con numerose operazioni di I/O.
- Se si vuole usare un account Premium per archiviare i dati replicati, sarà necessario anche un account di archiviazione standard per l'archiviazione dei log di replica in cui vengono acquisite le modifiche in corso ai dati locali.
- A seconda del modello di risorsa da usare per le macchine virtuali di Azure di cui si esegue il failover, l'account deve essere configurato in [modalità Resource Manager](../storage/common/storage-create-storage-account.md) o in [modalità classica](../storage/common/storage-create-storage-account.md).
- È consigliabile configurare un account di archiviazione prima di iniziare. In caso contrario sarà necessario eseguire l'operazione durante la distribuzione di Site Recovery. L'account deve trovarsi nella stessa area dell'insieme di credenziali di Servizi di ripristino.
- Non è possibile spostare gli account di archiviazione usati da Site Recovery tra gruppi di risorse nella stessa sottoscrizione o in diverse sottoscrizioni.


## <a name="next-steps"></a>Passaggi successivi

Andare a [Passaggio 6: Preparare le risorse Hyper-V](hyper-v-site-walkthrough-prepare-hyper-v.md)
