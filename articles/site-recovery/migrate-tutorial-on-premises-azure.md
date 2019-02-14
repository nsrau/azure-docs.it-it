---
title: Eseguire la migrazione di computer locali ad Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo illustra come eseguire la migrazione di computer locali ad Azure usando Azure Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6c97289cd1ad406769613621afdc16c8096f4aeb
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2019
ms.locfileid: "56116168"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Eseguire la migrazione di computer locali ad Azure

Oltre a usare il servizio [Azure Site Recovery](site-recovery-overview.md) per gestire e orchestrare il ripristino di emergenza dei computer locali e delle VM di Azure ai fini di continuità aziendale e ripristino di emergenza (BCDR), è inoltre possibile usare Site Recovery per gestire la migrazione dei computer locali in Azure.


Questa esercitazione descrive come eseguire la migrazione di VM locali e server fisici in Azure. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Selezionare un obiettivo di replica
> * Configurare l'ambiente di origine e di destinazione
> * Configurare criteri di replica
> * Abilitare la replica
> * Eseguire una migrazione di test per verificare che tutti gli elementi funzionino come previsto
> * Eseguire un unico failover in Azure

Questa è la terza esercitazione di una serie. In questa esercitazione si presuppone che siano già state completate le attività delle esercitazioni precedenti:

1. [Preparare Azure](tutorial-prepare-azure.md)
2. Preparare i server [VMware](vmware-azure-tutorial-prepare-on-premises.md) o [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) locali.

Prima di iniziare è utile esaminare le architetture [VMware](vmware-azure-architecture.md) o [Hyper-V](hyper-v-azure-architecture.md) per il ripristino di emergenza.


## <a name="prerequisites"></a>Prerequisiti

I dispositivi esportati da driver paravirtualizzati non sono supportati.


## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

1. Accedere a [portale di Azure](https://portal.azure.com) > **Servizi di ripristino**.
2. Fare clic su **Crea una risorsa** > **Strumenti di gestione** > **Backup e Site Recovery (OMS)**.
3. In **Nome** specificare il nome descrittivo **ContosoVMVault**. Se è disponibile più di una sottoscrizione, selezionare quella appropriata.
4. Creare un gruppo di risorse **ContosoRG**.
5. Specificare un'area di Azure. Per verificare le aree supportate, vedere la sezione relativa alla disponibilità a livello geografico in [Prezzi di Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Per accedere rapidamente all'insieme di credenziali dal dashboard, fare clic su **Aggiungi al dashboard** e quindi su **Crea**.

   ![Nuovo insieme di credenziali](./media/migrate-tutorial-on-premises-azure/onprem-to-azure-vault.png)

Il nuovo insieme di credenziali viene aggiunto al **Dashboard**, nella sezione **Tutte le risorse**, e nella pagina **Insiemi di credenziali dei servizi di ripristino** principale.



## <a name="select-a-replication-goal"></a>Selezionare un obiettivo di replica

Selezionare gli elementi da replicare e la posizione in cui eseguire la replica.
1. Fare clic su **Insiemi di credenziali dei servizi di ripristino** e quindi sull'insieme di credenziali.
2. Nel menu Risorsa fare clic su **Site Recovery** > **Preparare l'infrastruttura** > **Obiettivo di protezione**.
3. In **Protection goal** (Obiettivo di protezione) selezionare ciò che si intende migrare.
    - **VMware**: Selezionare **In Azure** > **Sì, con VMWare vSphere Hypervisor**.
    - **Computer fisico**: Selezionare **In Azure** > **Non virtualizzato/Altro**.
    - **Hyper-V**: Selezionare **In Azure** > **,** . Se le macchine virtuali Hyper-V sono gestite da VMM, selezionare **Sì**.


## <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

- [Impostare](vmware-azure-tutorial.md#set-up-the-source-environment) l'ambiente di origine per le macchine virtuali VMware.
- [Impostare](physical-azure-disaster-recovery.md#set-up-the-source-environment) l'ambiente di origine per i server fisici.
- [Impostare](hyper-v-azure-tutorial.md#set-up-the-source-environment) l'ambiente di origine per le macchine virtuali Hyper-V.

## <a name="set-up-the-target-environment"></a>Configurare l'ambiente di destinazione

Selezionare e verificare le risorse di destinazione.

1. Fare clic su **Preparare l'infrastruttura** > **Destinazione** e selezionare la sottoscrizione di Azure da usare.
2. Specificare il modello di distribuzione di Gestione risorse.
3. Site Recovery verifica la disponibilità di uno o più account di archiviazione di Azure e reti compatibili.

## <a name="set-up-a-replication-policy"></a>Configurare criteri di replica

- [Impostare i criteri di replica](vmware-azure-tutorial.md#create-a-replication-policy) per le macchine virtuali VMware.
- [Impostare i criteri di replica](physical-azure-disaster-recovery.md#create-a-replication-policy) per i server fisici.
- [Impostare i criteri di replica](hyper-v-azure-tutorial.md#set-up-a-replication-policy) per le macchine virtuali Hyper-V.


## <a name="enable-replication"></a>Abilitare la replica

- [Abilitare la replica](vmware-azure-tutorial.md#enable-replication) per le macchine virtuali VMware.
- [Abilitare la replica](physical-azure-disaster-recovery.md#enable-replication) per i server fisici.
- Abilitare la replica per le macchine virtuali Hyper-V [con](hyper-v-vmm-azure-tutorial.md#enable-replication) o [senza VMM](hyper-v-azure-tutorial.md#enable-replication).


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

    - Il processo di migrazione viene completato, viene arrestata la replica per la macchina virtuale AWS e viene arrestata la fatturazione di Site Recovery per la macchina virtuale.
    - In questo passaggio vengono eliminati i dati di replica, ma non le macchine virtuali di cui è stata eseguita la migrazione.

    ![Completare la migrazione](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **Non annullare un failover in corso**: Prima dell'avvio del failover, la replica della macchina virtuale viene arrestata. Se si annulla un failover in corso, il failover viene arrestato ma non viene eseguita di nuovo la replica della macchina virtuale.

In alcuni scenari il failover richiede un'ulteriore elaborazione il cui completamento richiede da 8 a 10 minuti. L'esecuzione del failover di test potrebbe richiedere più tempo per server fisici, computer Linux VMware, macchine virtuali VMware per cui non è abilitato il servizio DHCP e macchine virtuali VMware che non hanno i driver di avvio seguenti: storvsc, vmbus, storflt, intelide, atapi.

## <a name="after-migration"></a>Dopo la migrazione

Dopo la migrazione di una macchina in Azure, è necessario completare una serie di passaggi.

Alcuni passaggi possono essere automatizzati nell'ambito del processo di migrazione usando la funzionalità degli script di automazione integrata nei [piani di ripristino]( https://docs.microsoft.com/azure/site-recovery/site-recovery-runbook-automation)   


### <a name="post-migration-steps-in-azure"></a>Passaggi post-migrazione in Azure

- Apportare nell'app le eventuali modifiche post-migrazione necessarie, come l'aggiornamento delle stringhe di connessione del database e delle configurazioni dei server Web. 
- Eseguire i test di accettazione della migrazione e dell'applicazione finale sull'applicazione migrata ora in esecuzione in Azure.
- L'[agente di macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) gestisce l'interazione tra le macchine virtuali e il controller di infrastruttura di Azure. È obbligatorio per alcuni servizi di Azure, tra cui Backup di Azure, Site Recovery e Sicurezza di Azure.
    - Se si esegue la migrazione di macchine VMware e server fisici, il programma di installazione del Servizio Mobility installa nei computer Windows l'agente di macchine virtuali di Azure disponibile. Nelle macchine virtuali Linux si consiglia di installare l'agente dopo il failover.
    - Se si esegue la migrazione di macchine virtuali di Azure in un'area secondaria, prima della migrazione è necessario effettuare il provisioning dell'agente di macchine virtuali di Azure nella macchina virtuale.
    - Se si esegue la migrazione di macchine virtuali Hyper-V in Azure, dopo la migrazione è necessario installare l'agente di macchine virtuali di Azure nella macchina virtuale di Azure.
- Rimuovere manualmente qualsiasi provider/agente di Site Recovery dalla macchina virtuale. Se si esegue la migrazione di macchine virtuali VMware o server fisici, [disinstallare il Servizio Mobility][vmware-azure-install-mobility-service.md#uninstall-mobility-service-on-a-windows-server-computer] dalla macchina virtuale di Azure.
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

In questa esercitazione è stata eseguita la migrazione dalle macchine virtuali locali alle macchine virtuali di Azure. È possibile ora [configurare il ripristino di emergenza](azure-to-azure-replicate-after-migration.md) in un'area di Azure secondaria per le macchine virtuali di Azure.

  
