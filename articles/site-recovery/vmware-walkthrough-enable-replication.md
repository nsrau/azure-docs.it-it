---
title: Abilitare la replica per le VM VMware in Azure con Azure Site Recovery | Microsoft Docs
description: Riepiloga i passaggi necessari per abilitare la replica delle macchine virtuali VMware in Azure con il servizio Azure Site Recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 519c5559-7032-4954-b8b5-f24f5242a954
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 470b9ddd8df4a4e74ec7174f79020c252323e502
ms.contentlocale: it-it
ms.lasthandoff: 06/29/2017

---
# <a name="step-11-enable-replication-for-vmware-virtual-machines-to-azure"></a>Passaggio 11: Abilitare la replica delle macchine virtuali VMware in Azure


Questo articolo illustra come abilitare la replica delle macchine virtuali VMware locali in Azure usando il servizio [Azure Site Recovery](site-recovery-overview.md) nel portale di Azure.

Inserire commenti e domande nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="before-you-start"></a>Prima di iniziare

- Nelle macchine virtuali VMware deve essere installato il [componente del servizio Mobility](vmware-walkthrough-install-mobility.md). Se una VM è pronta per l'installazione push, il server di elaborazione installa automaticamente il servizio Mobility quando si abilita la replica.
- L'account utente di Azure deve avere [autorizzazioni](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) specifiche per abilitare la replica di una nuova VM in Azure.
- Quando si aggiungono o si modificano VM, possono trascorrere 15 minuti o più prima che le modifiche diventino effettive e vengano visualizzate nel portale.
- È possibile controllare l'ora dell'ultima individuazione di VM in **Server di configurazione** > **Ora ultimo contatto**.
- Per aggiungere VM senza attendere l'individuazione pianificata, evidenziare il server di configurazione, senza selezionarlo, e fare clic su **Aggiorna**.



## <a name="exclude-disks-from-replication"></a>Escludere dischi dalla replica

Per impostazione predefinita, vengono replicati tutti i dischi in un computer. È possibile escludere dischi dalla replica. Ad esempio, è possibile evitare di replicare i dischi con dati temporanei o dati che vengono aggiornati ogni volta che un computer o un'applicazione viene riavviata, come pagefile.sys o tempdb di SQL Server. [Altre informazioni](site-recovery-exclude-disk.md)

## <a name="replicate-vms"></a>Replicare le VM

Prima di iniziare, guardare una rapida panoramica video

>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video3-Protect-VMware-Virtual-Machines/player]

1. Fare clic su **Passaggio 2: Eseguire la replica dell'applicazione** > **Origine**.
2. In **Origine** selezionare il server di configurazione.
3. In **Tipo di computer** selezionare **Macchine virtuali**.
4. In **vCenter/vSphere Hypervisor** selezionare il server vCenter che gestisce l'host di vSphere oppure selezionare l'host.
5. Selezionare il server di elaborazione. Se non sono stati creati server di elaborazione aggiuntivi, questo sarà il server di configurazione. Fare quindi clic su **OK**.

    ![Abilitare la replica](./media/vmware-walkthrough-enable-replication/enable-replication2.png)

6. In **Destinazione** selezionare la sottoscrizione e il gruppo di risorse in cui si vogliono creare le VM di cui viene effettuato il failover. Scegliere il modello di distribuzione (classica o Resource Manager) da usare in Azure per le VM di cui viene effettuato il failover.


7. Selezionare l'account di archiviazione di Azure da usare per la replica dei dati. Se non si vuole usare un account già configurato, è possibile crearne uno nuovo.

8. Selezionare la rete di Azure e la subnet a cui dovranno connettersi le VM di Azure create dopo il failover. Scegliere **Configurare ora per le macchine virtuali selezionate** per applicare le impostazioni di rete a tutti i computer selezionati per la protezione. Scegliere **Configurare in seguito** per selezionare la rete di Azure per ogni computer. Se non si vuole usare una rete esistente, è possibile crearne una.

    ![Abilitare la replica](./media/vmware-walkthrough-enable-replication/enable-rep3.png)
9. In **Macchine virtuali** > **Seleziona macchine virtuali** fare clic per selezionare tutte le macchine virtuali da replicare. È possibile selezionare solo i computer per cui è possibile abilitare la replica. Fare quindi clic su **OK**.

    ![Abilitare la replica](./media/vmware-walkthrough-enable-replication/enable-replication5.png)
10. In **Proprietà** > **Configura proprietà**selezionare l'account che verrà usato dal server di elaborazione per installare automaticamente il servizio Mobility nel computer.
11. Per impostazione predefinita, vengono replicati tutti i dischi. Fare clic su **Tutti i dischi** e deselezionare i dischi da non replicare. Fare quindi clic su **OK**. È possibile impostare proprietà aggiuntive delle VM in un secondo momento.

    ![Abilitare la replica](./media/vmware-walkthrough-enable-replication/enable-replication6.png)
11. In **Impostazioni della replica** > **Configura impostazioni di replica** verificare che siano selezionati i criteri di replica corretti. Se si modificano i criteri, le modifiche verranno applicate al computer di replica e ai nuovi computer.
12. Abilitare la **Coerenza tra più VM** per raccogliere le macchine in un gruppo di replica e specificare un nome per il gruppo. Fare quindi clic su **OK**. Si noti che:

    * I computer in gruppi di replica vengono replicati insieme e hanno punti di ripristino condivisi coerenti con l'arresto anomalo del sistema e coerenti con l'app in caso di failover.
    * È consigliabile raggruppare le macchine virtuali e i server fisici in modo da rispecchiare i carichi di lavoro. L'abilitazione della coerenza tra più VM può influire sulle prestazioni del carico di lavoro e deve essere usata solo se i computer eseguono lo stesso carico di lavoro ed è necessaria la coerenza.

    ![Abilitare la replica](./media/vmware-walkthrough-enable-replication/enable-replication7.png)
13. Fare clic su **Abilita la replica**. È possibile tenere traccia dello stato del processo **Abilita protezione** in **Impostazioni** > **Processi** > **Processi di Site Recovery**. Dopo l'esecuzione del processo **Finalizza protezione** la macchina virtuale è pronta per il failover.

## <a name="next-steps"></a>Passaggi successivi

Andare al [Passaggio 12: Eseguire un failover di test](vmware-walkthrough-test-failover.md)

