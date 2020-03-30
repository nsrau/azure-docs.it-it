---
title: Eseguire il failback delle macchine virtuali Hyper-V da Azure con Azure Site Recovery
description: Come eseguire il failback delle macchine virtuali Hyper-V a un sito locale da Azure con Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 09/12/2019
ms.author: rajanaki
ms.openlocfilehash: 4b005ae308576db6fd26fcf079161430b266ec3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281782"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>Eseguire il failback per le macchine virtuali Hyper-V

In questo articolo viene descritto come eseguire il failback delle macchine virtuali di Azure create dopo il failover delle macchine virtuali Hyper-V da un sito locale ad Azure, con [Azure Site Recovery.](site-recovery-overview.md)

- Si esegue il failback delle macchine virtuali Hyper-V da Azure eseguendo un failover pianificato da Azure al sito locale. Se la direzione del failover è da Azure a locale, viene considerata un failback.
- Poiché Azure è un ambiente a disponibilità elevata e le macchine virtuali sono sempre disponibili, il failback da Azure è un'attività pianificata. È possibile pianificare un tempo di inattività di piccole dimensioni in modo che i carichi di lavoro possano iniziare nuovamente a eseguire in locale. 
- Il failback pianificato disattiva le macchine virtuali in Azure e scarica le modifiche più recenti. Non è prevista alcuna perdita di dati.

## <a name="before-you-start"></a>Prima di iniziare

1. [Esaminare i tipi di failback](failover-failback-overview.md#hyper-v-reprotectionfailback) che è possibile utilizzare, ovvero il ripristino della posizione originale e il ripristino della posizione alternativa.
2. Verificare che le macchine virtuali di Azure utilizzino un account di archiviazione e non i dischi gestiti. Il failback delle macchine virtuali Hyper-V replicate tramite dischi gestiti non è supportato.
3. Verificare che l'host Hyper-V locale (o il server VMM di System Center se si usa Site Recovery) sia in esecuzione e connesso ad Azure. 
4. Assicurarsi che il failover e il commit siano completi per le macchine virtuali. Non è necessario configurare componenti di Site Recovery specifici per il failback delle macchine virtuali Hyper-V da Azure.You don't need to set up any specific Site Recovery components for failback of Hyper-V VMs from Azure.
5. Il tempo necessario per completare la sincronizzazione dei dati e avviare la macchina virtuale locale dipenderà da una serie di fattori. Per velocizzare il download dei dati, è possibile configurare l'agente di Servizi di ripristino Microsoft per l'utilizzo di più thread per parallelizzare il download. [Scopri di più](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage).


## <a name="fail-back-to-the-original-location"></a>Failback nella posizione originaria

Per eseguire il failback delle macchine virtuali Hyper-V in Azure alla macchina virtuale locale originale, eseguire un failover pianificato da Azure al sito locale come segue:To fail back Hyper-V VMs in Azure to the original on-premises VM, run a planned failover from Azure to the on-premises site as follows:

1. Nell'insieme di credenziali > **elementi replicati**selezionare la macchina virtuale. Fare clic con il pulsante destro del mouse sulla macchina virtuale > **failover pianificato**. Se si esegue il failback di un piano di ripristino, selezionare il nome del piano e fare clic su **Failover** > **pianificato failover**.
2. In **Conferma failover pianificato**scegliere i percorsi di origine e di destinazione. Prendere nota della direzione del failover. Se il failover da primario ha funzionato come previsto e tutte le macchine virtuali si trovano nella posizione secondaria, questo è solo a scopo informativo.
3. In **Sincronizzazione dati**selezionare un'opzione:
    - **Sincronizza dati prima del failover (sincronizza solo le modifiche delta):** questa opzione riduce al minimo i tempi di inattività per le macchine virtuali durante la sincronizzazione senza arrestarle.
        - **Fase 1:** crea uno snapshot della macchina virtuale di Azure e lo copia nell'host Hyper-V locale. La macchina continua l'esecuzione in Azure.
        - **Fase 2:** arresta la macchina virtuale di Azure in modo che non si verifichino nuove modifiche. Il set finale di modifiche delta viene trasferito al server locale e la macchina virtuale locale viene avviata.
    - Sincronizza dati solo durante il **failover (download completo):** questa opzione è più veloce perché si presuppone che la maggior parte del disco sia cambiata e non si vuole dedicare tempo al calcolo dei checksum. Questa opzione non esegue alcun calcolo del checksum.
        - Esegue il download del disco. 
        - È consigliabile usare questa opzione se si esegue Azure da un certo periodo di tempo (un mese o più) o se la macchina virtuale locale viene eliminata.

4. Solo per VMM, se la crittografia dei dati è abilitata per il cloud, in Chiave di **crittografia**selezionare il certificato emesso durante l'installazione del provider nel server VMM.
5. Avviare il failover. Nella scheda **Processi** è possibile monitorare l’avanzamento del failover.
6. Se è stata selezionata l'opzione per sincronizzare i dati prima del failover, al termine della sincronizzazione dei dati iniziale e si è pronti per arrestare le macchine virtuali in Azure, fare clic su **Processi** > nome del processo > **Failover completo**. Vengono eseguite le operazioni seguenti:
    - Arresta il computer Azure.
    - Trasferisce le modifiche più recenti alla macchina virtuale locale.
    - Avvia la macchina virtuale locale.
7. È ora possibile accedere al computer della macchina virtuale locale per verificare che sia disponibile come previsto.
8. La macchina virtuale è in uno stato di attesa di commit. Fare clic su **Commit** per eseguire il commit del failover.
9. Per completare il failback, fare clic su **Replica inversa** per avviare nuovamente la replica della macchina virtuale locale in Azure.To complete the failback, click Reverse Replicate to start replicating the on-premises VM to Azure again.



## <a name="fail-back-to-an-alternate-location"></a>Failback in una posizione alternativa 

Eseguire il failback in un percorso alternativo come segue:

1. Se si sta configurando un nuovo hardware, installare una [versione supportata di Windows](hyper-v-azure-support-matrix.md#replicated-vms)e il ruolo Hyper-V nel computer.
2. Creare un commutatore di rete virtuale con lo stesso nome presente nel server originale.
3. In **Protected Items** > **Protection GroupName** > \< \<> ->> VirtualMachineName selezionare la macchina virtuale di cui si vuole eseguire il failback e quindi selezionare **Failover pianificato**.
4. In **Conferma failover pianificato**scegliere Crea macchina virtuale locale se non **esiste.**
5. In **Nome host**selezionare il nuovo server host Hyper-V in cui si desidera inserire la macchina virtuale.
6. In **Sincronizzazione dati**è consigliabile selezionare l'opzione per sincronizzare i dati prima del failover. In questo modo si riduce al minimo i tempi di inattività delle macchine virtuali durante la sincronizzazione senza arrestarle. Esegue le operazioni seguenti:
    - **Fase 1:** crea uno snapshot della macchina virtuale di Azure e lo copia nell'host Hyper-V locale. La macchina continua l'esecuzione in Azure.
    - **Fase 2:** arresta la macchina virtuale di Azure in modo che non si verifichino nuove modifiche. Il set finale di modifiche viene trasferito al server locale e viene avviata la macchina virtuale locale.
    
7. Fare clic sul segno di spunta per iniziare il failover (failback).
8. Al termine della sincronizzazione iniziale e dopo aver arrestato la macchina virtuale di Azure, fare clic su **Processi** > \<processo di failover pianificato> > **Failover completo**. In questo modo il computer Azure viene arrestato, vengono trasferite le modifiche più recenti alla macchina virtuale locale e avviata.
9. È possibile accedere alla macchina virtuale locale per verificare che tutto funzioni come previsto.
10. Fare clic su **Commit** per completare il failover. Il commit elimina la macchina virtuale di Azure e i relativi dischi e prepara nuovamente la macchina virtuale locale per la protezione.
10. Fare clic su **Replica inversa** per avviare la replica della macchina virtuale locale in Azure.Click Reverse Replicate to start replicating the on-premises VM to Azure. Verranno replicate solo le modifiche delta dopo la disattivazione della macchina virtuale in Azure.Only the delta changes since the VM was turned off in Azure will be replicated.

    > [!NOTE]
    > Se si annulla il processo di failback durante la sincronizzazione dei dati, la macchina virtuale locale sarà in uno stato danneggiato. Ciò è dovuto al fatto che la sincronizzazione dei dati copia i dati più recenti dai dischi delle macchine virtuali di Azure ai dischi dati locali e fino al completamento della sincronizzazione, i dati del disco potrebbero non essere in uno stato coerente. Se la macchina virtuale locale viene avviata dopo l'annullamento della sincronizzazione dei dati, potrebbe non essere avviata. In questo caso, eseguire nuovamente il failover per completare la sincronizzazione dei dati.


## <a name="next-steps"></a>Passaggi successivi
Dopo che la macchina virtuale locale è in fase di replica in Azure, è possibile [eseguire un altro failover in](site-recovery-failover.md) Azure in base alle esigenze.
