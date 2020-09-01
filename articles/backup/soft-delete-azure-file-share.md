---
title: Eliminare accidentalmente la protezione per le condivisioni file di Azure
description: Informazioni su come l'eliminazione temporanea può proteggere le condivisioni file di Azure da eliminazioni accidentali.
ms.topic: conceptual
ms.date: 02/02/2020
ms.custom: references_regions
ms.openlocfilehash: 52a116320e07c25d4ee7f00b8063ca15faeb8560
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2020
ms.locfileid: "89179913"
---
# <a name="accidental-delete-protection-for-azure-file-shares-using-azure-backup"></a>Eliminare accidentalmente la protezione per le condivisioni file di Azure con backup di Azure

Per garantire la protezione contro attacchi cibernetici o l'eliminazione accidentale, l' [eliminazione](../storage/files/storage-files-prevent-file-share-deletion.md) temporanea è abilitata per tutte le condivisioni file in un account di archiviazione quando si configura il backup per qualsiasi condivisione file nel rispettivo account di archiviazione. Con l'eliminazione temporanea, anche se un attore malintenzionato Elimina la condivisione file, il contenuto e i punti di ripristino della condivisione file (snapshot) vengono conservati per almeno 14 giorni aggiuntivi, consentendo il ripristino delle condivisioni file senza perdita di dati.  L'eliminazione temporanea è supportata per gli account di archiviazione standard e Premium e l'impostazione è abilitata da backup di Azure per tutti gli account di archiviazione che ospitano condivisioni file di cui è stato eseguito il backup.

Il diagramma di flusso seguente mostra i diversi passaggi e Stati di un elemento di backup quando l'eliminazione temporanea è abilitata per le condivisioni file in un account di archiviazione:

 ![Diagramma di flusso di eliminazione temporanea](./media/soft-delete-afs/soft-delete-flow-chart.png)

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="when-will-soft-delete-be-enabled-for-file-shares-in-my-storage-account"></a>Quando l'eliminazione temporanea sarà abilitata per le condivisioni file nell'account di archiviazione?

Quando si configura il backup per la prima volta per una condivisione file in un account di archiviazione, il servizio backup di Azure Abilita l'eliminazione temporanea per tutte le condivisioni file nel rispettivo account di archiviazione.

### <a name="can-i-configure-the-number-of-days-for-which-my-snapshots-and-restore-points-will-be-retained-in-soft-deleted-state-after-i-delete-the-file-share"></a>È possibile configurare il numero di giorni per cui gli snapshot e i punti di ripristino verranno conservati in uno stato di eliminazione temporanea dopo l'eliminazione della condivisione file?

Sì, è possibile impostare il periodo di conservazione in base ai requisiti. In [questo documento](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal) vengono illustrati i passaggi per configurare il periodo di conservazione. Per gli account di archiviazione con condivisioni file di cui è stato eseguito il backup, l'impostazione minima del periodo di memorizzazione deve essere 14 giorni.

### <a name="does-azure-backup-reset-my-retention-setting-because-i-configured-it-to-less-than-14-days"></a>Backup di Azure Reimposta l'impostazione di conservazione perché ho configurato un valore inferiore a 14 giorni?

Dal punto di vista della sicurezza, è consigliabile avere una conservazione minima di 14 giorni per gli account di archiviazione con condivisioni file di cui è stato eseguito il backup. Quindi, durante l'esecuzione di ogni processo di backup, se backup di Azure identifica l'impostazione su un valore inferiore a 14 giorni, viene reimpostato su 14 giorni.

### <a name="what-is-the-cost-incurred-during-the-retention-period"></a>Qual è il costo sostenuto durante il periodo di conservazione?

Durante il periodo di eliminazione temporanea, il costo dell'istanza protetta e il costo di archiviazione degli snapshot restano invariati.  Inoltre, ti verrà addebitato il costo della capacità utilizzata alla tariffa normale per le condivisioni file standard e la frequenza di archiviazione snapshot per le condivisioni file Premium.

### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-deleted-state"></a>È possibile eseguire un'operazione di ripristino quando i dati sono in stato di eliminazione temporanea?

È prima di tutto necessario annullare l'eliminazione della condivisione file eliminata temporaneamente per eseguire operazioni di ripristino. L'operazione di annullamento dell'eliminazione consente di portare la condivisione file nello stato di backup in cui è possibile eseguire il ripristino in qualsiasi momento. Per informazioni su come annullare l'eliminazione della condivisione file, visitare [questo collegamento](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) o vedere [annullare l'eliminazione dello script di condivisione file](./scripts/backup-powershell-script-undelete-file-share.md).

### <a name="how-can-i-purge-the-data-of-a-file-share-in-a-storage-account-that-has-at-least-one-protected-file-share"></a>Come è possibile eliminare i dati di una condivisione file in un account di archiviazione con almeno una condivisione file protetta?

Se si dispone di almeno una condivisione file protetta in un account di archiviazione, significa che l'eliminazione temporanea è abilitata per tutte le condivisioni file nell'account e i dati verranno conservati per 14 giorni dopo l'operazione di eliminazione. Tuttavia, se si desidera eliminare immediatamente i dati e non si desidera che vengano conservati, attenersi alla procedura seguente:

1. Se la condivisione file è già stata eliminata mentre l'eliminazione temporanea è stata abilitata, è necessario prima annullare l'eliminazione della condivisione file dal [portale file](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) oppure usare lo [script Annulla eliminazione file di condivisione file](./scripts/backup-powershell-script-undelete-file-share.md).
2. Disabilitare l'eliminazione temporanea per le condivisioni file nell'account di archiviazione attenendosi alla procedura descritta in [questo documento](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#disable-soft-delete).
3. A questo punto, eliminare la condivisione file di cui si desidera eliminare immediatamente il contenuto.

>[!NOTE]
>È necessario eseguire il passaggio 2 prima che il processo di backup pianificato successivo venga eseguito sulla condivisione file protetta nell'account di archiviazione. Poiché ogni volta che viene eseguito il processo di backup, l'eliminazione temporanea viene abilitata per tutte le condivisioni file nell'account di archiviazione.

>[!WARNING]
>Dopo la disabilitazione dell'eliminazione temporanea nel passaggio 2, le operazioni di eliminazione eseguite sulle condivisioni file sono un'operazione di eliminazione permanente. Se quindi si elimina accidentalmente la condivisione file di cui è stato eseguito il backup dopo la disabilitazione dell'eliminazione temporanea, si perderanno tutti gli snapshot e non sarà possibile recuperare i dati.

### <a name="in-the-context-of-a-file-shares-soft-delete-setting-what-changes-does-azure-backup-do-when-i-unregister-a-storage-account"></a>Nel contesto dell'impostazione di eliminazione temporanea di una condivisione file, quali modifiche eseguono backup di Azure quando si annulla la registrazione di un account di archiviazione?

Al momento dell'annullamento della registrazione, backup di Azure controlla l'impostazione del periodo di memorizzazione per le condivisioni file e, se è maggiore di 14 giorni o meno di 14 giorni, lascia la conservazione così com'è. Tuttavia, se la conservazione è di 14 giorni, la si considera abilitata da backup di Azure e quindi si disabilita l'eliminazione temporanea durante il processo di annullamento della registrazione. Se si vuole annullare la registrazione dell'account di archiviazione mantenendo l'impostazione di conservazione così com'è, abilitarla di nuovo dal riquadro account di archiviazione dopo aver completato l'annullamento della registrazione. È possibile fare riferimento a [questo collegamento](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) per i passaggi di configurazione.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [eseguire il backup di condivisioni file di Azure dal portale di Azure](backup-afs.md)
