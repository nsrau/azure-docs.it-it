---
title: Esaminare i prerequisiti per la replica Hyper-V in un sito VMM secondario con Azure Site Recovery | Microsoft Docs
description: Descrive i prerequisiti per la replica di macchine virtuali Hyper-V in un sito VMM secondario con Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 21ff0545-8be5-4495-9804-78ab6e24add6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: 7897a30bf1774609ca8e6037dabcd5fbf4151271
ms.contentlocale: it-it
ms.lasthandoff: 08/01/2017

---
# <a name="step-2-review-the-prerequisites-and-limitations-for-hyper-v-vm-replication-to-a-secondary-vmm-site"></a>Passaggio 2: Esaminare i prerequisiti e le limitazioni per la replica di una macchina virtuale Hyper-V in un sito VMM secondario


Dopo aver analizzato l'[architettura dello scenario](vmm-to-vmm-walkthrough-architecture.md), leggere questo articolo per comprendere con esattezza i prerequisiti di distribuzione necessari quando si replicano in un sito secondario macchine virtuali Hyper-V locali gestite in cloud di System Center Virtual Machine Manager usando [Azure Site Recovery](site-recovery-overview.md) nel portale di Azure.

È possibile inviare commenti nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="prerequisites-and-limitations"></a>Prerequisiti e limitazioni

**Requisito** | **Dettagli**
--- | ---
**Azzurro** | Una sottoscrizione di [Microsoft Azure](http://azure.microsoft.com/).<br/><br/> È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> [Altre informazioni](https://azure.microsoft.com/pricing/details/site-recovery/) sui prezzi di Site Recovery.<br/><br/> Consultare le informazioni sulla disponibilità a livello geografico e sulle aree supportate nella pagina relativa ai [dettagli sui prezzi per Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
**Server VMM** | È consigliabile eseguire la distribuzione con due server VMM, uno nel sito primario e uno nel sito secondario.<br/><br/> È possibile eseguire la replica tra cloud in un unico server VMM.<br/><br/> I server VMM devono eseguire almeno System Center 2012 SP1 con gli aggiornamenti più recenti.<br/><br/> I server VMM richiedono l'accesso a Internet.
**Cloud VMM** | In ogni server VMM devono essere configurati uno o più cloud e in tutti i cloud deve essere impostato il profilo di capacità Hyper-V. <br/><br/>I cloud devono contenere uno o più gruppi host VMM.<br/><br/> Se si dispone solo di un server VMM, sono necessari almeno due cloud, che fungano uno da primario e uno da secondario.
**Hyper-V** | I server Hyper-V devono eseguire almeno Windows Server 2012 con ruolo Hyper-V e con gli ultimi aggiornamenti installati.<br/><br/> Il server Hyper-V deve contenere una o più macchine virtuali.<br/><br/>  I server host Hyper-V devono trovarsi nei gruppi host disponibili nei cloud VMM primario e secondario.<br/><br/> Se si esegue Hyper-V in un cluster in Windows Server 2012 R2 installare l'[aggiornamento 2961977](https://support.microsoft.com/kb/2961977)<br/><br/> Se si esegue Hyper-V in un cluster basato su indirizzi IP statici in Windows Server 2012, il broker del cluster non viene creato automaticamente. Configurare manualmente il broker del cluster. [Altre informazioni](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).<br/><br/> I server Hyper-V richiedono l'accesso a Internet.




## <a name="next-steps"></a>Passaggi successivi

Andare a [Passaggio 3: Pianificare la rete](vmm-to-vmm-walkthrough-network.md).

