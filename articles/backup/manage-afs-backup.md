---
title: Gestire i backup di condivisioni file di Azure
description: Questo articolo descrive le attività comuni per la gestione e il monitoraggio delle condivisioni file di Azure di cui è stato eseguito il backup da Backup di Azure.This article describes common tasks for managing and monitoring the Azure file shares that are backed up by Azure Backup.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: cb764fa441c063328dc350cf26f42c5bc7a0ca99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247657"
---
# <a name="manage-azure-file-share-backups"></a>Gestire i backup di condivisioni file di Azure

In questo articolo vengono descritte le attività comuni per la gestione e il monitoraggio delle condivisioni file di Azure di cui è stato eseguito il backup da Backup di [Azure.](https://docs.microsoft.com/azure/backup/backup-overview) Si apprenderà come eseguire attività di gestione nell'insieme di credenziali dei servizi di ripristino.

## <a name="monitor-jobs"></a>Monitorare i processi

Quando si attiva un'operazione di backup o ripristino, il servizio di backup crea un processo per il rilevamento. È possibile monitorare lo stato di avanzamento di tutti i processi nella pagina **Processi di backup**.

Per aprire la pagina **Processi di backup**:

1. Aprire il vault Servizi di ripristino utilizzato per configurare il backup per le condivisioni file. Nel riquadro **Panoramica** selezionare **Processi di backup** nella sezione Monitoraggio.In the Overview pane, select Backup Jobs under the **Monitoring** section.

   ![Sezione Processi di backup nel monitoraggio](./media/manage-afs-backup/backup-jobs.png)

1. Dopo aver selezionato **OK**, il riquadro Processi di **backup** elenca lo stato di tutti i processi. Selezionare il nome del carico di lavoro corrispondente alla condivisione file che si desidera monitorare.

   ![Nome del carico di lavoroWorkload name](./media/manage-afs-backup/workload-name.png)

## <a name="create-a-new-policy"></a>Creare nuovi criteri

È possibile creare un nuovo criterio per eseguire il backup delle condivisioni file di Azure dalla sezione Criteri di **backup** dell'insieme di credenziali di Servizi di ripristino. Tutti i criteri creati durante la configurazione del backup per le condivisioni file vengono visualizzati con il **tipo di criteri** come condivisione file di **Azure.**

Per visualizzare i criteri di backup esistenti:

1. Aprire il vault Servizi di ripristino utilizzato per configurare il backup per la condivisione file. Nel menu dell'insieme di credenziali di Servizi di ripristino selezionare **Criteri di backup** nella sezione **Gestisci.** Vengono visualizzati tutti i criteri di backup configurati nell'insieme di credenziali.

   ![Tutti i criteri di backup](./media/manage-afs-backup/all-backup-policies.png)

1. Per visualizzare i criteri specifici della **condivisione file**di Azure, selezionare **Condivisione file** di Azure dall'elenco a discesa in alto a destra.

   ![Selezionare Condivisione file di AzureSelect Azure File Share](./media/manage-afs-backup/azure-file-share.png)

Per creare un nuovo criterio di backup:

1. Nel riquadro Criteri di **backup,** selezionare **Aggiungi**.

   ![Nuovi criteri di backup](./media/manage-afs-backup/new-backup-policy.png)

1. Nel riquadro **Aggiungi** selezionare **Condivisione file** di Azure come **Tipo di criterio**. Verrà visualizzato il riquadro Criteri di backup per **Condivisione file** di Azure.The **Backup policy** pane for Azure File Share opens. Specificare il nome del criterio, la frequenza di backup e l'intervallo di conservazione per i punti di ripristino. Dopo aver definito il criterio, selezionare **OK**.

   ![Definire i criteri di backup](./media/manage-afs-backup/define-backup-policy.png)

## <a name="modify-policy"></a>Modifica dei criteri

È possibile modificare un criterio di backup per modificare la frequenza o l'intervallo di conservazione del backup.

Per modificare un criterio:

1. Aprire il vault Servizi di ripristino utilizzato per configurare il backup per la condivisione file. Nel menu dell'insieme di credenziali di Servizi di ripristino selezionare **Criteri di backup** nella sezione **Gestisci.** Vengono visualizzati tutti i criteri di backup configurati nell'insieme di credenziali.

   ![Tutti i criteri di backup nell'insieme di credenziali](./media/manage-afs-backup/all-backup-policies-modify.png)

1. Per visualizzare criteri specifici di una condivisione file di Azure, selezionare **Condivisione file** di Azure dall'elenco a discesa in alto a destra. Selezionare il criterio di backup che si desidera modificare.

   ![Condivisione file di Azure da modificareAzure file share to modify](./media/manage-afs-backup/azure-file-share-modify.png)

1. Viene visualizzato il riquadro **Pianificazione.** Modificare la pianificazione del **backup** e **l'intervallo di conservazione** in base alle esigenze, quindi selezionare **Salva**. Vedrai un messaggio "Aggiorna in corso" nel riquadro. Dopo che le modifiche ai criteri vengono aggiornate correttamente, verrà visualizzato il messaggio "Aggiornamento del criterio di backup completato".

   ![Salvare il criterio modificato](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>Interrompere la protezione in una condivisione file

Per interrompere la protezione di condivisioni file di Azure è possibile procedere in due modi:

* Arrestare tutti i processi di backup futuri ed *eliminare tutti i punti di ripristino*.
* Arrestare tutti i processi di backup futuri, ma *lasciare i punti*di ripristino .

Potrebbe esserci un costo associato all'abbandono dei punti di ripristino nell'archiviazione, perché gli snapshot sottostanti creati da Backup di Azure verranno mantenuti. Il vantaggio di lasciare i punti di ripristino è che è possibile ripristinare la condivisione file in un secondo momento. Per informazioni sul costo dell'uscita dai punti di ripristino, vedere i dettagli sui [prezzi.](https://azure.microsoft.com/pricing/details/backup/) Se si decide di eliminare tutti i punti di ripristino, non è possibile ripristinare la condivisione file.

Per interrompere la protezione di una condivisione file di Azure:

1. Aprire l'insieme di credenziali di Servizi di ripristino contenente i punti di ripristino della condivisione file. Selezionare **Elementi di backup** nella sezione Elementi **protetti.** Viene visualizzato l'elenco dei tipi di elementi di backup.

   ![Elementi di backup](./media/manage-afs-backup/backup-items.png)

1. Nell'elenco **Tipo di gestione di backup** selezionare **Archiviazione di Azure (File di Azure)**. Viene visualizzato l'elenco **Elementi di backup (Archiviazione di Azure (file di Azure).**

   ![Selezionare Archiviazione di Azure (file di Azure)Select Azure Storage (Azure Files)](./media/manage-afs-backup/azure-storage-azure-files.png)

1. Nell'elenco Elementi di backup (File di **Azure)selezionare** l'elemento di backup per il quale si vuole interrompere la protezione.

1. Selezionare l'opzione **Interrompi backup.**

   ![Selezionare Interrompi backup](./media/manage-afs-backup/stop-backup.png)

1. Nel riquadro **Interrompi backup** selezionare Mantieni dati **di backup** o Elimina dati **di backup**. Quindi selezionare **Interrompi backup**.

    ![Selezionare Mantieni dati di backup o Elimina dati di backup](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>Riprendere la protezione in una condivisione file

Se è stata selezionata l'opzione **Mantieni dati di backup** quando la protezione per la condivisione file è stata interrotta, è possibile riprendere la protezione. Se è stata selezionata l'opzione **Elimina dati di backup,** la protezione per la condivisione file non può essere ripresa.

Per riprendere la protezione per la condivisione file di Azure:To resume protection for the Azure file share:

1. Aprire l'insieme di credenziali di Servizi di ripristino contenente i punti di ripristino della condivisione file. Selezionare **Elementi di backup** nella sezione Elementi **protetti.** Viene visualizzato l'elenco dei tipi di elementi di backup.

   ![Elementi di backup per la ripresa](./media/manage-afs-backup/backup-items-resume.png)

1. Nell'elenco **Tipo di gestione di backup** selezionare **Archiviazione di Azure (File di Azure)**. Viene visualizzato l'elenco **Elementi di backup (Archiviazione di Azure (file di Azure).**

   ![Elenco di Archiviazione di Azure (file di Azure)Elenco of Azure Storage (Azure Files)](./media/manage-afs-backup/azure-storage-azure-files.png)

1. Nell'elenco Elementi di backup (File di **Azure)selezionare** l'elemento di backup per il quale si vuole riprendere la protezione.

1. Selezionare l'opzione **Riprendi backup.**

   ![Selezionare Riprendi backup](./media/manage-afs-backup/resume-backup.png)

1. Viene visualizzato il riquadro **Criteri di backup.** Selezionare un criterio di propria scelta per riprendere il backup.

1. Dopo aver selezionato un criterio di backup, selezionare **Salva**. Vedrai un messaggio "Aggiornamento in corso" nel portale. Dopo la ripresa del backup, verrà visualizzato il messaggio "Criteri di backup aggiornati correttamente per la condivisione file di Azure protetto".

   ![Criteri di backup aggiornati correttamente](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>Elimina dati di backup

È possibile eliminare il backup di una condivisione file durante il processo **di arresto del backup** o in qualsiasi momento dopo l'interruzione della protezione. Potrebbe essere utile attendere giorni o addirittura settimane prima di eliminare i punti di ripristino. Quando si eliminano i dati di backup, non è possibile scegliere punti di ripristino specifici da eliminare. Se si decide di eliminare i dati di backup, si eliminano tutti i punti di ripristino associati alla condivisione file.

Nella procedura seguente si presuppone che la protezione sia stata interrotta per la condivisione file.

Per eliminare i dati di backup per la condivisione file di Azure:To delete backup data for the Azure file share:

1. Dopo l'arresto del processo di backup, le opzioni **Riprendi backup** ed **Elimina dati di backup** sono disponibili nel dashboard Elemento di **backup.** Selezionare l'opzione **Elimina dati di backup.**

   ![Elimina dati di backup](./media/manage-afs-backup/delete-backup-data.png)

1. Viene visualizzato il riquadro **Elimina dati di backup.** Immettere il nome della condivisione file per confermare l'eliminazione. Facoltativamente, fornire ulteriori informazioni nelle caselle **Motivo** o **Commenti.** Dopo aver verificato l'eliminazione dei dati di backup, selezionare **Elimina**.

   ![Confermare l'eliminazione dei dati](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-a-storage-account"></a>Annullare la registrazione di un account di archiviazioneUnregister a storage account

Per proteggere le condivisioni file in un account di archiviazione specifico usando un insieme di credenziali dei servizi di ripristino diverso, arrestare innanzitutto la [protezione per tutte le condivisioni file](#stop-protection-on-a-file-share) in tale account di archiviazione. Annullare quindi la registrazione dell'account dall'insieme di credenziali dei servizi di ripristino corrente utilizzato per la protezione.

Nella procedura seguente si presuppone che la protezione sia stata interrotta per tutte le condivisioni file nell'account di archiviazione di cui si vuole annullare la registrazione.

Per annullare la registrazione dell'account di archiviazione:To unregister the storage account:

1. Aprire l'insieme di credenziali dei servizi di ripristino in cui è registrato l'account di archiviazione.
1. Nel riquadro **Panoramica** selezionare l'opzione **Infrastruttura di backup** nella sezione **Gestisci.**

   ![Selezionare Infrastruttura di backup](./media/manage-afs-backup/backup-infrastructure.png)

1. Viene visualizzato il riquadro **Infrastruttura di backup.** Selezionare **Account di archiviazione** nella sezione Account di archiviazione di Azure.Select Storage Accounts under the Azure Storage **Accounts** section.

   ![Selezionare Account di archiviazione](./media/manage-afs-backup/storage-accounts.png)

1. Dopo aver selezionato **Account di archiviazione**, viene visualizzato un elenco di account di archiviazione registrati con l'insieme di credenziali.
1. Fare clic con il pulsante destro del mouse sull'account di archiviazione di cui si desidera annullare la registrazione e scegliere **Annulla registrazione**.

   ![Selezionare Annulla registrazione](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [Risolvere i problemi di backup delle condivisioni file](https://docs.microsoft.com/azure/backup/troubleshoot-azure-files)di Azure.For more information, see Troubleshoot Azure file shares backup .
