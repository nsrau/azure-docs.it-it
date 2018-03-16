---
title: Failover in Site Recovery | Microsoft Docs
description: Azure Site Recovery coordina la replica, il failover e il ripristino di macchine virtuali e server fisici. Informazioni sul failover in Azure o in un centro dati secondario.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/09/2018
ms.author: ponatara
ms.openlocfilehash: f7a60cd82508629ad3cf46882564aa68995ba3e6
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="failover-in-site-recovery"></a>Failover in Site Recovery
Questo articolo descrive come eseguire il failover di macchine virtuali e server fisici protetti da Site Recovery.

## <a name="prerequisites"></a>prerequisiti
1. Prima di eseguire un failover, eseguire un [failover di test](site-recovery-test-failover-to-azure.md) per verificare che tutto funzioni come previsto.
1. [Preparare la rete](site-recovery-network-design.md) nel percorso di destinazione prima di eseguire un failover.  

Usare la tabella seguente per informazioni sulle opzioni di failover fornite da Azure Site Recovery. Queste opzioni sono anche elencate per diversi scenari di failover.

| Scenario | Requisito di ripristino dell'applicazione | Flusso di lavoro per Hyper-V | Flusso di lavoro per VMware
|---|--|--|--|
|Failover pianificato dovuto a un imminente tempo di inattività del data center| Nessuna perdita di dati per l'applicazione quando viene eseguita un'attività pianificata| Per Hyper-V, ASR replica i dati con una frequenza di copia che viene specificata dall'utente. Il failover pianificato viene usato per eseguire l'override della frequenza e replicare le modifiche finali prima dell'avvio di un failover. <br/> <br/> 1.    Pianificare una finestra di manutenzione in base al processo di gestione del cambiamento dell'azienda. <br/><br/> 2. Informare gli utenti dell'imminente tempo di inattività. <br/><br/> 3. Portare offline l'applicazione rivolta all'utente.<br/><br/>4. Avviare il failover pianificato tramite il portale di ASR. La macchina virtuale locale si arresta automaticamente.<br/><br/>Perdita di dati effettiva dell'applicazione = 0 <br/><br/>Viene anche fornito un giornale di registrazione di punti di recupero all'interno di un periodo di conservazione qualora un utente intendesse usare un punto di recupero precedente (24 ore di conservazione per Hyper-V).| Per VMware, ASR replica i dati continuamente tramite CDP. È inclusa un'opzione che consente all'utente di effettuare il failover ai dati più recenti (inclusi quelli dopo l'arresto dell'applicazione).<br/><br/> 1. Pianificare una finestra di manutenzione in base al processo di gestione del cambiamento. <br/><br/>2. Informare gli utenti dell'imminente tempo di inattività. <br/><br/>3.    Portare offline l'applicazione rivolta all'utente. <br/><br/>4.  Tramite il portale di ASR, avviare un failover pianificato nel punto più recente da quando l'applicazione è offline. Usare l'opzione di failover non pianificato del portale e selezionare il punto più recente per il failover. La macchina virtuale locale si arresta automaticamente.<br/><br/>Perdita di dati effettiva dell'applicazione = 0 <br/><br/>Viene fornito un giornale di registrazione di punti di recupero all'interno di un periodo di conservazione qualora un utente intendesse usare un punto di recupero precedente (72 ore di conservazione per VMware).
|Failover dovuto a un tempo di inattività non pianificato del data center (disastro naturale o IT) | Perdita di dati minima per l'applicazione | 1. Avviare il piano BCP dell'organizzazione. <br/><br/>2. Tramite il portale di ASR, avviare un failover non pianificato nel punto più recente o in un punto all'interno del periodo di conservazione (giornale di registrazione).| 1. Avviare il piano BCP dell'organizzazione. <br/><br/>2.  Tramite il portale di ASR, avviare un failover non pianificato nel punto più recente o in un punto all'interno del periodo di conservazione (giornale di registrazione).


## <a name="run-a-failover"></a>Eseguire un failover
Questa procedura descrive come eseguire un failover per un [piano di ripristino](site-recovery-create-recovery-plans.md). In alternativa è possibile eseguire il failover per una singola macchina virtuale o un server fisico dalla pagina **Elementi replicati**.


![Failover](./media/site-recovery-failover/Failover.png)

1. Selezionare **Piani di ripristino** > *nome_pianodiripristino*. Fare clic su **Failover**.
2. Nella schermata **Failover** selezionare un **punto di ripristino** in cui eseguire il failover. È possibile usare una delle opzioni seguenti.
    1.  **Più recente** (predefinita): questa opzione avvia il processo elaborando prima tutti i dati inviati al servizio Site Recovery. L'elaborazione dei dati crea un punto di ripristino per ogni macchina virtuale. Questo punto di ripristino viene usato dalla macchina virtuale durante il failover. Questa opzione offre il valore RPO (Recovery Point Objective) più basso, perché la macchina virtuale creata dopo il failover contiene tutti i dati che sono stati replicati nel servizio Site Recovery all'attivazione del failover.
    1.  **Punto di ripristino elaborato più recente**: con questa opzione viene eseguito il failover di tutte le macchine virtuali del piano di ripristino nell'ultimo punto di ripristino elaborato dal servizio Site Recovery. Quando si esegue il failover di test di una macchina virtuale, viene visualizzato anche il timestamp del punto di ripristino elaborato più recente. Se si esegue il failover di un piano di ripristino, per ottenere tali informazioni è possibile passare alla singola macchina virtuale ed esaminare il riquadro **Latest Recovery Points** (Punti di ripristino più recenti). Dato che non viene impiegato tempo per elaborare dati non elaborati, questa opzione offre un failover con valore RTO (Recovery Time Objective) basso.
    1.  **Punto di ripristino più recente coerente con l'applicazione**: con questa opzione viene eseguito il failover di tutte le macchine virtuali del piano di ripristino nell'ultimo punto di ripristino coerente con l'applicazione elaborato dal servizio Site Recovery. Quando si esegue il failover di test di una macchina virtuale, viene visualizzato anche il timestamp del punto di ripristino coerente con l'app più recente. Se si esegue il failover di un piano di ripristino, per ottenere tali informazioni è possibile passare alla singola macchina virtuale ed esaminare il riquadro **Latest Recovery Points** (Punti di ripristino più recenti).
    1.  **Latest multi-VM processed** (Più recente coerente tra più VM elaborato): questa opzione è disponibile solo per i piani di ripristino con almeno una macchina virtuale in cui è abilitata la coerenza tra più macchine virtuali. Le macchine virtuali che fanno parte di un gruppo di replica eseguono il failover nel punto di recupero coerente tra più macchine comune più recente. Le altre macchine virtuali eseguono il failover nel relativo punto di recupero più recente elaborato.  
    1.  **Latest multi-VM app-consistent** (Più recente coerente con l'applicazione tra più VM): questa opzione è disponibile solo per i piani di ripristino con almeno una macchina virtuale in cui è abilitata la coerenza tra più macchine virtuali. Le macchine virtuali che fanno parte di un gruppo di replica eseguono il failover nel punto di recupero coerente a livello applicazione tra più macchine comune più recente. Le altre macchine virtuali eseguono il failover nel relativo punto di recupero più recente coerente con l'applicazione.
    1.  **Personalizzato**: in caso di failover di test di una macchina virtuale, è possibile usare questa opzione per eseguire il failover in un determinato punto di ripristino.

    > [!NOTE]
    > La possibilità di scegliere un punto di ripristino è disponibile solo quando viene eseguito il failover in Azure.
    >
    >


1. Se alcune delle macchine virtuali nel piano di ripristino sono state sottoposte a failover in un'esecuzione precedente e ora sono attive nel percorso di origine e in quello di destinazione, è possibile usare l'opzione **Cambia direzione** per stabilire la direzione in cui deve essere eseguito il failover.
1. Se le macchine virtuali Hyper-V sono protette da un server VMM e si esegue il failover in Azure con la crittografia dei dati abilitata per il cloud, in **Chiave di crittografia** selezionare il certificato emesso quando è stata abilitata la crittografia dei dati durante l'installazione nel server VMM.
1. Selezionare **Arrestare la macchina virtuale prima di iniziare il failover** se si vuole provare ad arrestare le macchine virtuali di origine tramite Site Recovery prima di attivare il failover. Il failover continua anche se l'arresto ha esito negativo.  

    > [!NOTE]
    > Se le macchine virtuali Hyper-V sono protette, l'opzione di arresto prova anche a sincronizzare i dati locali che non sono ancora stati inviati al servizio prima di attivare il failover.
    >
    >

1. Nella pagina **Processi** è possibile seguire lo stato del failover. Anche se si verificano errori durante un failover non pianificato, il piano di ripristino viene eseguito fino al completamento.
1. Dopo il failover, convalidare la macchina virtuale eseguendo l'accesso. Per passare a un altro punto di ripristino della macchina virtuale, è possibile usare l'opzione **Modifica punto di ripristino**.
1. Quando la macchina virtuale sottoposta a failover è pronta, è possibile eseguire il **commit** del failover. **Il commit elimina tutti i punti di ripristino disponibili con il servizio** e l'opzione **Modifica punto di ripristino** non è più disponibile.

## <a name="planned-failover"></a>Failover pianificato
Le macchine virtuali e i server fisici protetti con Site Recovery supportano anche il **failover pianificato**. Il failover pianificato è un'opzione di failover senza alcuna perdita di dati. Quando viene attivato un failover pianificato, prima vengono arrestate le macchine virtuali di origine, vengono sincronizzati i dati più recenti e quindi viene attivato il failover.

> [!NOTE]
> Durante il failover di macchine virtuali Hyper-V da un sito locale a un altro sito locale, per tornare al sito locale primario è prima necessario **eseguire la replica inversa** della macchina virtuale nel sito primario e quindi attivare il failover. Se la macchina virtuale primaria non è disponibile, prima di avviare la **replica inversa** è necessario ripristinare la macchina virtuale da un backup.   
>
>
## <a name="failover-job"></a>Processo di failover

![Failover](./media/site-recovery-failover/FailoverJob.png)

L'attivazione di un failover comporta l'esecuzione dei passaggi riportati di seguito.

1. Verifica preliminare: questo passaggio garantisce che siano soddisfatte tutte le condizioni necessarie per il failover.
1. Failover: questo passaggio elabora i dati preparandoli in modo che possano essere usati per creare una macchina virtuale di Azure. Se si è scelto il punto di ripristino **Più recente**, viene creato un punto di ripristino dai dati che sono stati inviati al servizio.
1. Avvio: questo passaggio crea una macchina virtuale di Azure usando i dati elaborati nel passaggio precedente.

> [!WARNING]
> **Non annullare un failover in corso**: prima dell'avvio del failover, la replica della macchina virtuale viene arrestata. Se si **annulla** un processo in corso, il failover viene arrestato ma la macchina virtuale non avvia la replica. Non è possibile riavviare nuovamente la replica.
>
>

## <a name="time-taken-for-failover-to-azure"></a>Tempo impiegato per il failover in Azure

Il failover di macchine virtuali richiede in alcuni casi un passaggio aggiuntivo intermedio che in genere viene completato in circa 8-10 minuti. Nei casi seguenti, il tempo impiegato per eseguire il failover sarà superiore al consueto:

* Macchine virtuali VMware che usano una versione del servizio di mobilità precedente alla 9.8
* Server fisici
* Macchine virtuali VMware Linux
* Macchine virtuali Hyper-V protette come server fisici
* Macchine virtuali VMware in cui i driver seguenti non sono presenti come driver di avvio
    * storvsc
    * vmbus
    * storflt
    * intelide
    * atapi
* Macchine virtuali VMware che non dispongono del servizio DHCP abilitato indipendentemente che usino indirizzi IP statici o DHCP.

In tutti gli altri casi questo passaggio intermedio non è necessario e il tempo impiegato per il failover è inferiore.





## <a name="using-scripts-in-failover"></a>Uso di script per il failover
È possibile automatizzare determinate azioni durante un failover. A questo scopo, è possibile usare script o [runbook di Automazione di Azure](site-recovery-runbook-automation.md) nei [piani di ripristino](site-recovery-create-recovery-plans.md).

## <a name="post-failover-considerations"></a>Considerazioni successive al failover
Dopo il failover è consigliabile tenere presente quanto segue:
### <a name="retaining-drive-letter-after-failover"></a>Mantenimento della lettera di unità dopo il failover
Per mantenere la lettera di unità nelle macchine virtuali dopo il failover, è possibile impostare il **criterio SAN** per la macchina virtuale su **OnlineAll**. [Altre informazioni](https://support.microsoft.com/en-us/help/3031135/how-to-preserve-the-drive-letter-for-protected-virtual-machines-that-are-failed-over-or-migrated-to-azure).



## <a name="next-steps"></a>Passaggi successivi

> [!WARNING]
> Dopo aver eseguito il failover delle macchine virtuali e aver accertato la disponibilità del data center locale, è necessario [**riproteggere**](vmware-azure-reprotect.md) le macchine virtuali VMware nel data center locale.

Usare l'opzione [**Failover pianificato**](hyper-v-azure-failback.md) per eseguire il **failback** delle macchine virtuali Hyper-V in locale da Azure.

Se è stato eseguito il failover di una macchina virtuale Hyper-V in un data center locale gestito da un server VMM e il data center principale è disponibile, usare l'opzione **Esegui replica inversa** per avviare la replica al data center primario.
