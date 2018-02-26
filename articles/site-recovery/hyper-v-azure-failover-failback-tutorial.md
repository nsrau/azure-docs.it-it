---
title: Failover e failback di macchine virtuali Hyper-V replicate in Azure con Site Recovery | Microsoft Docs
description: Informazioni su come eseguire il failover di macchine virtuali Hyper-V in Azure e il failback nel sito locale con Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 02/14/2018
ms.author: raynew
ms.openlocfilehash: 7d2d99c2429a461307cbb9a276eb3b62d13718d2
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2018
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-azure"></a>Failover e failback di macchine virtuali Hyper-V replicate in Azure

Questa esercitazione descrive come eseguire il failover di una macchina virtuale Hyper-V in Azure. Dopo il failover è possibile eseguire il failback nel sito locale quando disponibile. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Controllare le proprietà della macchina virtuale Hyper-V per verificare la conformità ai requisiti di Azure
> * Eseguire un failover in Azure
> * Riproteggere le macchine virtuali di Azure nel sito locale
> * Eseguire il failback da Azure nel sito locale
> * Riproteggere le macchine virtuali locali per avviare nuovamente la replica in Azure

Questa è la quinta esercitazione di una serie. In questa esercitazione si presuppone che siano già state completate le attività delle esercitazioni precedenti.

1. [Preparare Azure](tutorial-prepare-azure.md)
2. [Preparare istanze di VMware locali](tutorial-prepare-on-premises-hyper-v.md)
3. Configurare il ripristino di emergenza per [macchine virtuali Hyper-V](tutorial-hyper-v-to-azure.md) o per [macchine virtuali Hyper-V gestite in cloud System Center VMM](tutorial-hyper-v-vmm-to-azure.md)
4. [Eseguire un'esercitazione sul ripristino di emergenza](tutorial-dr-drill-azure.md)

## <a name="prepare-for-failover-and-failback"></a>Prepararsi per il failover e il failback

Assicurarsi che non siano presenti snapshot nella macchina virtuale e che la macchina virtuale locale sia spenta durante la riprotezione. in modo da garantire la coerenza dei dati durante la replica. Non accendere la macchina virtuale al termine della riprotezione. 

L'operazione di failover e failback comprende quattro fasi:

1. **Failover in Azure**: eseguire il failover dei computer dal sito locale in Azure.
2. **Riprotezione delle macchine virtuali di Azure**: riproteggere le macchine virtuali di Azure in modo da avviarne la replica nelle macchine virtuali Hyper-V locali.
3. **Failover in locale**: eseguire un failover da Azure nel sito locale, quando disponibile.
4. **Riprotezione delle macchine virtuali locali**: dopo il failback dei dati, riproteggere le macchine virtuali locali per da avviarne la replica in Azure.

## <a name="verify-vm-properties"></a>Verificare le proprietà della macchina virtuale

Verificare le proprietà della macchina virtuale e assicurarsi che sia conforme ai [requisiti di Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. In **Elementi protetti** fare clic su **Elementi replicati** ><nome macchina virtuale>.

2. Nel riquadro **Elemento replicato** esaminare le informazioni sulla macchina virtuale, lo stato integrità e gli ultimi punti di ripristino disponibili. Fare clic su **Proprietà** per visualizzare altri dettagli.
     - In **Calcolo e rete** è possibile modificare le impostazioni della macchina virtuale e di rete, inclusa la rete/subnet in cui la macchina virtuale di Azure. I dischi gestiti non sono supportati per il failback da Azure in Hyper-V.
   sarà posizionata dopo il failover e l'indirizzo IP che le verrà assegnato.
    - In **Dischi** è possibile vedere le informazioni sul sistema operativo e sui dischi dati della VM.

## <a name="fail-over-to-azure"></a>Failover in Azure

1. In **Impostazioni** > **Elementi replicati** fare clic sulla macchina virtuale > **Failover**.
2. In **Failover** selezionare l'**ultimo** punto di ripristino. 
3. Selezionare **Arrestare la macchina prima di iniziare il failover**. Site Recovery proverà ad arrestare la macchina virtuale di origine prima di attivare il failover. Il failover continua anche se l'arresto ha esito negativo. Nella pagina **Processi** è possibile seguire lo stato del failover.
4. Dopo la verifica del failover, fare clic su **Esegui commit**. In questo modo tutti i punti di ripristino disponibili verranno eliminati.

> [!WARNING]
> **Non annullare un failover in corso**: prima dell'avvio del failover, la replica della macchina virtuale viene arrestata. Se si annulla un failover in corso, il failover viene arrestato ma non viene eseguita di nuovo la replica della macchina virtuale.

## <a name="reprotect-azure-vms"></a>Riproteggere le macchine virtuali di Azure

1. In **AzureVMVault** > **Elementi replicati** fare clic con il pulsante destro del mouse sulla macchina virtuale di cui è stato effettuato il failover e quindi selezionare **Riproteggi**.
2. Verificare che la direzione di protezione sia impostata su **Da Azure a locale**.
3. La macchina virtuale locale viene spenta durante la riprotezione, per garantire la coerenza dei dati. Non accendere la macchina virtuale al termine della riprotezione.
4. Dopo il processo di riprotezione, la macchina virtuale inizia la replica da Azure al sito locale.



## <a name="fail-over-from-azure-and-reprotect-the-on-premises-vm"></a>Eseguire il failover da Azure e riproteggere la macchina virtuale locale

Eseguire il failover da Azure nel sito locale e avviare la replica delle macchine virtuali dal sito locale in Azure.

1. In **Impostazioni** > **Elementi replicati** fare clic sulla macchina virtuale > **Failover pianificato**.
2. In **Conferma failover pianificato** verificare la direzione di failover (da Azure) e selezionare il percorso di origine e di destinazione.
3. Selezionare **Sincronizza i dati prima del failover (sincronizza solo modifiche differenziali)**. Questa opzione consente di ridurre al minimo i tempi di inattività della macchina virtuale poiché esegue la sincronizzazione senza arrestarla.
4. Avviare il failover. Nella scheda **Processi** è possibile monitorare l’avanzamento del failover.
5. Dopo la sincronizzazione iniziale dei dati, quando si è pronti ad arrestare le macchine virtuali di Azure, fare clic su **Processi** > nome del processo di failover pianificato > **Completa failover**. La macchina virtuale di Azure viene arrestata, le modifiche più recenti vengono trasferite in locale e la macchina virtuale locale viene avviata.
6. Accedere alla macchina virtuale locale per verificare che sia disponibile come previsto.
7. La macchina virtuale locale è ora in uno stato di **commit in sospeso**. Fare clic su **Esegui commit**. Le macchine virtuali di Azure e i relativi dischi verranno eliminati e la macchina virtuale locale verrà preparata per la replica inversa.
Per avviare la replica della macchina virtuale locale in Azure, abilitare **Replica inversa**. In questo modo viene attivata la replica delle modifiche delta che si sono verificate dopo lo spegnimento della macchina virtuale di Azure.  
