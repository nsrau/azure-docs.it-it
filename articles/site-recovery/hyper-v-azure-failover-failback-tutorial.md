---
title: Failover e failback di macchine virtuali Hyper-V durante il ripristino di emergenza in Azure con Azure Site Recovery | Microsoft Docs
description: Informazioni su come eseguire il failover e failback di macchine virtuali Hyper-V durante il ripristino di emergenza in Azure con il servizio Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 53b2c2945062ef348104e24a352895a14eed1a04
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314842"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-azure"></a>Failover e failback di macchine virtuali Hyper-V replicate in Azure

Questa esercitazione descrive come eseguire il failover di una macchina virtuale Hyper-V in Azure. Dopo il failover è possibile eseguire il failback nel sito locale quando disponibile. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Controllare le proprietà della macchina virtuale Hyper-V per verificare la conformità ai requisiti di Azure
> * Eseguire un failover in Azure
> * Eseguire il failback da Azure nel sito locale
> * Eseguire la replica inversa delle macchine virtuali locali per avviare nuovamente la replica in Azure

Questa è la quinta esercitazione di una serie. Si presuppone che siano già state completate le attività delle esercitazioni precedenti.    

1. [Preparare Azure](tutorial-prepare-azure.md)
2. [Preparare Hyper-V in locale](tutorial-prepare-on-premises-hyper-v.md)
3. Configurare il ripristino di emergenza per [macchine virtuali Hyper-V](tutorial-hyper-v-to-azure.md) o per [macchine virtuali Hyper-V gestite in cloud System Center VMM](tutorial-hyper-v-vmm-to-azure.md)
4. [Eseguire un'esercitazione sul ripristino di emergenza](tutorial-dr-drill-azure.md)

## <a name="prepare-for-failover-and-failback"></a>Prepararsi per il failover e il failback

Assicurarsi che non siano presenti snapshot nella macchina virtuale e che la macchina virtuale locale sia spenta durante il failback. In questo modo, si garantisce la coerenza dei dati durante la replica. Non accendere la macchina virtuale locale durante il failback. 

Le operazioni di failover e failback includono tre fasi:

1. **Failover in Azure**: failover delle macchine virtuali Hyper-V dal sito locale ad Azure.
2. **Failback nel sito locale**: failover delle macchine virtuali di Azure nel sito locale, quando questo è disponibile. Inizia la sincronizzazione dei dati da Azure in locale e al completamento, vengono visualizzate le macchine virtuali in locale.  
3. **Replica inversa delle macchine virtuali locali**: dopo il failback nel sito locale, replica inversa delle macchine virtuali locali per avviarne la replica in Azure.

## <a name="verify-vm-properties"></a>Verificare le proprietà della macchina virtuale

Prima del failover, verificare le proprietà della macchine virtuali e assicurarsi che soddisfino i [requisiti di Azure](hyper-v-azure-support-matrix.md#replicated-vms).

In **Elementi protetti** fare clic su **Elementi replicati** > macchina virtuale.

1. Nel riquadro **Elemento replicato** è possibile vedere un riepilogo relativo a informazioni sulla macchina virtuale, stato integrità e ultimi punti di ripristino disponibili. Fare clic su **Proprietà** per visualizzare altri dettagli.

1. In **Calcolo e rete** è possibile modificare il nome Azure, il gruppo di risorse, le dimensioni di destinazione, il [set di disponibilità](../virtual-machines/windows/tutorial-availability-sets.md) e le impostazioni del disco gestito.

1. È possibile visualizzare e modificare le impostazioni di rete, tra cui la rete/subnet in cui si troverà la macchina virtuale di Azure dopo il failover e l'indirizzo IP che le verrà assegnato.

1. In **Dischi** è possibile vedere le informazioni sul sistema operativo e sui dischi dati della VM.

## <a name="failover-to-azure"></a>Failover in Azure

1. In **Impostazioni** > **Elementi replicati**, fare clic sulla macchina virtuale > **Failover**.
2. In **Failover** selezionare il punto di ripristino **più recente**. 
3. Selezionare **Arrestare la macchina prima di iniziare il failover**. Site Recovery proverà ad arrestare la macchina virtuale di origine prima di attivare il failover. Il failover continua anche se l'arresto ha esito negativo. Nella pagina **Processi** è possibile seguire lo stato del failover.
4. Dopo la verifica del failover, fare clic su **Esegui commit**. Vengono eliminati tutti i punti di ripristino disponibili.

> [!WARNING]
> **Non annullare un failover in corso**: Se si annulla un failover in corso, il failover viene arrestato ma non viene eseguita di nuovo la replica della macchina virtuale.

## <a name="failback-azure-vm-to-on-premises-and-reverse-replicate-the-on-premises-vm"></a>Eseguire il failback della macchina virtuale di Azure nel sito locale ed eseguire la replica inversa delle macchine virtuali locali

Di base, l'operazione di failback è un failover da Azure al sito locale e nella replica inversa avvia di nuovo la replica delle macchine virtuali dal sito locale ad Azure.

1. In **Impostazioni** > **Elementi replicati** fare clic sulla macchina virtuale > **Failover pianificato**.
2. In **Conferma failover pianificato** verificare la direzione di failover (da Azure) e selezionare il percorso di origine e di destinazione.
3. Selezionare **Sincronizza i dati prima del failover (sincronizza solo modifiche differenziali)**. Questa opzione consente di ridurre al minimo i tempi di inattività della macchina virtuale poiché esegue la sincronizzazione senza arrestarla.
4. Avviare il failover. Nella scheda **Processi** è possibile monitorare l’avanzamento del failover.
5. Dopo la sincronizzazione iniziale dei dati, quando si è pronti ad arrestare le macchine virtuali di Azure, fare clic su **Processi** > nome del processo di failover pianificato > **Completa failover**. La macchina virtuale di Azure viene arrestata, le modifiche più recenti vengono trasferite in locale e viene avviata la macchina virtuale locale.
6. Accedere alla macchina virtuale locale per verificare che sia disponibile come previsto.
7. La macchina virtuale locale è ora in uno stato di **commit in sospeso**. Fare clic su **Esegui commit**. Le macchine virtuali di Azure e i rispettivi dischi vengono eliminati e la macchina virtuale locale viene preparata per la replica inversa.
Per avviare la replica della macchina virtuale locale in Azure, abilitare **Replica inversa**. Viene attivata la replica delle modifiche delta che si sono verificate dopo lo spegnimento della macchina virtuale di Azure.  
