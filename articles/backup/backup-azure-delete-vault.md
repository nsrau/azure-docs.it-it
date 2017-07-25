---
title: " Eliminare un insieme di credenziali di Backup in Azure | Microsoft Docs "
description: "Procedura per eliminare un insieme di credenziali di Backup di Azure e dei Servizi di ripristino. Un insieme di credenziali di backup può essere definito come un insieme di credenziali cloud o un insieme di credenziali di ripristino di Azure. Risoluzione dei problemi quando non è possibile eliminare un insieme di credenziali di backup nel portale classico o nel portale di Azure."
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
ms.date: 06/14/2017
ms.author: markgal;trinadhk
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: d8be07fe4cd3fd50827fb7021a75eb05b6f075e0
ms.contentlocale: it-it
ms.lasthandoff: 06/16/2017


---
# <a name="delete-an-azure-backup-vault"></a>Eliminare un insieme di credenziali di Backup di Azure
Il servizio Backup di Azure presenta insiemi di credenziali di due tipi: l'insieme di credenziali per il backup e l'insieme di credenziali dei servizi di ripristino. L'insieme di credenziali per il backup è stato creato per primo. L'insieme di credenziali dei servizi di ripristino è stato creato in seguito per supportare le distribuzioni Resource Manager espanse. A causa delle funzionalità espanse e delle dipendenze delle informazioni che devono essere archiviate nell'insieme di credenziali, l'eliminazione di un insieme di credenziali di Backup o dei Servizi di ripristino può sembrare complessa. In questo articolo viene illustrato come eliminare gli insiemi di credenziali nel portale classico e nel portale di Azure.  

| **Tipo di distribuzione** | **Portale** | **Nome dell'insieme di credenziali** |
| --- | --- | --- |
| Classico |Classico |Insieme di credenziali per il backup |
| Gestione risorse |Azure |Insieme di credenziali dei servizi di ripristino |

> [!NOTE]
> Le credenziali per il backup non consentono di proteggere le soluzioni distribuite con Resource Manager. Per proteggere le VM e i server distribuiti in modo tradizionale, è tuttavia possibile usare un insieme di credenziali dei servizi di ripristino.  
>

> [!IMPORTANT]
> È ora possibile aggiornare gli insiemi di credenziali di Backup ad insiemi di credenziali dei servizi di ripristino. Per altre informazioni, vedere l'articolo [Aggiornare un insieme di credenziali di Backup a un insieme di credenziali di Servizi di ripristino](backup-azure-upgrade-backup-to-recovery-services.md). Microsoft consiglia di aggiornare gli insiemi di credenziali di Backup a insiemi di credenziali dei servizi di ripristino.<br/> **A partire dal 1° novembre 2017**:
>- Eventuali insiemi di credenziali di Backup rimanenti verranno automaticamente aggiornati a insiemi di credenziali di servizi di ripristino
>- e non sarà più possibile accedere ai dati di backup nel portale classico. Sarà possibile invece usare il portale di Azure per accedere ai dati di backup negli insiemi di credenziali di servizi di ripristino.
>

In questo articolo il termine insieme di credenziali viene usato per fare riferimento alla forma generica dell'insieme di credenziali per il backup o dell'insieme di credenziali dei servizi di ripristino. Il nome formale, ovvero insieme di credenziali per il backup o insieme di credenziali dei servizi di ripristino, viene usato quando è necessario distinguere tra gli insiemi di credenziali.

## <a name="deleting-a-recovery-services-vault"></a>Eliminazione di un insieme di credenziali dei servizi di ripristino
L'eliminazione di un insieme di credenziali dei servizi di ripristino è un processo costituito da un solo passaggio, *purché l'insieme di credenziali non contenga risorse*. Prima di potere eliminare un insieme di credenziali dei servizi di ripristino, è necessario rimuovere o eliminare tutte le risorse nell'insieme di credenziali. Se si cerca di eliminare un insieme di credenziali contenente risorse, viene visualizzato un errore analogo a quello riportato nell'immagine seguente:

![Errore di eliminazione dell'insieme di credenziali](./media/backup-azure-delete-vault/vault-deletion-error.png) <br/>

Fino a quando non si cancellano le risorse dall'insieme di credenziali, la selezione di **Riprova** produce lo stesso errore. Se si rimane bloccati su questo messaggio di errore, fare clic su **Annulla** e seguire la procedura seguente per eliminare le risorse nell'insieme di credenziali.

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

    Digitare il nome dell'elemento per confermare che si desidera eliminarlo. Il pulsante **Interrompi backup** si attiva dopo aver verificato l'elemento. Se la finestra di dialogo per l'immissione del nome dell'elemento di backup non viene visualizzata, è stata selezionata l'opzione **Conserva i dati di backup**.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    È facoltativamente possibile specificare un motivo per l'eliminazione dei dati e aggiungere commenti. Dopo avere selezionato **Arresta backup**, è necessario consentire il completamento del processo di eliminazione prima di provare a eliminare l'insieme di credenziali. Per verificare il completamento del processo, controllare i messaggi di Azure ![delete backup data](./media/backup-azure-delete-vault/messages.png). <br/>
    Al termine del processo, verrà visualizzato un messaggio che indica che il processo di backup è stato arrestato e che i dati di backup sono stati eliminati per l'elemento specifico.

    c. Dopo l'eliminazione di un elemento dall'elenco, scegliere **Aggiorna** dal menu **Elementi di backup** per visualizzare gli elementi rimanenti nell'insieme di credenziali.

      ![Elimina dati di backup](./media/backup-azure-delete-vault/empty-items-list.png)

      Quando nell'elenco non sono presenti elementi, passare al riquadro **Informazioni di base** nel pannello Insieme di credenziali di backup. Nell'elenco non dovrebbero essere presenti **Elementi di backup**, **Server di gestione di backup** o **Elementi replicati**. Se nell'insieme di credenziali sono ancora presenti elementi, tornare al passaggio tre e scegliere un elenco di tipi di elementi diverso.  
5. Quando sulla barra degli strumenti dell'insieme di credenziali non sono più presenti elementi, fare clic su **Elimina**.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/delete-vault.png)
6. Per confermare l'eliminazione dell'insieme di credenziali, fare clic su **Sì**.

    L'insieme di credenziali viene eliminato e il portale torna al menu **Nuovo** del servizio.

## <a name="what-if-i-stopped-the-backup-process-but-retained-the-data"></a>Conseguenze dell'arresto del processo di backup e della conservazione dei dati
Se si arresta il processo di backup ma si *conservano* accidentalmente i dati, per potere eliminare l'insieme di credenziali è necessario eliminare prima di tutto i dati di backup. Per eliminare i dati di backup:

1. Nel pannello **Elementi di backup** fare clic con il pulsante destro del mouse sull'elemento, quindi scegliere **Elimina dati di backup** dal menu contestuale.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/delete-backup-data-menu.png)

    Verrà visualizzato il pannello **Elimina dati di backup** .
2. Nel pannello **Elimina dati di backup** immettere il nome dell'elemento e fare clic su **Elimina**.

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

    Per eliminare un insieme di credenziali, è necessario cancellare o eliminare, i dati protetti dell'insieme di credenziali. In base al numero di punti di ripristino e alla quantità di dati presenti nel gruppo protezione dati, l'eliminazione dei dati potrebbe richiedere da qualche secondo ad alcuni minuti. La finestra di dialogo **Arresta protezione** mostra lo stato al completamento del processo.

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

    ![aprire il pannello Server di produzione](./media/backup-azure-delete-vault/delete-production-server.png)

    Viene visualizzato il pannello **Server di produzione** , in cui sono elencati tutti i server di produzione presenti nell'insieme di credenziali.

    ![elenco di server di produzione](./media/backup-azure-delete-vault/list-of-production-servers.png)
2. Nel pannello **Server di produzione** fare clic con il pulsante destro del mouse sul server e quindi scegliere **Elimina**.

    ![eliminare il server di produzione ](./media/backup-azure-delete-vault/delete-server-on-production-server-blade.png)

    Viene visualizzato il pannello **Elimina** .

    ![eliminare il server di produzione ](./media/backup-azure-delete-vault/delete-blade.png)
3. Nel pannello **Elimina** confermare il nome del server e fare clic su **Elimina**. È necessario immettere correttamente il nome del server per attivare il pulsante **Elimina**.

    Dopo l'eliminazione dell'insieme di credenziali, si riceverà un messaggio che indica che l'insieme di credenziali è stato eliminato. Dopo l'eliminazione di tutti i server nell'insieme di credenziali, tornare al pannello Informazioni di base nel dashboard dell'insieme di credenziali.
4. Nel dashboard dell'insieme di credenziali assicurarsi che non siano presenti **Elementi di backup**, **Server di gestione di backup** o **Elementi replicati**. Sulla barra degli strumenti dell'insieme di credenziali fare clic su **Elimina**.
5. Per confermare l'eliminazione dell'insieme di credenziali, fare clic su **Sì**.

    L'insieme di credenziali viene eliminato e il portale torna al menu **Nuovo** del servizio.

## <a name="delete-a-backup-vault-in-classic-portal"></a>Eliminare un insieme di credenziali di backup nel portale classico
Le istruzioni seguenti illustrano come eliminare un insieme di credenziali per il backup nel portale classico. Prima di poter eliminare l'insieme di credenziali di Backup, è necessario eliminare i punti di ripristino o gli elementi su cui è stato eseguito il backup e rimuovere i server registrati. I server registrati sono Windows Server, le workstation o le macchine virtuali che sono stati registrati nell'insieme di credenziali.

1. Aprire il [portale classico](https://manage.windowsazure.com).

2. Selezionare l'insieme di credenziali da eliminare dall'elenco di insiemi di credenziali di backup.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/classic-portal-delete-vault-open-vault.png)

    Viene aperto il dashboard dell'insieme di credenziali. Verificare il numero di macchine virtuali di Azure e/o server Windows associati all'insieme di credenziali. Verificare anche lo spazio di archiviazione totale utilizzato in Azure. Arrestare tutti i processi di backup ed eliminare tutti i dati prima di eliminare l'insieme di credenziali.

3. Fare clic sulla scheda **Elementi protetti** e quindi su **Arresta protezione**.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/classic-portal-delete-vault-stop-protect.png)

    Viene visualizzata la finestra di dialogo **Arresta la protezione di 'insieme di credenziali'** .
4. Nella finestra di dialogo **Arresta la protezione di 'insieme di credenziali'** selezionare l'opzione **Elimina i dati di backup associati** e fare clic sul ![segno di spunta](./media/backup-azure-delete-vault/checkmark.png). <br/>
    È facoltativamente possibile scegliere un motivo per l'arresto della protezione e aggiungere un commento.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/classic-portal-delete-vault-verify-stop-protect.png)

    Dopo l'eliminazione degli elementi dall'insieme di credenziali, l'insieme di credenziali sarà vuoto.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/classic-portal-delete-vault-post-delete-data.png)
5. Nell'elenco di schede fare clic su **Elementi registrati**. Il menu a discesa **Tipo** consente di scegliere il tipo di server registrato nell'insieme di credenziali. Il tipo può essere Windows Server o una macchina virtuale di Azure. Nell'esempio seguente, selezionare la macchina virtuale registrata nell'insieme di credenziali e fare clic su **Annulla registrazione**.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/classic-portal-unregister.png)

  Se si desidera eliminare la registrazione per Windows Server, nel menu a discesa **Tipo** selezionare **Windows Server**, fare clic sul ![segno di spunta](./media/backup-azure-delete-vault/checkmark.png) per aggiornare la schermata e quindi fare clic su **Elimina**. <br/>

  ![selezionare Windows Server](./media/backup-azure-delete-vault/select-windows-server.png)

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

