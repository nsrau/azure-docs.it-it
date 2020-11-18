---
title: Esercitazione per eseguire l'analisi del ripristino di emergenza delle VM di Azure con Azure Site Recovery
description: In questa esercitazione si eseguirà l'analisi del ripristino di emergenza in un'altra area con Azure Site Recovery.
services: site-recovery
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: c7cd1898f27f3b7255009efb40f6bcc8938dbf9e
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395651"
---
# <a name="tutorial-run-a-disaster-recovery-drill-for-azure-vms"></a>Esercitazione: Eseguire un'analisi del ripristino di emergenza per le macchine virtuali di Azure

Informazioni su come eseguire un'analisi del ripristino di emergenza in un'area di Azure differente per le VM di Azure con [Azure Site Recovery](site-recovery-overview.md). In questo articolo si apprenderà come:

> [!div class="checklist"]
> * Verificare i prerequisiti
> * Controllare le impostazioni della macchina virtuale prima dell'analisi
> * Eseguire un failover di test
> * Eseguire la pulizia dopo l'analisi


> [!NOTE]
> Questa esercitazione illustra i passaggi minimi per eseguire un'analisi del ripristino di emergenza. Se si vuole eseguire un'analisi con test completi dell'infrastruttura, vedere le informazioni su [rete](azure-to-azure-about-networking.md), [automazione](azure-to-azure-powershell.md) e [risoluzione dei problemi](azure-to-azure-troubleshoot-errors.md) delle macchine virtuali di Azure.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario abilitare il ripristino di emergenza per una o più macchine virtuali di Azure. A questo scopo, [completare la prima esercitazione](azure-to-azure-tutorial-enable-replication.md) della serie.

## <a name="verify-vm-settings"></a>Verificare le impostazioni delle macchine virtuali

1. Nell'insieme di credenziali > **Elementi replicati** selezionare la macchina virtuale.

    ![Opzione per aprire la pagina Ripristino di emergenza nelle proprietà della macchina virtuale](./media/azure-to-azure-tutorial-dr-drill/vm-settings.png)

2. Nella pagina **Panoramica** verificare che la macchina virtuale sia protetta e integra.
3. Quando si esegue un failover di test, si seleziona una rete virtuale di Azure nell'area di destinazione. La macchina virtuale di Azure creata dopo il failover è posizionata in questa rete. 

    - In questa esercitazione si seleziona una rete esistente quando si esegue il failover di test.
    - È consigliabile scegliere una rete non di produzione per l'analisi, in modo che gli indirizzi IP e i componenti di rete rimangano disponibili nelle reti di produzione.
   - È anche possibile preconfigurare le impostazioni di rete da usare per il failover di test. Le impostazioni granulari che è possibile assegnare per ogni NIC includono subnet, indirizzo IP privato, indirizzo IP pubblico, bilanciamento del carico e gruppo di sicurezza di rete. In questo caso non verrà usato questo metodo. Per saperne di più, [leggere questo articolo](azure-to-azure-customize-networking.md#customize-failover-and-test-failover-networking-configurations).


## <a name="run-a-test-failover"></a>Eseguire un failover di test


1. Nella pagina **Panoramica** selezionare **Failover di test**.

    
    ![Pulsante Failover di test per l'elemento replicato](./media/azure-to-azure-tutorial-dr-drill/test-failover-button.png)

2. In **Failover di test** scegliere un punto di ripristino. Viene creata la macchina virtuale di Azure nell'area di destinazione usando i dati di questo punto di ripristino.
  
   - **Elaborato più recente**: Usa l'ultimo punto di ripristino elaborato da Site Recovery. Viene visualizzato il timestamp. Non viene impiegato tempo per l'elaborazione dei dati e si ottiene quindi un valore RTO (Recovery Time Objective) basso.
   -  **Più recente**: Vengono elaborati tutti i dati inviati a Site Recovery per creare un punto di ripristino per ogni macchina virtuale e quindi viene eseguito il failover in tale punto di ripristino. Offre il valore RPO (Recovery Point Objective) più basso perché tutti i dati vengono replicati in Site Recovery all'attivazione del failover.
   - **Coerente con l'app più recente**: Questa opzione esegue il failover delle macchine virtuali al più recente punto di recupero coerente con l'app. Viene visualizzato il timestamp.
   - **Custom**: esegue il failover a un determinato punto di ripristino. Questa opzione è disponibile solo quando si esegue il failover di una singola macchina virtuale e non si usa un piano di ripristino.

3. In **Rete virtuale di Azure**, selezionare la rete di destinazione in cui inserire le macchine virtuali di Azure create dopo il failover. Selezionare una rete non di produzione, se possibile, e non la rete creata quando è stata abilitata la replica.

    ![Pagina delle impostazioni del failover di test](./media/azure-to-azure-tutorial-dr-drill/test-failover-settings.png)    

4. Per avviare il failover, fare clic su **OK**.
5. Monitorare il failover di test nelle notifiche.

    ![Notifica di stato](./media/azure-to-azure-tutorial-dr-drill/notification-start-test-failover.png) ![Notifica di completamento](./media/azure-to-azure-tutorial-dr-drill/notification-finish-test-failover.png)     


5. Al termine del failover, la macchina virtuale di Azure creata nell'area di destinazione viene visualizzata nel portale di Azure in **Macchine virtuali**. Verificare che la macchina virtuale sia in esecuzione, che sia stata dimensionata in modo adeguato e che sia connessa alla rete selezionata.

## <a name="clean-up-resources"></a>Pulire le risorse

1. Nella pagina **Essentials** selezionare **Pulisci failover di test**.

    ![Pulsante per avviare il processo di pulizia](./media/azure-to-azure-tutorial-dr-drill/select-cleanup.png)

2. In **Pulizia del failover di test** > **Note**, annotare e salvare eventuali osservazioni associate al failover di test. 
3. Selezionare **Il test è stato completato** per eliminare le macchine virtuali create durante il failover di test.

    ![Pagina con opzioni di pulizia](./media/azure-to-azure-tutorial-dr-drill/cleanup-failover.png)

4. Monitorare lo stato della pulizia nelle notifiche.

    ![Notifica di stato pulizia](./media/azure-to-azure-tutorial-dr-drill/notification-start-cleanup.png) ![Notifica di completamento pulizia](./media/azure-to-azure-tutorial-dr-drill/notification-finish-cleanup.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata eseguita un'analisi del ripristino di emergenza per verificare che il failover funzioni come previsto. A questo punto è possibile provare un failover completo.

> [!div class="nextstepaction"]
> [Eseguire un failover di produzione](azure-to-azure-tutorial-failover-failback.md)
