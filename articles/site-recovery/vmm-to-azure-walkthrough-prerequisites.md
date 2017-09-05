---
title: Esaminare i prerequisiti per la replica Hyper-V in Azure (con System Center VMM) usando Azure Site Recovery | Microsoft Docs
description: Questo articolo illustra i prerequisiti per la configurazione di replica, failover e ripristino di macchine virtuali Hyper-V locali in cloud VMM in Azure, usando Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a1c30fd5-c979-473c-af44-4f725ad3e3ba
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/24/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 47c178c66ec98fe5d333edd725b64465026e73ed
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---



# <a name="step-2-review-the-prerequisites-for-hyper-v-with-vmm-to-azure-replication"></a>Passaggio 2: Esaminare i prerequisiti per la replica Hyper-V (con VMM) in Azure

Dopo aver esaminato l'[architettura dello scenario](vmm-to-azure-walkthrough-architecture.md), leggere questo articolo per comprendere i prerequisiti di distribuzione. 

## <a name="prerequisites-and-limitations"></a>Prerequisiti e limitazioni

**Requisito** | **Dettagli**
--- | ---
**Account di Azure** | È necessario un account [Microsoft Azure](http://azure.microsoft.com/).
**Archiviazione di Azure** | Per archiviare i dati replicati è necessario un account di archiviazione di Azure.<br/><br/> L'account di archiviazione deve trovarsi nella stessa area dell'insieme di credenziali di Servizi di ripristino di Azure.<br/><br/>È possibile usare l'[archiviazione con ridondanza geografica](../storage/common/storage-redundancy.md#geo-redundant-storage) o locale. È consigliabile usare l'archiviazione con ridondanza geografica, che permette di garantire una maggiore resilienza dei dati in caso di interruzione del servizio a livello di area o se non è possibile ripristinare l'area primaria.<br/><br/> È possibile usare un account di archiviazione Standard di Azure oppure [Archiviazione Premium](../storage/common/storage-premium-storage.md) di Azure. Archiviazione Premium può ospitare carichi di lavoro con I/O elevato e viene in genere usata per le macchine virtuali che richiedono un livello di prestazioni di I/O costantemente elevato e bassa latenza. Se si usa Archiviazione Premium per i dati replicati, è necessario anche un account di archiviazione Standard. Quest'ultimo permette di archiviare i log di replica in cui vengono acquisite le modifiche in corso ai dati locali.
**Rete di Azure** | È necessaria una [rete di Azure](../virtual-network/virtual-network-get-started-vnet-subnet.md) a cui le macchine virtuali di Azure possano connettersi dopo il failover. La rete di Azure deve trovarsi nella stessa area dell'insieme di credenziali di Servizi di ripristino.
**Server VMM locali** | È necessario avere a disposizione uno o più server VMM che eseguono System Center 2012 R2 o versioni successive.<br/><br/> Ogni server VMM deve avere uno o più cloud privati. Ogni cloud deve avere uno o più gruppi host.<br/><br/> Il server VMM deve avere accesso a Internet.
**Hyper-V locale** | I server host Hyper-V devono eseguire almeno Windows Server 2012 R2 con il ruolo Hyper-V abilitato o Microsoft Hyper-V Server 2012 R2, con gli ultimi aggiornamenti installati.<br/><br/> L'host Hyper-V deve trovarsi in un gruppo host VMM, in un cloud VMM.<br/><br/> Un host deve avere una o più macchine virtuali da replicare.<br/><br/> Gli host Hyper-V devono essere connessi a Internet per la replica in Azure, direttamente o con un proxy. I server Hyper-V devono avere le correzioni specificate nell'articolo [2961977](https://support.microsoft.com/kb/2961977).
**VM Hyper-V locali** | Le VM da replicare devono eseguire un [sistema operativo supportato](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) ed essere conformi ai [prerequisiti di Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements). Dopo aver abilitato la replica è possibile modificare il nome della macchina virtuale. 




## <a name="next-steps"></a>Passaggi successivi

Andare a [Passaggio 3: Pianificare la capacità](vmm-to-azure-walkthrough-capacity.md)

