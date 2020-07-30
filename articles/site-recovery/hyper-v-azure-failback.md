---
title: Eseguire il failback di macchine virtuali Hyper-V da Azure con Azure Site Recovery
description: Come eseguire il failback di macchine virtuali Hyper-V in un sito locale da Azure con Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 09/12/2019
ms.author: ramamill
ms.openlocfilehash: a31a28728dd0521262bd0518cc49a385f4314302
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87416231"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>Eseguire il failback per le macchine virtuali Hyper-V

Questo articolo descrive come eseguire il failback di macchine virtuali di Azure create dopo il failover di macchine virtuali Hyper-V da un sito locale ad Azure, con [Azure Site Recovery](site-recovery-overview.md).

- Per eseguire il failback di macchine virtuali Hyper-V da Azure, eseguire un failover pianificato da Azure al sito locale. Se la direzione del failover è da Azure a locale, viene considerato un failback.
- Poiché Azure è un ambiente a disponibilità elevata e le macchine virtuali sono sempre disponibili, il failback da Azure è un'attività pianificata. È possibile pianificare un tempo di inattività ridotto in modo che i carichi di lavoro possano essere riavviati in locale. 
- Il failback pianificato disattiva le macchine virtuali in Azure e Scarica le modifiche più recenti. Non è prevista alcuna perdita di dati.

## <a name="before-you-start"></a>Prima di iniziare

1. [Esaminare i tipi di failback](failover-failback-overview.md#hyper-v-reprotectionfailback) che è possibile usare, ovvero il ripristino del percorso originale e il ripristino in un percorso alternativo.
2. Verificare che le macchine virtuali di Azure utilizzino un account di archiviazione e non i dischi gestiti. Il failback delle VM Hyper-V replicate con Managed Disks non è supportato.
3. Verificare che l'host Hyper-V locale (o il server System Center VMM se si usa con Site Recovery) sia in esecuzione e connesso ad Azure. 
4. Verificare che il failover e il commit siano completi per le macchine virtuali. Non è necessario configurare componenti di Site Recovery specifici per il failback di macchine virtuali Hyper-V da Azure.
5. Il tempo necessario per completare la sincronizzazione dei dati e avviare la macchina virtuale locale dipende da diversi fattori. Per velocizzare il download dei dati, è possibile configurare l'agente di servizi di ripristino di Microsoft per usare più thread per parallelizzare il download. [Altre informazioni](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage).


## <a name="fail-back-to-the-original-location"></a>Failback nella posizione originaria

Per eseguire il failback di macchine virtuali Hyper-V in Azure nella macchina virtuale locale originale, eseguire un failover pianificato da Azure al sito locale come indicato di seguito:

1. Nell'insieme di credenziali > **gli elementi replicati**selezionare la macchina virtuale. Fare clic con il pulsante destro del mouse sulla macchina virtuale > **failover pianificato**. Se si esegue il failback di un piano di ripristino, selezionare il nome del piano **e fare clic su failover**  >  **pianificato**failover.
2. In **conferma failover pianificato**scegliere i percorsi di origine e di destinazione. Prendere nota della direzione del failover. Se il failover dal database primario funzionava come previsto e tutte le macchine virtuali si trovano nella posizione secondaria, questo è solo a fini informativi.
3. In **sincronizzazione dati**selezionare un'opzione:
    - **Sincronizza i dati prima del failover (Sincronizza solo modifiche**differenziali): questa opzione riduce al minimo i tempi di inattività per le macchine virtuali durante la sincronizzazione senza arrestarli.
        - **Fase 1**: acquisisce uno snapshot della macchina virtuale di Azure e lo copia nell'host Hyper-V locale. La macchina continua l'esecuzione in Azure.
        - **Fase 2**: arresta la macchina virtuale di Azure in modo che non vengano apportate nuove modifiche. Il set finale di modifiche delta viene trasferito al server locale e viene avviata la macchina virtuale locale.
    - **Sincronizza i dati durante il failover (download completo)**: questa opzione è più veloce perché si presume che la maggior parte del disco sia cambiata e non si vuole dedicare tempo al calcolo dei checksum. Questa opzione non esegue alcun calcolo del checksum.
        - Esegue il download del disco. 
        - Si consiglia di usare questa opzione se Azure è in esecuzione per un periodo di tempo (un mese o più) o se la macchina virtuale locale è stata eliminata.

4. Solo per VMM, se la crittografia dei dati è abilitata per il cloud, in **chiave di crittografia**selezionare il certificato emesso quando è stata abilitata la crittografia dei dati durante l'installazione del provider nel server VMM.
5. Avviare il failover. Nella scheda **Processi** è possibile monitorare l’avanzamento del failover.
6. Se è stata selezionata l'opzione per sincronizzare i dati prima del failover, al termine della sincronizzazione dei dati iniziale e si è pronti per arrestare le macchine virtuali in Azure, fare clic su **processi** > nome processo > **completa failover**. Vengono eseguite le operazioni seguenti:
    - Arresta il computer Azure.
    - Trasferisce le ultime modifiche alla macchina virtuale locale.
    - Avvia la macchina virtuale locale.
7. È ora possibile accedere al computer della macchina virtuale locale per verificare che sia disponibile come previsto.
8. La macchina virtuale è in uno stato di attesa di commit. Fare clic su **Commit** per eseguire il commit del failover.
9. Per completare il failback, fare clic su **replica inversa** per avviare di nuovo la replica della macchina virtuale locale in Azure.



## <a name="fail-back-to-an-alternate-location"></a>Failback in una posizione alternativa 

Eseguire il failback in un percorso alternativo, come indicato di seguito:

1. Se si sta configurando un nuovo hardware, installare una [versione supportata di Windows](hyper-v-azure-support-matrix.md#replicated-vms)e il ruolo Hyper-V nel computer.
2. Creare un commutatore di rete virtuale con lo stesso nome presente nel server originale.
3. In **Protected Items**  >  **gruppo protezione**elementi protetti  >  \<ProtectionGroupName>  ->  \<VirtualMachineName> selezionare la macchina virtuale di cui si vuole eseguire il failback e quindi selezionare **failover pianificato**.
4. In **conferma failover pianificato**, scegliere **Crea macchina virtuale locale se non esiste**.
5. In **nome host**selezionare il nuovo server host Hyper-V in cui si desidera inserire la macchina virtuale.
6. In **sincronizzazione dati**è consigliabile selezionare l'opzione per sincronizzare i dati prima del failover. Questo riduce al minimo i tempi di inattività delle macchine virtuali durante la sincronizzazione senza chiuderli. Esegue le operazioni seguenti:
    - **Fase 1**: acquisisce snapshot della macchina virtuale di Azure e lo copia nell'host Hyper-V locale. La macchina continua l'esecuzione in Azure.
    - **Fase 2**: arresta la macchina virtuale di Azure in modo che non vengano apportate nuove modifiche. Il set finale di modifiche viene trasferito al server locale e viene avviata la macchina virtuale locale.
    
7. Fare clic sul segno di spunta per iniziare il failover (failback).
8. Al termine della sincronizzazione iniziale e si è pronti per arrestare la macchina virtuale di Azure, fare clic su **processi**  >  \<planned failover job>  >  **failover completo**. Questo arresta il computer Azure, trasferisce le modifiche più recenti alla VM locale e le avvia.
9. È possibile accedere alla macchina virtuale locale per verificare che tutto funzioni come previsto.
10. Fare clic su **commit** per completare il failover. Il commit Elimina la macchina virtuale di Azure e i relativi dischi e prepara la macchina virtuale locale per la protezione.
10. Fare clic su **replica inversa** per avviare la replica della macchina virtuale locale in Azure. Verranno replicate solo le modifiche delta apportate dopo la disattivazione della macchina virtuale in Azure.

    > [!NOTE]
    > Se si annulla il processo di failback durante la sincronizzazione dei dati, la macchina virtuale locale si troverà in uno stato danneggiato. Questo perché la sincronizzazione dei dati copia i dati più recenti dai dischi delle macchine virtuali di Azure nei dischi dati locali e fino al completamento della sincronizzazione, i dati del disco potrebbero non essere coerenti. Se la macchina virtuale locale viene avviata dopo che la sincronizzazione dei dati è stata annullata, potrebbe non essere avviata. In questo caso, eseguire di nuovo il failover per completare la sincronizzazione dei dati.


## <a name="next-steps"></a>Passaggi successivi
Dopo la replica della macchina virtuale locale in Azure, è possibile [eseguire un altro failover](site-recovery-failover.md) in Azure in base alle esigenze.
