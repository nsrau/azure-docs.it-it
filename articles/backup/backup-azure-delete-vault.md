---
title: " Eliminare un insieme di credenziali dei servizi di ripristino in Azure | Microsoft Docs "
description: Questo articolo descrive come eliminare un insieme di credenziali dei servizi di ripristino. L'articolo include procedure per la risoluzione dei problemi quando si tenta di eliminare un insieme di credenziali senza riuscirci.
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
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="delete-a-recovery-services-vault"></a>Eliminare un insieme di credenziali dei servizi di ripristino
Questo articolo descrive come eliminare un insieme di credenziali dei servizi di ripristino nel portale di Azure. Se erano presenti insiemi di credenziali di backup, sono stati convertiti in insiemi di credenziali dei servizi di ripristino.   

L'eliminazione di un insieme di credenziali dei servizi di ripristino è un processo costituito da un solo passaggio, *purché l'insieme di credenziali non contenga risorse*. Prima di potere eliminare un insieme di credenziali dei servizi di ripristino, è necessario rimuovere o eliminare tutte le risorse nell'insieme di credenziali. Se si cerca di eliminare un insieme di credenziali contenente risorse, viene visualizzato un errore analogo a quello riportato nell'immagine seguente:

![Errore di eliminazione dell'insieme di credenziali](./media/backup-azure-delete-vault/vault-deletion-error.png) <br/>

Fino a quando non si cancellano le risorse dall'insieme di credenziali, la selezione di **Riprova** produce lo stesso errore. Se si rimane bloccati su questo messaggio di errore, fare clic su **Annulla** e seguire la procedura seguente per eliminare le risorse nell'insieme di credenziali.

## <a name="removing-items-from-a-vault-protecting-a-vm"></a>Rimozione di elementi da un insieme di credenziali che protegge una VM
Se l'insieme di credenziali dei servizi di ripristino è già aperto, procedere al secondo passaggio.

1. Aprire il portale di Azure e dal dashboard aprire l'insieme di credenziali da eliminare.

   Se l'insieme di credenziali dei servizi di ripristino non è stato aggiunto al dashboard, scegliere **More Services** (Altri servizi) dal menu Hub e nell'elenco di risorse digitare **Servizi di ripristino**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Fare clic su **Insiemi di credenziali dei servizi di ripristino**.

   ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 1](./media/backup-azure-delete-vault/open-recovery-services-vault.png) <br/>

   Viene visualizzato l'elenco degli insiemi di credenziali dei servizi di ripristino. Selezionare dall'elenco l'insieme di credenziali da eliminare.

   ![scegliere un insieme di credenziali dall'elenco](./media/backup-azure-work-with-vaults/choose-vault-to-delete.png)
2. Nella visualizzazione dell'insieme di credenziali esaminare il riquadro **Informazioni di base** . Per eliminare un insieme di credenziali, è necessario che non siano presenti elementi protetti. Se **Elementi di backup** o **Server di gestione di backup** non contiene il valore zero, è necessario rimuovere tali elementi. Non è possibile eliminare l'insieme di credenziali se contiene dati.

    ![Cercare gli elementi protetti nel riquadro Informazioni di base](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

    Le macchine virtuali e i file o le cartelle sono considerati elementi di backup e sono elencati nell'area **Elementi di backup** del riquadro Informazioni di base. Un server DPM viene elencato nell'area **Server di gestione di backup** del riquadro Informazioni di base. **Elementi replicati** sono relativi al servizio Azure Site Recovery.
3. Per iniziare a rimuovere gli elementi protetti dall'insieme di credenziali, trovare gli elementi nell'insieme di credenziali. Nel dashboard dell'insieme di credenziali fare clic su **Impostazioni**, quindi su **Elementi di backup** per aprire il manu.

    ![scegliere un insieme di credenziali dall'elenco](./media/backup-azure-delete-vault/open-settings-and-backup-items.png)

    Il menu **Elementi di backup** include elenchi separati, in base al tipo di elemento, macchine virtuali di Azure o file/cartelle, come illustrato nell'immagine. L'elenco Tipo di elemento visualizzato per impostazione predefinita è Macchine virtuali di Azure. Per visualizzare l'elenco di elementi di tipo file/cartelle nell'insieme di credenziali, selezionare **File-cartelle** dal menu a discesa.
4. Prima di potere eliminare un elemento dall'insieme di credenziali che protegge una VM, è necessario arrestare il processo di backup ed eliminare i dati del punto di ripristino. Seguire questa procedura per ogni elemento dell'insieme di credenziali:

    a. Nel menu **Elementi di backup** fare clic con il pulsante destro del mouse sull'elemento, quindi scegliere **Arresta backup** dal menu contestuale.

    ![arrestare il processo di backup](./media/backup-azure-delete-vault/stop-the-backup-process.png)

    Verrà visualizzato il menu Arresta backup.

    b. In **Arresta backup** dal menu **Scegliere un'opzione** selezionare **Elimina i dati di backup** > immettere il nome dell'elemento > quindi fare clic su **Arresta backup**.

    Digitare il nome dell'elemento per confermare che si desidera eliminarlo. Il pulsante **Interrompi backup** si attiva dopo aver verificato l'elemento. Se la finestra di dialogo per l'immissione del nome dell'elemento di backup non viene visualizzata, è stata selezionata l'opzione **Conserva i dati di backup**.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    È facoltativamente possibile specificare un motivo per l'eliminazione dei dati e aggiungere commenti. Dopo avere selezionato **Arresta backup**, è necessario consentire il completamento del processo di eliminazione prima di provare a eliminare l'insieme di credenziali. Per verificare il completamento del processo, controllare i messaggi di Azure ![delete backup data](./media/backup-azure-delete-vault/messages.png). <br/>
    Al termine del processo, il servizio invia un messaggio che indica che il processo di backup è stato arrestato e che i dati di backup sono stati eliminati.

    c. Dopo l'eliminazione di un elemento dall'elenco, scegliere **Aggiorna** dal menu **Elementi di backup** per visualizzare gli elementi rimanenti nell'insieme di credenziali.

      ![Elimina dati di backup](./media/backup-azure-delete-vault/empty-items-list.png)

      Quando nell'elenco non sono presenti elementi, passare al riquadro **Informazioni di base** nel menu Insieme di credenziali di Servizi di ripristino. Nell'elenco non dovrebbero essere presenti **Elementi di backup**, **Server di gestione di backup** o **Elementi replicati**. Se nell'insieme di credenziali sono ancora presenti elementi, tornare al passaggio tre e scegliere un elenco di tipi di elementi diverso.  
5. Quando sulla barra degli strumenti dell'insieme di credenziali non sono più presenti elementi, fare clic su **Elimina**.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/delete-vault.png)
6. Per confermare l'eliminazione dell'insieme di credenziali, fare clic su **Sì**.

    L'insieme di credenziali viene eliminato e il portale torna al menu **Nuovo** del servizio.

## <a name="what-if-i-stopped-the-backup-process-but-retained-the-data"></a>Conseguenze dell'arresto del processo di backup e della conservazione dei dati
Se si arresta il processo di backup ma si *conservano* accidentalmente i dati, per potere eliminare l'insieme di credenziali è necessario eliminare prima di tutto i dati di backup. Per eliminare i dati di backup:

1. Nel menu **Elementi di backup** fare clic con il pulsante destro del mouse sull'elemento, quindi scegliere **Elimina dati di backup** dal menu contestuale.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/delete-backup-data-menu.png)

    Verrà visualizzato il menu **Elimina dati di backup**.
2. Nel menu **Elimina dati di backup** immettere il nome dell'elemento e fare clic su **Elimina**.

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

    Per eliminare un insieme di credenziali, è necessario cancellare o eliminare, i dati protetti dell'insieme di credenziali. Se sono presenti molti punti di ripristino e dati nel gruppo protezione dati, potrebbero essere necessari alcuni minuti per eliminare i dati. La finestra di dialogo **Arresta protezione** indica il completamento del processo.

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

    ![aprire il menu Server di produzione](./media/backup-azure-delete-vault/delete-production-server.png)

    Viene aperto il menu **Server di produzione**, in cui sono elencati tutti i server di produzione presenti nell'insieme di credenziali.

    ![elenco di server di produzione](./media/backup-azure-delete-vault/list-of-production-servers.png)
2. Nel menu **Server di produzione** fare clic con il pulsante destro del mouse sul server e quindi scegliere **Elimina**.

    ![eliminare il server di produzione ](./media/backup-azure-delete-vault/delete-server-on-production-server-blade.png)

    Viene aperto il menu **Elimina**.

    ![eliminare il server di produzione ](./media/backup-azure-delete-vault/delete-blade.png)
3. Nel menu **Elimina** confermare il nome del server e fare clic su **Elimina**. È necessario immettere correttamente il nome del server per attivare il pulsante **Elimina**.

    Dopo l'eliminazione dell'insieme di credenziali, si riceverà un messaggio che indica che l'insieme di credenziali è stato eliminato. Dopo l'eliminazione di tutti i server nell'insieme di credenziali, tornare al pannello Informazioni di base nel dashboard dell'insieme di credenziali.
4. Nel dashboard dell'insieme di credenziali assicurarsi che non siano presenti **Elementi di backup**, **Server di gestione di backup** o **Elementi replicati**. Sulla barra degli strumenti dell'insieme di credenziali fare clic su **Elimina**.
5. Per confermare l'eliminazione dell'insieme di credenziali, fare clic su **Sì**.

    L'insieme di credenziali viene eliminato e il portale torna al menu **Nuovo** del servizio.

## <a name="find-the-backup-management-servers-registered-to-the-vault"></a>Trovare i server di gestione di backup registrati nell'insieme di credenziali
Se in un insieme di credenziali sono registrati più server, potrebbe essere difficile ricordarli. Per visualizzare i server registrati nell'insieme di credenziali ed eliminarli:

1. Aprire il dashboard dell'insieme di credenziali.
2. Nel riquadro **Informazioni di base** fare clic su **Impostazioni** per aprire il menu corrispondente.

    ![aprire il menu impostazioni](./media/backup-azure-delete-vault/backup-vault-click-settings.png)
3. Nel menu **Impostazioni** fare clic su **Infrastruttura di backup**.
4. Nel menu **Infrastruttura di backup** fare clic su **Server di gestione di backup**. Viene aperto il menu Server di gestione di backup.

    ![elenco di server di gestione di backup](./media/backup-azure-delete-vault/list-of-backup-management-servers.png)
5. Per eliminare un server dall'elenco, fare clic con il pulsante destro del mouse sul nome del server e quindi scegliere **Elimina**.
    Viene aperto il menu **Elimina**.
6. Nel menu **Elimina** specificare il nome del server. Se il nome è lungo, è possibile copiarlo e incollarlo dall'elenco di Server di gestione di backup. Fare quindi clic su **Elimina**.  
