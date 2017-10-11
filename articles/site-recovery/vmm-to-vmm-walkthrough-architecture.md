---
title: Esaminare l'architettura per la replica Hyper-V in un sito secondario con Azure Site Recovery | Microsoft Docs
description: Questo articolo offre una panoramica dell'architettura per la replica di macchine virtuali Hyper-V locali in un sito System Center VMM secondario con Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 07099161-4cc7-4f32-8eb9-2a71bbf0750b
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: b78cd0d5a5395873afaddc8856004775f447e8ea
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="step-1-review-the-architecture-for-hyper-v-replication-to-a-secondary-site"></a>Passaggio 1: Esaminare l'architettura per la replica Hyper-V in un sito secondario

Questo articolo descrive i componenti e i processi coinvolti durante la replica in un sito VMM secondario di macchine virtuali Hyper-V locali presenti in cloud System Center Virtual Machine Manager (VMM) tramite il servizio [Azure Site Recovery](site-recovery-overview.md) nel portale di Azure.

Per inviare commenti è possibile usare la parte inferiore di questo articolo oppure il [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



## <a name="architectural-components"></a>Componenti dell'architettura

Di seguito sono indicati i componenti necessari per la replica di macchine virtuali Hyper-V in un sito VMM secondario.

**Componente** | **Posizione** | **Dettagli**
--- | --- | ---
**Azzurro** | Sottoscrizione di Azure. | Creare un insieme di credenziali di Servizi di ripristino nella sottoscrizione di Azure per orchestrare e gestire la replica tra i percorsi VMM.
**Server VMM** | È necessario avere a disposizione un percorso VMM primario e uno secondario. | È consigliabile distribuire un server VMM in un sito primario e uno nel sito secondario. 
**Server Hyper-V** |  Uno o più server host Hyper-V nei cloud VMM primario e secondario. | I dati vengono replicati tra il server host Hyper-V primario e secondario su LAN o VPN usando l'autenticazione Kerberos o del certificato.  
**VM Hyper-V** | Nel server host Hyper-V. | Il server host di origine deve avere almeno una VM che si vuole replicare.

## <a name="replication-process"></a>Processo di replica

1. Configurare l'account Azure, creare un insieme di credenziali di Servizi di ripristino e specificare di cosa si vuole eseguire la replica.
2. Configurare le impostazioni di replica di origine e di destinazione, inclusa l'installazione del provider di Azure Site Recovery nei server VMM, e l'agente di Servizi di ripristino di Microsoft Azure in ogni host Hyper-V.
3. Creare criteri di replica per il cloud VMM di origine. Il criterio viene applicato a tutte le VM negli host del cloud.
4. Abilitare la replica per ogni VMM. La replica iniziale di una macchina virtuale viene eseguita in base alle impostazioni scelte.
5. Dopo la replica iniziale, viene avviata la replica delle modifiche differenziali. Le modifiche rilevate per un elemento vengono salvate in un file HRL.


![Da sito locale a sito locale](./media/vmm-to-vmm-walkthrough-architecture/arch-onprem-onprem.png)

## <a name="failover-and-failback-process"></a>Processo di failover e failback

1. È possibile eseguire un [failover](site-recovery-failover.md) pianificato o non pianificato tra siti locali. Se si esegue un failover pianificato, le macchine virtuali di origine vengono arrestate per assicurare che non si verifichino perdite di dati.
2. È possibile effettuare il failover di un solo computer o creare [piani di ripristino](site-recovery-create-recovery-plans.md) per orchestrare il failover di più computer.
4. Se si esegue un failover non pianificato in un sito secondario, dopo il failover i computer della posizione secondaria non sono abilitati per la protezione o la replica. Se è stato eseguito un failover pianificato, dopo il failover i computer della posizione secondaria sono protetti.
5. Si esegue quindi il commit del failover per avviare l'accesso al carico di lavoro dalla VM di replica.
6. Quando il sito primario è nuovamente disponibile, si avvia la replica inversa dal sito secondario a quello primario. La replica inversa porta le macchine virtuali in uno stato protetto, ma per il datacenter secondario resta la posizione attiva.
7. Per rendere di nuovo il sito primario la posizione attiva, occorre avviare un failover pianificato da sito secondario a primario, seguito da un'altra replica inversa.



## <a name="next-steps"></a>Passaggi successivi

Andare a [Passaggio 2: Esaminare i prerequisiti e le limitazioni](vmm-to-vmm-walkthrough-prerequisites.md).
