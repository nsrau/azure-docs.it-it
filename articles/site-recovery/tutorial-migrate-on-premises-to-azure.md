---
title: Eseguire la migrazione di computer locali ad Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo illustra come eseguire la migrazione di computer locali ad Azure usando Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/07/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: ee9397406cbca21d8bd53019d9daac5a037f508c
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2018
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
2. Preparare i server [VMware](tutorial-prepare-on-premises-vmware.md) o Hyper-V locali.

Prima di iniziare è utile esaminare le architetture [VMware](concepts-vmware-to-azure-architecture.md) o [Hyper-V](concepts-hyper-v-to-azure-architecture.md) per il ripristino di emergenza.


## <a name="prerequisites"></a>Prerequisiti

I dispositivi esportati da driver paravirtualizzati non sono supportati.


## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

1. Accedere a [portale di Azure](https://portal.azure.com) > **Servizi di ripristino**.
2. Fare clic su **Nuovo** > **Monitoraggio e gestione** > **Backup e Site Recovery**.
3. In **Nome** specificare il nome descrittivo **ContosoVMVault**. Se è disponibile più di una sottoscrizione, selezionare quella appropriata.
4. Creare un gruppo di risorse **ContosoRG**.
5. Specificare un'area di Azure. Per verificare le aree supportate, vedere la sezione relativa alla disponibilità a livello geografico in [Prezzi di Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Per accedere rapidamente all'insieme di credenziali dal dashboard, fare clic su **Aggiungi al dashboard** e quindi su **Crea**.

   ![Nuovo insieme di credenziali](./media/tutorial-migrate-on-premises-to-azure/onprem-to-azure-vault.png)

Il nuovo insieme di credenziali viene aggiunto al **Dashboard**, nella sezione **Tutte le risorse**, e nella pagina **Insiemi di credenziali dei servizi di ripristino** principale.



## <a name="select-a-replication-goal"></a>Selezionare un obiettivo di replica

Selezionare gli elementi da replicare e la posizione in cui eseguire la replica.
1. Fare clic su **Insiemi di credenziali dei servizi di ripristino** e quindi sull'insieme di credenziali.
2. Nel menu Risorsa fare clic su **Site Recovery** > **Preparare l'infrastruttura** > **Obiettivo di protezione**.
3. In **Protection goal** (Obiettivo di protezione) selezionare ciò che si intende migrare.
    - **VMware**: selezionare **In Azure** > **Sì con VMWare vSphere Hypervisor**.
    - **Computer fisico**: selezionare **In Azure** > **Non virtualizzato/Altro**.
    - **Hyper-V**: selezionare **In Azure** > **Sì con Hyper-V**. Se le macchine virtuali Hyper-V sono gestite da VMM, selezionare **Sì**.


## <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

- [Impostare](tutorial-vmware-to-azure.md#set-up-the-source-environment) l'ambiente di origine per le macchine virtuali VMware.
- [Impostare](tutorial-physical-to-azure.md#set-up-the-source-environment) l'ambiente di origine per i server fisici.
- [Impostare](tutorial-hyper-v-to-azure.md#set-up-the-source-environment) l'ambiente di origine per le macchine virtuali Hyper-V.

## <a name="set-up-the-target-environment"></a>Configurare l'ambiente di destinazione

Selezionare e verificare le risorse di destinazione.

1. Fare clic su **Preparare l'infrastruttura** > **Destinazione** e selezionare la sottoscrizione di Azure da usare.
2. Specificare il modello di distribuzione di Gestione risorse.
3. Site Recovery verifica la disponibilità di uno o più account di archiviazione di Azure e reti compatibili.

## <a name="set-up-a-replication-policy"></a>Configurare criteri di replica

- [Impostare i criteri di replica](tutorial-vmware-to-azure.md#create-a-replication-policy) per le macchine virtuali VMware.
- [Impostare i criteri di replica](tutorial-physical-to-azure.md#create-a-replication-policy) per i server fisici.
- [Impostare i criteri di replica](tutorial-hyper-v-to-azure.md#set-up-a-replication-policy) per le macchine virtuali Hyper-V.


## <a name="enable-replication"></a>Abilitare la replica

- [Abilitare la replica](tutorial-vmware-to-azure.md#enable-replication) per le macchine virtuali VMware.
- [Abilitare la replica](tutorial-physical-to-azure.md#enable-replication) per i server fisici.
- [Abilitare la replica](tutorial-hyper-v-to-azure.md#enable-replication) per le macchine virtuali Hyper-V.


## <a name="run-a-test-migration"></a>Eseguire una migrazione di test

Eseguire un [failover di test](tutorial-dr-drill-azure.md) in Azure per verificare che tutto funzioni correttamente.


## <a name="migrate-to-azure"></a>Eseguire la migrazione ad Azure

Eseguire un failover per i computer di cui si vuole eseguire la migrazione.

1. In **Impostazioni** > **Elementi replicati** fare clic su computer > **Failover**.
2. In **Failover** selezionare un **Punto di ripristino** in cui eseguire il failover. Selezionare l'ultimo punto di ripristino.
3. L'impostazione della chiave di crittografia non è rilevante per questo scenario.
4. Selezionare **Shut down machine before beginning failover** (Arrestare la macchina prima di iniziare il failover). Site Recovery proverà ad arrestare le macchine virtuali di origine prima di attivare il failover. Il failover continua anche se l'arresto ha esito negativo. Nella pagina **Processi** è possibile seguire lo stato del failover.
5. Assicurarsi che la macchina virtuale di Azure sia visualizzata in Azure come previsto.
6. In **Elementi replicati** fare clic con il pulsante destro del mouse su macchina virtuale > **Completa la migrazione**. Il processo di migrazione viene completato, viene arrestata la replica per la macchina virtuale e viene arrestata la fatturazione di Site Recovery per la macchina virtuale.

    ![Completare la migrazione](./media/tutorial-migrate-on-premises-to-azure/complete-migration.png)


> [!WARNING]
> **Non annullare un failover in corso**: prima dell'avvio del failover, la replica della macchina virtuale viene arrestata. Se si annulla un failover in corso, il failover viene arrestato ma non viene eseguita di nuovo la replica della macchina virtuale.

In alcuni scenari il failover richiede un'ulteriore elaborazione il cui completamento richiede da 8 a 10 minuti. L'esecuzione del failover di test potrebbe richiedere più tempo per server fisici, computer Linux VMware, macchine virtuali VMware per cui non è abilitato il servizio DHCP e macchine virtuali VMware che non hanno i driver di avvio seguenti: storvsc, vmbus, storflt, intelide, atapi.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata eseguita la migrazione dalle macchine virtuali locali alle macchine virtuali di Azure. Ora è possibile configurare il ripristino di emergenza per le macchine virtuali di Azure.

> [!div class="nextstepaction"]
> [Configurare il ripristino di emergenza](site-recovery-azure-to-azure-after-migration.md) per le macchine virtuali di Azure dopo la migrazione da un sito locale.
