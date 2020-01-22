---
title: Ripristinare le condivisioni file di Azure
description: Informazioni su come usare la portale di Azure per ripristinare un'intera condivisione file o file specifici da un punto di ripristino creato dal servizio backup di Azure.
ms.topic: conceptual
ms.date: 01/12/2020
ms.openlocfilehash: b16eb4120ff2d269135ae8ae6555ef4fdbdbda5d
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294396"
---
# <a name="restore-azure-file-shares"></a>Ripristinare le condivisioni file di Azure

Questo articolo illustra come usare la portale di Azure per ripristinare un'intera condivisione file o file specifici da un punto di ripristino creato dal servizio [backup di Azure](https://docs.microsoft.com/azure/backup/backup-overview) .

In questa guida si apprenderà come:

* Ripristinare una condivisione file di Azure completa
* Ripristinare singoli file o cartelle
* Tenere traccia dello stato dell'operazione di ripristino

## <a name="steps-to-perform-restore"></a>Passaggi per eseguire il ripristino

### <a name="select-the-file-share-to-restore"></a>Selezionare la condivisione file da ripristinare

1. Nella [portale di Azure](https://portal.azure.com/)aprire l'insieme di credenziali di servizi di ripristino usato per configurare il backup per la condivisione file.

2. Fare clic su **elementi di backup** nella sezione **elementi protetti** del pannello **Panoramica** .

    ![Fare clic su elementi di backup](./media/restore-afs/backup-items.png)

3. Quando si fa clic su **elementi di backup**, viene visualizzato un nuovo pannello che elenca tutti i tipi di gestione di backup accanto al pannello **Panoramica** , come illustrato di seguito:

    ![Tipi di gestione di backup](./media/restore-afs/backup-management.png)

4. In **elementi di backup**, in **tipo di gestione di backup**, selezionare archiviazione di **Azure (file di Azure)** . Verrà visualizzato un elenco di tutte le condivisioni file e il relativo account di archiviazione di cui è stato eseguito il backup con questo insieme di credenziali.

    ![Elenco di tutte le condivisioni file](./media/restore-afs/file-shares.png)

5. Dall'elenco delle condivisioni file di Azure selezionare la condivisione file desiderata per cui si vuole eseguire l'operazione di ripristino.

### <a name="full-share-recovery"></a>Ripristino con condivisione completa

È possibile utilizzare questa opzione di ripristino per ripristinare la condivisione file completa nel percorso originale o in un percorso alternativo.

1. Selezionare l'opzione **Ripristina condivisione** dal pannello **elemento di backup** che viene visualizzato dopo aver selezionato la condivisione file desiderata da ripristinare nel passaggio 5 della sezione [selezionare la condivisione file da ripristinare](#select-the-file-share-to-restore) .

   ![Selezionare la condivisione di ripristino](./media/restore-afs/restore-share.png)

2. Quando si fa clic su **Ripristina condivisione**, viene aperto il pannello **Ripristina** con un menu **punto di ripristino** che Visualizza l'elenco dei punti di ripristino disponibili per la condivisione file selezionata.

3. Selezionare il punto di ripristino che si desidera utilizzare per eseguire l'operazione di ripristino e fare clic su **OK**.

    ![Seleziona punto di ripristino](./media/restore-afs/restore-point.png)

4. Quando si fa clic su OK, il menu Ripristina pannello passa a **percorso ripristino**. In **percorso ripristino**specificare dove (o come) ripristinare i dati. È possibile scegliere una delle due opzioni seguenti:

    * **Percorso originale**: ripristinare la condivisione file completa nello stesso percorso dell'origine originale.
    * **Percorso alternativo**: ripristinare la condivisione file completa in un percorso alternativo e mantengono la condivisione file originale **così come è**.

#### <a name="restore-to-original-location"></a>Ripristina nel percorso originale

1. Scegliere il **percorso originale** come **destinazione di ripristino** e scegliere se ignorare o sovrascrivere se sono presenti conflitti. Fare clic su **OK** dopo aver effettuato la selezione appropriata.

    ![Scegliere il percorso originale](./media/restore-afs/original-location.png)

2. Fare clic su **Ripristina** per avviare l'operazione di ripristino.

    ![Fare clic su Ripristina per avviare](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Ripristino in un percorso alternativo

1. Scegliere il **percorso alternativo** come destinazione di ripristino.
2. Selezionare l'account di archiviazione di destinazione, in cui si vuole ripristinare il contenuto di cui è stato eseguito il backup, dal menu a discesa del campo **account di archiviazione** .
3. In base all'account di archiviazione selezionato nel passaggio 2, nel menu a discesa **Seleziona condivisione file** viene visualizzato l'elenco delle condivisioni file presenti nell'account di archiviazione selezionato. Selezionare la condivisione file in cui si desidera ripristinare il contenuto di cui è stato eseguito il backup.
4. Nel campo **nome cartella** specificare il nome della cartella che si desidera creare nella condivisione file di destinazione con il contenuto ripristinato.
5. Selezionare se ignorare o sovrascrivere se sono presenti conflitti.
6. Fare clic su **OK** dopo aver immesso i valori appropriati in tutti i campi.

    ![Selezionare un percorso alternativo](./media/restore-afs/alternate-location.png)

7. Fare clic su Ripristina per avviare l'operazione di ripristino.

    ![Fare clic su Ripristina per avviare](./media/restore-afs/click-restore.png)

### <a name="item-level-recovery"></a>Ripristino a livello di elemento

È possibile utilizzare questa opzione di ripristino per ripristinare singoli file o cartelle nel percorso originale o in un percorso alternativo.

1. Selezionare l'opzione **ripristino file** dal pannello **elemento di backup** che viene visualizzato dopo aver selezionato la condivisione file desiderata da ripristinare nel passaggio 5 della sezione [selezionare la condivisione file da ripristinare](#select-the-file-share-to-restore) .

    ![Seleziona ripristino file](./media/restore-afs/file-recovery.png)

2. Quando si fa clic su **ripristino file**, viene aperto il pannello **Ripristina** con un menu **punto di ripristino** che visualizza un elenco di punti di ripristino disponibili per la condivisione file selezionata.

3. Selezionare il punto di ripristino che si desidera utilizzare per eseguire l'operazione di ripristino e fare clic su **OK**.

    ![Seleziona punto di ripristino](./media/restore-afs/restore-point.png)

4. Quando si fa clic su OK, il menu Ripristina pannello passa a **percorso ripristino**. In **percorso ripristino**specificare dove (o come) ripristinare i dati. È possibile scegliere una delle due opzioni seguenti:

    * **Percorso originale**: ripristinare i file o le cartelle selezionati nella stessa condivisione file dell'origine originale.
    * **Percorso alternativo**: ripristinare i file o le cartelle selezionati in un percorso alternativo e proteggere il contenuto della condivisione file originale **così com'è**.

#### <a name="restore-to-original-location"></a>Ripristino nel percorso originale

1. Scegliere il **percorso originale** come **destinazione di ripristino** e scegliere se ignorare o sovrascrivere se sono presenti conflitti.

    ![Percorso originale per il ripristino a livello di elemento](./media/restore-afs/original-location-item-level.png)

2. Fare clic su **Seleziona file** per scegliere i file e le cartelle che si desidera ripristinare.

    ![Fare clic su Seleziona file](./media/restore-afs/select-file.png)

3. Quando si fa clic su **Seleziona file**, viene visualizzato un pannello Condivisione file che Visualizza il contenuto del punto di ripristino della condivisione file selezionato per il ripristino.

4. Selezionare la casella corrispondente a file/cartella che si desidera ripristinare e fare clic su **Seleziona**.

    ![Seleziona file o cartella](./media/restore-afs/select-file-folder.png)

5. Ripetere i passaggi 2-4 per selezionare più file/cartelle per il ripristino.
6. Dopo aver selezionato tutti gli elementi che si desidera ripristinare, fare clic su **OK**.

    ![Dopo aver selezionato tutti gli elementi da ripristinare, fare clic su OK.](./media/restore-afs/after-selecting-items.png)

7. Fare clic su Ripristina per avviare l'operazione di ripristino.

    ![Fare clic su Ripristina per avviare](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Ripristinare in un percorso alternativo

1. Scegliere il **percorso alternativo** come destinazione di ripristino.
2. Selezionare l'account di archiviazione di destinazione, in cui si vuole ripristinare il contenuto di cui è stato eseguito il backup, dal menu a discesa del campo **account di archiviazione** .
3. In base all'account di archiviazione selezionato nel passaggio 2, nel menu a discesa **Seleziona condivisione file** viene visualizzato l'elenco delle condivisioni file presenti nell'account di archiviazione selezionato. Selezionare la condivisione file in cui si desidera ripristinare il contenuto di cui è stato eseguito il backup.
4. Nel campo **nome cartella** specificare il nome della cartella che si desidera creare nella condivisione file di destinazione con il contenuto ripristinato.
5. Selezionare se ignorare o sovrascrivere se sono presenti conflitti.
6. Fare clic su **Seleziona file** per scegliere i file e le cartelle che si desidera ripristinare.

    ![Selezionare gli elementi da ripristinare in un percorso alternativo](./media/restore-afs/restore-to-alternate-location.png)

7. Quando si fa clic su **Seleziona file**, viene visualizzato un pannello Condivisione file che Visualizza il contenuto del punto di ripristino della condivisione file selezionato per il ripristino.
8. Selezionare la casella corrispondente al file o alla cartella che si desidera ripristinare e fare clic su **Seleziona**.

    ![Selezionare la destinazione di ripristino](./media/restore-afs/recovery-destination.png)

9. Ripetere i passaggi 6-8 per selezionare più file/cartelle per il ripristino.
10. Dopo aver selezionato tutti gli elementi che si desidera ripristinare, fare clic su **OK**.

    [Fare clic su OK dopo aver selezionato tutti i file](./media/restore-afs/after-selecting-all-items.png)

11. Fare clic su **Ripristina** per avviare l'operazione di ripristino.

## <a name="track-restore-operation"></a>Rileva operazione di ripristino

Dopo l'attivazione dell'operazione di ripristino, il servizio di backup crea un processo per tenerne traccia. Backup di Azure consente di visualizzare le notifiche relative al processo nel portale. Per visualizzare le operazioni relative al processo, fare clic sul collegamento ipertestuale relativo alle notifiche.

![Fare clic su collegamento ipertestuale notifiche](./media/restore-afs/notifications-link.png)

È anche possibile monitorare lo stato di avanzamento del ripristino dall'insieme di credenziali di servizi di ripristino. Ecco i passaggi per controllare lo stato dell'operazione di ripristino:

1. Aprire l'insieme di credenziali di servizi di ripristino da cui è stata attivata l'operazione di ripristino.
2. Fare clic su **processi di backup** nella **sezione monitoraggio** del pannello **Panoramica** per visualizzare lo stato delle operazioni in esecuzione in carichi di lavoro diversi.

    ![Fare clic su processi di backup](./media/restore-afs/backup-jobs.png)

3. Fare clic sul nome del carico di lavoro corrispondente alla condivisione file per visualizzare altri dettagli sull'operazione di ripristino, ad esempio i dati trasferiti, il numero di file ripristinati e così via.

    ![Vedere i dettagli ripristinati](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [gestire i backup di condivisioni file di Azure](manage-afs-backup.md)
