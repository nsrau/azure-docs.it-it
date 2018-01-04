---
title: Eseguire un'analisi di ripristino di emergenza per computer locali in Azure tramite Azure Site Recovery | Microsoft Docs
description: Informazioni sull'esecuzione di un'analisi di ripristino di emergenza locale in Azure, con Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/31/2017
ms.author: raynew
ms.openlocfilehash: f7dc5e2df95a64685a8b70d25e839c371d4fc2de
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>Eseguire un'esercitazione sul ripristino di emergenza in Azure

In questa esercitazione viene illustrato come eseguire un'analisi di ripristino di emergenza per un computer locale in Azure, utilizzando un failover di test. Un'analisi convalida la strategia di replica senza perdita di dati. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare una rete isolata per il failover di test
> * Preparare la connessione alla macchina virtuale di Azure dopo il failover
> * Eseguire un failover di test per un singolo computer

Questa è la quarta esercitazione di una serie. In questa esercitazione si presuppone che siano già state completate le attività delle esercitazioni precedenti.

1. [Preparare Azure](tutorial-prepare-azure.md)
2. [Preparare istanze di VMware locali](tutorial-prepare-on-premises-vmware.md)
3. [Configurare il ripristino di emergenza](tutorial-vmware-to-azure.md)

## <a name="verify-vm-properties"></a>Verificare le proprietà della macchina virtuale

Prima di eseguire un failover di test, verificare le proprietà della macchina virtuale e assicurarsi che sia conforme ai [requisiti di Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. In **Elementi protetti** fare clic su **Elementi replicati** > macchina virtuale.
2. Nel riquadro **Elemento replicato** è possibile vedere un riepilogo relativo a informazioni sulla macchina virtuale, stato integrità e ultimi punti di ripristino disponibili. Fare clic su **Proprietà** per visualizzare altri dettagli.
3. In **Calcolo e rete** è possibile modificare il nome Azure, il gruppo di risorse, le dimensioni di destinazione, il [set di disponibilità](../virtual-machines/windows/tutorial-availability-sets.md) e le impostazioni del disco gestito.
   
      >[!NOTE]
      Il failback di macchine virtuali di Azure con dischi gestiti macchine Hyper-V locale non è attualmente supportato. Deve utilizzare solo l'opzione di dischi gestiti per il failover se si prevede di eseguire la migrazione di macchine virtuali locali in Azure, senza li failback.
   
4. È possibile visualizzare e modificare le impostazioni di rete, tra cui la rete/subnet in cui si troverà la macchina virtuale di Azure dopo il failover e l'indirizzo IP che le verrà assegnato.
5. In **Dischi** è possibile vedere le informazioni sul sistema operativo e sui dischi dati della VM.

## <a name="run-a-test-failover-for-a-single-vm"></a>Eseguire un failover di test per una singola macchina virtuale

Quando si esegue un failover di test, si verifica quanto segue:

1. Viene eseguito un controllo dei prerequisiti per verificare che tutte le condizioni necessarie per il failover siano in atto.
2. Il failover elabora i dati, in modo che sia possibile creare una macchina virtuale di Azure. Selezionando il punto di recupero più recente, viene creato un punto di recupero dai dati.
3. Una macchina virtuale di Azure viene creata usando i dati elaborati nel passaggio precedente.

Eseguire il failover di test come descritto di seguito:

1. In **Impostazioni** > **Elementi replicati** fare clic sulla macchina virtuale > **+Failover di test**.
2. Selezionare il **più recenti elaborati** punto di ripristino per questa esercitazione. Questo viene eseguito il failover la macchina virtuale per il più recente punto nel tempo. Viene visualizzato il timestamp. Con questa opzione, non viene impiegato alcun tempo di elaborazione dati, pertanto viene fornito un RTO (Recovery Time Objective) basso.
3. In **Failover di test** selezionare la rete di Azure di destinazione a cui vengono connesse le macchine virtuali di Azure dopo il failover.
4. Fare clic su **OK** per iniziare il failover. È possibile tenere traccia dell'avanzamento facendo clic sulla macchina virtuale per visualizzarne le proprietà. In alternativa, è possibile fare clic sul processo **Failover di test** nel nome dell'insieme di credenziali, quindi su **Impostazioni** > **Processi** >
   **Site Recovery jobs** (Processi di Site Recovery).
5. Al termine del failover, la macchina virtuale di Azure di replica viene visualizzata nel portale di Azure in **Macchine virtuali**. Verificare che la macchina virtuale sia delle dimensioni appropriate, che sia connessa alla rete corretta e che sia in esecuzione.
6. Sarà ora possibile connettersi alla macchina virtuale replicata in Azure.
7. Per eliminare le macchine virtuali di Azure creato durante il failover di test, fare clic su **il failover di test di pulizia** nella macchina virtuale. Fare clic su **Note** per registrare e salvare eventuali osservazioni associate al failover di test.

In alcuni scenari il failover richiede un'altra elaborazione il cui completamento richiede da 8 a 10 minuti. L'esecuzione del failover di test potrebbe richiedere più tempo per computer Linux VMware, macchine virtuali VMware per cui non è abilitato il servizio DHCP e macchine virtuali VMware che non hanno i driver di avvio seguenti: storvsc, vmbus, storflt, intelide, atapi.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Eseguire failover e failback per macchine virtuali VMware locali](tutorial-vmware-to-azure-failover-failback.md).
