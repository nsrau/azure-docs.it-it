---
title: Abilitare la replica in Azure per le macchine virtuali Hyper-V nei cloud VMM con Azure Site Recovery | Microsoft Docs
description: Questo articolo descrive come abilitare la replica in Azure per macchine virtuali Hyper-V nei cloud VMM con il servizio Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 89a2c4fc-7e03-4a86-a2c0-52831ccebc1a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: raynew
ms.openlocfilehash: 96a817e43a830e836f2faa4603fc88ed9c0b1828
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="step-11-enable-replication-to-azure-for-hyper-v-vms-in-vmm-clouds"></a>Passaggio 11: Abilitare la replica in Azure per le macchine virtuali Hyper-V nei cloud VMM

Dopo avere impostato i criteri di replica, usare le istruzioni disponibili in questo articolo per abilitare la replica delle macchine virtuali Hyper-V locali gestite in cloud System Center Virtual Machine Manager (VMM) in Azure tramite il servizio [Azure Site Recovery](site-recovery-overview.md) nel portale di Azure.

Inserire commenti e domande nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="before-you-start"></a>Prima di iniziare

Assicurarsi che l'account Azure includa le [autorizzazioni](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) corrette per la creazione di macchine virtuali di Azure. [Altre informazioni](../active-directory/role-based-access-built-in-roles.md) sul controllo degli accessi in base al ruolo di Azure.

## <a name="exclude-disks-from-replication"></a>Escludere dischi dalla replica

Per impostazione predefinita, vengono replicati tutti i dischi in un computer. È possibile escludere dischi dalla replica. Ad esempio, è possibile evitare di replicare i dischi con dati temporanei o dati che vengono aggiornati ogni volta che un computer o un'applicazione viene riavviata, come pagefile.sys o tempdb di SQL Server. [Altre informazioni](site-recovery-exclude-disk.md)

## <a name="replicate-vms"></a>Replicare le VM

Abilitare la replica per le macchine virtuali nel modo seguente:  

1. Fare clic su **Passaggio 2: Eseguire la replica dell'applicazione** > **Origine**. Dopo avere abilitato la replica per la prima volta, è necessario fare clic su **+Replica** nell'insieme di credenziali per abilitare la replica per altre macchine.

    ![Abilitare la replica](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication1.png)
2. Nel pannello **Origine** selezionare il cloud e il server VMM in cui si trovano gli host Hyper-V. Fare quindi clic su **OK**.

    ![Abilitare la replica](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication-source.png)
3. In **Destinazione** selezionare la sottoscrizione, il modello di distribuzione da usare dopo il failover e l'account di archiviazione usato per i dati replicati.

    ![Abilitare la replica](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication-target.png)
4. Selezionare l'account di archiviazione da usare. Per usare un account di archiviazione diverso da quelli disponibili, è possibile [crearne uno](#set-up-an-azure-storage-account). Se si usa un account di archiviazione Premium per i dati replicati, è necessario selezionare un account di archiviazione Standard aggiuntivo per l'archiviazione dei log di replica in cui vengono acquisite le modifiche in corso ai dati locali. Per creare un account di archiviazione usando il modello di Resource Manager, fare clic su **Crea nuovo**. Per creare un account di archiviazione con il modello di distribuzione classica, usare il [portale di Azure](../storage/common/storage-create-storage-account.md). Fare quindi clic su **OK**.
5. Selezionare la rete di Azure e la subnet a cui dovranno connettersi le VM di Azure create dopo il failover. Scegliere **Configurare ora per le macchine virtuali selezionate** per applicare le impostazioni di rete a tutti i computer selezionati per la protezione. Scegliere **Configurare in seguito** per selezionare la rete di Azure per ogni computer. Se si vuole usare una rete diversa da quelle disponibili, è possibile [crearne una](#set-up-an-azure-network). Per creare una rete con il modello di distribuzione di Resource Manager, fare clic su **Crea nuovo**. Per creare una rete con il modello di distribuzione classica, usare il [portale di Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Selezionare una subnet, se applicabile. Fare quindi clic su **OK**.
6. In **Macchine virtuali** > **Seleziona macchine virtuali** fare clic per selezionare tutte le macchine virtuali da replicare. È possibile selezionare solo i computer per cui è possibile abilitare la replica. Fare quindi clic su **OK**.

    ![Abilitare la replica](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication5.png)

7. In **Proprietà** > **Configura proprietà**selezionare il sistema operativo per le VM selezionate e il disco del sistema operativo.

    - Verificare che il nome della VM di Azure (nome di destinazione) sia conforme ai [requisiti per le macchine virtuali di Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).   
    - Per impostazione predefinita, tutti i dischi della VM sono selezionati per la replica. È tuttavia possibile cancellarli per escluderli,

        - ad esempio per ridurre la larghezza di banda di replica. È possibile evitare, ad esempio, di replicare i dischi con dati temporanei o dati che vengono aggiornati ad ogni riavvio di un computer o di un'applicazione, come pagefile.sys o tempdb di Microsoft SQL Server. Per escludere un disco dalla replica, è sufficiente selezionarlo.
        - Solo i dischi di base possono essere esclusi dalla replica. Non è possibile escludere dischi del sistema operativo
        - e non è consigliabile escludere dischi dinamici. Site Recovery non può determinare se un disco rigido virtuale all'interno della macchina virtuale guest è di base o dinamico. Se non sono esclusi tutti i volumi dinamici dipendenti, in caso di failover della VM il disco dinamico protetto risulterà guasto e non sarà possibile accedere ai dati presenti su tale disco.
        - Dopo aver abilitato la replica, non è più possibile aggiungere o rimuovere dischi da replicare. Se si desidera aggiungere o escludere un disco, è necessario disabilitare la protezione della macchina virtuale e riabilitarla al termine dell'operazione.
        - Per i dischi creati manualmente in Azure non viene eseguito il failback. Ad esempio, se si esegue il failover di tre dischi e se ne creano due direttamente in Azure, verrà eseguito il failback da Azure a Hyper-V soltanto dei tre dischi. Non è possibile includere nel failback o nella replica inversa da Hyper-V ad Azure i dischi creati manualmente.
        - Se si esclude un disco necessario per il funzionamento di un'applicazione, dopo il failover in Azure è necessario crearlo manualmente in Azure per consentire l'esecuzione dell'applicazione replicata. È possibile in alternativa integrare Automazione di Azure in un piano di ripristino per creare il disco durante il failover del computer.

    Fare clic su **OK** per salvare le modifiche. È possibile impostare proprietà aggiuntive in un secondo momento.

    ![Abilitare la replica](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication6-with-exclude-disk.png)

8. In **Impostazioni della replica** > **Configurare le impostazioni di replica** selezionare i criteri di replica da applicare per le VM protette. Fare quindi clic su **OK**. È possibile modificare i criteri di replica in **Criteri di replica** > nome del criterio > **Modifica impostazioni**. Le modifiche applicate vengono usate per i computer di cui è già in corso la replica e per i nuovi computer.

   ![Abilitare la replica](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication7.png)

È possibile tenere traccia dello stato del processo **Abilita protezione** in **Processi** > **Site Recovery jobs** (Processi di Site Recovery). Dopo l'esecuzione del processo **Finalizza protezione** la macchina virtuale è pronta per il failover.



## <a name="next-steps"></a>Passaggi successivi

Andare al [Passaggio 12: Eseguire un failover di test](vmm-to-azure-walkthrough-test-failover.md)
