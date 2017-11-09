---
title: Failover e failback di macchine virtuali Hyper-V replicate in un data center secondario con Site Recovery | Microsoft Docs
description: Informazioni su come eseguire il failover di macchine virtuali Hyper-V in un sito locale secondario e il failback nel sito primario con Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 44a662fa-2e7a-4996-86df-fdd6d6f5dedf
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/16/2017
ms.author: raynew
ms.openlocfilehash: 8f139070de99c4249207d048d445e86dd41e9060
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>Failover e failback di macchine virtuali Hyper-V replicate in un sito locale secondario

Il servizio [Azure Site Recovery](site-recovery-overview.md) gestisce e controlla la replica, il failover e il failback dei computer locali e delle macchine virtuali di Azure.

In questa esercitazione viene descritto come eseguire il failover di una macchina virtuale Hyper-V gestita in un cloud System Center Virtual Machine Manager (VMM) in un sito VMM secondario. Dopo il failover è possibile eseguire il failback nel sito locale quando disponibile. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Failover di una macchina virtuale Hyper-V da un cloud VMM primario in un cloud VMM secondario
> * Riprotezione dal sito secondario al sito primario e failback
> * Avvio opzionaale della replica nuovamente da primario a secondario

## <a name="overview"></a>Panoramica

L'operazione di failover e failback comprende tre fasi:

1. **Failover nel sito secondario**: failover di macchine dal sito primario a quello secondario.
2. **Failback dal sito secondario**: replica di macchine virtuali dal sito secondario al sito primario ed esecuzione di un failover pianificato per eseguire il failback.
3. Dopo il failover pianificato, se lo si desidera avviare nuovamente la replica dal sito primario al sito secondario.


## <a name="fail-over-to-a-secondary-site"></a>Failover in un sito secondario

### <a name="failover-prerequisites"></a>Prerequisiti di failover

Assicurarsi di aver completato una [esercitazione sul ripristino di emergenza](tutorial-dr-drill-secondary.md) per verificare che tutto funzioni come previsto.


### <a name="run-a-failover-from-primary-to-secondary"></a>Eseguire il failover da primario a secondario

Per le macchine virtuali Hyper-V è possibile eseguire un failover regolare o pianificato.

- Usare un failover regolare per interruzioni impreviste. Quando si esegue questo failover, Site Recovery crea una macchina virtuale nel sito secondario e la accende. Il failover viene eseguito su un punto di recupero specifico. Può verificarsi una perdita di dati, a seconda del punto di recupero usato.
- È possibile usare un failover pianificato per le attività di manutenzione o durante un'interruzione prevista. Questa opzione offre una perdita di dati pari a zero. Quando viene attivato un failover pianificato, le macchine virtuali di origine vengono arrestate. I dati non sincronizzati vengono sincronizzati e il failover viene attivato. 
- 
Questa procedura descrive come eseguire un failover regolare.


1. In **Impostazioni** > **Elementi replicati** fare clic sulla macchina virtuale > **Failover**.
2. In **Failover** selezionare un **Punto di ripristino** in cui eseguire il failover. È possibile usare una delle opzioni seguenti.
    - **Più recente** (predefinita): questa opzione elabora prima tutti i dati inviati a Site Recovery. Offre il valore RPO (Recovery Point Objective) più basso perché la macchina virtuale di replica creata dopo il failover contiene tutti i dati che sono stati replicati in Site Recovery all'attivazione del failover.
    - **Elaborato più recente**: questa opzione consente di eseguire il failover della macchina virtuale nel punto di recupero più recente elaborato da Site Recovery. Offre un RTO (Recovery Time Objective) basso poiché non viene impiegato tempo per elaborare dati non elaborati.
    - **Coerente con l'app più recente**: questa opzione esegue il failover della macchina virtuale nel punto di recupero coerente con l'app più recente elaborato da Site Recovery. 
3. La chiave di crittografia non è rilevante per questo scenario.
4. Selezionare **Arrestare la macchina prima di iniziare il failover** se si vuole provare ad arrestare le macchine virtuali di origine tramite Site Recovery prima di attivare il failover. Prima di attivare il failover, Site Recovery tenterà anche di sincronizzare i dati locali non ancora inviati al sito secondario. Si noti che il failover continuerà anche se l'arresto non riesce. Nella pagina **Processi** è possibile seguire lo stato del failover.
5. È ora possibile visualizzare la macchina virtuale nel cloud VMM secondario.
6. Dopo la verifica della macchina virtuale, **eseguire il commit** del failover. In questo modo tutti i punti di ripristino disponibili verranno eliminati.

> [!WARNING]
> **Non annullare un failover in corso**: prima dell'avvio del failover, la replica della macchina virtuale viene arrestata. Se si annulla un failover in corso, il failover viene arrestato ma non viene eseguita di nuovo la replica della macchina virtuale.  


## <a name="reprotect-and-fail-back-from-secondary-to-primary"></a>Ricreare la protezione ed eseguire il failback da secondario a primario

### <a name="prerequisites-for-failback"></a>Prerequisiti per il failback

Per completare il failback, verificare che i server VMM primario e secondario siano connessi a Site Recovery.


### <a name="reprotect-and-fail-back"></a>Ricreare la protezione ed eseguire il failback

Avviare la replica dal sito secondario al primario ed eseguire il failback nel sito primario. Quando le macchine virtuali sono nuovamente in esecuzione nel sito primario, è possibile replicarle nuovamente nel sito secondario.  

1. In **Impostazioni** > **Elementi replicati** scegliere la macchina virtuale e abilitare **Replica inversa**. La macchina virtuale inizia la replica inversa del sito primario.
2. Fare clic sulla macchina virtuale > **Failover pianificato**.
3. In **Conferma failover pianificato**, verificare la direzione di failover (dal cloud VMM secondario) e selezionare il percorso di origine e di destinazione. 
4. In **Sincronizzazione dati**, specificare la modalità di sincronizzazione:
    - **Sincronizza i dati prima del failover (sincronizza solo modifiche differenziali)**: questa opzione riduce al minimo i tempi di inattività della macchina virtuale poiché la sincronizza senza arrestarla. Vengono eseguite le operazioni seguenti:
        - Crea uno snapshot della macchina virtuale di replica e lo copia nell'host Hyper-V primario. La macchina virtuale di replica resta in esecuzione.
        - Arresta la macchina virtuale di replica in modo che non possano essere apportate nuove modifiche. Il set di modifiche delta finale viene trasferito nel sito primario e viene avviata la macchina virtuale nel sito primario.
    - **Sincronizza i dati durante il failover (download completo)**: usare questa opzione se il sito secondario è in esecuzione da molto tempo. Questa opzione è più veloce poiché sono previste molteplici modifiche del disco e non viene speso tempo in calcoli del checksum. Questa opzione esegue il download del disco. È utile anche quando la macchina virtuale primaria è stata eliminata.
5. La chiave di crittografia non è rilevante per questo scenario.
6. Avviare il failover. Nella scheda **Processi** è possibile monitorare l’avanzamento del failover.
7. Se si è scelto di sincronizzare i dati prima del failover, dopo la sincronizzazione iniziale dei dati, quando si è pronti ad arrestare le macchine virtuali di replica nel sito secondario, fare clic su **Processi** > nome del processo di failover pianificato > **Completa failover**. Questa procedura arresta la macchina virtuale secondaria, trasferisce le modifiche più recenti al sito primario e avvia la macchina virtuale primaria.
8. Nel cloud VMM primario, controllare che la macchina virtuale sia disponibile.
9. La macchina virtuale primaria è ora in uno stato di commit in sospeso. Fare clic su **Commit** per eseguire il commit del failover.
10. Per avviare di nuovo la replica della macchina virtuale primaria nel sito secondario, abilitare **Replica inversa**.


> [!NOTE]
> La replica inversa replica solo le modifiche apportate dopo che la macchina virtuale di replica è stata spenta e vengono inviate solo le modifiche differenziali.

