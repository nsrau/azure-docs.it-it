---
title: Failover e failback di macchine virtuali Hyper-V replicate in Azure con Site Recovery | Microsoft Docs
description: Informazioni su come eseguire il failover di macchine virtuali Hyper-V in Azure e il failback nel sito locale con Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 12/31/2017
ms.author: raynew
ms.openlocfilehash: 390fe98bc718da4fe07f580bbf1dcbffbf8fc1ba
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-azure"></a>Failover e failback di macchine virtuali Hyper-V replicate in Azure

Questa esercitazione descrive come eseguire il failover di una macchina virtuale Hyper-V in Azure. Dopo il failover è possibile eseguire il failback nel sito locale quando disponibile. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Verificare le proprietà della macchina virtuale Hyper-V per verificare siano conformi ai requisiti di Azure
> * Eseguire un failover in Azure
> * Riproteggere le macchine virtuali di Azure nel sito locale
> * Eseguire il failback da Azure nel sito locale
> * Riproteggere le macchine virtuali locali per avviare nuovamente la replica in Azure

Questa è la quinta esercitazione di una serie. In questa esercitazione si presuppone che siano già state completate le attività delle esercitazioni precedenti.

1. [Preparare Azure](tutorial-prepare-azure.md)
2. [Preparare istanze di VMware locali](tutorial-prepare-on-premises-hyper-v.md)
3. Configurare il ripristino di emergenza per [macchine virtuali Hyper-V](tutorial-hyper-v-to-azure.md), o per [le macchine virtuali Hyper-V gestiti in cloud System Center VMM](tutorial-hyper-v-vmm-to-azure.md)
4. [Eseguire un'esercitazione sul ripristino di emergenza](tutorial-dr-drill-azure.md)

## <a name="prepare-for-failover-and-failback"></a>Preparazione per il failover e failback

Verificare che non sono disponibili snapshot della macchina virtuale e che la macchina virtuale locale è stata disattivato durante una nuova protezione. in modo da garantire la coerenza dei dati durante la replica. Non accendere la macchina virtuale al termine della riprotezione. 

L'operazione di failover e failback comprende quattro fasi:

1. **Failover in Azure**: eseguire il failover dei computer dal sito locale in Azure.
2. **Macchine virtuali di Azure riproteggere**: ricrea la protezione di macchine virtuali di Azure, in modo che inizino a replicare le macchine virtuali Hyper-V locale.
3. **Eseguire il failover in locale**: eseguire un failover da Azure al sito locale, quando è disponibile.
4. **Riprotezione locale macchine virtuali**: ricrea la protezione dopo che dati ha eseguito nuovamente, le macchine virtuali in locale per avviare la replica Azure.

## <a name="verify-vm-properties"></a>Verificare le proprietà della macchina virtuale

Verificare le proprietà della macchina virtuale e assicurarsi che sia conforme ai [requisiti di Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. In **elementi protetti**, fare clic su **elementi replicati** >< VM-name >.

2. Nel **elemento replicato** riquadro, esaminare le informazioni della macchina virtuale, lo stato di integrità e i punti di ripristino disponibile più recenti. Fare clic su **Proprietà** per visualizzare altri dettagli.
     - In **di calcolo e rete**, è possibile modificare le impostazioni della macchina virtuale e rete impostazioni, tra cui la sottorete/rete in cui la macchina virtuale di Azure. Dischi gestiti non sono supportati per il failback da Azure per Hyper-V.
   sarà posizionato dopo il failover e l'indirizzo IP che verrà assegnato a esso.
    - In **Dischi** è possibile vedere le informazioni sul sistema operativo e sui dischi dati della VM.

## <a name="fail-over-to-azure"></a>Failover in Azure

1. In **Impostazioni** > **Elementi replicati** fare clic sulla macchina virtuale > **Failover**.
2. In **Failover** selezionare il **più recente** punto di ripristino. 
3. Selezionare **spegnere la macchina prima di iniziare il failover**. Il ripristino del sito tenta di eseguire un arresto delle macchine virtuali di origine prima di attivare il failover. Il failover continua anche se l'arresto ha esito negativo. Nella pagina **Processi** è possibile seguire lo stato del failover.
4. Dopo aver verificato il clic failover **Commit**. In questo modo tutti i punti di ripristino disponibili verranno eliminati.

> [!WARNING]
> **Non annullare un failover in corso**: prima dell'avvio del failover, la replica della macchina virtuale viene arrestata. Se si Annulla in corso, viene arrestato il failover, ma non eseguire nuovamente la replica della macchina virtuale.

## <a name="reprotect-azure-vms"></a>Riproteggere le macchine virtuali di Azure

1. Nel **AzureVMVault** > **gli elementi replicati**, la macchina virtuale che è stata eseguita il failover e scegliere **riproteggere**.
2. Verificare che la direzione di protezione è impostata su **Azure a locale**.
3. La macchina virtuale locale è stata disattivato durante una nuova protezione, al fine di garantire la coerenza dei dati. Non abilitarlo dopo il completamento di una nuova protezione.
4. Dopo il processo di una nuova protezione, la macchina virtuale inizia a replicare da Azure al sito locale.



## <a name="fail-over-from-azure-and-reprotect-the-on-premises-vm"></a>Eseguire il failover da Azure e riproteggere la macchina virtuale locale

Eseguire il failover da Azure nel sito locale e avviare la replica delle macchine virtuali dal sito locale in Azure.

1. In **impostazioni** > **gli elementi replicati**, scegliere la macchina virtuale > **Failover pianificato**.
2. In **Conferma failover pianificato** verificare la direzione di failover (da Azure) e selezionare il percorso di origine e di destinazione.
3. Selezionare **sincronizzare i dati prima del failover (Sincronizza solo modifiche differenziali)**. Questa opzione consente di ridurre i tempi di inattività di macchina virtuale esegue la sincronizzazione senza arrestare la macchina virtuale.
4. Avviare il failover. Nella scheda **Processi** è possibile monitorare l’avanzamento del failover.
5. Dopo la data iniziale viene eseguita la sincronizzazione e si è pronti per arrestare le macchine virtuali di Azure fare clic su **processi** > pianificati-failover-job-name > **completare il Failover**. La macchina virtuale di Azure viene arrestata, le modifiche più recenti vengono trasferite in locale e la macchina virtuale locale viene avviata.
6. Accedere alla macchina virtuale locale per verificare che sia disponibile come previsto.
7. La macchina virtuale locale è ora un **Commit in sospeso** stato. Fare clic su **Commit**. Le macchine virtuali di Azure e i relativi dischi verranno eliminati e la macchina virtuale locale verrà preparata per la replica inversa.
Per avviare la replica della macchina virtuale locale in Azure, abilitare **Replica inversa**. In questo modo viene attivata la replica delle modifiche delta che si sono verificati dopo la macchina virtuale di Azure è stata disattivata.  
