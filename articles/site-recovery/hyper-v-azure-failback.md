---
title: Eseguire il failback su un sito locale per macchine virtuali Hyper-V | Microsoft Docs
description: Azure Site Recovery coordina la replica, il failover e il ripristino di macchine virtuali e server fisici. Informazioni su come eseguire il failback da Azure al data center locale.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 02/14/2018
ms.author: rajanaki
ms.openlocfilehash: d344174ffa290b55386918ae19e2f792bb801a8a
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2018
---
# <a name="run-a-failback-for-hyper-v-vms"></a>Eseguire il failback per le macchine virtuali Hyper-V

Questo articolo descrive come eseguire il failback di macchine virtuali Hyper-V protette da Site Recovery.

## <a name="prerequisites"></a>prerequisiti
1. Leggere i dettagli sui [diversi tipi di failback](concepts-types-of-failback.md) e le rispettive avvertenze.
1. Assicurarsi che il server host VMM o Hyper-V del sito primario sia connesso ad Azure.
2. È necessario aver completato il **commit** nella macchina virtuale.

## <a name="perform-failback"></a>Eseguire il failback
Dopo il failover dalla posizione primaria alla posizione secondaria, le macchine virtuali replicate non sono protette da Site Recovery e la posizione secondaria funge da posizione attiva. Per eseguire il failback di macchine virtuali in un piano di ripristino, eseguire un failover pianificato dal sito secondario a quello primario, come segue. 
1. Selezionare **Piani di ripristino** > *nome_pianodiripristino*. Fare clic su **Failover** > **Planned Failover**.
2. Nella pagina **Conferma failover pianificato** scegliere i percorsi di origine e di destinazione. Prendere nota della direzione del failover. Se il failover dal sito primario funziona nel modo previsto e tutte le macchine virtuali si trovano nella posizione secondaria, la direzione ha solo scopo informativo.
3. Se si esegue il failback da Azure, selezionare le impostazioni in **Sincronizzazione dati**:
    - **Sincronizza i dati prima del failover (sincronizza solo modifiche differenziali)**: questa opzione riduce al minimo i tempi di inattività delle macchine virtuali poiché le sincronizza senza arrestarle. Esegue i passaggi seguenti:
        - Fase 1: Crea uno snapshot della macchina virtuale in Azure e lo copia negli host Hyper-V in locale. La macchina continua l'esecuzione in Azure.
        - Fase 2: Arresta la macchina virtuale in Azure in modo che non vengano apportate nuove modifiche. Il set finale di modifiche differenziali viene trasferito al server locale e viene avviata la macchina virtuale locale.

    - **Sincronizza i dati durante il failover (download completo)**: questa opzione è più rapida.
        - Questa opzione è più veloce perché si prevede che la maggior parte del disco sia cambiata e non si intende sprecare tempo nel calcolo del checksum. Esegue il download del disco. Questa opzione è utile anche se la macchina virtuale locale è stata eliminata.
        - È consigliabile scegliere questa opzione se si usa Azure da tempo (un mese o più) o se la macchina virtuale locale è stata eliminata. Questa opzione non esegue alcun calcolo del checksum.


4. Se la crittografia dei dati è abilitata per il cloud, in **Chiave di crittografia** selezionare il certificato emesso quando è stata abilitata la crittografia dei dati durante l'installazione del provider nel server VMM.
5. Avviare il failover. Nella scheda **Processi** è possibile monitorare l'avanzamento del failover.
6. Se è stata selezionata l'opzione per sincronizzare i dati prima del failover, dopo la sincronizzazione dati iniziale, quando si è pronti ad arrestare le macchine virtuali in Azure, fare clic su **Processi** nome processo failover pianificato **Failover completo**. La macchina Azure viene arrestata e le modifiche più recenti vengono trasferite alla macchina virtuale locale, che viene avviata in locale.
7. A questo punto è possibile accedere alla macchina virtuale per verificare che sia disponibile come previsto.
8. La macchina virtuale è in uno stato di attesa di commit. Fare clic su **Commit** per eseguire il commit del failover.
9. Per completare il failback fare clic su **Replica inversa** per iniziare a proteggere la macchina virtuale nel sito primario.


Utilizzare queste procedure per eseguire il failback al sito primario originale. Questa procedura descrive come eseguire un failover pianificato per un piano di ripristino. In alternativa è possibile eseguire il failover per una singola macchina nella scheda **Macchine virtuali** .


## <a name="failback-to-an-alternate-location-in-hyper-v-environment"></a>Failback in un percorso alternativo nell'ambiente Hyper-V
Se è stata distribuita la protezione tra un [sito Hyper-V e Azure](site-recovery-hyper-v-site-to-azure.md) , è possibile eseguire il failback da Azure a una posizione alternativa locale. Ciò è utile se è necessario configurare nuovo hardware locale. Di seguito viene indicato come procedere.

1. Se si configura nuovo hardware, installare Windows Server 2012 R2 e il ruolo Hyper-V nel server.
2. Creare un commutatore di rete virtuale con lo stesso nome presente nel server originale.
3. Selezionare **Elementi protetti** -> **Gruppo protezione dati** -> <ProtectionGroupName> -> <VirtualMachineName>di cui si vuole eseguire il failback e selezionare **Failover pianificato**.
4. Fare clic su **Conferma failover pianificato** select **Crea macchina virtuale locale, se non esiste**.
5. In Nome host selezionare il nuovo server host Hyper-V in cui si vuole collocare la macchina virtuale.
6. In Sincronizzazione dati è consigliabile selezionare l'opzione **Sincronizza i dati prima del failover**. Questa opzione riduce al minimo i tempi di inattività per le macchine virtuali senza arrestarle. Effettua le seguenti operazioni:

    - Fase 1: Crea uno snapshot della macchina virtuale in Azure e lo copia negli host Hyper-V in locale. La macchina continua l'esecuzione in Azure.
    - Fase 2: Arresta la macchina virtuale in Azure in modo che non vengano apportate nuove modifiche. Il set finale di modifiche viene trasferito al server locale e viene avviata la macchina virtuale locale.
    
7. Fare clic sul segno di spunta per iniziare il failover (failback).
8. Quando è stata completata la sincronizzazione iniziale e si è pronti per arrestare la macchina virtuale in Azure, fare clic su **Processi** > <planned failover job> > **Failover completo**. La macchina Azure viene arrestata e le modifiche più recenti vengono trasferite alla macchina virtuale locale, che viene avviata.
9. È possibile accedere alla macchina virtuale locale per verificare che tutto funzioni come previsto. Fare clic su **Commit** per completare il failover. Il commit elimina la macchina virtuale di Azure e i relativi dischi e prepara la VM a essere protetta di nuovo.
10. Fare clic su **Replica inversa** per iniziare a proteggere la macchina virtuale in locale.

    > [!NOTE]
    > Se si annulla il processo di failback durante la fase di sincronizzazione dei dati, la macchina virtuale locale ne risulterà corrotta. Questo avviene perché la sincronizzazione dei dati copia i dati più recenti dai dischi della macchina virtuale di Azure sui dischi di dati locali e, fino al completamento della sincronizzazione, il disco dati potrebbe non trovarsi in uno stato coerente. La macchina virtuale locale potrebbe non avviarsi dopo aver annullato la sincronizzazione dei dati. Riattivare il failover per completare la sincronizzazione dei dati.


## <a name="why-is-there-no-button-called-failback"></a>Perché non è disponibile alcun pulsante denominato "Failback"?
Nel portale non esiste alcuna azione esplicita chiamata "Failback". Il failback è un passaggio in cui si torna al sito primario. Per definizione, il failback si verifica quando si esegue il failover delle macchine virtuali dal sito di ripristino a quello primario.

Quando si avvia un'operazione di failover, il pannello segnala la direzione in cui le macchine virtuali devono essere spostate. Se lo spostamento avviene da Azure al sistema locale, si tratta di un failback.

## <a name="why-is-there-only-a-planned-failover-gesture-to-failback"></a>Perché esiste una sola azione di failover pianificata per il failback?
Azure è un ambiente a elevata disponibilità che assicura la costante disponibilità delle macchine virtuali. Il failback è un'attività pianificata in cui si decide di accettare un breve tempo di inattività in modo che i carichi di lavoro possano iniziare di nuovo a essere eseguiti in locale. Non è prevista alcuna perdita di dati. Pertanto è disponibile una sola azione di failover pianificata, che disattiva le VM in Azure, scarica le modifiche più recenti e verifica che non vi sia alcuna perdita di dati.

## <a name="do-i-need-a-process-server-in-azure-to-failback-to-hyper-v"></a>È necessario un server di elaborazione in Azure per eseguire il failback in Hyper-v?
No, il server di elaborazione è necessario solo quando si proteggono le macchine virtuali VMware. Non sono necessari componenti aggiuntivi da distribuire quando si protegge o si esegue il failback di macchine virtuali Hyper-v.


## <a name="time-taken-to-failback"></a>Tempo richiesto per il failback
Il tempo richiesto per completare la sincronizzazione dei dati e avviare la macchina virtuale dipende da diversi fattori. Per approfondire l'argomento relativo al tempo necessario, viene descritto cosa accade durante la sincronizzazione dei dati.

La sincronizzazione dei dati crea uno snapshot dei dischi della macchina virtuale, avvia il controllo blocco per blocco e calcola il checksum. Il checksum calcolato viene inviato in locale per confrontarlo con il checksum locale dello stesso blocco. In caso di corrispondenza tra i checksum, il blocco di dati non viene trasferito. Se non c'è corrispondenza, il blocco di dati viene trasferito in locale. Il tempo di trasferimento dipende dalla larghezza di banda disponibile. La velocità del checksum è pochi GB al minuto. 

Per rendere più veloce il download dei dati, è possibile configurare l'agente MARS affinché usi più thread per eseguire i download in parallelo. Consultare il [documento qui](https://support.microsoft.com/en-us/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) su come modificare i thread di download nell'agente.


## <a name="next-steps"></a>Passaggi successivi

Dopo aver eseguito il **commit**, è possibile avviare la *replica inversa*. In questo modo si inizia a proteggere la macchina virtuale dalla posizione locale ad Azure. Con questa operazione vengono replicate solo le modifiche apportate dal momento in cui la VM è stata disattivata in Azure e pertanto vengono inviate solo le modifiche differenziali.
