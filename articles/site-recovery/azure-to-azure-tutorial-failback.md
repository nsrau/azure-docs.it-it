---
title: Esercitazione per eseguire il failback delle VM di Azure in un'area primaria durante il ripristino di emergenza con il servizio Azure Site Recovery.
description: Esercitazione per apprendere come eseguire il failback delle VM di Azure in un'area primaria con Azure Site Recovery.
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: 5c127010a7988bf08c77340a4fc10bb32dc76f87
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93393951"
---
# <a name="tutorial-fail-back-azure-vm-to-the-primary-region"></a>Esercitazione: Eseguire il failback della macchina virtuale di Azure nell'area primaria

Dopo aver eseguito il failover di una macchina virtuale di Azure in un'area di Azure secondaria, seguire questa esercitazione per eseguire il failback della macchina virtuale nell'area di Azure primaria, usando [Azure Site Recovery](site-recovery-overview.md).  In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> 
> * Esaminare i prerequisiti.
> * Eseguire il failback della VM nell'area secondaria.
> * Riproteggere le macchine virtuali primarie nell'area secondaria.
> 
> [!NOTE]
> Questa esercitazione illustra come eseguire il failback con passaggi minimi. Se si vuole eseguire un failover con le impostazioni complete, vedere le informazioni su [rete](azure-to-azure-about-networking.md), [automazione](azure-to-azure-powershell.md) e [risoluzione dei problemi](azure-to-azure-troubleshoot-errors.md) delle macchine virtuali di Azure.



## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario aver:

1. [Configurato la replica](azure-to-azure-tutorial-enable-replication.md) per almeno una macchina virtuale di Azure e aver provato a eseguire un'[analisi del ripristino di emergenza](azure-to-azure-tutorial-dr-drill.md).
2. [Eseguito il failover della macchina virtuale](azure-to-azure-tutorial-failover-failback.md) dall'area primaria a un'area secondaria e averla riprotetta in modo che venga replicata dall'area secondaria alla primaria. 
3. Controllare che l'area primaria sia disponibile e che sia possibile crearvi nuove risorse e accedere a tali risorse.

## <a name="fail-back-to-the-primary-region"></a>Eseguire il failback nell'area primaria

Dopo la riprotezione delle VM, è possibile eseguire il failback nell'area primaria in base alle esigenze.

1. Nell'insieme di credenziali > **Elementi replicati** selezionare la macchina virtuale.

2. Nella pagina Panoramica verificare che la macchina virtuale sia integra e che la sincronizzazione sia completata, prima di eseguire un failover. La macchina virtuale dovrebbe trovarsi in uno stato *Protetto*.

    ![Pagina Panoramica VM che mostra la macchina virtuale in uno stato protetto](./media/azure-to-azure-tutorial-failback/protected-state.png)

3. Nella pagina Panoramica selezionare **Failover**. Poiché questa volta non si sta eseguendo un failover di test, viene richiesto di verificare.

    [Pagina che mostra l'accettazione dell'esecuzione del failover senza un failover di test](./media/azure-to-azure-tutorial-failback/no-test.png)

4. In **Failover** prendere nota della direzione da secondaria a primaria e selezionare un punto di ripristino. Viene creare la macchina virtuale di Azure nell'area di destinazione (area primaria) usando i dati di questo punto.
   - **Elaborato più recente**: Usa l'ultimo punto di ripristino elaborato da Site Recovery. Viene visualizzato il timestamp. Non viene impiegato tempo per l'elaborazione dei dati e si ottiene quindi un valore RTO (Recovery Time Objective) basso.
   -  **Più recente**: Vengono elaborati tutti i dati inviati a Site Recovery per creare un punto di ripristino per ogni macchina virtuale e quindi viene eseguito il failover in tale punto di ripristino. Offre il valore RPO (Recovery Point Objective) più basso perché tutti i dati vengono replicati in Site Recovery all'attivazione del failover.
   - **Coerente con l'app più recente**: Questa opzione esegue il failover delle macchine virtuali al più recente punto di recupero coerente con l'app. Viene visualizzato il timestamp.
   - **Custom**: esegue il failover a un determinato punto di ripristino. Questa opzione è disponibile solo quando si esegue il failover di una singola macchina virtuale e non si usa un piano di ripristino.

    > [!NOTE]
    > Se si effettua il failover di una macchina virtuale a cui si aggiunge un disco, i punti di replica visualizzeranno i dischi disponibili per il ripristino dopo aver abilitato la replica per la macchina virtuale. Ad esempio, un punto di replica che è stato creato prima dell'aggiunta di un secondo disco viene visualizzato come "1 di 2 dischi".

4. Selezionare **Shut down machine before beginning failover** (Arrestare la macchina prima di iniziare il failover) per provare ad arrestare le VM di origine con Site Recovery prima di avviare il failover. L'arresto del sistema consente di evitare perdite di dati. Il failover continua anche se l'arresto ha esito negativo. 

    ![Pagina impostazioni failover](./media/azure-to-azure-tutorial-failback/failover.png)    

3. Per avviare il failover, fare clic su **OK**.
4. Monitorare il failover nelle notifiche.

    ![Notifica per lo stato del failover](./media/azure-to-azure-tutorial-failback/notification-progress.png)  
    ![Notifica per il completamento del failover](./media/azure-to-azure-tutorial-failback/notification-success.png)   

## <a name="reprotect-vms"></a>Riproteggere le VM

Dopo aver eseguito il failback delle VM nell'area primaria, è necessario riproteggerle, in modo che inizino nuovamente la replica nell'area secondaria.

1. Nella pagina **Panoramica** per la VM selezionare **Riproteggi**.

    ![Pulsante per riproteggere dall'area primaria](./media/azure-to-azure-tutorial-failback/reprotect.png)  

2. Esaminare le impostazioni di destinazione per l'area primaria. Le risorse contrassegnate come nuove verranno create da Site Recovery nell'ambito dell'operazione di riprotezione.
3. Selezionare **OK** per avviare il processo di riprotezione. Il processo invia i dati iniziali al percorso di destinazione e quindi replica le informazioni differenziali per le macchine virtuali nella destinazione.

     ![Pagina che mostra le impostazioni di replica](./media/azure-to-azure-tutorial-failback/replication-settings.png) 

4. Monitorare lo stato di riprotezione nelle notifiche. 

    ![Notifica di stato riprotezione](./media/azure-to-azure-tutorial-failback/notification-reprotect-start.png) [Notifica di stato riprotezione](./media/azure-to-azure-tutorial-failback/notification-reprotect-finish.png)
    
  

## <a name="clean-up-resources"></a>Pulire le risorse

Per le VM con dischi gestiti, dopo aver completato il failback e aver riprotetto le VM per la replica dall'area primaria alla secondaria, Site Recovery pulisce automaticamente le macchine nell'area secondaria del ripristino di emergenza. Non è necessario eliminare manualmente le macchine virtuali e le schede di interfaccia di rete nell'area secondaria. VM con dischi non gestiti non puliti.

Se la replica viene disabilitata completamente dopo il failback, Site Recovery pulisce i computer protetti. In questo caso, pulisce anche i dischi per le macchine virtuali che non usano dischi gestiti. 
 
## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato eseguito il failback delle VM dall'area secondaria all'area primaria. Questo è l'ultimo passaggio del processo che include l'abilitazione della replica per una macchina virtuale, l'analisi del ripristino di emergenza, il failover dall'area primaria all'area secondaria e infine il failback.

> [!div class="nextstepaction"]
> A questo punto è possibile provare a eseguire il ripristino di emergenza in Azure per una [macchina virtuale locale](vmware-azure-tutorial-prepare-on-premises.md)

