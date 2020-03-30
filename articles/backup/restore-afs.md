---
title: Ripristinare le condivisioni file di AzureRestore Azure file shares
description: Informazioni su come usare il portale di Azure per ripristinare un'intera condivisione file o file specifici da un punto di ripristino creato da Backup di Azure.Learn how to use the Azure portal to restore an entire file share or specific files from a restore point created by Azure Backup.
ms.topic: conceptual
ms.date: 01/12/2020
ms.openlocfilehash: c22078ebd89f5f6f8299e1424d4d9e21edce8b92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586952"
---
# <a name="restore-azure-file-shares"></a>Ripristinare le condivisioni file di AzureRestore Azure file shares

Questo articolo illustra come usare il portale di Azure per ripristinare un'intera condivisione file o file specifici da un punto di ripristino creato da Backup di [Azure.](https://docs.microsoft.com/azure/backup/backup-overview)

In questo articolo si apprenderà come:

* Ripristinare una condivisione file di Azure completa.
* Ripristinare singoli file o cartelle.
* Tenere traccia dello stato dell'operazione di ripristino.

## <a name="steps-to-perform-a-restore-operation"></a>Passaggi per eseguire un'operazione di ripristinoSteps to perform a restore operation

Per eseguire un'operazione di ripristino, attenersi alla seguente procedura.

### <a name="select-the-file-share-to-restore"></a>Selezionare la condivisione file da ripristinare

1. Nel [portale di Azure](https://portal.azure.com/)aprire l'insieme di credenziali di Servizi di ripristino usato per configurare il backup per la condivisione file.

1. Nel riquadro Panoramica selezionare **Elementi di backup** nella sezione Elementi **protetti.**

    ![Selezionare Elementi di backup](./media/restore-afs/backup-items.png)

1. Dopo aver selezionato **Elementi di backup**, accanto al riquadro Panoramica verrà visualizzato un nuovo riquadro in cui sono elencati tutti i tipi di gestione dei backup.

    ![Tipi di gestione dei backup](./media/restore-afs/backup-management.png)

1. Nel riquadro **Elementi di backup,** in Tipo di **gestione backup,** selezionare **Archiviazione di Azure (File di Azure)**. Verrà visualizzato un elenco di tutte le condivisioni file e degli account di archiviazione corrispondenti di cui è stato eseguito il backup usando questo insieme di credenziali.

    ![Elenco di tutte le condivisioni file](./media/restore-afs/file-shares.png)

1. Nell'elenco delle condivisioni file di Azure selezionare la condivisione file per cui si vuole eseguire l'operazione di ripristino.

### <a name="full-share-recovery"></a>Recupero completo delle azioni

È possibile utilizzare questa opzione di ripristino per ripristinare la condivisione file completa nel percorso originale o in un percorso alternativo.

1. Selezionare l'opzione **Ripristina condivisione** nel riquadro Elemento di **backup** visualizzato dopo aver selezionato la condivisione file da ripristinare nel passaggio 5 della sezione [Selezionare la condivisione file da ripristinare.](#select-the-file-share-to-restore)

   ![Seleziona Ripristina condivisione](./media/restore-afs/restore-share.png)

1. Dopo aver selezionato **Ripristina condivisione**, il riquadro **Ripristina** si apre con un menu Punto di **ripristino** che visualizza un elenco di punti di ripristino disponibili per la condivisione file selezionata.

1. Selezionare il punto di ripristino che si desidera utilizzare per eseguire l'operazione di ripristino, quindi scegliere **OK**.

    ![Seleziona punto di ripristino](./media/restore-afs/restore-point.png)

1. Dopo aver selezionato **OK**, il menu del riquadro **Ripristino** passa a **Percorso di ripristino**. In **Percorso di ripristino**specificare dove o come ripristinare i dati. Selezionare una delle due opzioni seguenti:

    * **Posizione originale**: Ripristina la condivisione file completa nello stesso percorso dell'origine originale.
    * **Percorso alternativo**: Ripristinare la condivisione file completa in un percorso alternativo e mantenere la condivisione file originale così com'è.

#### <a name="restore-to-the-original-location"></a>Ripristina nella posizione originale

1. Selezionare **Posizione originale** come **destinazione di ripristino**e scegliere se ignorare o sovrascrivere in caso di conflitti. Dopo aver effettuato la selezione appropriata, selezionare **OK**.

    ![Seleziona posizione originale](./media/restore-afs/original-location.png)

1. Selezionare **Ripristina** per avviare l'operazione di ripristino.

    ![Selezionare Ripristina per avviare](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Ripristinare in un percorso alternativo

1. Selezionare **Percorso alternativo** come **destinazione di ripristino**.
1. Selezionare l'account di archiviazione di destinazione in cui si vuole ripristinare il contenuto di cui è stato eseguito il backup dall'elenco a discesa **Account di archiviazione.**
1. Nell'elenco a discesa **Seleziona condivisione file** vengono visualizzate le condivisioni file presenti nell'account di archiviazione selezionato nel passaggio 2. Selezionare la condivisione file in cui si desidera ripristinare il contenuto di cui è stato eseguito il backup.
1. Nella casella **Nome cartella** specificare un nome di cartella che si desidera creare nella condivisione file di destinazione con il contenuto ripristinato.
1. Selezionare se ignorare o sovrascrivere in caso di conflitti.
1. Dopo aver immesso i valori appropriati in tutte le caselle, selezionare **OK**.

    ![Seleziona posizione alternativa](./media/restore-afs/alternate-location.png)

1. Selezionare **Ripristina** per avviare l'operazione di ripristino.

    ![Selezionare Ripristina per avviare](./media/restore-afs/click-restore.png)

### <a name="item-level-recovery"></a>Ripristino a livello di elemento

È possibile utilizzare questa opzione di ripristino per ripristinare singoli file o cartelle nel percorso originale o in un percorso alternativo.

1. Selezionare l'opzione **Ripristino file** nel riquadro Elemento di **backup** visualizzato dopo aver selezionato la condivisione file da ripristinare nel passaggio 5 della sezione [Selezionare la condivisione file da ripristinare.](#select-the-file-share-to-restore)

    ![Seleziona recupero file](./media/restore-afs/file-recovery.png)

1. Dopo aver selezionato **Recupero file**, il riquadro **Ripristino** si apre con un menu Punto di **ripristino** che visualizza un elenco di punti di ripristino disponibili per la condivisione file selezionata.

1. Selezionare il punto di ripristino che si desidera utilizzare per eseguire l'operazione di ripristino, quindi scegliere **OK**.

    ![Seleziona punto di ripristino](./media/restore-afs/restore-point.png)

1. Dopo aver selezionato **OK**, il menu del riquadro di ripristino passa a **Percorso di ripristino**. In **Percorso di ripristino**specificare dove o come ripristinare i dati. Selezionare una delle due opzioni seguenti:

    * **Posizione originale**: Ripristina i file o le cartelle selezionati nella stessa condivisione file dell'origine originale.
    * **Posizione alternativa**: Ripristinare i file o le cartelle selezionati in una posizione alternativa e mantenere il contenuto della condivisione file originale così com'è.

#### <a name="restore-to-the-original-location"></a>Ripristina nella posizione originale

1. Selezionare **Posizione originale** come **destinazione di ripristino**e scegliere se ignorare o sovrascrivere in caso di conflitti.

    ![Posizione originale per il ripristino a livello di elemento](./media/restore-afs/original-location-item-level.png)

1. Scegliere **Seleziona file** per selezionare i file o le cartelle da ripristinare.

    ![Scegliere Seleziona file](./media/restore-afs/select-file.png)

1. Dopo aver scelto **Seleziona file**, un riquadro di condivisione file visualizza il contenuto del punto di ripristino della condivisione file selezionato per il ripristino.

1. Selezionare la casella di controllo corrispondente al file o alla cartella che si desidera ripristinare e scegliere **Seleziona**.

    ![Selezionare un file o una cartella](./media/restore-afs/select-file-folder.png)

1. Ripetere i passaggi da 2 a 4 per selezionare più file o cartelle da ripristinare.
1. Dopo aver selezionato tutti gli elementi da ripristinare, selezionare **OK**.

    ![Dopo aver selezionato tutti gli elementi da ripristinare, selezionare OK](./media/restore-afs/after-selecting-items.png)

1. Selezionare **Ripristina** per avviare l'operazione di ripristino.

    ![Selezionare Ripristina per avviare](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Ripristinare in un percorso alternativo

1. Selezionare **Percorso alternativo** come **destinazione di ripristino**.
1. Selezionare l'account di archiviazione di destinazione in cui si vuole ripristinare il contenuto di cui è stato eseguito il backup dall'elenco a discesa **Account di archiviazione.**
1. Nell'elenco a discesa **Seleziona condivisione file** vengono visualizzate le condivisioni file presenti nell'account di archiviazione selezionato nel passaggio 2. Selezionare la condivisione file in cui si desidera ripristinare il contenuto di cui è stato eseguito il backup.
1. Nella casella **Nome cartella** specificare un nome di cartella che si desidera creare nella condivisione file di destinazione con il contenuto ripristinato.
1. Selezionare se ignorare o sovrascrivere in caso di conflitti.
1. Scegliere **Seleziona file** per selezionare i file o le cartelle da ripristinare.

    ![Selezionare gli elementi da ripristinare in una posizione alternativa](./media/restore-afs/restore-to-alternate-location.png)

1. Quando si sceglie **Seleziona file**, in un riquadro di condivisione file viene visualizzato il contenuto del punto di ripristino della condivisione file selezionato per il ripristino.
1. Selezionare la casella di controllo corrispondente al file o alla cartella che si desidera ripristinare e scegliere **Seleziona**.

    ![Seleziona destinazione di ripristino](./media/restore-afs/recovery-destination.png)

1. Ripetere i passaggi da 6 a 8 per selezionare più file o cartelle da ripristinare.
1. Dopo aver selezionato tutti gli elementi da ripristinare, selezionare **OK**.

    ![Selezionare OK dopo aver selezionato tutti i file](./media/restore-afs/after-selecting-all-items.png)

1. Selezionare **Ripristina** per avviare l'operazione di ripristino.

## <a name="track-a-restore-operation"></a>Tenere traccia di un'operazione di ripristino

Dopo l'attivazione dell'operazione di ripristino, il servizio di backup crea un processo per tenerne traccia. Backup di Azure consente di visualizzare le notifiche relative al processo nel portale. Per visualizzare le operazioni per il processo, selezionare il collegamento ipertestuale delle notifiche.

![Collegamento ipertestuale Seleziona notifiche](./media/restore-afs/notifications-link.png)

È inoltre possibile monitorare lo stato di avanzamento del ripristino dall'insieme di credenziali di Servizi di ripristino:

1. Aprire l'insieme di credenziali dei servizi di ripristino da cui è stata attivata l'operazione di ripristino.
1. Nel riquadro Panoramica selezionare **Processi di backup** nella sezione **Monitoraggio** per visualizzare lo stato delle operazioni in esecuzione su carichi di lavoro diversi.

    ![Seleziona processi di backup](./media/restore-afs/backup-jobs.png)

1. Selezionare il nome del carico di lavoro corrispondente alla condivisione file per visualizzare ulteriori dettagli sull'operazione di ripristino, ad esempio **Dati trasferiti** e **Numero di file ripristinati**.

    ![Visualizzare i dettagli ripristinati](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come gestire i backup di [condivisione file di Azure.](manage-afs-backup.md)
