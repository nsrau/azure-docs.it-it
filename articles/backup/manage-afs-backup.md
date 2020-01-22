---
title: Gestire i backup di condivisioni file di Azure
description: Questo articolo descrive le attività comuni per la gestione e il monitoraggio delle condivisioni file di Azure di cui è stato eseguito il backup dal servizio backup di Azure.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: a5477d021b6e3600693e183d8707e11592b7cc38
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294552"
---
# <a name="manage-azure-file-share-backups"></a>Gestire i backup di condivisioni file di Azure

Questo articolo descrive le attività comuni per la gestione e il monitoraggio delle condivisioni file di Azure di cui è stato eseguito il backup dal servizio [backup di Azure](https://docs.microsoft.com/azure/backup/backup-overview) . Si apprenderà come eseguire le attività di gestione seguenti nell'insieme di credenziali di servizi di ripristino:

* [Monitorare i processi](#monitor-jobs)
* [Creare nuovi criteri](#create-a-new-policy)
* [Modificare i criteri](#modify-policy)
* [Interrompere la protezione in una condivisione file](#stop-protection-on-a-file-share)
* [Riprendere la protezione in una condivisione file](#resume-protection-on-a-file-share)
* [Eliminare i dati di backup](#delete-backup-data)
* [Annulla la registrazione dell'account di archiviazione](#unregister-storage-account)

## <a name="monitor-jobs"></a>Monitorare i processi

Quando si attiva un'operazione di backup o ripristino, il servizio di backup crea un processo per il rilevamento. È possibile monitorare lo stato di avanzamento di tutti i processi nella pagina **Processi di backup**.

Per aprire la pagina **Processi di backup**:

1. Aprire l'insieme di credenziali di servizi di ripristino usato per configurare il backup per le condivisioni file. Nel pannello **Panoramica** fare clic su **processi di backup** nella sezione **monitoraggio** .

   ![Processi di backup nella sezione monitoraggio](./media/manage-afs-backup/backup-jobs.png)

2. Quando si fa clic su OK, viene visualizzato il pannello **processi di backup** che elenca lo stato di tutti i processi. È possibile fare clic sul nome del carico di lavoro corrispondente alla condivisione file che si desidera monitorare.

   ![Nome del carico di lavoro](./media/manage-afs-backup/workload-name.png)

## <a name="create-a-new-policy"></a>Creare nuovi criteri

È possibile creare un nuovo criterio per eseguire il backup delle condivisioni file di Azure dalla sezione **criteri di backup** dell'insieme di credenziali di servizi di ripristino. Tutti i criteri creati quando è stato configurato il backup per le condivisioni file vengono visualizzati con il tipo di criterio come condivisione file di Azure.

Per visualizzare i criteri di backup esistenti:

1. Aprire l'insieme di credenziali di servizi di ripristino usato per configurare il backup per la condivisione file e nel menu dell'insieme di credenziali dei servizi di ripristino fare clic su **criteri di backup** nella sezione Gestisci. Verranno elencati tutti i criteri di backup configurati nell'insieme di credenziali.

   ![Tutti i criteri di backup](./media/manage-afs-backup/all-backup-policies.png)

2. Per visualizzare i criteri specifici della condivisione file di Azure, selezionare **condivisione file di Azure** dall'elenco a discesa in alto a destra.

   ![Scegliere la condivisione file di Azure](./media/manage-afs-backup/azure-file-share.png)

Per creare un nuovo criterio di backup:

1. Fare clic su **+ Aggiungi** nel pannello criteri di backup.

   ![Nuovi criteri di backup](./media/manage-afs-backup/new-backup-policy.png)

2. Selezionare **condivisione file di Azure** come **tipo di criteri** nel pannello **Aggiungi** . Viene aperto il pannello criteri di backup per la condivisione file di Azure. Specificare il nome del criterio, la frequenza di backup e il periodo di mantenimento dati per i punti di ripristino. Quando il criterio è stato definito, fare clic su **OK** .

   ![Definire i criteri di backup](./media/manage-afs-backup/define-backup-policy.png)

## <a name="modify-policy"></a>Modifica criterio

È possibile modificare i criteri di backup per modificare la frequenza di backup o il periodo di mantenimento dati.

Per modificare un criterio:

1. Aprire l'insieme di credenziali di servizi di ripristino usato per configurare il backup per la condivisione file e nel menu dell'insieme di credenziali di servizi di ripristino fare clic su **criteri di backup** nella sezione Gestisci. Verranno elencati tutti i criteri di backup configurati nell'insieme di credenziali.

   ![Tutti i criteri di backup nell'insieme di credenziali](./media/manage-afs-backup/all-backup-policies-modify.png)

2. Per visualizzare i criteri specifici di una condivisione file di Azure, selezionare **condivisione file di Azure** dall'elenco a discesa in alto a destra. Fare clic sul criterio di backup che si desidera modificare.

   ![Condivisione file di Azure da modificare](./media/manage-afs-backup/azure-file-share-modify.png)

3. Si aprirà il pannello **pianificazione** . Modificare la pianificazione del backup o il periodo di mantenimento dati in base alle esigenze e fare clic su **Salva**. Nel pannello verrà visualizzato il messaggio "aggiornamento in corso" e quando le modifiche apportate ai criteri verranno aggiornate correttamente, verrà visualizzato il messaggio "aggiornamento dei criteri di backup completato".

   ![Salva il criterio modificato](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>Interrompere la protezione in una condivisione file

Per interrompere la protezione di condivisioni file di Azure è possibile procedere in due modi:

* Arrestare tutti i processi di backup futuri ed *eliminare tutti i punti di ripristino*
* Arrestare tutti i processi di backup futuri, *lasciando i punti di ripristino*

È possibile che vi sia un costo associato a lasciare i punti di ripristino nella risorsa di archiviazione, poiché verranno conservati gli snapshot sottostanti creati da backup di Azure. Il vantaggio offerto dalla conservazione dei punti di ripristino è tuttavia la possibilità di ripristinare eventualmente la condivisione file in un secondo momento. Per informazioni sul costo associato al mantenimento dei punti di ripristino, vedere [Dettagli prezzi](https://azure.microsoft.com/pricing/details/backup/). Se si sceglie di eliminare tutti i punti di ripristino, non sarà possibile ripristinare la condivisione file.

Per interrompere la protezione di una condivisione file di Azure:

1. Aprire l'insieme di credenziali di servizi di ripristino che contiene i punti di ripristino della condivisione file e fare clic su **elementi di backup** nella sezione elementi protetti. Verrà visualizzato l'elenco degli elementi di backup.

   ![Elementi di backup](./media/manage-afs-backup/backup-items.png)

2. Nell'elenco **Tipo di gestione di backup** selezionare **Archiviazione di Azure (File di Azure)** . Viene visualizzato l'elenco degli **elementi di backup per (archiviazione di Azure (file di Azure))** .

   ![Selezionare archiviazione di Azure (File di Azure)](./media/manage-afs-backup/azure-storage-azure-files.png)

3. Nell'elenco degli **elementi di backup (archiviazione di Azure (file di Azure))** selezionare l'elemento di backup per il quale si desidera arrestare la protezione.

4. Selezionare l'opzione **Interrompi backup** nel menu del pannello dell' **elemento di backup** .

   ![Selezionare Interrompi backup](./media/manage-afs-backup/stop-backup.png)

5. Nel pannello **Interrompi backup** scegliere di **mantenere i dati di backup** o **eliminare i dati di backup** e fare clic su **Interrompi backup**.

    ![Scegliere Mantieni o Elimina dati di backup](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>Riprendere la protezione in una condivisione file

Se è stata scelta l'opzione **Mantieni dati di backup** quando è stata arrestata la protezione per la condivisione file, è possibile riprendere la protezione. Se è stata scelta l'opzione **Elimina dati di backup**, la protezione della condivisione file non può essere ripresa.

Per riprendere la protezione per la condivisione file di Azure:

1. Aprire l'insieme di credenziali di servizi di ripristino che contiene i punti di ripristino della condivisione file e fare clic su **elementi di backup** nella sezione elementi protetti. Verrà visualizzato l'elenco degli elementi di backup.

   ![Elementi di backup per Resume](./media/manage-afs-backup/backup-items-resume.png)

2. Nell'elenco **Tipo di gestione di backup** selezionare **Archiviazione di Azure (File di Azure)** . Viene visualizzato l'elenco degli **elementi di backup per (archiviazione di Azure (file di Azure))** .

   ![Elenco di archiviazione di Azure (File di Azure)](./media/manage-afs-backup/azure-storage-azure-files.png)

3. Nell'elenco degli **elementi di backup (archiviazione di Azure (file di Azure))** selezionare l'elemento di backup per il quale si desidera riprendere la protezione.

4. Selezionare l'opzione **Riprendi backup** dal menu del pannello dell' **elemento di backup** .

   ![Seleziona Riprendi backup](./media/manage-afs-backup/resume-backup.png)

5. Il pannello **criteri di backup** si aprirà ed è possibile scegliere un criterio di propria scelta per riprendere il backup.

6. Fare clic su **Salva** dopo aver selezionato i **criteri di backup**desiderati. Verrà visualizzato un messaggio di aggiornamento in corso nel portale e una volta che il backup verrà ripreso correttamente, verrà visualizzato il messaggio "aggiornamento dei criteri di backup per la condivisione file di Azure protetta" completato.

   ![I criteri di backup sono stati aggiornati](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>Elimina dati di backup

È possibile eliminare il backup di una condivisione file durante il processo di **arresto del backup** o in qualsiasi momento dopo aver interrotto la protezione. Attendere settimane o mesi prima di eliminare i punti di ripristino potrebbe anche essere utile. Quando si eliminano i dati di backup, non è possibile scegliere punti di ripristino specifici da eliminare. Se si sceglie di eliminare i dati di backup, si eliminano tutti i punti di ripristino associati alla condivisione file.

Nella procedura seguente si presuppone che la protezione sia stata interrotta per la condivisione file.

Per eliminare i dati di backup per la condivisione file di Azure:

1. Una volta arrestato il processo di backup, nel dashboard dell' **elemento di backup** sono disponibili le opzioni **Riprendi backup** ed **Elimina dati di backup** . Fare clic sull'opzione **Elimina dati di backup** nel menu del pannello dell' **elemento di backup** .

   ![Elimina dati di backup](./media/manage-afs-backup/delete-backup-data.png)

2. Viene visualizzato il pannello **Elimina dati di backup** . Digitare il nome della condivisione file per confermare l'eliminazione. Facoltativamente, specificare un **motivo** per l'eliminazione o il **Commento**. Fare clic su **Elimina** quando si è certi di eliminare i dati di backup.

   ![Confermare l'eliminazione dei dati](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-storage-account"></a>Annulla la registrazione dell'account di archiviazione

Se si vuole proteggere le condivisioni file in un account di archiviazione specifico usando un insieme di credenziali di servizi di ripristino diverso, [arrestare prima di tutto la protezione per tutte le condivisioni file](#stop-protection-on-a-file-share) nell'account di archiviazione. Annullare quindi la registrazione dell'account dall'insieme di credenziali dei servizi di ripristino corrente usato per la protezione.

La procedura seguente presuppone che la protezione sia stata interrotta per tutte le condivisioni file nell'account di archiviazione di cui si vuole annullare la registrazione.

Per annullare la registrazione dell'account di archiviazione:

1. Aprire l'insieme di credenziali di servizi di ripristino in cui è registrato l'account di archiviazione.
2. Fare clic sull'opzione **infrastruttura di backup** nella sezione **Gestisci** del pannello **Panoramica** .

   ![Fare clic su infrastruttura di backup](./media/manage-afs-backup/backup-infrastructure.png)

3. Viene visualizzato il pannello **infrastruttura di backup** . Fare clic su **account di archiviazione** nella sezione account di **archiviazione di Azure** in questo pannello.

   ![Fare clic su account di archiviazione](./media/manage-afs-backup/storage-accounts.png)

4. Quando si fa clic su **account di archiviazione**, verrà visualizzato un elenco di account di archiviazione registrati con l'insieme di credenziali.
5. Fare clic con il pulsante destro del mouse sull'account di archiviazione di cui si vuole annullare la registrazione e scegliere **Annulla registrazione**.

   ![Selezionare Annulla registrazione](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [risolvere gli errori di backup/ripristino per le condivisioni file di Azure](https://docs.microsoft.com/azure/backup/troubleshoot-azure-files)
