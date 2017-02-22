---
title: Eliminare un insieme di credenziali di Backup di Azure | Documentazione Microsoft
description: 'How to delete an Azure Backup vault. Troubleshooting why you can''t delete a backup vault. '
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
ms.date: 11/28/2016
ms.author: markgal;trinadhk
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 704c21aef6ed8b476bbd14f05bce89dc5b708c21


---
# <a name="delete-an-azure-backup-vault"></a>Eliminare un insieme di credenziali di Backup di Azure
Il servizio Backup di Azure presenta insiemi di credenziali di due tipi: l'insieme di credenziali per il backup e l'insieme di credenziali dei servizi di ripristino. L'insieme di credenziali per il backup è stato creato per primo. L'insieme di credenziali dei servizi di ripristino è stato creato in seguito per supportare le distribuzioni Resource Manager espanse. A causa delle funzionalità espanse e delle dipendenze delle informazioni che devono essere archiviate nell'insieme di credenziali, l'eliminazione di un insieme di credenziali dei servizi di ripristino può sembrare più complessa di quanto non sia in realtà.

| **Tipo di distribuzione** | **Portale** | **Nome dell'insieme di credenziali** |
| --- | --- | --- |
| Classico |Classico |Insieme di credenziali per il backup |
| Gestione risorse |Azure |Insieme di credenziali dei servizi di ripristino |

> [!NOTE]
> Le credenziali per il backup non consentono di proteggere le soluzioni distribuite con Resource Manager. Per proteggere le VM e i server distribuiti in modo tradizionale, è tuttavia possibile usare un insieme di credenziali dei servizi di ripristino.  
>
>

In questo articolo il termine insieme di credenziali viene usato per fare riferimento alla forma generica dell'insieme di credenziali per il backup o dell'insieme di credenziali dei servizi di ripristino. Il nome formale, ovvero insieme di credenziali per il backup o insieme di credenziali dei servizi di ripristino, viene usato quando è necessario distinguere tra gli insiemi di credenziali.

## <a name="deleting-a-recovery-services-vault"></a>Eliminazione di un insieme di credenziali dei servizi di ripristino
L'eliminazione di un insieme di credenziali dei servizi di ripristino è un processo costituito da un solo passaggio, *purché l'insieme di credenziali non contenga risorse*. Prima di potere eliminare un insieme di credenziali dei servizi di ripristino, è necessario rimuovere o eliminare tutte le risorse nell'insieme di credenziali. Se si prova a eliminare un insieme di credenziali contenente risorse, viene visualizzato un errore analogo a quello riportato nell'immagine seguente.

![Errore di eliminazione dell'insieme di credenziali](./media/backup-azure-delete-vault/vault-deletion-error.png) <br/>

Fino a quando non si cancellano le risorse dall'insieme di credenziali, la selezione di **Riprova** produce lo stesso errore. Se si rimane bloccati su questo messaggio di errore, fare clic su **Annulla** e seguire la procedura seguente per eliminare le risorse nell'insieme di credenziali dei servizi di ripristino.

### <a name="removing-the-items-from-a-vault-protecting-a-vm"></a>Rimozione degli elementi da un insieme di credenziali che protegge una VM
Se l'insieme di credenziali dei servizi di ripristino è già aperto, procedere al secondo passaggio.

1. Aprire il portale di Azure e dal dashboard aprire l'insieme di credenziali da eliminare.

   Se l'insieme di credenziali dei servizi di ripristino non è stato aggiunto al dashboard, scegliere **More Services** (Altri servizi) dal menu Hub e nell'elenco di risorse digitare **Servizi di ripristino**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Fare clic su **Insiemi di credenziali dei servizi di ripristino**.

   ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 1](./media/backup-azure-delete-vault/open-recovery-services-vault.png) <br/>

   Viene visualizzato l'elenco degli insiemi di credenziali dei servizi di ripristino. Selezionare dall'elenco l'insieme di credenziali da eliminare.

   ![scegliere un insieme di credenziali dall'elenco](./media/backup-azure-work-with-vaults/choose-vault-to-delete.png)
2. Nella visualizzazione dell'insieme di credenziali esaminare il riquadro **Informazioni di base** . Per eliminare un insieme di credenziali, è necessario che non siano presenti elementi protetti. Se viene visualizzato un numero diverso da zero in **Elementi di backup** o **Server di gestione di backup**, è necessario rimuovere questi elementi prima di potere eliminare l'insieme di credenziali.

    ![Cercare gli elementi protetti nel riquadro Informazioni di base](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

    Le macchine virtuali e i file o le cartelle sono considerati elementi di backup e sono elencati nell'area **Elementi di backup** del riquadro Informazioni di base. Un server DPM viene elencato nell'area **Server di gestione di backup** del riquadro Informazioni di base. **Elementi replicati** sono relativi al servizio Azure Site Recovery.
3. Per iniziare a rimuovere gli elementi protetti dall'insieme di credenziali, trovare gli elementi nell'insieme di credenziali. Nel dashboard dell'insieme di credenziali fare clic su **Impostazioni**, quindi su **Elementi di backup** per aprire il pannello.

    ![scegliere un insieme di credenziali dall'elenco](./media/backup-azure-delete-vault/open-settings-and-backup-items.png)

    Il pannello **Elementi di backup** include elenchi separati, in base al tipo di elemento, macchine virtuali di Azure o file/cartelle, come illustrato nell'immagine. L'elenco Tipo di elemento visualizzato per impostazione predefinita è Macchine virtuali di Azure. Per visualizzare l'elenco di elementi di tipo file/cartelle nell'insieme di credenziali, selezionare **File-cartelle** dal menu a discesa.
4. Prima di potere eliminare un elemento dall'insieme di credenziali che protegge una VM, è necessario arrestare il processo di backup ed eliminare i dati del punto di ripristino. Seguire questa procedura per ogni elemento dell'insieme di credenziali:

    a. Nel pannello **Elementi di backup** fare clic con il pulsante destro del mouse sull'elemento, quindi scegliere **Arresta backup** dal menu contestuale.

    ![arrestare il processo di backup](./media/backup-azure-delete-vault/stop-the-backup-process.png)

    Verrà visualizzato il pannello Arresta backup.

    b. Nel pannello **Arresta backup** dal menu **Scegliere un'opzione** selezionare **Elimina i dati di backup** > immettere il nome dell'elemento > quindi fare clic su **Arresta backup**.

      Immettere il nome dell'elemento per confermare che lo si vuole eliminare. Il pulsante **Arresta backup** verrà attivato solo dopo la verifica dell'elemento da arrestare. Se la finestra di dialogo per l'immissione del nome dell'elemento di backup non viene visualizzata, è stata selezionata l'opzione **Conserva i dati di backup** .

    ![Elimina dati di backup](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

      È facoltativamente possibile specificare un motivo per l'eliminazione dei dati e aggiungere commenti. Dopo avere selezionato **Arresta backup**, è necessario consentire il completamento del processo di eliminazione prima di provare a eliminare l'insieme di credenziali. Per verificare il completamento del processo, controllare i messaggi di Azure ![delete backup data](./media/backup-azure-delete-vault/messages.png). <br/>
   Al termine del processo, verrà visualizzato un messaggio che indica che il processo di backup è stato arrestato e che i dati di backup sono stati eliminati per l'elemento specifico.

    c. Dopo l'eliminazione di un elemento dall'elenco, scegliere **Aggiorna** dal menu **Elementi di backup** per visualizzare gli elementi rimanenti nell'insieme di credenziali.

      ![Elimina dati di backup](./media/backup-azure-delete-vault/empty-items-list.png)

      Quando nell'elenco non sono presenti elementi, passare al riquadro **Informazioni di base** nel pannello Insieme di credenziali di backup. Nell'elenco non dovrebbero essere presenti **Elementi di backup**, **Server di gestione di backup** o **Elementi replicati**. Se nell'insieme di credenziali sono ancora presenti elementi, tornare al passaggio tre precedente e scegliere un elenco di tipi di elemento diverso.  
5. Quando sulla barra degli strumenti dell'insieme di credenziali non sono più presenti elementi, fare clic su **Elimina**.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/delete-vault.png)
6. Quando viene richiesta la conferma dell'eliminazione dell'insieme di credenziali, fare clic su **Sì**.

    L'insieme di credenziali viene eliminato e il portale torna al menu **Nuovo** del servizio.

## <a name="what-if-i-stopped-the-backup-process-but-retained-the-data"></a>Conseguenze dell'arresto del processo di backup e della conservazione dei dati
Se si arresta il processo di backup ma si *conservano* accidentalmente i dati, per potere eliminare l'insieme di credenziali è necessario eliminare prima di tutto i dati di backup. Per eliminare i dati di backup:

1. Nel pannello **Elementi di backup** fare clic con il pulsante destro del mouse sull'elemento, quindi scegliere **Elimina dati di backup** dal menu contestuale.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/delete-backup-data-menu.png)

    Verrà visualizzato il pannello **Elimina dati di backup** .
2. Nel pannello **Elimina dati di backup** immettere il nome dell'elemento e fare clic su **Elimina**.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/delete-retained-vault.png)

    Dopo avere eliminato i dati, tornare al Passaggio 4c e continuare con il processo.

## <a name="delete-a-vault-used-to-protect-a-dpm-server"></a>Eliminare un insieme di credenziali usato per proteggere un server DPM
Per potere eliminare un insieme di credenziali usato per proteggere un server DPM, è necessario cancellare prima di tutto eventuali punti di ripristino creati e quindi annullare la registrazione del server dall'insieme di credenziali.

Per eliminare i dati associati al gruppo di protezione:

1. Nella Console amministrazione DPM fare clic su **Protezione**, selezionare un gruppo di protezione, scegliere il membro del gruppo di protezione e quindi fare clic su **Rimuovi** sulla barra multifunzione dello strumento. Per consentire la visualizzazione del pulsante **Rimuovi** sulla barra multifunzione dello strumento, è necessario selezionare il membro. Nell'esempio il nome del membro è **dummyvm9**. Se nel gruppo di protezione sono presenti più membri, tenere premuto il tasto CTRL per selezionare più membri.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/az-portal-delete-protection-group.png)

    Viene visualizzata la finestra di dialogo **Arresta protezione** .
2. Nella finestra di dialogo **Arresta protezione** selezionare **Elimina dati protetti** e quindi fare clic su **Arresta protezione**.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/delete-dpm-protection-group.png)

    È consigliabile non conservare i dati protetti, perché è necessario cancellare gli elementi dell'insieme di credenziali per eliminarlo. In base al numero di punti di ripristino e alla quantità di dati presenti nel gruppo di protezione, l'eliminazione dei dati potrebbe richiedere da qualche secondo ad alcuni minuti. La finestra di dialogo **Arresta protezione** mostra lo stato al completamento del processo.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/success-deleting-protection-group.png)
3. Continuare questo processo per tutti i membri di tutti i gruppi di protezione.

    È necessario rimuovere tutti i dati protetti e tutti i gruppi di protezione.
4. Dopo avere eliminato tutti i membri dal gruppo di protezione, passare al portale di Azure. Aprire il dashboard dell'insieme di credenziali e assicurarsi che non siano presenti **Elementi di backup**, **Server di gestione di backup** o **Elementi replicati**. Sulla barra degli strumenti dell'insieme di credenziali fare clic su **Elimina**.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/delete-vault.png)

    Se nell'insieme di credenziali sono registrati server di gestione di backup, non sarà possibile eliminare l'insieme di credenziali anche se nell'insieme di credenziali non sono presenti dati. Se è già stata eseguita l'eliminazione dei server di gestione di backup associati all'insieme di credenziali, ma nel riquadro **Informazioni di base** vengono ancora visualizzati server, vedere [Trovare i server di gestione di backup registrati nell'insieme di credenziali](backup-azure-delete-vault.md#find-the-backup-management-servers-registered-to-the-vault).
5. Quando viene richiesta la conferma dell'eliminazione dell'insieme di credenziali, fare clic su **Sì**.

    L'insieme di credenziali viene eliminato e il portale torna al menu **Nuovo** del servizio.

## <a name="delete-a-vault-used-to-protect-a-production-server"></a>Eliminare un insieme di credenziali usato per proteggere un server di produzione
Per potere eliminare un insieme di credenziali usato per proteggere un server di produzione, è prima di tutto necessario eliminare o annullare la registrazione del server dall'insieme di credenziali.

Per eliminare il server di produzione associato all'insieme di credenziali:

1. Nel portale di Azure aprire il dashboard dell'insieme di credenziali e fare clic su **Impostazioni** > **Infrastruttura di backup** > **Server di produzione**.

    ![aprire il pannello Server di produzione](./media/backup-azure-delete-vault/delete-production-server.png)

    Viene visualizzato il pannello **Server di produzione** , in cui sono elencati tutti i server di produzione presenti nell'insieme di credenziali.

    ![elenco di server di produzione](./media/backup-azure-delete-vault/list-of-production-servers.png)
2. Nel pannello **Server di produzione** fare clic con il pulsante destro del mouse sul server e quindi scegliere **Elimina**.

    ![eliminare il server di produzione ](./media/backup-azure-delete-vault/delete-server-on-production-server-blade.png)

    Viene visualizzato il pannello **Elimina** .

    ![eliminare il server di produzione ](./media/backup-azure-delete-vault/delete-blade.png)
3. Nel pannello **Elimina** confermare il nome del server da eliminare e quindi fare clic su **Elimina**. È necessario immettere correttamente il nome del server per attivare il pulsante **Elimina** .

    Dopo l'eliminazione dell'insieme di credenziali, si riceverà un messaggio che indica che l'insieme di credenziali è stato eliminato. Dopo l'eliminazione di tutti i server nell'insieme di credenziali, tornare al pannello Informazioni di base nel dashboard dell'insieme di credenziali.
4. Nel dashboard dell'insieme di credenziali assicurarsi che non siano presenti **Elementi di backup**, **Server di gestione di backup** o **Elementi replicati**. Sulla barra degli strumenti dell'insieme di credenziali fare clic su **Elimina**.
5. Quando viene richiesta la conferma dell'eliminazione dell'insieme di credenziali, fare clic su **Sì**.

    L'insieme di credenziali viene eliminato e il portale torna al menu **Nuovo** del servizio.

## <a name="delete-a-backup-vault"></a>Eliminare un insieme di credenziali per il backup
Le istruzioni seguenti illustrano come eliminare un insieme di credenziali per il backup nel portale classico. La procedura è identica per un insieme di credenziali per il backup e un insieme di credenziali di servizi di ripristino, ovvero prima di potere eliminare l'insieme di credenziali, è necessario eliminare gli elementi e i dati conservati.

1. Aprire il portale classico.
2. Selezionare l'insieme di credenziali da eliminare dall'elenco di insiemi di credenziali di backup.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/classic-portal-delete-vault-open-vault.png)

    Viene aperto il dashboard dell'insieme di credenziali. Verificare il numero di macchine virtuali di Azure e/o server Windows associati all'insieme di credenziali. Verificare anche lo spazio di archiviazione totale utilizzato in Azure. Sarà necessario arrestare eventuali processi di backup ed eliminare i dati esistenti prima di eliminare l'insieme di credenziali.
3. Fare clic sulla scheda **Elementi protetti** e quindi su **Arresta protezione**.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/classic-portal-delete-vault-stop-protect.png)

    Viene visualizzata la finestra di dialogo **Arresta la protezione di 'insieme di credenziali'** .
4. Nella finestra di dialogo **Arresta la protezione di 'insieme di credenziali'** selezionare l'opzione **Elimina i dati di backup associati** e fare clic sul ![segno di spunta](./media/backup-azure-delete-vault/checkmark.png). <br/>
    È facoltativamente possibile scegliere un motivo per l'arresto della protezione e aggiungere un commento.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/classic-portal-delete-vault-verify-stop-protect.png)

    Dopo l'eliminazione degli elementi dall'insieme di credenziali, l'insieme di credenziali sarà vuoto.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/classic-portal-delete-vault-post-delete-data.png)
5. Nell'elenco di schede fare clic su **Elementi registrati**. Per ogni elemento registrato nell'insieme di credenziali selezionare l'elemento e fare clic su **Annulla registrazione**.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/classic-portal-unregister.png)
6. Nell'elenco di schede fare clic su **Dashboard** per aprire la scheda corrispondente. Assicurarsi che non siano presenti macchine virtuali di Azure o server registrati protetti nel cloud. Verificare anche che la risorsa di archiviazione non includa dati. Fare clic su **Elimina** per eliminare l'insieme di credenziali.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/classic-portal-list-of-tabs-dashboard.png)

    Viene visualizzata la schermata di conferma Elimina insieme di credenziali di backup. Selezionare un'opzione corrispondente al motivo dell'eliminazione dell'insieme di credenziali e quindi fare clic su  ![segno di spunta](./media/backup-azure-delete-vault/checkmark.png). <br/>

    ![Elimina dati di backup](./media/backup-azure-delete-vault/classic-portal-delete-vault-confirmation-1.png)

    L'insieme di credenziali viene eliminato e si torna al dashboard del portale classico.

### <a name="find-the-backup-management-servers-registered-to-the-vault"></a>Trovare i server di gestione di backup registrati nell'insieme di credenziali
Se in un insieme di credenziali sono registrati più server, potrebbe essere difficile ricordarli. Per visualizzare i server registrati nell'insieme di credenziali ed eliminarli:

1. Aprire il dashboard dell'insieme di credenziali.
2. Nel riquadro **Informazioni di base** fare clic su **Impostazioni** per aprire il pannello corrispondente.

    ![aprire il pannello Impostazioni](./media/backup-azure-delete-vault/backup-vault-click-settings.png)
3. Nel pannello **Impostazioni** fare clic su **Infrastruttura di backup**.
4. Nel pannello **Infrastruttura di backup** fare clic su **Server di gestione di backup**. Viene aperto il pannello Server di gestione di backup.

    ![elenco di server di gestione di backup](./media/backup-azure-delete-vault/list-of-backup-management-servers.png)
5. Per eliminare un server dall'elenco, fare clic con il pulsante destro del mouse sul nome del server e quindi scegliere **Elimina**.
    Viene visualizzato il pannello **Elimina** .
6. Nel pannello **Elimina** specificare il nome del server. Se il nome è lungo, è possibile copiarlo e incollarlo dall'elenco di Server di gestione di backup. Fare quindi clic su **Elimina**.  



<!--HONumber=Nov16_HO3-->


