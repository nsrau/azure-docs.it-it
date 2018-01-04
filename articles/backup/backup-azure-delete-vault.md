---
title: " Eliminare un insieme di credenziali dei servizi di ripristino in Azure | Microsoft Docs "
description: "In questo articolo viene illustrato come eliminare un insieme di credenziali di servizi di ripristino. L'articolo include passaggi la risoluzione dei problemi quando si tenta di eliminare un insieme di credenziali, ma non è possibile."
services: service-name
documentationcenter: dev-center-name
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 5fa08157-2612-4020-bd90-f9e3c3bc1806
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/20/2017
ms.author: markgal;trinadhk
ms.openlocfilehash: 4f4a92159b01b197984130c15195419e1b166fd3
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="delete-a-recovery-services-vault"></a>Eliminare un insieme di credenziali dei servizi di ripristino
In questo articolo viene illustrato come eliminare un insieme di credenziali di servizi di ripristino nel portale di Azure. Se si dispone di insiemi di credenziali di Backup, sono stati convertiti per gli insiemi di credenziali di servizi di ripristino.   

L'eliminazione di un insieme di credenziali dei servizi di ripristino è un processo costituito da un solo passaggio, *purché l'insieme di credenziali non contenga risorse*. Prima di potere eliminare un insieme di credenziali dei servizi di ripristino, è necessario rimuovere o eliminare tutte le risorse nell'insieme di credenziali. Se si cerca di eliminare un insieme di credenziali contenente risorse, viene visualizzato un errore analogo a quello riportato nell'immagine seguente:

![Errore di eliminazione dell'insieme di credenziali](./media/backup-azure-delete-vault/vault-deletion-error.png) <br/>

Fino a quando non si cancellano le risorse dall'insieme di credenziali, la selezione di **Riprova** produce lo stesso errore. Se si rimane bloccati su questo messaggio di errore, fare clic su **Annulla** e seguire la procedura seguente per eliminare le risorse nell'insieme di credenziali.

## <a name="removing-items-from-a-vault-protecting-a-vm"></a>Rimozione di elementi da un insieme di credenziali di protezione di una macchina virtuale
Se l'insieme di credenziali dei servizi di ripristino è già aperto, procedere al secondo passaggio.

1. Aprire il portale di Azure e dal dashboard aprire l'insieme di credenziali da eliminare.

   Se l'insieme di credenziali dei servizi di ripristino non è stato aggiunto al dashboard, scegliere **More Services** (Altri servizi) dal menu Hub e nell'elenco di risorse digitare **Servizi di ripristino**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Fare clic su **Insiemi di credenziali dei servizi di ripristino**.

   ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 1](./media/backup-azure-delete-vault/open-recovery-services-vault.png) <br/>

   Viene visualizzato l'elenco degli insiemi di credenziali dei servizi di ripristino. Selezionare dall'elenco l'insieme di credenziali da eliminare.

   ![scegliere un insieme di credenziali dall'elenco](./media/backup-azure-work-with-vaults/choose-vault-to-delete.png)
2. Nella visualizzazione dell'insieme di credenziali esaminare il riquadro **Informazioni di base** . Per eliminare un insieme di credenziali, è necessario che non siano presenti elementi protetti. Se il **elementi Backup** o **server di gestione di Backup** non Mostra zero, è necessario rimuovere tali elementi. È possibile eliminare l'insieme di credenziali che contiene dati.

    ![Cercare gli elementi protetti nel riquadro Informazioni di base](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

    Le macchine virtuali e i file o le cartelle sono considerati elementi di backup e sono elencati nell'area **Elementi di backup** del riquadro Informazioni di base. Un server DPM viene elencato nell'area **Server di gestione di backup** del riquadro Informazioni di base. **Elementi replicati** sono relativi al servizio Azure Site Recovery.
3. Per iniziare a rimuovere gli elementi protetti dall'insieme di credenziali, trovare gli elementi nell'insieme di credenziali. Nel dashboard dell'insieme di credenziali fare clic su **impostazioni**e quindi fare clic su **Backup elementi** per aprire il menu corrispondente.

    ![scegliere un insieme di credenziali dall'elenco](./media/backup-azure-delete-vault/open-settings-and-backup-items.png)

    Il **elementi Backup** menu sono elenchi separati, in base al tipo di elemento: macchine virtuali di Azure o cartelle di File (vedere l'immagine). L'elenco Tipo di elemento visualizzato per impostazione predefinita è Macchine virtuali di Azure. Per visualizzare l'elenco di elementi di tipo file/cartelle nell'insieme di credenziali, selezionare **File-cartelle** dal menu a discesa.
4. Prima di potere eliminare un elemento dall'insieme di credenziali che protegge una VM, è necessario arrestare il processo di backup ed eliminare i dati del punto di ripristino. Seguire questa procedura per ogni elemento dell'insieme di credenziali:

    a. Nel **gli elementi di Backup** menu, l'elemento e il menu di scelta rapida scegliere **Interrompi backup**.

    ![arrestare il processo di backup](./media/backup-azure-delete-vault/stop-the-backup-process.png)

    Verrà visualizzato il menu di interrompere il Backup.

    b. Nel **Interrompi Backup** menu dal **scegliere un'opzione** dal menu **Elimina dati di Backup** > digitare il nome dell'elemento > e fare clic su **Interrompi backup**.

    Digitare il nome dell'elemento per confermare che si desidera eliminarlo. Il pulsante **Interrompi backup** si attiva dopo aver verificato l'elemento. Se la finestra di dialogo per l'immissione del nome dell'elemento di backup non viene visualizzata, è stata selezionata l'opzione **Conserva i dati di backup**.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    È facoltativamente possibile specificare un motivo per l'eliminazione dei dati e aggiungere commenti. Dopo avere selezionato **Arresta backup**, è necessario consentire il completamento del processo di eliminazione prima di provare a eliminare l'insieme di credenziali. Per verificare il completamento del processo, controllare i messaggi di Azure ![delete backup data](./media/backup-azure-delete-vault/messages.png). <br/>
    Una volta completato il processo, il servizio invia un messaggio: il processo di backup è stato arrestato e i dati di backup è stati eliminati.

    c. Dopo l'eliminazione di un elemento dall'elenco, scegliere **Aggiorna** dal menu **Elementi di backup** per visualizzare gli elementi rimanenti nell'insieme di credenziali.

      ![Elimina dati di backup](./media/backup-azure-delete-vault/empty-items-list.png)

      Quando non esistono elementi nell'elenco, scorrere il **Essentials** riquadro nel menu di insieme di credenziali di servizi di ripristino. Nell'elenco non dovrebbero essere presenti **Elementi di backup**, **Server di gestione di backup** o **Elementi replicati**. Se nell'insieme di credenziali sono ancora presenti elementi, tornare al passaggio tre e scegliere un elenco di tipi di elementi diverso.  
5. Quando sulla barra degli strumenti dell'insieme di credenziali non sono più presenti elementi, fare clic su **Elimina**.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/delete-vault.png)
6. Per confermare l'eliminazione dell'insieme di credenziali, fare clic su **Sì**.

    L'insieme di credenziali viene eliminato e il portale torna al menu **Nuovo** del servizio.

## <a name="what-if-i-stopped-the-backup-process-but-retained-the-data"></a>Conseguenze dell'arresto del processo di backup e della conservazione dei dati
Se si arresta il processo di backup ma si *conservano* accidentalmente i dati, per potere eliminare l'insieme di credenziali è necessario eliminare prima di tutto i dati di backup. Per eliminare i dati di backup:

1. Nel **gli elementi di Backup** menu, l'elemento e scegliere il menu di scelta rapida **Elimina dati di backup**.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/delete-backup-data-menu.png)

    Il **Elimina dati di Backup** menu viene aperto.
2. Nel **Elimina dati di Backup** menu, digitare il nome dell'elemento e fare clic su **eliminare**.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/delete-retained-vault.png)

    Dopo avere eliminato i dati, tornare al passaggio 4c e continuare con il processo.

## <a name="delete-a-vault-used-to-protect-a-dpm-server"></a>Eliminare un insieme di credenziali usato per proteggere un server DPM
Per potere eliminare un insieme di credenziali usato per proteggere un server DPM, è necessario cancellare prima di tutto eventuali punti di ripristino creati e quindi annullare la registrazione del server dall'insieme di credenziali.

Per eliminare i dati associati al gruppo di protezione:

1. Nella Console amministrazione DPM fare clic su **Protezione** > selezionare un gruppo protezione dati > scegliere il membro del gruppo protezione dati > quindi fare clic su **Rimuovi** sulla barra multifunzione.

  Selezionare il membro del gruppo protezione dati per attivare il pulsante **Rimuovi** nella barra multifunzione. Nell'esempio il nome del membro è **dummyvm9**. Per selezionare più membri nel gruppo protezione dati, tenere premuto il tasto CTRL e fare clic sui membri.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/az-portal-delete-protection-group.png)

    Viene visualizzata la finestra di dialogo **Arresta protezione** .
2. Nella finestra di dialogo **Arresta protezione** selezionare **Elimina dati protetti** e quindi fare clic su **Arresta protezione**.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/delete-dpm-protection-group.png)

    Per eliminare un insieme di credenziali, è necessario cancellare o eliminare, i dati protetti dell'insieme di credenziali. Se sono presenti molti punti di ripristino e dati del gruppo protezione dati, può richiedere alcuni minuti per eliminare i dati. Il **Arresta protezione dati** finestra di dialogo appare quando il processo è stata completata.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/success-deleting-protection-group.png)
3. Continuare questo processo per tutti i membri di tutti i gruppi di protezione.

    Rimuovere tutti i dati protetti e tutti i gruppi di protezione dati.
4. Dopo avere eliminato tutti i membri dal gruppo di protezione, passare al portale di Azure. Aprire il dashboard dell'insieme di credenziali e assicurarsi che non siano presenti **Elementi di backup**, **Server di gestione di backup** o **Elementi replicati**. Sulla barra degli strumenti dell'insieme di credenziali fare clic su **Elimina**.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/delete-vault.png)

    Se nell'insieme di credenziali sono registrati server di gestione di backup, non sarà possibile eliminare l'insieme di credenziali anche se nell'insieme di credenziali non sono presenti dati. Se è già stata eseguita l'eliminazione dei server di gestione di backup associati all'insieme di credenziali, ma nel riquadro **Informazioni di base** vengono ancora visualizzati server, vedere [Trovare i server di gestione di backup registrati nell'insieme di credenziali](backup-azure-delete-vault.md#find-the-backup-management-servers-registered-to-the-vault).
5. Per confermare l'eliminazione dell'insieme di credenziali, fare clic su **Sì**.

    L'insieme di credenziali viene eliminato e il portale torna al menu **Nuovo** del servizio.

## <a name="delete-a-vault-used-to-protect-a-production-server"></a>Eliminare un insieme di credenziali usato per proteggere un server di produzione
Per potere eliminare un insieme di credenziali usato per proteggere un server di produzione, è prima di tutto necessario eliminare o annullare la registrazione del server dall'insieme di credenziali.

Per eliminare il server di produzione associato all'insieme di credenziali:

1. Nel portale di Azure aprire il dashboard dell'insieme di credenziali e fare clic su **Impostazioni** > **Infrastruttura di backup** > **Server di produzione**.

    ![Aprire il menu di server di produzione](./media/backup-azure-delete-vault/delete-production-server.png)

    Il **server di produzione** menu viene aperto e sono elencati tutti i server di produzione nell'insieme di credenziali.

    ![elenco di server di produzione](./media/backup-azure-delete-vault/list-of-production-servers.png)
2. Nel **server di produzione** menu, fare doppio clic sul server e fare clic su **eliminare**.

    ![eliminare il server di produzione ](./media/backup-azure-delete-vault/delete-server-on-production-server-blade.png)

    Il **eliminare** menu viene aperto.

    ![eliminare il server di produzione ](./media/backup-azure-delete-vault/delete-blade.png)
3. Nel **eliminare** menu, verificare il nome del server e fare clic su **eliminare**. È necessario immettere correttamente il nome del server per attivare il pulsante **Elimina**.

    Dopo l'eliminazione dell'insieme di credenziali, si riceverà un messaggio che indica che l'insieme di credenziali è stato eliminato. Dopo l'eliminazione di tutti i server nell'insieme di credenziali, tornare al pannello Informazioni di base nel dashboard dell'insieme di credenziali.
4. Nel dashboard dell'insieme di credenziali assicurarsi che non siano presenti **Elementi di backup**, **Server di gestione di backup** o **Elementi replicati**. Sulla barra degli strumenti dell'insieme di credenziali fare clic su **Elimina**.
5. Per confermare l'eliminazione dell'insieme di credenziali, fare clic su **Sì**.

    L'insieme di credenziali viene eliminato e il portale torna al menu **Nuovo** del servizio.

## <a name="find-the-backup-management-servers-registered-to-the-vault"></a>Trovare i server di gestione di backup registrati nell'insieme di credenziali
Se in un insieme di credenziali sono registrati più server, potrebbe essere difficile ricordarli. Per visualizzare i server registrati nell'insieme di credenziali ed eliminarli:

1. Aprire il dashboard dell'insieme di credenziali.
2. Nel **Essentials** riquadro, fare clic su **impostazioni** per aprire il menu corrispondente.

    ![Aprire il menu di impostazioni](./media/backup-azure-delete-vault/backup-vault-click-settings.png)
3. Nel **impostazioni** menu, fare clic su **infrastruttura Backup**.
4. Nel **Backup infrastruttura** menu, fare clic su **il server di gestione di Backup**. Verrà visualizzato il menu di server di gestione di Backup.

    ![elenco di server di gestione di backup](./media/backup-azure-delete-vault/list-of-backup-management-servers.png)
5. Per eliminare un server dall'elenco, fare clic con il pulsante destro del mouse sul nome del server e quindi scegliere **Elimina**.
    Il **eliminare** menu viene aperto.
6. Nel **eliminare** menu, specificare il nome del server. Se il nome è lungo, è possibile copiarlo e incollarlo dall'elenco di Server di gestione di backup. Fare quindi clic su **Elimina**.  
