---
title: Abilitare la replica Hyper-V in un sito secondario con Azure Site Recovery | Microsoft Docs
description: Descrive come abilitare la replica per VM Hyper-V in un sito System Center VMM secondario con Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d8782d14-9fef-4396-8912-ff945efc851b
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: 6673d192dbc18bfc955d9e7e3c55893512511ffb
ms.contentlocale: it-it
ms.lasthandoff: 08/01/2017

---
# <a name="step-9-enable-replication-to-a-secondary-site-for-hyper-v-vms"></a>Passaggio 9: Abilitare la replica in un sito secondario per VM Hyper-V


Dopo avere configurato un criterio di replica, usare questo articolo per abilitare la replica in un sito System Center Virtual Machine Manager (VMM) secondario per macchine virtuali Hyper-V locali tramite [Azure Site Recovery](site-recovery-overview.md).

È possibile inviare commenti nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



## <a name="select-vms-to-replicate"></a>Selezionare le VM da replicare

1. Fare clic su **Passaggio 2: Eseguire la replica dell'applicazione** > **Origine**. 

    ![Abilitare la replica](./media/vmm-to-vmm-walkthrough-enable-replication/enable-replication1.png)

2. In **Origine** selezionare il server VMM e il cloud in cui si trovano gli host Hyper-V da replicare. Fare quindi clic su **OK**.

    ![Abilitare la replica](./media/vmm-to-vmm-walkthrough-enable-replication/enable-replication2.png)
3. In **Destinazione** verificare il cloud e il server VMM secondario.
4. In **Macchine virtuali** selezionare nell'elenco le macchine virtuali da proteggere.

    ![Abilitare la protezione delle macchine virtuali](./media/vmm-to-vmm-walkthrough-enable-replication/enable-replication5.png)

È possibile tenere traccia dello stato del processo **Abilita protezione** in **Processi** > **Site Recovery jobs** (Processi di Site Recovery). Dopo il completamento del processo **Finalizza protezione**, la replica iniziale è completata e la macchina virtuale è pronta per il failover.

Si noti che:

- È anche possibile abilitare la protezione per le macchine virtuali nella console VMM. Fare clic su **Abilita protezione** nella barra degli strumenti della scheda **Azure Site Recovery** nelle proprietà della macchina virtuale.
- Dopo aver abilitato la replica è possibile visualizzare le proprietà della macchina virtuale in **Elementi replicati**. Nel dashboard **Informazioni di base** è possibile visualizzare informazioni sui criteri di replica per la macchina virtuale e il relativo stato. Per altri dettagli, fare clic su **Proprietà** .

## <a name="onboard-existing-vms"></a>Eseguire l'onboarding di VM esistenti

Se in VMM sono presenti macchine virtuali replicate tramite la replica Hyper-V, è possibile caricarle per la replica in Azure Site Recovery nel modo seguente:

1. Assicurarsi che il server Hyper-V che ospita la macchina virtuale esistente si trovi nel cloud primario e che il server Hyper-V che ospita la macchina virtuale di replica si trovi nel cloud secondario.
2. Assicurarsi che per il cloud VMM primario sia configurato un criterio di replica.
3. Abilitare la replica per la macchina virtuale primaria. Azure Site Recovery e VMM garantiscono che vengano rilevati la stessa macchina virtuale e lo stesso host di replica. Azure Site Recovery riuserà e ristabilirà la replica tramite le impostazioni configurate.


## <a name="next-steps"></a>Passaggi successivi

Andare al [Passaggio 10: Eseguire un failover di test](vmm-to-vmm-walkthrough-test-failover.md).

