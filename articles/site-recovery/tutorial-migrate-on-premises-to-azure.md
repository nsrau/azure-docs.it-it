---
title: Eseguire la migrazione di computer locali ad Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo illustra come eseguire la migrazione di computer locali ad Azure usando Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: ddb412fd-32a8-4afa-9e39-738b11b91118
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/01/2017
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: cfd44f7f06faa7d1d00efa9427dbf5d1d0a89ef1
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2017
---
# <a name="migrate-on-premises-machines-to-azure"></a>Eseguire la migrazione di computer locali ad Azure

Il servizio [Azure Site Recovery](site-recovery-overview.md) gestisce e controlla la replica, il failover e il failback dei computer locali e delle macchine virtuali di Azure.

Questa esercitazione descrive come eseguire la migrazione di macchine virtuali locali e server fisici ad Azure usando Site Recovery. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Impostare i prerequisiti per la distribuzione
> * Creare un insieme di credenziali dei servizi di ripristino per Site Recovery
> * Distribuire i server di gestione locali
> * Impostare i criteri di replica e abilitare la replica
> * Eseguire un'analisi del ripristino di emergenza per assicurarsi che tutto funzioni correttamente
> * Eseguire un unico failover in Azure

## <a name="overview"></a>Panoramica

Per eseguire la migrazione di un computer è necessario abilitare la replica per il computer ed eseguirne il failover in Azure.


## <a name="prerequisites"></a>Prerequisiti

Di seguito sono elencate le operazioni che è necessario eseguire per questa esercitazione.

- [Preparare](tutorial-prepare-azure.md) le risorse di Azure, inclusi un abbonamento ad Azure, una rete virtuale di Azure e un account di archiviazione.
- [Preparare](tutorial-prepare-on-premises-vmware.md) i server e le macchine virtuali VMware locali.
- Si noti che i dispositivi esportati da driver paravirtualizzati non sono supportati.


## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Scegliere un obiettivo di protezione

Selezionare gli elementi da replicare e la posizione in cui eseguire la replica.
1. Fare clic su **Insiemi di credenziali dei servizi di ripristino** e quindi sull'insieme di credenziali.
2. Nel menu Risorsa fare clic su **Site Recovery** > **Preparare l'infrastruttura** > **Obiettivo di protezione**.
3. In **Obiettivo di protezione** selezionare:
    - **VMware**: selezionare **In Azure** > **Sì con VMWare vSphere Hypervisor**.
    - **Computer fisico**: selezionare **In Azure** > **Non virtualizzato/Altro**.
    - **Hyper-V**: selezionare **In Azure** > **Sì con Hyper-V**.


## <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

- [Impostare](tutorial-vmware-to-azure.md#set-up-the-source-environment) l'ambiente di origine per le macchine virtuali VMware.
- [Impostare](tutorial-physical-to-azure.md#set-up-the-source-environment) l'ambiente di origine per i server fisici.
- [Impostare](tutorial-hyper-v-to-azure.md#set-up-the-source-environment) l'ambiente di origine per le macchine virtuali Hyper-V.

## <a name="set-up-the-target-environment"></a>Configurare l'ambiente di destinazione

Selezionare e verificare le risorse di destinazione.

1. Fare clic su **Preparare l'infrastruttura** > **Destinazione** e selezionare la sottoscrizione di Azure da usare.
2. Specificare il modello di distribuzione di destinazione.
3. Site Recovery verifica la disponibilità di uno o più account di archiviazione di Azure e reti compatibili.

## <a name="create-a-replication-policy"></a>Creare un criterio di replica

- [Impostare i criteri di replica](tutorial-vmware-to-azure.md#create-a-replication-policy) per le macchine virtuali VMware.


## <a name="enable-replication"></a>Abilitare la replica

- [Abilitare la replica](tutorial-vmware-to-azure.md#enable-replication) per le macchine virtuali VMware.


## <a name="run-a-test-migration"></a>Eseguire una migrazione di test

Eseguire un [failover di test](tutorial-dr-drill-azure.md) in Azure per verificare che tutto funzioni correttamente.


## <a name="migrate-to-azure"></a>Eseguire la migrazione ad Azure

Eseguire un failover per i computer di cui si vuole eseguire la migrazione.

1. In **Impostazioni** > **Elementi replicati** fare clic su computer > **Failover**.
2. In **Failover** selezionare un **Punto di ripristino** in cui eseguire il failover. Selezionare l'ultimo punto di ripristino.
3. L'impostazione della chiave di crittografia non è rilevante per questo scenario.
4. Selezionare **Arrestare la macchina prima di iniziare il failover** se si vuole provare ad arrestare le macchine virtuali di origine tramite Site Recovery prima di attivare il failover. Il failover continua anche se l'arresto ha esito negativo. Nella pagina **Processi** è possibile seguire lo stato del failover.
5. Assicurarsi che la macchina virtuale di Azure sia visualizzata in Azure come previsto.
6. In **Elementi replicati** fare clic con il pulsante destro del mouse su macchina virtuale > **Completa la migrazione**. Il processo di migrazione viene completato, viene arrestata la replica per la macchina virtuale e viene arrestata la fatturazione di Site Recovery per la macchina virtuale.

    ![Completare la migrazione](./media/tutorial-migrate-on-premises-to-azure/complete-migration.png)


> [!WARNING]
> **Non annullare un failover in corso**: prima dell'avvio del failover, la replica della macchina virtuale viene arrestata. Se si annulla un failover in corso, il failover viene arrestato ma non viene eseguita di nuovo la replica della macchina virtuale.

In alcuni scenari il failover richiede un'ulteriore elaborazione il cui completamento richiede da 8 a 10 minuti. L'esecuzione del failover di test potrebbe richiedere più tempo per server fisici, computer Linux VMware, macchine virtuali VMware per cui non è abilitato il servizio DHCP e macchine virtuali VMware che non hanno i driver di avvio seguenti: storvsc, vmbus, storflt, intelide, atapi.


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Replicare le macchine virtuali di Azure in un'altra area dopo la migrazione ad Azure](site-recovery-azure-to-azure-after-migration.md)
