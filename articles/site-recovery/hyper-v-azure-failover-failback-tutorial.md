---
title: Configurare il failover delle macchine virtuali Hyper-V in Azure con Azure Site Recovery
description: Informazioni su come configurare il failover delle macchine virtuali Hyper-V in Azure con Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/16/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b46a2ea12a697afde8223cc3595365c1286512c7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86132465"
---
# <a name="fail-over-hyper-v-vms-to-azure"></a>Effettuare il failover delle macchine virtuali Hyper-V in Azure

Questa esercitazione descrive come effettuare il failover delle macchine virtuali Hyper-V in Azure con [Azure Site Recovery](site-recovery-overview.md). Dopo il failover è possibile eseguire il failback nel sito locale quando disponibile. In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Controllare le proprietà delle macchine virtuali Hyper-V per verificare la conformità ai requisiti di Azure.
> * Effettuare il failover di macchine virtuali specifiche in Azure.


Questa è la quinta esercitazione di una serie. Si presuppone che siano già state completate le attività delle esercitazioni precedenti.    

1. [Preparare Azure](tutorial-prepare-azure.md)
2. [Preparare Hyper-V in locale](./hyper-v-prepare-on-premises-tutorial.md)
3. Configurare il ripristino di emergenza per [macchine virtuali Hyper-V](./hyper-v-azure-tutorial.md) o per [macchine virtuali Hyper-V gestite in cloud System Center VMM](./hyper-v-vmm-azure-tutorial.md)
4. [Eseguire un'esercitazione sul ripristino di emergenza](tutorial-dr-drill-azure.md)

[Informazioni](failover-failback-overview.md#types-of-failover) sui vari tipi di failover. Per effettuare il failover di più macchine virtuali in un piano di ripristino, vedere [questo articolo](site-recovery-failover.md).

## <a name="prepare-for-failover"></a>Preparare il failover 
Assicurarsi che non siano presenti snapshot nella macchina virtuale e che la macchina virtuale locale sia spenta durante il failback. In questo modo, si garantisce la coerenza dei dati durante la replica. Non accendere la macchina virtuale locale durante il failback. 

Le operazioni di failover e failback includono tre fasi:

1. **Failover in Azure**: eseguire il failover delle macchine virtuali Hyper-V dal sito locale ad Azure.
2. **Failover nel sito locale**: eseguire il failover delle macchine virtuali di Azure nel sito locale, quando questo è disponibile. Inizia la sincronizzazione dei dati da Azure in locale e al completamento, vengono visualizzate le macchine virtuali in locale.  
3. **Replica inversa delle macchine virtuali locali**: dopo il failback dei dati in locale, eseguire la replica inversa delle macchine virtuali locali per avviarne la replica in Azure.

## <a name="verify-vm-properties"></a>Verificare le proprietà della macchina virtuale

Prima del failover, verificare le proprietà della macchine virtuali e assicurarsi che soddisfino i [requisiti di Azure](hyper-v-azure-support-matrix.md#replicated-vms).

In **Elementi protetti** fare clic su **Elementi replicati** > macchina virtuale.

1. Nel riquadro **Elemento replicato** è possibile vedere un riepilogo relativo a informazioni sulla macchina virtuale, stato integrità e ultimi punti di ripristino disponibili. Fare clic su **Proprietà** per visualizzare altri dettagli.

1. In **Calcolo e rete** è possibile modificare il nome Azure, il gruppo di risorse, le dimensioni di destinazione, il [set di disponibilità](../virtual-machines/windows/tutorial-availability-sets.md) e le impostazioni del disco gestito.

1. È possibile visualizzare e modificare le impostazioni di rete, tra cui la rete/subnet in cui si troverà la macchina virtuale di Azure dopo il failover e l'indirizzo IP che le verrà assegnato.

1. In **Dischi** è possibile vedere le informazioni sul sistema operativo e sui dischi dati della VM.

## <a name="fail-over-to-azure"></a>Failover in Azure

1. In **Impostazioni** > **Elementi replicati**, fare clic sulla macchina virtuale > **Failover**.
2. In **Failover** selezionare il punto di ripristino **più recente**. 
3. Selezionare **Arrestare la macchina prima di iniziare il failover**. Site Recovery proverà ad arrestare la macchina virtuale di origine prima di attivare il failover. Il failover continua anche se l'arresto ha esito negativo. Nella pagina **Processi** è possibile seguire lo stato del failover.
4. Dopo la verifica del failover, fare clic su **Esegui commit**. Vengono eliminati tutti i punti di ripristino disponibili.

> [!WARNING]
> **Non annullare un failover in corso**: se si annulla l'operazione in corso, il failover viene arrestato, ma la macchina virtuale non viene più replicata.

## <a name="connect-to-failed-over-vm"></a>Connettersi alla VM sottoposta a failover

1. Se si desidera connettersi alle macchine virtuali di Azure dopo il failover tramite Remote Desktop Protocol (RDP) e Secure Shell (SSH), [verificare che siano stati soddisfatti i requisiti](failover-failback-overview.md#connect-to-azure-after-failover).
2. Dopo il failover, passare alla macchina virtuale e convalidarla [connettendosi](../virtual-machines/windows/connect-logon.md) a essa.
3. Usare **Modificare il punto di ripristino** se si vuole usare un punto di ripristino diverso dopo il failover. Dopo aver eseguito il commit del failover nel passaggio successivo, questa opzione non sarà più disponibile.
4. Dopo la convalida, selezionare **Commit** per finalizzare il punto di ripristino della VM dopo il failover.
5. Dopo il commit, tutti gli altri punti di ripristino disponibili vengono eliminati. Questo passaggio completa il failover.

>[!TIP]
> Se si verificano problemi di connettività dopo il failover, seguire la guida alla [risoluzione dei problemi](site-recovery-failover-to-azure-troubleshoot.md).


## <a name="next-steps"></a>Passaggi successivi

Dopo il failover, riproteggere le macchine virtuali di Azure per consentirne la replica da Azure all'ambiente locale. Quando le VM sono di nuovo protette e viene eseguita la replica nel sito locale, eseguire il failback da Azure quando si è pronti.
