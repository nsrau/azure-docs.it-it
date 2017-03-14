---
title: Failback in Azure Site Recovery per macchine virtuali Hyper-V | Documentazione Microsoft
description: Azure Site Recovery coordina la replica, il failover e il ripristino di macchine virtuali e server fisici. Informazioni su come eseguire il failback da Azure al data center locale.
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 2/15/2017
ms.author: ruturajd
translationtype: Human Translation
ms.sourcegitcommit: 88a09929582e69941101e884697592c91c0f5cfa
ms.openlocfilehash: 862b80bdb00efcbf8863699df12aacdbab60bcef
ms.lasthandoff: 02/23/2017


---
# <a name="failback-in-site-recovery"></a>Failback in Site Recovery
Questo articolo descrive come eseguire il failback di macchine virtuali protette da Site Recovery. 

## <a name="prerequisites"></a>Prerequisiti
1. Assicurarsi che il server VMM o Hyper-V del sito primario sia connesso.
2. È necessario aver completato il **commit** nella macchina virtuale.

## <a name="why-is-there-no-button-called-failback"></a>Perché non è disponibile alcun pulsante denominato "Failback"?
Nel portale non esiste alcuna azione esplicita chiamata "Failback". Il failback è un passaggio in cui si torna al sito primario. Per definizione, il failover è quando si esegue il failover delle macchine virtuali dal sito primario (locale) al sito di ripristino (Azure) e il failback è quando si esegue il failover delle macchine virtuali dal sito di ripristino al sito primario.

Quando si avvia un failover, il pannello indica la direzione del processo. Se la direzione è da Azure al sito locale, è un failback.

## <a name="why-is-there-only-a-planned-failover-gesture-to-failback"></a>Perché esiste una sola azione di failover pianificata per il failback?
Azure è un ambiente a elevata disponibilità e le macchine virtuali saranno sempre disponibili. Il failback è un'attività pianificata in cui si decide di accettare un breve tempo di inattività in modo che i carichi di lavoro possano iniziare di nuovo a essere eseguiti in locale. Non è prevista alcuna perdita di dati. Pertanto è disponibile una sola azione di failover pianificata, che disattiva le VM in Azure, scarica le modifiche più recenti e verifica che non vi sia alcuna perdita di dati.

## <a name="initiate-failback"></a>Avviare il failback
Dopo il failover dalla posizione primaria alla posizione secondaria, le macchine virtuali replicate non sono protette da Site Recovery e la posizione secondaria funge da posizione attiva. Utilizzare queste procedure per eseguire il failback al sito primario originale. Questa procedura descrive come eseguire un failover pianificato per un piano di ripristino. In alternativa è possibile eseguire il failover per una singola macchina nella scheda **Macchine virtuali** .

1. Selezionare **Piani di ripristino** > *nome_pianodiripristino*. Fare clic su **Failover** > **Planned Failover**.
2. Nella pagina **Conferma failover pianificato **selezionare il percorso di origine e destinazione. Prendere nota della direzione del failover. Se il failover dal sito primario funziona nel modo previsto e tutte le macchine virtuali si trovano nella posizione secondaria, la direzione ha solo scopo informativo.
3. Se si esegue il failback da Azure, selezionare le impostazioni in **Sincronizzazione dati**:

   * **Sincronizza i dati prima del failover (sincronizza solo modifiche differenziali)**: questa opzione riduce al minimo i tempi di inattività delle macchine virtuali poiché le sincronizza senza arrestarle. Effettua le seguenti operazioni:
     * Fase 1: Crea uno snapshot della macchina virtuale in Azure e lo copia negli host Hyper-V in locale. La macchina continua l'esecuzione in Azure.
     * Fase 2: Arresta la macchina virtuale in Azure in modo che non vengano apportate nuove modifiche. Il set finale di modifiche differenziali viene trasferito al server locale e viene avviata la macchina virtuale locale.

    - **Sincronizza i dati durante il failover (download completo)**: usare questa opzione se Azure è in esecuzione da molto tempo. Questa opzione è più veloce perché si prevede che la maggior parte del disco sia cambiata e non si intende sprecare tempo nel calcolo del checksum. Esegue il download del disco. Questa opzione è utile anche se la macchina virtuale locale è stata eliminata.

    >[!NOTE] 
    >È consigliabile scegliere questa opzione se si usa Azure da molto tempo (un mese o più) o se la macchina virtuale locale è stata eliminata. Questa opzione non esegue alcun calcolo del checksum.
    >
    >




4. Se la crittografia dei dati è abilitata per il cloud, in **Chiave di crittografia** selezionare il certificato emesso quando è stata abilitata la crittografia dei dati durante l'installazione del provider nel server VMM.
5. Avviare il failover. Nella scheda **Processi** è possibile monitorare l’avanzamento del failover.
6. Se è stata selezionata l'opzione per sincronizzare i dati prima del failover, dopo la sincronizzazione dati iniziale, quando si è pronti ad arrestare le macchine virtuali in Azure, fare clic su **Processi** > <planned failover job name> **Failover completo**. La macchina Azure viene arrestata e le modifiche più recenti vengono trasferite alla macchina virtuale locale, che viene avviata in locale.
7. A questo punto è possibile accedere alla macchina virtuale per verificare che sia disponibile come previsto.
8. La macchina virtuale è in uno stato di attesa di commit. Fare clic su **Commit** per eseguire il commit del failover.
9. Per completare il failback fare clic su **Replica inversa** per iniziare a proteggere la macchina virtuale nel sito primario.

## <a name="failback-to-an-alternate-location"></a>Failback in una posizione alternativa
Se è stata distribuita la protezione tra un [sito Hyper-V e Azure](site-recovery-hyper-v-site-to-azure.md) , è possibile eseguire il failback da Azure a una posizione alternativa locale. Ciò è utile se è necessario configurare nuovo hardware locale. Di seguito viene indicato come procedere.

1. Se si configura nuovo hardware, installare Windows Server 2012 R2 e il ruolo Hyper-V nel server.
2. Creare un commutatore di rete virtuale con lo stesso nome presente nel server originale.
3. Selezionare **Elementi protetti** -> **Gruppo protezione dati** -> <ProtectionGroupName> -> <VirtualMachineName>di cui si vuole eseguire il failback e selezionare **Failover pianificato**.
4. Fare clic su **Conferma failover pianificato** select **Crea macchina virtuale locale, se non esiste**.
5. In **Nome host** selezionare il nuovo server host Hyper-V in cui si desidera collocare la macchina virtuale.
6. In Sincronizzazione dati è consigliabile selezionare l'opzione **Sincronizzare i dati prima del failover**. Questa opzione riduce al minimo i tempi di inattività per le macchine virtuali senza arrestarle. Effettua le seguenti operazioni:

   * Fase 1: Crea uno snapshot della macchina virtuale in Azure e lo copia negli host Hyper-V in locale. La macchina continua l'esecuzione in Azure.
   * Fase 2: Arresta la macchina virtuale in Azure in modo che non vengano apportate nuove modifiche. Il set finale di modifiche viene trasferito al server locale e viene avviata la macchina virtuale locale.
7. Fare clic sul segno di spunta per iniziare il failover (failback).
8. Quando è stata completata la sincronizzazione iniziale e si è pronti per arrestare la macchina virtuale in Azure, fare clic su **Processi** > <planned failover job> > **Failover completo**. La macchina Azure viene arrestata e le modifiche più recenti vengono trasferite alla macchina virtuale locale, che viene avviata.
9. È possibile accedere alla macchina virtuale locale per verificare che tutto funzioni come previsto. Fare clic su **Commit** per completare il failover.
10. Fare clic su **Replica inversa** per iniziare a proteggere la macchina virtuale in locale.

    > [!NOTE]
    > Se si annulla il processo di failback durante la fase di sincronizzazione dei dati, la macchina virtuale locale ne risulterà corrotta. Questo avviene perché la sincronizzazione dei dati copia i dati più recenti dai dischi della macchina virtuale di Azure sui dischi di dati locali e, fino al completamento della sincronizzazione, il disco dati potrebbe non trovarsi in uno stato coerente. La macchina virtuale locale potrebbe non avviarsi dopo aver annullato la sincronizzazione dei dati. Riattivare il failover per completare la sincronizzazione dei dati.
    >
    >



## <a name="next-steps"></a>Passaggi successivi

Dopo aver completato il processo di failback, eseguire il **commit** della macchina virtuale. Il commit elimina la macchina virtuale di Azure e i relativi dischi e prepara la VM a essere protetta di nuovo.

Dopo aver eseguito il **commit**, è possibile avviare la *replica inversa*. In questo modo si inizierà a proteggere la macchina virtuale dalla posizione locale ad Azure. Si noti che saranno replicate solo le modifiche dal momento in cui la VM è stata disattivata in Azure e pertanto saranno inviate solo le modifiche differenziali.



