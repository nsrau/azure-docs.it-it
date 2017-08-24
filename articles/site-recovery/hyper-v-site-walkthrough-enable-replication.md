---
title: Abilitare la replica per le VM Hyper-V in Azure (senza System Center VMM) con Azure Site Recovery| Microsoft Docs
description: Vengono riepilogati i passaggi necessari per abilitare la replica delle VM Hyper-V in Azure usando il servizio Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: bd31ef01-69f1-4591-a519-e42510a6e2f4
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/21/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: aabe99dbd375b80e4a87ca7a067927008672b4ed
ms.contentlocale: it-it
ms.lasthandoff: 06/23/2017

---

# <a name="step-10-enable-replication-for-hyper-v-vms-replicating-to-azure"></a>Passaggio 10: Abilitare la replica per le VM Hyper-V in Azure


Questo articolo illustra come abilitare la replica per le macchine virtuali Hyper-V locali (non gestite da System Center VMM) in Azure usando il servizio [Azure Site Recovery](site-recovery-overview.md) nel portale di Azure.

Inserire commenti e domande nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).




## <a name="before-you-start"></a>Prima di iniziare

Prima di iniziare, verificare che l'account utente di Azure abbia le [autorizzazioni](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) necessarie per abilitare la replica di una nuova macchina virtuale in Azure.

## <a name="exclude-disks-from-replication"></a>Escludere dischi dalla replica

Per impostazione predefinita, vengono replicati tutti i dischi in un computer. È possibile escludere dischi dalla replica. Ad esempio, è possibile evitare di replicare i dischi con dati temporanei o dati che vengono aggiornati ogni volta che un computer o un'applicazione viene riavviata, come pagefile.sys o tempdb di SQL Server. [Altre informazioni](site-recovery-exclude-disk.md)


## <a name="replicate-vms"></a>Replicare le VM

Abilitare la replica per le macchine virtuali nel modo seguente:          

1. Fare clic su **Eseguire la replica dell'applicazione** > **Origine**. Dopo avere configurato la replica per la prima volta, è possibile fare clic su **+Replica** per abilitare la replica per altri computer.

    ![Abilitare la replica](./media/hyper-v-site-walkthrough-enable-replication/enable-replication.png)
2. In **Origine** selezionare il sito Hyper-V. Fare quindi clic su **OK**.
3. In **Destinazione** selezionare la sottoscrizione dell'insieme di credenziali e il modello di failover da usare in Azure (modalità classica o Resource Manager) dopo il failover.
4. Selezionare l'account di archiviazione da usare. Se non si dispone di un account da usare, è possibile [crearne uno](#set-up-an-azure-storage-account). Fare quindi clic su **OK**.
5. Selezionare la rete di Azure e la subnet a cui dovranno connettersi le macchine virtuali di Azure create dopo il failover.

    - Per applicare le impostazioni di rete a tutti i computer selezionati per la replica, selezionare **Configurare ora per le macchine virtuali selezionate**.
    - Scegliere **Configurare in seguito** per selezionare la rete di Azure per ogni computer.
    - Se non si dispone di una rete da usare, è possibile [crearne una](#set-up-an-azure-network). Selezionare una subnet, se applicabile. Fare quindi clic su **OK**.

   ![Abilitare la replica](./media/hyper-v-site-walkthrough-enable-replication/enable-replication11.png)

6. In **Macchine virtuali** > **Seleziona macchine virtuali** fare clic per selezionare tutte le macchine virtuali da replicare. È possibile selezionare solo i computer per cui è possibile abilitare la replica. Fare quindi clic su **OK**.

    ![Abilitare la replica](./media/hyper-v-site-walkthrough-enable-replication/enable-replication5-for-exclude-disk.png)

7. In **Proprietà** > **Configura proprietà**selezionare il sistema operativo per le VM selezionate e il disco del sistema operativo.
8. Verificare che il nome della VM di Azure (nome di destinazione) sia conforme ai [requisiti per le macchine virtuali di Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
9. Per impostazione predefinita, tutti i dischi della VM sono selezionati per la replica. Deselezionare i dischi per escluderli.
10. Fare clic su **OK** per salvare le modifiche. È possibile impostare proprietà aggiuntive in un secondo momento.

    ![Abilitare la replica](./media/hyper-v-site-walkthrough-enable-replication/enable-replication6-with-exclude-disk.png)

11. In **Impostazioni della replica** > **Configurare le impostazioni di replica** selezionare i criteri di replica da applicare per le VM protette. Fare quindi clic su **OK**. È possibile modificare i criteri di replica in **Criteri di replica** > nome-criterio > **Modifica impostazioni**. Le modifiche applicate verranno usate per i computer di cui è già in corso la replica e per i nuovi computer.


   ![Abilitare la replica](./media/hyper-v-site-walkthrough-enable-replication/enable-replication7.png)

È possibile tenere traccia dello stato del processo **Abilita protezione** in **Processi** > **Site Recovery jobs** (Processi di Site Recovery). Dopo l'esecuzione del processo **Finalizza protezione** la macchina virtuale è pronta per il failover.


## <a name="next-steps"></a>Passaggi successivi


Andare a [Passaggio 11: Eseguire un failover di test](hyper-v-site-walkthrough-test-failover.md)

