---
title: Ripristinare le condivisioni file di Azure
description: Informazioni su come usare la portale di Azure per ripristinare un'intera condivisione file o file specifici da un punto di ripristino creato da backup di Azure.
ms.topic: conceptual
ms.date: 01/12/2020
ms.openlocfilehash: c22078ebd89f5f6f8299e1424d4d9e21edce8b92
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77586952"
---
# <a name="restore-azure-file-shares"></a>Ripristinare le condivisioni file di Azure

Questo articolo illustra come usare la portale di Azure per ripristinare un'intera condivisione file o file specifici da un punto di ripristino creato da [backup di Azure](https://docs.microsoft.com/azure/backup/backup-overview).

In questo articolo si apprenderà come:

* Ripristinare una condivisione file di Azure completa.
* Ripristinare singoli file o cartelle.
* Tenere traccia dello stato dell'operazione di ripristino.

## <a name="steps-to-perform-a-restore-operation"></a>Passaggi per eseguire un'operazione di ripristino

Per eseguire un'operazione di ripristino, attenersi alla seguente procedura.

### <a name="select-the-file-share-to-restore"></a>Selezionare la condivisione file da ripristinare

1. Nella [portale di Azure](https://portal.azure.com/)aprire l'insieme di credenziali di servizi di ripristino usato per configurare il backup per la condivisione file.

1. Nel riquadro Panoramica selezionare elementi di **backup** nella sezione **elementi protetti** .

    ![Seleziona elementi di backup](./media/restore-afs/backup-items.png)

1. Dopo aver selezionato **gli elementi di backup**, viene aperto un nuovo riquadro che elenca tutti i tipi di gestione di backup accanto al riquadro Panoramica.

    ![Tipi di gestione di backup](./media/restore-afs/backup-management.png)

1. Nel riquadro **elementi di backup** , in **tipo di gestione di backup**, selezionare archiviazione di **Azure (file di Azure)** . Verrà visualizzato un elenco di tutte le condivisioni file e dei relativi account di archiviazione di cui è stato eseguito il backup con questo insieme di credenziali.

    ![Elenco di tutte le condivisioni file](./media/restore-afs/file-shares.png)

1. Dall'elenco delle condivisioni file di Azure selezionare la condivisione file per cui si vuole eseguire l'operazione di ripristino.

### <a name="full-share-recovery"></a>Ripristino con condivisione completa

È possibile utilizzare questa opzione di ripristino per ripristinare la condivisione file completa nel percorso originale o in un percorso alternativo.

1. Selezionare l'opzione **Ripristina condivisione** nel riquadro **elemento di backup** che viene visualizzato dopo aver selezionato la condivisione file da ripristinare nel passaggio 5 della sezione [selezionare la condivisione file da ripristinare](#select-the-file-share-to-restore) .

   ![Selezionare la condivisione di ripristino](./media/restore-afs/restore-share.png)

1. Dopo aver selezionato **Ripristina condivisione**, viene aperto il riquadro **ripristino** con un menu **punto di ripristino** in cui è visualizzato un elenco di punti di ripristino disponibili per la condivisione file selezionata.

1. Selezionare il punto di ripristino che si desidera utilizzare per eseguire l'operazione di ripristino e selezionare **OK**.

    ![Seleziona punto di ripristino](./media/restore-afs/restore-point.png)

1. Dopo aver selezionato **OK**, il menu **Ripristina** riquadro passa a **percorso ripristino**. In **percorso ripristino**specificare dove o come ripristinare i dati. Selezionare una delle due opzioni seguenti:

    * **Percorso originale**: ripristinare la condivisione file completa nello stesso percorso dell'origine originale.
    * **Percorso alternativo**: ripristinare la condivisione file completa in un percorso alternativo e mantengono la condivisione file originale così come è.

#### <a name="restore-to-the-original-location"></a>Ripristina nel percorso originale

1. Selezionare il **percorso originale** come **destinazione di ripristino**e scegliere se ignorare o sovrascrivere se sono presenti conflitti. Dopo aver apportato la selezione appropriata, fare clic su **OK**.

    ![Selezionare il percorso originale](./media/restore-afs/original-location.png)

1. Selezionare **Ripristina** per avviare l'operazione di ripristino.

    ![Selezionare Ripristina per avviare](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Ripristinare in un percorso alternativo

1. Selezionare il **percorso alternativo** come **destinazione di ripristino**.
1. Selezionare l'account di archiviazione di destinazione in cui si vuole ripristinare il contenuto di cui è stato eseguito il backup dall'elenco a discesa **account di archiviazione** .
1. L'elenco a discesa **Seleziona condivisione file** consente di visualizzare le condivisioni file presenti nell'account di archiviazione selezionato nel passaggio 2. Selezionare la condivisione file in cui si desidera ripristinare il contenuto di cui è stato eseguito il backup.
1. Nella casella **nome cartella** specificare il nome della cartella che si desidera creare nella condivisione file di destinazione con il contenuto ripristinato.
1. Selezionare se ignorare o sovrascrivere se sono presenti conflitti.
1. Dopo aver immesso i valori appropriati in tutte le caselle, fare clic su **OK**.

    ![Selezionare un percorso alternativo](./media/restore-afs/alternate-location.png)

1. Selezionare **Ripristina** per avviare l'operazione di ripristino.

    ![Selezionare Ripristina per avviare](./media/restore-afs/click-restore.png)

### <a name="item-level-recovery"></a>Ripristino a livello di elemento

È possibile utilizzare questa opzione di ripristino per ripristinare singoli file o cartelle nel percorso originale o in un percorso alternativo.

1. Selezionare l'opzione **ripristino file** nel riquadro **elemento di backup** che viene visualizzato dopo aver selezionato la condivisione file da ripristinare nel passaggio 5 della sezione [selezionare la condivisione file da ripristinare](#select-the-file-share-to-restore) .

    ![Seleziona ripristino file](./media/restore-afs/file-recovery.png)

1. Dopo aver selezionato **ripristino file**, viene aperto il riquadro **ripristino** con un menu **punto di ripristino** in cui è visualizzato un elenco di punti di ripristino disponibili per la condivisione file selezionata.

1. Selezionare il punto di ripristino che si desidera utilizzare per eseguire l'operazione di ripristino e selezionare **OK**.

    ![Seleziona punto di ripristino](./media/restore-afs/restore-point.png)

1. Dopo aver selezionato **OK**, il menu ripristina riquadro passa a **percorso ripristino**. In **percorso ripristino**specificare dove o come ripristinare i dati. Selezionare una delle due opzioni seguenti:

    * **Percorso originale**: ripristinare i file o le cartelle selezionati nella stessa condivisione file dell'origine originale.
    * **Percorso alternativo**: ripristinare i file o le cartelle selezionati in un percorso alternativo e mantengono il contenuto della condivisione file originale così come sono.

#### <a name="restore-to-the-original-location"></a>Ripristina nel percorso originale

1. Selezionare il **percorso originale** come **destinazione di ripristino**e scegliere se ignorare o sovrascrivere se sono presenti conflitti.

    ![Percorso originale per il ripristino a livello di elemento](./media/restore-afs/original-location-item-level.png)

1. Scegliere **Seleziona file** per selezionare i file o le cartelle che si desidera ripristinare.

    ![Scegliere Seleziona file](./media/restore-afs/select-file.png)

1. Dopo aver scelto **Seleziona file**, un riquadro condivisione file Visualizza il contenuto del punto di ripristino della condivisione file selezionato per il ripristino.

1. Selezionare la casella di controllo corrispondente al file o alla cartella che si desidera ripristinare e scegliere **Seleziona**.

    ![Seleziona file o cartella](./media/restore-afs/select-file-folder.png)

1. Ripetere i passaggi da 2 a 4 per selezionare più file o cartelle per il ripristino.
1. Dopo aver selezionato tutti gli elementi da ripristinare, fare clic su **OK**.

    ![Dopo aver selezionato tutti gli elementi da ripristinare, fare clic su OK.](./media/restore-afs/after-selecting-items.png)

1. Selezionare **Ripristina** per avviare l'operazione di ripristino.

    ![Selezionare Ripristina per avviare](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Ripristinare in un percorso alternativo

1. Selezionare il **percorso alternativo** come **destinazione di ripristino**.
1. Selezionare l'account di archiviazione di destinazione in cui si vuole ripristinare il contenuto di cui è stato eseguito il backup dall'elenco a discesa **account di archiviazione** .
1. L'elenco a discesa **Seleziona condivisione file** consente di visualizzare le condivisioni file presenti nell'account di archiviazione selezionato nel passaggio 2. Selezionare la condivisione file in cui si desidera ripristinare il contenuto di cui è stato eseguito il backup.
1. Nella casella **nome cartella** specificare il nome della cartella che si desidera creare nella condivisione file di destinazione con il contenuto ripristinato.
1. Selezionare se ignorare o sovrascrivere se sono presenti conflitti.
1. Scegliere **Seleziona file** per selezionare i file o le cartelle che si desidera ripristinare.

    ![Selezionare gli elementi da ripristinare in un percorso alternativo](./media/restore-afs/restore-to-alternate-location.png)

1. Quando si sceglie **Seleziona file**, un riquadro condivisione file Visualizza il contenuto del punto di ripristino della condivisione file selezionato per il ripristino.
1. Selezionare la casella di controllo corrispondente al file o alla cartella che si desidera ripristinare e scegliere **Seleziona**.

    ![Selezionare la destinazione di ripristino](./media/restore-afs/recovery-destination.png)

1. Ripetere i passaggi da 6 a 8 per selezionare più file o cartelle per il ripristino.
1. Dopo aver selezionato tutti gli elementi da ripristinare, fare clic su **OK**.

    ![Selezionare OK dopo aver selezionato tutti i file](./media/restore-afs/after-selecting-all-items.png)

1. Selezionare **Ripristina** per avviare l'operazione di ripristino.

## <a name="track-a-restore-operation"></a>Tenere traccia di un'operazione di ripristino

Dopo l'attivazione dell'operazione di ripristino, il servizio di backup crea un processo per tenerne traccia. Backup di Azure consente di visualizzare le notifiche relative al processo nel portale. Per visualizzare le operazioni per il processo, selezionare il collegamento ipertestuale notifiche.

![Collegamento ipertestuale selezionare le notifiche](./media/restore-afs/notifications-link.png)

È anche possibile monitorare lo stato di avanzamento del ripristino dall'insieme di credenziali di servizi di ripristino:

1. Aprire l'insieme di credenziali di servizi di ripristino da cui è stata attivata l'operazione di ripristino.
1. Nel riquadro Panoramica selezionare processi di **backup** nella sezione **monitoraggio** per visualizzare lo stato delle operazioni in esecuzione in carichi di lavoro diversi.

    ![Seleziona processi di backup](./media/restore-afs/backup-jobs.png)

1. Selezionare il nome del carico di lavoro corrispondente alla condivisione file per visualizzare altri dettagli sull'operazione di ripristino, ad esempio i **dati trasferiti** e il **numero di file ripristinati**.

    ![Vedere i dettagli ripristinati](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [gestire i backup di condivisioni file di Azure](manage-afs-backup.md).
