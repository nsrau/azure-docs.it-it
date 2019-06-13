---
title: Eseguire la migrazione di computer locali ad Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo illustra come eseguire la migrazione di computer locali ad Azure usando Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: df4f89bd1b2e3c0423f5d758cfa637e4da9e04d0
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66396531"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Eseguire la migrazione di computer locali ad Azure


Questo articolo illustra come eseguire la migrazione di computer locali ad Azure usando [Azure Site Recovery](site-recovery-overview.md). Site Recovery viene generalmente usato per gestire e orchestrare il ripristino di emergenza di computer locali e macchine virtuali di Azure, tuttavia può anche essere usato per la migrazione. La migrazione usa gli stessi passaggi del ripristino di emergenza con un'eccezione. In una migrazione, il failover delle macchine dal sito locale è il passaggio finale. A differenza del ripristino di emergenza, in uno scenario di migrazione non è possibile eseguire il failback all'ambiente locale.


Questa esercitazione descrive come eseguire la migrazione di VM locali e server fisici in Azure. Si apprenderà come:

> [!div class="checklist"]
> * Configurare l'ambiente di origine e di destinazione per la migrazione
> * Configurare criteri di replica
> * Abilitare la replica
> * Eseguire una migrazione di test per verificare che tutti gli elementi funzionino come previsto
> * Eseguire un unico failover in Azure


> [!TIP]
> Il servizio Azure Migrate offre ora l'anteprima di una nuova esperienza senza agenti per la migrazione di VM VMware in Azure. [vedere altre informazioni](https://aka.ms/migrateVMs-signup).


## <a name="before-you-start"></a>Prima di iniziare

Si noti che i dispositivi esportati da driver paravirtualizzati non sono supportati.


## <a name="prepare-azure-and-on-premises"></a>Preparare Azure e l'ambiente locale

1. Preparare Azure come descritto in [questo articolo](tutorial-prepare-azure.md). Anche se questo articolo descrive i passaggi di preparazione per il ripristino di emergenza, la procedura è valida anche per la migrazione.
2. Preparare i server [VMware](vmware-azure-tutorial-prepare-on-premises.md) o [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) locali. Se si esegue la migrazione di computer fisici non è necessario preparare nulla. È sufficiente verificare la [matrice di supporto](vmware-physical-azure-support-matrix.md).


## <a name="select-a-replication-goal"></a>Selezionare un obiettivo di replica

Selezionare gli elementi da replicare e la posizione in cui eseguire la replica.
1. Fare clic su **Insiemi di credenziali dei servizi di ripristino** e quindi sull'insieme di credenziali.
2. Nel menu Risorsa fare clic su **Site Recovery** > **Preparare l'infrastruttura** > **Obiettivo di protezione**.
3. In **Protection goal** (Obiettivo di protezione) selezionare ciò che si intende migrare.
    - **VMware**: Selezionare **In Azure** > **Sì, con VMWare vSphere Hypervisor**.
    - **Computer fisico**: Selezionare **In Azure** > **Non virtualizzato/Altro**.
    - **Hyper-V**: Selezionare **In Azure** >  **,** . Se le macchine virtuali Hyper-V sono gestite da VMM, selezionare **Sì**.


## <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

**Scenario** | **Dettagli**
--- | --- 
VMware | Configurare l'[ambiente di origine](vmware-azure-set-up-source.md) e il [server di configurazione](vmware-azure-deploy-configuration-server.md).
Computer fisico | [Configurare](physical-azure-set-up-source.md) l'ambiente di origine e il server di configurazione.
Hyper-V | Configurare l'[ambiente di origine](hyper-v-azure-tutorial.md#set-up-the-source-environment)<br/><br/> Configurare l'[ambiente di origine](hyper-v-vmm-azure-tutorial.md#set-up-the-source-environment) per Hyper-V distribuito con System Center VMM.

## <a name="set-up-the-target-environment"></a>Configurare l'ambiente di destinazione

Selezionare e verificare le risorse di destinazione.

1. Fare clic su **Preparare l'infrastruttura** > **Destinazione** e selezionare la sottoscrizione di Azure da usare.
2. Specificare il modello di distribuzione di Gestione risorse.
3. Site Recovery verifica le risorse di Azure.
    - Se si esegue la migrazione di VM VMware o server fisici, Site Recovery verifica che sia presente una rete di Azure in cui verranno posizionate le VM di Azure al momento della loro creazione dopo il failover.
    - Se si esegue la migrazione di VM Hyper-V, Site Recovery verifica che siano presenti un account di archiviazione e una rete di Azure compatibili.
4. Se si esegue la migrazione di VM gestite da System Center VMM, configurare il [mapping di rete](hyper-v-vmm-azure-tutorial.md#configure-network-mapping).

## <a name="set-up-a-replication-policy"></a>Configurare criteri di replica

**Scenario** | **Dettagli**
--- | --- 
VMware | Configurare [criteri di replica](vmware-azure-set-up-replication.md) per VM VMware.
Computer fisico | Configurare [criteri di replica](physical-azure-disaster-recovery.md#create-a-replication-policy) per computer fisici.
Hyper-V | Configurare [criteri di replica](hyper-v-azure-tutorial.md#set-up-a-replication-policy)<br/><br/> Configurare [criteri di replica](hyper-v-vmm-azure-tutorial.md#set-up-a-replication-policy) per Hyper-V distribuito con System Center VMM.

## <a name="enable-replication"></a>Abilitare la replica

**Scenario** | **Dettagli**
--- | --- 
VMware | [Abilitare la replica](vmware-azure-enable-replication.md) per le macchine virtuali VMware.
Computer fisico | [Abilitare la replica](physical-azure-disaster-recovery.md#enable-replication) per i computer fisici.
Hyper-V | [Abilitare la replica](hyper-v-azure-tutorial.md#enable-replication)<br/><br/> [Abilitare la replica](hyper-v-vmm-azure-tutorial.md#enable-replication) per Hyper-V distribuito con System Center VMM.


## <a name="run-a-test-migration"></a>Eseguire una migrazione di test

Eseguire un [failover di test](tutorial-dr-drill-azure.md) in Azure per verificare che tutto funzioni correttamente.


## <a name="migrate-to-azure"></a>Eseguire la migrazione ad Azure

Eseguire un failover per i computer di cui si vuole eseguire la migrazione.

1. In **Impostazioni** > **Elementi replicati** fare clic su computer > **Failover**.
2. In **Failover** selezionare un **Punto di ripristino** in cui eseguire il failover. Selezionare l'ultimo punto di ripristino.
3. L'impostazione della chiave di crittografia non è rilevante per questo scenario.
4. Selezionare **Arrestare la macchina prima di iniziare il failover**. Site Recovery proverà ad arrestare le macchine virtuali prima di attivare il failover. Il failover continua anche se l'arresto ha esito negativo. Nella pagina **Processi** è possibile seguire lo stato del failover.
5. Assicurarsi che la macchina virtuale di Azure sia visualizzata in Azure come previsto.
6. In **Elementi replicati** fare clic con il pulsante destro del mouse su macchina virtuale > **Completa la migrazione**. Vengono eseguite le operazioni seguenti:

   - Il processo di migrazione viene completato, viene arrestata la replica per la VM locale e viene interrotta la fatturazione di Site Recovery per la VM.
   - In questo passaggio vengono eliminati i dati di replica, ma non le macchine virtuali di cui è stata eseguita la migrazione.

     ![Completare la migrazione](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **Non annullare un failover in corso**: Prima dell'avvio del failover, la replica della macchina virtuale viene arrestata. Se si annulla un failover in corso, il failover viene arrestato ma non viene eseguita di nuovo la replica della macchina virtuale.

In alcuni scenari il failover richiede un'altra elaborazione il cui completamento richiede da 8 a 10 minuti. L'esecuzione del failover di test potrebbe richiedere più tempo per server fisici, computer Linux VMware, macchine virtuali VMware per cui non è abilitato il servizio DHCP e macchine virtuali VMware che non hanno i driver di avvio seguenti: storvsc, vmbus, storflt, intelide, atapi.

## <a name="after-migration"></a>Dopo la migrazione

Dopo la migrazione di una macchina in Azure, è necessario completare una serie di passaggi.

Alcuni passaggi possono essere automatizzati nell'ambito del processo di migrazione usando la funzionalità degli script di automazione integrata nei [piani di ripristino](site-recovery-runbook-automation.md)   


### <a name="post-migration-steps-in-azure"></a>Passaggi post-migrazione in Azure

- Apportare nell'app le eventuali modifiche post-migrazione necessarie, come l'aggiornamento delle stringhe di connessione del database e delle configurazioni dei server Web. 
- Eseguire i test di accettazione della migrazione e dell'applicazione finale sull'applicazione migrata ora in esecuzione in Azure.
- L'[agente di macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) gestisce l'interazione tra le macchine virtuali e il controller di infrastruttura di Azure. È obbligatorio per alcuni servizi di Azure, tra cui Backup di Azure, Site Recovery e Sicurezza di Azure.
    - Se si esegue la migrazione di macchine VMware e server fisici, il programma di installazione del Servizio Mobility installa nei computer Windows l'agente di macchine virtuali di Azure disponibile. Nelle macchine virtuali Linux si consiglia di installare l'agente dopo il failover.
    - Se si esegue la migrazione di macchine virtuali di Azure in un'area secondaria, prima della migrazione è necessario effettuare il provisioning dell'agente di macchine virtuali di Azure nella macchina virtuale.
    - Se si esegue la migrazione di macchine virtuali Hyper-V in Azure, dopo la migrazione è necessario installare l'agente di macchine virtuali di Azure nella macchina virtuale di Azure.
- Rimuovere manualmente qualsiasi provider/agente di Site Recovery dalla macchina virtuale. Se si esegue la migrazione di server fisici o VM VMware, disinstallare il servizio Mobility dalla VM.
- Per una maggiore resilienza:
    - Proteggere i dati eseguendo il backup delle macchine virtuali di Azure con il servizio Backup di Azure. [Altre informazioni]( https://docs.microsoft.com/azure/backup/quick-backup-vm-portal)
    - Mantenere i carichi di lavoro in esecuzione e sempre disponibili eseguendo la replica delle macchine virtuali di Azure in un'area secondaria con Site Recovery. [Altre informazioni](azure-to-azure-quickstart.md)
- Per una maggiore sicurezza:
    - Bloccare e limitare l'accesso del traffico in ingresso con la funzionalità [Amministrazione JIT]( https://docs.microsoft.com/azure/security-center/security-center-just-in-time) del Centro sicurezza di Azure.
    - Limitare il traffico di rete verso gli endpoint di gestione con la funzionalità [Gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Distribuire [Crittografia dischi di Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) per garantire la sicurezza dei dischi e proteggere i dati da furti e accessi non autorizzati.
    - Per altre informazioni sulla [protezione delle risorse IaaS]( https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/ ), visitare il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/ ).
- Per il monitoraggio e la gestione:
    - È consigliabile distribuire [Gestione costi di Azure](https://docs.microsoft.com/azure/cost-management/overview) per monitorare l'utilizzo delle risorse e le spese.

### <a name="post-migration-steps-on-premises"></a>Passaggi post-migrazione in locale

- Spostare il traffico dell'app sull'app in esecuzione nell'istanza della macchina virtuale di Azure migrata.
- Rimuovere le macchine virtuali locali dall'inventario delle macchine virtuali locale.
- Rimuovere le macchine virtuali locali dai processi di backup locali.
- Aggiornare la documentazione interna con la nuova posizione e il nuovo indirizzo IP delle macchine virtuali di Azure.




## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata eseguita la migrazione dalle macchine virtuali locali alle macchine virtuali di Azure. Now

> [!div class="nextstepaction"]
> [Configurare il ripristino di emergenza](azure-to-azure-replicate-after-migration.md) in un'area di Azure secondaria per le macchine virtuali di Azure.

  
