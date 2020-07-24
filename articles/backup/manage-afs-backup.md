---
title: Gestire i backup di condivisioni file di Azure
description: Questo articolo descrive le attività comuni per la gestione e il monitoraggio delle condivisioni file di Azure di cui è stato eseguito il backup da backup di Azure.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 6684cb348c9edb35b5f3e46fb7922e9aa265c725
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87073301"
---
# <a name="manage-azure-file-share-backups"></a>Gestire i backup di condivisioni file di Azure

Questo articolo descrive le attività comuni per la gestione e il monitoraggio delle condivisioni file di Azure di cui è stato eseguito il [backup da backup di Azure](./backup-overview.md). Si apprenderà come eseguire le attività di gestione nell'insieme di credenziali di servizi di ripristino.

## <a name="monitor-jobs"></a>Monitorare i processi

Quando si attiva un'operazione di backup o ripristino, il servizio di backup crea un processo per il rilevamento. È possibile monitorare lo stato di avanzamento di tutti i processi nella pagina **Processi di backup**.

Per aprire la pagina **Processi di backup**:

1. Aprire l'insieme di credenziali di servizi di ripristino usato per configurare il backup per le condivisioni file. Nel riquadro **Panoramica** selezionare processi di **backup** nella sezione **monitoraggio** .

   ![Processi di backup nella sezione monitoraggio](./media/manage-afs-backup/backup-jobs.png)

1. Dopo aver selezionato **OK**, il riquadro **processi di backup** elenca lo stato di tutti i processi. Selezionare il nome del carico di lavoro corrispondente alla condivisione file che si desidera monitorare.

   ![Nome del carico di lavoro](./media/manage-afs-backup/workload-name.png)

## <a name="create-a-new-policy"></a>Creare nuovi criteri

È possibile creare un nuovo criterio per eseguire il backup delle condivisioni file di Azure dalla sezione **criteri di backup** dell'insieme di credenziali di servizi di ripristino. Tutti i criteri creati quando è stato configurato il backup per le condivisioni file vengono visualizzati con il **tipo di criterio** come **condivisione file di Azure**.

Per visualizzare i criteri di backup esistenti:

1. Aprire l'insieme di credenziali di servizi di ripristino usato per configurare il backup per la condivisione file. Nel menu dell'insieme di credenziali di servizi di ripristino selezionare **criteri di backup** nella sezione **Gestisci** . Vengono visualizzati tutti i criteri di backup configurati nell'insieme di credenziali.

   ![Tutti i criteri di backup](./media/manage-afs-backup/all-backup-policies.png)

1. Per visualizzare i criteri specifici della **condivisione file di Azure**, selezionare **condivisione file di Azure** dall'elenco a discesa in alto a destra.

   ![Selezionare la condivisione file di Azure](./media/manage-afs-backup/azure-file-share.png)

Per creare un nuovo criterio di backup:

1. Nel riquadro **criteri di backup** selezionare **+ Aggiungi**.

   ![Nuovo criterio di backup](./media/manage-afs-backup/new-backup-policy.png)

1. Nel riquadro **Aggiungi** selezionare **condivisione file di Azure** come tipo di **criteri**. Viene visualizzato il riquadro **criteri di backup** per la **condivisione file di Azure** . Specificare il nome del criterio, la frequenza di backup e il periodo di mantenimento dati per i punti di ripristino. Dopo aver definito i criteri, fare clic su **OK**.

   ![Definire i criteri di backup](./media/manage-afs-backup/define-backup-policy.png)

## <a name="modify-policy"></a>Modifica dei criteri

È possibile modificare i criteri di backup per modificare la frequenza di backup o il periodo di mantenimento dati.

Per modificare un criterio:

1. Aprire l'insieme di credenziali di servizi di ripristino usato per configurare il backup per la condivisione file. Nel menu dell'insieme di credenziali di servizi di ripristino selezionare **criteri di backup** nella sezione **Gestisci** . Vengono visualizzati tutti i criteri di backup configurati nell'insieme di credenziali.

   ![Tutti i criteri di backup nell'insieme di credenziali](./media/manage-afs-backup/all-backup-policies-modify.png)

1. Per visualizzare i criteri specifici di una condivisione file di Azure, selezionare **condivisione file di Azure** dall'elenco a discesa in alto a destra. Selezionare il criterio di backup che si desidera modificare.

   ![Condivisione file di Azure da modificare](./media/manage-afs-backup/azure-file-share-modify.png)

1. Verrà visualizzato il riquadro **pianificazione** . Modificare la **pianificazione del backup** e il periodo di **mantenimento** dati in base alle esigenze e selezionare **Salva**. Nel riquadro verrà visualizzato il messaggio "aggiornamento in corso". Dopo che il criterio è stato aggiornato correttamente, verrà visualizzato il messaggio "aggiornamento dei criteri di backup completato".

   ![Salva il criterio modificato](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>Interrompere la protezione in una condivisione file

Per interrompere la protezione di condivisioni file di Azure è possibile procedere in due modi:

* Arrestare tutti i processi di backup futuri ed *eliminare tutti i punti di ripristino*.
* Arrestare tutti i processi di backup futuri, *lasciando i punti di ripristino*.

È possibile che vi sia un costo associato a lasciare i punti di ripristino nella risorsa di archiviazione perché gli snapshot sottostanti creati da backup di Azure verranno conservati. Il vantaggio di lasciare i punti di ripristino è che è possibile ripristinare la condivisione file in un secondo momento. Per informazioni sul costo di lasciare i punti di ripristino, vedere i [dettagli relativi ai prezzi](https://azure.microsoft.com/pricing/details/backup/). Se si decide di eliminare tutti i punti di ripristino, non è possibile ripristinare la condivisione file.

Per interrompere la protezione di una condivisione file di Azure:

1. Aprire l'insieme di credenziali di servizi di ripristino che contiene i punti di ripristino della condivisione file. Selezionare **elementi di backup** nella sezione **elementi protetti** . Viene visualizzato l'elenco dei tipi di elemento di backup.

   ![Elementi di backup](./media/manage-afs-backup/backup-items.png)

1. Nell'elenco **Tipo di gestione di backup** selezionare **Archiviazione di Azure (File di Azure)**. Viene visualizzato l'elenco **elementi di backup (archiviazione di Azure (file di Azure))** .

   ![Selezionare archiviazione di Azure (File di Azure)](./media/manage-afs-backup/azure-storage-azure-files.png)

1. Nell'elenco **elementi di backup (archiviazione di Azure (file di Azure))** selezionare l'elemento di backup per il quale si desidera arrestare la protezione.

1. Selezionare l'opzione **Interrompi backup** .

   ![Selezionare Interrompi backup](./media/manage-afs-backup/stop-backup.png)

1. Nel riquadro **Interrompi backup** selezionare **Mantieni dati di backup** o **Elimina dati di backup**. Selezionare quindi **Interrompi backup**.

    ![Selezionare Mantieni dati di backup o Elimina dati di backup](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>Riprendere la protezione in una condivisione file

Se è stata selezionata l'opzione **Mantieni dati di backup** quando la protezione per la condivisione file è stata arrestata, è possibile riprendere la protezione. Se è stata selezionata l'opzione **Elimina dati di backup** , non è possibile riprendere la protezione per la condivisione file.

Per riprendere la protezione per la condivisione file di Azure:

1. Aprire l'insieme di credenziali di servizi di ripristino che contiene i punti di ripristino della condivisione file. Selezionare **elementi di backup** nella sezione **elementi protetti** . Viene visualizzato l'elenco dei tipi di elemento di backup.

   ![Elementi di backup per Resume](./media/manage-afs-backup/backup-items-resume.png)

1. Nell'elenco **Tipo di gestione di backup** selezionare **Archiviazione di Azure (File di Azure)**. Viene visualizzato l'elenco **elementi di backup (archiviazione di Azure (file di Azure))** .

   ![Elenco di archiviazione di Azure (File di Azure)](./media/manage-afs-backup/azure-storage-azure-files.png)

1. Nell'elenco **elementi di backup (archiviazione di Azure (file di Azure))** selezionare l'elemento di backup per il quale si desidera riprendere la protezione.

1. Selezionare l'opzione **Riprendi backup** .

   ![Seleziona Riprendi backup](./media/manage-afs-backup/resume-backup.png)

1. Verrà visualizzato il riquadro **criteri di backup** . Selezionare un criterio scelto per riprendere il backup.

1. Dopo aver selezionato un criterio di backup, selezionare **Salva**. Nel portale verrà visualizzato un messaggio di aggiornamento in corso. Dopo la ripresa del backup, verrà visualizzato il messaggio "aggiornamento dei criteri di backup per la condivisione file di Azure protetta completata".

   ![I criteri di backup sono stati aggiornati](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>Elimina dati di backup

È possibile eliminare il backup di una condivisione file durante il processo di **arresto del backup** o in qualsiasi momento dopo l'arresto della protezione. Potrebbe essere utile attendere giorni o persino settimane prima di eliminare i punti di ripristino. Quando si eliminano i dati di backup, non è possibile scegliere punti di ripristino specifici da eliminare. Se si decide di eliminare i dati di backup, si eliminano tutti i punti di ripristino associati alla condivisione file.

Nella procedura seguente si presuppone che la protezione sia stata interrotta per la condivisione file.

Per eliminare i dati di backup per la condivisione file di Azure:

1. Dopo l'arresto del processo di backup, nel dashboard dell' **elemento di backup** sono disponibili le opzioni **Riprendi backup** ed **Elimina dati di backup** . Selezionare l'opzione **Elimina dati di backup** .

   ![Elimina dati di backup](./media/manage-afs-backup/delete-backup-data.png)

1. Verrà visualizzato il riquadro **Elimina dati di backup** . Immettere il nome della condivisione file per confermare l'eliminazione. Facoltativamente, fornire altre informazioni nelle caselle **motivo** o **Commenti** . Quando si è certi di eliminare i dati di backup, selezionare **Elimina**.

   ![Confermare l'eliminazione dei dati](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-a-storage-account"></a>Annullare la registrazione di un account di archiviazione

Per proteggere le condivisioni file in un account di archiviazione specifico usando un insieme di credenziali di servizi di ripristino diverso, arrestare prima di [tutto la protezione per tutte le condivisioni file](#stop-protection-on-a-file-share) nell'account di archiviazione. Annullare quindi la registrazione dell'account dall'insieme di credenziali dei servizi di ripristino corrente usato per la protezione.

La procedura seguente presuppone che la protezione sia stata interrotta per tutte le condivisioni file nell'account di archiviazione di cui si vuole annullare la registrazione.

Per annullare la registrazione dell'account di archiviazione:

1. Aprire l'insieme di credenziali di servizi di ripristino in cui è registrato l'account di archiviazione.
1. Nel riquadro **Panoramica** selezionare l'opzione **infrastruttura di backup** nella sezione **Gestisci** .

   ![Selezionare Infrastruttura di backup](./media/manage-afs-backup/backup-infrastructure.png)

1. Verrà visualizzato il riquadro **infrastruttura di backup** . Selezionare **account di archiviazione** nella sezione **account di archiviazione di Azure** .

   ![Selezionare gli account di archiviazione](./media/manage-afs-backup/storage-accounts.png)

1. Dopo aver selezionato gli **account di archiviazione**, viene visualizzato un elenco di account di archiviazione registrati con l'insieme di credenziali.
1. Fare clic con il pulsante destro del mouse sull'account di archiviazione di cui si vuole annullare la registrazione e selezionare **Annulla registrazione**.

   ![Selezionare Annulla registrazione](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [risolvere i problemi di backup di condivisioni file di Azure](./troubleshoot-azure-files.md).
