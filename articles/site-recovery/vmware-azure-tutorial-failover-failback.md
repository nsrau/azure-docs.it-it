---
title: Failover e failback di macchine virtuali VMware e server fisici durante il ripristino di emergenza in Azure con Site Recovery | Microsoft Docs
description: Informazioni su come eseguire il failover di macchine virtuali VMware e server fisici in Azure e il failback nel sito locale durante il ripristino di emergenza in Azure con Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 9206e751fadab7a09c696fbe262aecdde002ae74
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797666"
---
# <a name="fail-over-and-fail-back-vmware-vms"></a>Failover e failback di VM VMware

Questo articolo descrive come eseguire il failover di una VM VMware locale al servizio [Azure Site Recovery](site-recovery-overview.md) di Azure. 

Questa è la quinta esercitazione di una serie che illustra come configurare il ripristino di emergenza in Azure per macchine locali.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Controllare le proprietà della macchina virtuale VMware per verificare la conformità ai requisiti di Azure
> * Eseguire un failover in Azure


> [!NOTE]
> Le esercitazioni illustrano il percorso di distribuzione più semplice per uno scenario. Quando possibile, vengono usate le opzioni predefinite e non sono riportati tutti i percorsi e le impostazioni possibili. Per informazioni dettagliate sul failover, [vedere questo articolo](site-recovery-failover.md).

## <a name="before-you-start"></a>Prima di iniziare
Completare le esercitazioni precedenti:

1. Assicurarsi di aver [configurato Azure](tutorial-prepare-azure.md) per il ripristino di emergenza locale di VM VMware, VM Hyper-V e computer fisici in Azure.
2. Preparare l'ambiente [VMware](vmware-azure-tutorial-prepare-on-premises.md) o [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) per il ripristino di emergenza. Se si sta configurando il ripristino di emergenza per i server fisici, esaminare la [matrice di supporto](vmware-physical-secondary-support-matrix.md).
3. Configurare il ripristino di emergenza per [VM VMware](vmware-azure-tutorial.md), [VM Hyper-V](hyper-v-azure-tutorial.md) o [computer fisici](physical-azure-disaster-recovery.md).
4. Eseguire un'[esercitazione di ripristino di emergenza](tutorial-dr-drill-azure.md) per verificare che tutto funzioni come previsto.


## <a name="failover-and-failback"></a>Failover e failback

Per il failover e il failback sono previste quattro fasi:

1. **Failover in Azure**: Quando il sito primario locale non funziona, eseguire il failover delle macchine in Azure. Dopo il failover, le macchine virtuali di Azure vengono create da dati replicati.
2. **Riprotezione delle macchine virtuali di Azure**: In Azure proteggere di nuovo le macchine virtuali di Azure in modo da avviarne la replica nelle macchine virtuali VMware locali. La macchina virtuale locale viene spenta durante la riprotezione, per garantire la coerenza dei dati.
3. **Failover nel sito locale**: Quando il sito locale è in funzione, eseguire un failover per il failback da Azure.
4. **Riprotezione delle macchine virtuali locali**: Dopo aver eseguito il failback dei dati, proteggere di nuovo le macchine virtuali locali in cui è stato eseguito il failback, in modo che inizino la replica in Azure.

## <a name="verify-vm-properties"></a>Verificare le proprietà della macchina virtuale

Prima di eseguire un failover, verificare le proprietà della VM e assicurarsi che le VM siano conformi ai [requisiti di Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

Verificare le proprietà come segue:

1. In **Elementi protetti** fare clic su **Elementi replicati** > macchina virtuale.

2. Nel riquadro **Elemento replicato** è possibile vedere un riepilogo relativo a informazioni sulla macchina virtuale, stato integrità e ultimi punti di ripristino disponibili. Fare clic su **Proprietà** per visualizzare altri dettagli.

3. In **Calcolo e rete** è possibile modificare le impostazioni relative a nome, gruppo di risorse, dimensioni di destinazione, [set di disponibilità](../virtual-machines/windows/tutorial-availability-sets.md) e dischi gestiti di Azure.

4. È possibile visualizzare e modificare le impostazioni di rete, tra cui la rete/subnet in cui si troverà la macchina virtuale di Azure dopo il failover e l'indirizzo IP che le verrà assegnato.

5. In **Dischi** è possibile vedere le informazioni sul sistema operativo e sui dischi dati della VM.

## <a name="run-a-failover-to-azure"></a>Eseguire un failover in Azure

1. In **Impostazioni** > **Elementi replicati**, fare clic sulla macchina virtuale > **Failover**.
2. In **Failover** selezionare un **Punto di ripristino** in cui eseguire il failover. È possibile usare una delle opzioni seguenti.
   - **Più recente**: questa opzione elabora prima tutti i dati inviati a Site Recovery. Offre il valore RPO (Recovery Point Objective) più basso perché la macchina virtuale di Azure creata dopo il failover contiene tutti i dati che sono stati replicati in Site Recovery all'attivazione del failover.
   - **Elaborato più recente**: questa opzione consente di eseguire il failover della macchina virtuale nel punto di ripristino più recente elaborato da Site Recovery. Offre un RTO (Recovery Time Objective) basso poiché non viene impiegato tempo per elaborare dati non elaborati.
   - **Coerente con l'app più recente**: questa opzione esegue il failover della macchina virtuale nel punto di ripristino coerente con l'app più recente elaborato da Site Recovery.
   - **Personalizzato**: specificare un punto di ripristino.

3. Selezionare **Arrestare la macchina virtuale prima di iniziare il failover** per provare ad arrestare le macchine virtuali di origine prima di attivare il failover. Il failover continua anche se l'arresto ha esito negativo. Nella pagina **Processi** è possibile seguire lo stato del failover.

In alcuni scenari il failover richiede un'altra elaborazione il cui completamento richiede da 8 a 10 minuti. Si possono notare tempi di failover di test più lunghi per:
- VM VMware che eseguono una versione del servizio Mobility precedente alla 9.8
- Server fisici
- Macchine virtuali Linux VMware
- VM Hyper-V protette come i server fisici
- VM VMware che non hanno il servizio DHCP abilitato
- VM VMware che non hanno i driver di avvio seguenti: storvsc, vmbus, storflt, intelide, atapi.

> [!WARNING]
> **Non annullare un failover in corso**: Prima dell'avvio del failover, la replica della macchina virtuale viene arrestata. Se si annulla un failover in corso, il failover viene arrestato ma non viene eseguita di nuovo la replica della macchina virtuale.

## <a name="connect-to-failed-over-vm"></a>Connettersi a macchine virtuali con failover

1. Per connettersi alle macchine virtuali di Azure usando RDP/SSH dopo il failover, [verificare questi requisiti](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
2. Dopo il failover, passare alla macchina virtuale e convalidarla [connettendosi](../virtual-machines/windows/connect-logon.md) a essa.
3. Usare **Modificare il punto di ripristino** se si vuole usare un punto di ripristino diverso dopo il failover. Dopo aver eseguito il commit del failover nel passaggio successivo, questa opzione non sarà più disponibile.
4. Dopo la convalida, fare clic su **Commit** per finalizzare il punto di ripristino della VM dopo il failover.
5. Dopo il commit, tutti gli altri punti di ripristino disponibili vengono eliminati. Il failover è così completato.

>[!TIP]
> Se si verificano problemi di connettività dopo il failover, seguire questa guida alla [risoluzione dei problemi](site-recovery-failover-to-azure-troubleshoot.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo il failover, proteggere di nuovo le macchine virtuali di Azure verso l'ambiente locale. Quando le VM sono di nuovo protette e viene eseguita la replica nel sito locale, eseguire il failback da Azure quando si è pronti.

> [!div class="nextstepaction"]
> [Riproteggere le macchine virtuali di Azure](vmware-azure-reprotect.md)
> [Failback da Azure](vmware-azure-failback.md) 
