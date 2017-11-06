---
title: Eseguire la migrazione di server fisici e VM locali ad Azure con Site Recovery | Microsoft Docs
description: Questo articolo illustra come eseguire la migrazione di server fisici e VM locali ad Azure con Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/30/2017
ms.author: raynew
ms.openlocfilehash: 423a1727efb0e1fd54eb0f8d5971ace3f8efc6cb
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2017
---
# <a name="migrate-to-azure-with-site-recovery"></a>Eseguire la migrazione ad Azure con Site Recovery

Questo articolo illustra come usare il servizio [Azure Site Recovery](site-recovery-overview.md) per eseguire la migrazione di server fisici e macchine virtuali (VM) locali a VM di Azure.

## <a name="before-you-start"></a>Prima di iniziare

Guardare questo video per una rapida panoramica dei passaggi necessari per eseguire la migrazione ad Azure.
>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/ASRHowTo-Video2-Migrate-Virtual-Machines-to-Azure/player]


## <a name="what-do-we-mean-by-migration"></a>Informazioni sulla migrazione

È possibile distribuire Site Recovery per eseguire la replica e la migrazione di server fisici e VM locali.

- Quando si esegue la replica, si configurano i computer locali per la replica a intervalli regolari in Azure. Quando si verifica un'interruzione, si effettua quindi il failover dei computer dal sito locale ad Azure e si accede ai computer da Azure. Quando il sito locale è di nuovo disponibile, si effettua il failback da Azure.
- Quando si usa Site Recovery per la migrazione, si esegue la replica dei computer locali in Azure. Se ne effettua quindi il failover dal sito locale ad Azure e si completa il processo di migrazione. Non viene eseguito il failback.  

## <a name="what-can-site-recovery-migrate"></a>Elementi di cui è possibile eseguire la migrazione con Site Recovery

È possibile:

- **Eseguire la migrazione da locale**. Eseguire la migrazione da server fisici e VM Hyper-V e VMware locali ad Azure. Al termine della migrazione, i carichi di lavoro in esecuzione nei computer locali verranno eseguiti in VM di Azure. 
- **Eseguire la migrazione all'interno di Azure**. Eseguire la migrazione di VM di Azure tra aree di Azure. 
- **Eseguire la migrazione da AWS**. Eseguire la migrazione di istanze Windows AWS a VM IaaS di Azure. 

## <a name="migrate-from-on-premises-to-azure"></a>Eseguire la migrazione da locale ad Azure

Per eseguire la migrazione di server fisici e VM VMware e Hyper-V locali, la procedura è quasi uguale a quella per la replica completa. 


## <a name="migrate-between-azure-regions"></a>Eseguire la migrazione tra aree di Azure

Per eseguire la migrazione di VM di Azure tra aree diverse, la procedura è quasi uguale a quella per la migrazione completa.

1. [Abilitare la replica](azure-to-azure-tutorial-enable-replication.md) per i computer di cui si vuole eseguire la migrazione.
2. [Effettuare un rapido failover di test](azure-to-azure-tutorial-dr-drill.md) per verificare che tutto funzioni correttamente.
3. [Effettuare quindi un failover non pianificato](azure-to-azure-tutorial-failover-failback.md) con l'opzione **Completa la migrazione**.
4. Al termine della migrazione, è possibile [configurare la replica per il ripristino di emergenza](site-recovery-azure-to-azure-after-migration.md) dall'area di Azure in cui è stata eseguita la migrazione a un'area secondaria.



## <a name="migrate-aws-to-azure"></a>Eseguire la migrazione da AWS ad Azure

È possibile eseguire la migrazione di istanze AWS a VM di Azure.
- In questo scenario è supportata solo la migrazione. In altri termini, è possibile replicare le istanze AWS ed effettuarne il failover in Azure, ma non eseguire il failback.
- Ai fini della migrazione, le istanze AWS vengono gestite come i server fisici. Si configura un insieme di credenziali di Servizi di ripristino, si distribuisce un server di configurazione locale per gestire la replica, si aggiunge tale server all'insieme di credenziali e si specificano le impostazioni di replica.
- Dopo aver abilitato la replica per i computer di cui si vuole eseguire la migrazione, si effettua un rapido failover di test. Si esegue quindi un failover non pianificato con l'opzione **Completa la migrazione**.






## <a name="next-steps"></a>Passaggi successivi

- [Eseguire la migrazione di computer locali ad Azure](tutorial-migrate-on-premises-to-azure.md)
- [Eseguire la migrazione di VM da un'area di Azure a un'altra](site-recovery-migrate-azure-to-azure.md)
- [Eseguire la migrazione da AWS ad Azure](tutorial-migrate-aws-to-azure.md)
