---
title: Preparare le macchine per configurare il ripristino di emergenza tra le aree di Azure dopo la migrazione ad Azure usando Site Recovery | Microsoft Docs
description: Questo articolo descrive come preparare le macchine per configurare il ripristino di emergenza tra le aree di Azure dopo la migrazione a Azure usando Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: ponatara
manager: abhemraj
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: ponatara
ms.openlocfilehash: 2aee0fb8d1ba1ff1584bee91b4d1cc34b654d97f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="replicate-azure-vms-to-another-region-after-migration-to-azure-by-using-azure-site-recovery"></a>Replicare le macchine virtuali di Azure in un'altra area dopo la migrazione ad Azure usando Azure Site Recovery

>[!NOTE]
> La replica di Azure Site Recovery per le macchine virtuali di Azure è attualmente in anteprima.

## <a name="overview"></a>Panoramica

Questo articolo contiene le informazioni relative alla preparazione delle macchine virtuali di Azure per la replica tra due aeree di Azure dopo che queste macchine sono state migrate da un ambiente on-premises ad Azure usando Azure Site Recovery.

## <a name="disaster-recovery-and-compliance"></a>Ripristino di emergenza e conformità
Oggi sempre più aziende spostano i loro carichi di lavoro in Azure. Di fronte a un numero sempre crescente di aziende impegnate a spostare in Azure i carichi di lavoro di produzione on-premises mission-critical, configurare il ripristino di emergenza è diventato fondamentale ai fini della conformità e della protezione da potenziali interruzioni del servizio in un'area di Azure.

## <a name="steps-for-preparing-migrated-machines-for-replication"></a>Preparazione delle macchine migrate per la replica
Per preparare le macchine migrate per la configurazione della replica in un'altra area di Azure:

1. Completare la migrazione.
2. Se necessario, installare l'agente di Azure.
3. Rimuovere il servizio Mobility.  
4. Riavviare la VM.

Questi passaggi vengono descritti più dettagliatamente nelle sezioni seguenti.

### <a name="step-1-migrate-workloads-running-on-hyper-v-vms-vmware-vms-and-physical-servers-to-run-on-azure-vms"></a>Passaggio 1: eseguire la migrazione dei carichi di lavoro in esecuzione in macchine virtuali Hyper-V e VMware e in server fisici affinché vengano eseguiti in macchine virtuali di Azure

Per configurare la replica ed eseguire la migrazione di carichi di lavoro Hyper-V, VMware on-premises e fisici in Azure, eseguire i passaggi riportati nell'articolo [Eseguire la migrazione delle macchine virtuali Iaas di Azure tra aeree di Azure con Azure Site Recovery](site-recovery-migrate-to-azure.md). 

Dopo la migrazione, non è necessario eseguire il commit di un failover né eliminarlo. In alternativa, selezionare l'opzione **Completa la migrazione** per ogni macchina di cui si desidera eseguire la migrazione:
1. In **Elementi replicati** fare clic con il pulsante destro del mouse sulla macchina virtuale e scegliere **Completa la migrazione**. Fare clic su **OK** per completare il passaggio. È possibile tenere traccia dei progressi nelle proprietà della VM monitorando il processo Completa la migrazione in **Processi di Site Recovery**.
2. L'azione **Completa la migrazione** conclude il processo di migrazione, rimuove la replica per la macchina e interrompe la fatturazione relativa di Site Recovery.

   ![completemigration](./media/site-recovery-hyper-v-site-to-azure/migrate.png)

### <a name="step-2-install-the-azure-vm-agent-on-the-virtual-machine"></a>Passaggio 2: installare l'agente di macchine virtuali di Azure nella macchina virtuale
L'[agente di macchine virtuali](../virtual-machines/windows/classic/agents-and-extensions.md#azure-vm-agents-for-windows-and-linux) di Azure deve essere installato nella macchina virtuale affinché l'estensione Site Recovery funzioni e ai fini della protezione della macchina virtuale.

>[!IMPORTANT]
>A partire dalla versione 9.7.0.0, nelle macchine virtuali di Windows il programma di installazione del servizio Mobility installa anche l'agente di macchine virtuali di Azure più recente. Durante la migrazione, la macchina virtuale soddisfa i prerequisiti di installazione dell'agente per l'uso di qualsiasi estensione della macchina virtuale, incluso Site Recovery. L'agente di macchine virtuali di Azure deve essere installato manualmente solo se il servizio Mobility installato nella macchina migrata è della versione 9.6 o precedente.

La tabella seguente fornisce informazioni aggiuntive sull'installazione dell'agente di macchine virtuali e sulla conferma di avvenuta installazione:

| **Operazione** | **Windows** | **Linux** |
| --- | --- | --- |
| Installazione dell'agente di VM |Scaricare e installare il file [MSI per l'agente](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Per completare l'installazione è necessario disporre dei privilegi di amministratore. |Installare l'[agente Linux](../virtual-machines/linux/agent-user-guide.md) più recente. Per completare l'installazione è necessario disporre dei privilegi di amministratore. È consigliabile installare l'agente dal repository di distribuzione. *Non è consigliabile* installare l'agente di macchine virtuali Linux direttamente da GitHub.  |
| Convalida dell'installazione dell'agente di VM |1. Passare alla cartella C:\WindowsAzure\Packages nella macchina virtuale di Azure. La cartella dovrebbe contenere il file WaAppAgent.exe. <br>2. Fare clic con il pulsante destro del mouse sul file, scegliere **Proprietà** e quindi selezionare la scheda **Dettagli**. Il campo **Versione prodotto** dovrebbe contenere 2.6.1198.718 o un numero più elevato. |N/D |


### <a name="step-3-remove-the-mobility-service-from-the-migrated-virtual-machine"></a>Passaggio 3: rimuovere il servizio Mobility dalla macchina virtuale migrata

Se le macchine VMware on-premises o i server fisici sono stati migrati in Windows/Linux,è necessario rimuovere/disinstallare manualmente il servizio Mobility dalla macchina virtuale migrata.

>[!IMPORTANT]
>Questo passaggio non è necessario le macchine Hyper-V migrate in Azure.

#### <a name="uninstall-the-mobility-service-on-a-windows-server-vm"></a>Disinstallare il servizio Mobility in una macchina virtuale Windows Server
Usare uno dei metodi seguenti per disinstallare il servizio Mobility in una macchina Windows Server.

##### <a name="uninstall-by-using-the-windows-ui"></a>Eseguire la disinstallazione usando l'interfaccia utente di Windows
1. Nel Pannello di controllo, selezionare **Programmi**.
2. Selezionare **Microsoft Azure Site Recovery Mobility Service/Master Target server** (Servizio Mobility di Microsoft Azure Site Recovery/server di destinazione master) e fare clic su **Disinstalla**.

##### <a name="uninstall-at-a-command-prompt"></a>Disinstallare dal prompt dei comandi
1. Aprire una finestra del Prompt dei comandi come amministratore.
2. Per disinstallare il servizio Mobility, eseguire il comando seguente:

   ```
   MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
   ```

#### <a name="uninstall-the-mobility-service-on-a-linux-computer"></a>Disinstallare il servizio Mobility in un computer Linux
1. Sul server Linux, accedere come utente **Root**.
2. Nel terminale, passare a /user/local/ASR.
3. Per disinstallare il servizio Mobility, eseguire il comando seguente:

   ```
   uninstall.sh -Y
   ```

### <a name="step-4-restart-the-vm"></a>Passaggio 4: riavviare la macchina virtuale

Dopo avere disinstallato il servizio Mobility, riavviare la macchina virtuale prima di configurare la replica in un'altra area di Azure.


## <a name="next-steps"></a>Passaggi successivi
- Iniziare a proteggere i carichi di lavoro [replicando le macchine virtuali di Azure](site-recovery-azure-to-azure.md).
- Altre informazioni sulle [indicazioni di connettività di rete per la replica delle macchine virtuali di Azure](site-recovery-azure-to-azure-networking-guidance.md).
