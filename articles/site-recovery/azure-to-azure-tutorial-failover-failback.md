---
title: Esercitazione per eseguire il failover delle VM di Azure in un'area secondaria per il ripristino di emergenza con il servizio Azure Site Recovery.
description: Esercitazione per imparare a eseguire il failover e riproteggere le VM di Azure replicate in un'area di Azure secondaria per il ripristino di emergenza con il servizio Azure Site Recovery.
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: 99263c83d25542073d63c1cba394a147bd5b2170
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392779"
---
# <a name="tutorial-fail-over-azure-vms-to-a-secondary-region"></a>Esercitazione: Eseguire il failover di macchine virtuali di Azure in un'area secondaria

Informazioni su come eseguire il failover di macchine virtuali di Azure abilitate per il ripristino di emergenza con il servizio [Azure Site Recovery](site-recovery-overview.md) in un'area secondaria di Azure. Dopo il failover, è necessario riproteggere le VM nell'area di destinazione in modo che possano essere nuovamente replicate nell'area primaria. In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Verificare i prerequisiti
> * Verificare le impostazioni delle macchine virtuali
> * Eseguire un failover nell'area secondaria
> * Avviare di nuovo la replica della macchina virtuale nell'area primaria.


> [!NOTE]
> Questa esercitazione illustra come eseguire il failover delle VM con passaggi minimi. Se si vuole eseguire un failover con le impostazioni complete, vedere le informazioni su [rete](azure-to-azure-about-networking.md), [automazione](azure-to-azure-powershell.md) e [risoluzione dei problemi](azure-to-azure-troubleshoot-errors.md) delle macchine virtuali di Azure.



## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario aver:

1. Configurato la replica per una o più macchine virtuali di Azure. Se non è già stato fatto, [completare la prima esercitazione](azure-to-azure-tutorial-enable-replication.md) in questa serie.
2. È consigliabile [eseguire un'analisi del ripristino di emergenza](azure-to-azure-tutorial-dr-drill.md) per le macchine virtuali replicate. Eseguire un'analisi prima di eseguire un failover completo garantisce che tutto funzioni come previsto, senza alcun impatto sull'ambiente di produzione. 


## <a name="verify-the-vm-settings"></a>Verificare le impostazioni della macchina virtuale

1. Nell'insieme di credenziali > **Elementi replicati** selezionare la macchina virtuale.

    ![Opzione per aprire le proprietà della macchina virtuale nella pagina Panoramica](./media/azure-to-azure-tutorial-failover-failback/vm-settings.png)

2. Nella pagina **Panoramica** della macchina virtuale verificare che la macchina virtuale sia protetta e integra, prima di eseguire un failover.
    ![Pagina per verificare lo stato e le proprietà della macchina virtuale](./media/azure-to-azure-tutorial-failover-failback/vm-state.png)

3. Prima di eseguire il failover, controllare quanto segue:
    - La macchina virtuale esegue un sistema operativo [Windows](azure-to-azure-support-matrix.md#windows) o [Linux](azure-to-azure-support-matrix.md#replicated-machines---linux-file-systemguest-storage) supportato.
    - La macchina virtuale è conforme con i requisiti di [calcolo](azure-to-azure-support-matrix.md#replicated-machines---compute-settings), [archiviazione](azure-to-azure-support-matrix.md#replicated-machines---storage) e [rete](azure-to-azure-support-matrix.md#replicated-machines---networking).

## <a name="run-a-failover"></a>Eseguire un failover


1. Nella pagina **Panoramica** della macchina virtuale selezionare **Failover**.

    ![Pulsante Failover per l'elemento replicato](./media/azure-to-azure-tutorial-failover-failback/failover-button.png)

3. In **Failover** scegliere un punto di ripristino. Viene creata la macchina virtuale di Azure nell'area di destinazione usando i dati di questo punto di ripristino.
  
   - **Elaborato più recente**: Usa l'ultimo punto di ripristino elaborato da Site Recovery. Viene visualizzato il timestamp. Non viene impiegato tempo per l'elaborazione dei dati e si ottiene quindi un valore RTO (Recovery Time Objective) basso.
   -  **Più recente**: Vengono elaborati tutti i dati inviati a Site Recovery per creare un punto di ripristino per ogni macchina virtuale e quindi viene eseguito il failover in tale punto di ripristino. Offre il valore RPO (Recovery Point Objective) più basso perché tutti i dati vengono replicati in Site Recovery all'attivazione del failover.
   - **Coerente con l'app più recente**: Questa opzione esegue il failover delle macchine virtuali al più recente punto di recupero coerente con l'app. Viene visualizzato il timestamp.
   - **Custom**: esegue il failover a un determinato punto di ripristino. Questa opzione è disponibile solo quando si esegue il failover di una singola macchina virtuale e non si usa un piano di ripristino.

    > [!NOTE]
    > Se è stato aggiunto un disco a una macchina virtuale dopo aver abilitato la replica, i punti di replica visualizzeranno i dischi disponibili per il ripristino. Ad esempio, un punto di replica creato prima dell'aggiunta di un secondo disco viene visualizzato come "1 di 2 dischi".

4. Selezionare **Shut down machine before beginning failover** (Arrestare la macchina prima di iniziare il failover) per provare ad arrestare le VM di origine con Site Recovery prima di avviare il failover. L'arresto del sistema consente di evitare perdite di dati. Il failover continua anche se l'arresto ha esito negativo. 

    ![Pagina impostazioni failover](./media/azure-to-azure-tutorial-failover-failback/failover-settings.png)    

3. Per avviare il failover, fare clic su **OK**.
4. Monitorare il failover nelle notifiche.

    ![Notifica di stato](./media/azure-to-azure-tutorial-failover-failback/notification-failover-start.png) ![Notifica di completamento](./media/azure-to-azure-tutorial-failover-failback/notification-failover-finish.png)     

5. Al termine del failover, la macchina virtuale di Azure creata nell'area di destinazione viene visualizzata in **Macchine virtuali**. Verificare che la macchina virtuale sia in esecuzione e sia stata dimensionata in modo adeguato. Per usare un punto di ripristino diverso per la macchina virtuale, selezionare **Modificare il punto di ripristino** nella pagina **Essentials**.
6. Quando si è soddisfatti della macchina virtuale sottoposta a failover, selezionare **Commit** nella pagina Panoramica per completare il failover.

    ![Pulsante Commit](./media/azure-to-azure-tutorial-failover-failback/commit-button.png) 

7. In **Commit** selezionare **OK** per confermare. Commit elimina tutti i punti di ripristino disponibili per la macchina virtuale in Site Recovery, quindi non sarà possibile modificare il punto di ripristino.

8. Monitorare lo stato di commit nelle notifiche.

    ![Notifica di stato commit](./media/azure-to-azure-tutorial-failover-failback/notification-commit-start.png) ![Notifica di completamento commit](./media/azure-to-azure-tutorial-failover-failback/notification-commit-finish.png)    

9. Site Recovery non esegue la pulizia della VM di origine dopo il failover. È necessario eseguire questa operazione manualmente.


## <a name="reprotect-the-vm"></a>Riproteggere la macchina virtuale

Dopo il failover, è necessario proteggere la VM nell'area secondaria in modo che possa essere nuovamente replicata nell'area primaria. 

1. Verificare che lo **stato** della VM sia *Commit del failover eseguito* prima di iniziare.
2. Controllare che sia possibile accedere all'area primaria e di essere autorizzati a crearvi nuove macchine virtuali.
3. Nella pagina **Panoramica** per la macchina virtuale selezionare **Riproteggi**.

   ![Pulsante che abilita la riprotezione per una macchina virtuale.](./media/azure-to-azure-tutorial-failover-failback/reprotect-button.png)

4. In **Riproteggi** verificare la direzione di replica (dall'area secondaria alla primaria) ed esaminare le impostazioni di destinazione per l'area primaria. Le risorse contrassegnate come nuove verranno create da Site Recovery nell'ambito dell'operazione di riprotezione.

     ![Pagina delle impostazioni di riprotezione](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

6. Selezionare **OK** per avviare il processo di riprotezione. Il processo invia i dati iniziali al percorso di destinazione e quindi replica le informazioni differenziali per le macchine virtuali nella destinazione.
7. Monitorare lo stato di riprotezione nelle notifiche. 

    ![Notifica di stato riprotezione](./media/azure-to-azure-tutorial-failover-failback/notification-reprotect-start.png) ![Notifica di completamento riprotezione](./media/azure-to-azure-tutorial-failover-failback/notification-reprotect-finish.png)
    

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato eseguito il failover dall'area primaria a quella secondaria ed è stata avviata la replica delle VM nell'area primaria. Ora è possibile eseguire il failback dall'area secondaria all'area primaria.

> [!div class="nextstepaction"]
> [Eseguire il failback nell'area primaria](azure-to-azure-tutorial-failback.md)
