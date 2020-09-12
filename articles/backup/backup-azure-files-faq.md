---
title: Domande frequenti sul backup di file di Azure
description: In questo articolo vengono fornite le risposte alle domande comuni su come proteggere le condivisioni file di Azure con il servizio Backup di Azure.
ms.date: 04/22/2020
ms.topic: conceptual
ms.openlocfilehash: c62f8376b220911edd26edbe18955d0103440b81
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89377421"
---
# <a name="questions-about-backing-up-azure-files"></a>Domande sul backup di file di Azure

Questo articolo risponde a domande comuni sul backup di file di Azure. Alcune risposte includono collegamenti ad articoli con informazioni complete. È anche possibile inserire le domande sul servizio Backup di Azure nella [pagina delle domande di Domande e risposte Microsoft per avviare una discussione](/answers/topics/azure-backup.html).

Per analizzare rapidamente le sezioni di questo articolo, usare i collegamenti riportati a destra sotto **In questo articolo**.

## <a name="configuring-the-backup-job-for-azure-files"></a>Configurazione del processo di backup per i file di Azure

### <a name="why-cant-i-see-some-of-my-storage-accounts-that-i-want-to-protect-which-contain-valid-azure-file-shares"></a>Perché non vengono visualizzati alcuni degli account di archiviazione da proteggere e che contengono condivisioni file di Azure valide?

Per verificare che l'account di archiviazione appartenga a uno dei tipi di account di archiviazione supportati, vedere la [matrice di supporto per il backup di condivisioni file di Azure](azure-file-share-support-matrix.md). È anche possibile che l'account di archiviazione che si sta cercando sia già protetto o registrato con un altro insieme di credenziali. [Annullare la registrazione dell'account di archiviazione](manage-afs-backup.md#unregister-a-storage-account) dall'insieme di credenziali per individuare l'account di archiviazione in altri insiemi di credenziali per la protezione.

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup"></a>Perché non vengono visualizzate alcune delle condivisioni file di Azure nell'account di archiviazione quando si prova a configurare il backup?

Verificare se la condivisione file di Azure è già protetta nello stesso insieme di credenziali di Servizi di ripristino oppure se è stata eliminata di recente.

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync"></a>È possibile proteggere condivisioni file connesse a un gruppo di sincronizzazione in Sincronizzazione file di Azure?

Sì. La protezione di condivisioni file di Azure connesse a gruppi di sincronizzazione è abilitata.

### <a name="when-trying-to-back-up-file-shares-i-selected-a-storage-account-to-discover-the-file-shares-in-it-however-i-didnt-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>Quando si tenta di eseguire il backup delle condivisioni file, è stato selezionato un account di archiviazione per individuare le condivisioni file. Le condivisioni non sono state tuttavia protette. Ricerca per categorie: proteggere queste condivisioni file con altri insiemi di credenziali

Durante un tentativo di backup, la selezione di un account di archiviazione per l'individuazione di condivisioni file incluse in tale account comporta la registrazione dell'account di archiviazione nell'insieme di credenziali da cui viene eseguita l'operazione. Se si sceglie di proteggere le condivisioni file con un insieme di credenziali diverso, [annullare la registrazione](manage-afs-backup.md#unregister-a-storage-account) dell'account di archiviazione scelto da questo insieme di credenziali.

### <a name="why-cant-i-change-the-vault-to-configure-backup-for-the-file-share"></a>Perché non è possibile modificare l'insieme di credenziali per configurare il backup per la condivisione file?

Se l'account di archiviazione è già registrato con un insieme di credenziali o altre condivisioni file nell'account di archiviazione sono protette tramite un insieme di credenziali, non è possibile modificarlo. Tutte le condivisioni file in un account di archiviazione possono essere protette solo dallo stesso insieme di credenziali. Se si vuole modificare l'insieme di credenziali, è necessario [arrestare la protezione per tutte le condivisioni file nell'account di archiviazione](manage-afs-backup.md#stop-protection-on-a-file-share) dall'insieme di credenziali connesso, annullare la [registrazione](manage-afs-backup.md#unregister-a-storage-account) dell'account di archiviazione e quindi scegliere un insieme di credenziali diverso per la protezione.

### <a name="can-i-change-the-vault-to-which-i-back-up-my-file-shares"></a>È possibile cambiare l'insieme di credenziali in cui si esegue il backup delle condivisioni file?

Sì. Sarà tuttavia necessario [interrompere la protezione della condivisione file](manage-afs-backup.md#stop-protection-on-a-file-share) dall'insieme di credenziali connesso, [annullare la registrazione](manage-afs-backup.md#unregister-a-storage-account) dell'account di archiviazione e quindi proteggerlo da un insieme di credenziali diverso.

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>È possibile proteggere due condivisioni file diverse dallo stesso account di archiviazione in insiemi di credenziali diversi?

No. Tutte le condivisioni file di un account di archiviazione possono essere protette solo dallo stesso insieme di credenziali.

## <a name="backup"></a>Backup

### <a name="what-should-i-do-if-my-backups-start-failing-due-to-the-maximum-limit-reached-error"></a>Cosa fare se i backup non vengono avviati a causa dell'errore limite massimo raggiunto?

È previsto un limite di 200 snapshot per ogni condivisione file in qualsiasi momento. Il limite include gli snapshot creati da Backup di Azure in base ai criteri definiti. Se si iniziano a rilevare errori con i backup dopo aver raggiunto questo limite, eliminare gli snapshot su richiesta per poter completare i backup successivi.

## <a name="restore"></a>Restore

### <a name="can-i-recover-from-a-deleted-azure-file-share"></a>È possibile recuperare una condivisione file di Azure eliminata?

Se la condivisione file è nello stato di eliminazione temporanea, è necessario prima annullare l'eliminazione della condivisione file per eseguire l'operazione di ripristino. L'operazione di annullamento dell'eliminazione consente di portare la condivisione file nello stato attivo in cui è possibile eseguire il ripristino in qualsiasi momento. Per informazioni su come annullare l'eliminazione della condivisione file, visitare [questo collegamento](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) o vedere [annullare l'eliminazione dello script di condivisione file](./scripts/backup-powershell-script-undelete-file-share.md). Se la condivisione file viene eliminata definitivamente, non sarà possibile ripristinare il contenuto e gli snapshot.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share"></a>È possibile eseguire il ripristino dai backup se è stata interrotta la protezione in una condivisione file di Azure?

Sì. Se si è scelto **Conserva i dati di backup** quando è stata interrotta la protezione, sarà possibile eseguire il ripristino da tutti i punti di ripristino esistenti.

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Cosa accade se si annulla un processo di ripristino in corso?

Se si annulla un processo di ripristino in corso, il processo si interrompe e tutti i file ripristinati prima dell'annullamento rimangono nella destinazione configurata (posizione originale o alternativa) senza rollback.

## <a name="manage-backup"></a>Gestire il backup

### <a name="can-i-use-powershell-to-configuremanagerestore-backups-of-azure-file-shares"></a>È possibile usare PowerShell per configurare/gestire/ripristinare i backup di condivisioni file di Azure?

Sì. Vedere la documentazione dettagliata [qui](backup-azure-afs-automation.md).

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-them"></a>È possibile accedere agli snapshot creati da backup di Azure e montarli?

È possibile accedere a tutti gli snapshot creati da Backup di Azure visualizzandoli nel portale, in PowerShell o nell'interfaccia della riga di comando. Per altre informazioni sugli snapshot di condivisioni file di Azure, vedere [Panoramica degli snapshot di condivisioni file di Azure](../storage/files/storage-snapshots-files.md).

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups"></a>Qual è il periodo di conservazione massimo configurabile per i backup?

Per informazioni dettagliate sul periodo di conservazione massimo, vedere la [matrice di supporto](azure-file-share-support-matrix.md). Backup di Azure esegue un calcolo in tempo reale del numero di snapshot quando si immettono i valori di conservazione durante la configurazione dei criteri di backup. Quando il numero di snapshot corrispondenti ai valori di conservazione definiti supera i 200, nel portale verrà visualizzato un avviso che richiede di modificare i valori di conservazione, in modo da non superare il limite massimo di snapshot supportato da File di Azure per una condivisione file in qualsiasi momento.

### <a name="what-is-the-impact-on-existing-recovery-points-and-snapshots-when-i-modify-the-backup-policy-for-an-azure-file-share-to-switch-from-daily-policy-to-gfs-policy"></a>Qual è l'effetto sui punti di ripristino e sugli snapshot esistenti quando si modificano i criteri di backup per una condivisione file di Azure per passare da "criteri giornalieri" a "criteri GFS"?

Quando si modifica un criterio di backup giornaliero in criteri GFS (aggiungendo la conservazione settimanale/mensile/annuale), il comportamento è il seguente:

- **Conservazione**: Se si aggiunge un periodo di conservazione settimanale/mensile/annuale nel quadro della modifica dei criteri, tutti i punti di ripristino futuri creati durante il backup pianificato verranno contrassegnati in base ai nuovi criteri. Tutti i punti di ripristino esistenti verranno comunque considerati come punti di ripristino giornalieri e pertanto non verranno contrassegnati come settimanali/mensili/annuali.

- **Pulizia di snapshot e punti di ripristino**

  - Se la conservazione giornaliera viene estesa, la data di scadenza dei punti di ripristino esistenti viene aggiornata in base al valore di conservazione giornaliero configurato nel nuovo criterio.
  - Se il periodo di conservazione giornaliero è ridotto, i punti di ripristino e gli snapshot esistenti vengono contrassegnati per l'eliminazione nel processo di pulizia successivo, in base al valore di conservazione giornaliero configurato nei nuovi criteri e quindi eliminati.

Di seguito è riportato un esempio di funzionamento:

#### <a name="existing-policy-p1"></a>Criteri esistenti [P1]

|Tipo di conservazione |Pianificazione |Conservazione  |
|---------|---------|---------|
|Giornaliera    |    Ogni giorno alle 20:00    |  100 giorni       |

#### <a name="new-policy-modified-p1"></a>Nuovi criteri [P1 modificati]

| Tipo di conservazione | Pianificazione                       | Conservazione |
| -------------- | ------------------------------ | --------- |
| Giornaliera          | Ogni giorno alle 21:00              | 50 giorni   |
| Settimanale         | Domenica alle 21:00              | 3 settimane   |
| Mensile        | Lunedì scorso alle 21:00         | 1 mese   |
| Annuale         | La terza domenica di gennaio alle 21:00 | 4 anni   |

#### <a name="impact"></a>Impatto

1. La data di scadenza dei punti di ripristino esistenti verrà regolata in base al valore di conservazione giornaliero dei nuovi criteri, ovvero 50 giorni. Qualsiasi punto di ripristino che risalga a più di 50 giorni prima verrà quindi contrassegnato per l'eliminazione.

2. In base ai nuovi criteri, i punti di ripristino esistenti non verranno contrassegnati come settimanali/mensili/annuali.

3. Tutti i backup futuri verranno attivati in base alla nuova pianificazione, ovvero alle 21:00.

4. La data di scadenza di tutti i punti di ripristino futuri verrà allineata con i nuovi criteri.

>[!NOTE]
>Le modifiche ai criteri avranno effetto solo sui punti di ripristino creati nel quadro dell'esecuzione del processo di backup pianificato. Per i backup su richiesta, la conservazione è determinata dal valore **Conserva backup fino a**, specificato al momento dell'esecuzione del backup.

### <a name="what-is-the-impact-on-existing-recovery-points-when-i-modify-an-existing-gfs-policy"></a>Qual è l'effetto sui punti di ripristino esistenti quando si modifica un criterio GFS esistente?

Quando alle condivisioni file viene applicato un nuovo criterio, tutti i futuri backup pianificati verranno eseguiti in base alla pianificazione configurata nei criteri modificati.  La conservazione di tutti i punti di ripristino esistenti è allineata in base ai nuovi valori di conservazione configurati. Se il periodo di conservazione viene esteso, i punti di ripristino esistenti vengono dunque contrassegnati per essere mantenuti in base al nuovo criterio. Se il periodo di conservazione viene ridotto, vengono contrassegnati per la pulizia nel processo di pulizia successivo e vengono quindi eliminati.

Di seguito è riportato un esempio:

#### <a name="existing-policy-p2"></a>Criteri esistenti [P2]

| Tipo di conservazione | Pianificazione           | Conservazione |
| -------------- | ------------------ | --------- |
| Giornaliera          | Ogni giorno alle 20:00 | 50 giorni   |
| Settimanale         | Lunedì alle 20:00  | 3 settimane   |

#### <a name="new-policy-modified-p2"></a>Nuovi criteri [P2 modificati]

| Tipo di conservazione | Pianificazione               | Conservazione |
| -------------- | ---------------------- | --------- |
| Giornaliera          | Ogni giorno alle 21:00     | 10 giorni   |
| Settimanale         | Lunedì alle 21:00      | 2 settimane   |
| Mensile        | Lunedì scorso alle 21:00 | 2 mesi  |

#### <a name="impact-of-change"></a>Effetti della modifica

1. La data di scadenza dei punti di ripristino giornalieri esistenti verrà allineata in base al nuovo valore di conservazione giornaliero, ovvero 10 giorni. Tutti i punti di ripristino giornalieri più vecchi di 10 giorni verranno quindi eliminati.

2. La data di scadenza dei punti di ripristino settimanali esistenti verrà allineata in base al nuovo valore di conservazione settimanale, ovvero due settimane. Tutti i punti di ripristino settimanali più vecchi di due settimane verranno quindi eliminati.

3. I punti di ripristino mensili verranno creati solo nell'ambito dei backup futuri in base alla configurazione dei nuovi criteri.

4. La data di scadenza di tutti i punti di ripristino futuri verrà allineata con i nuovi criteri.

>[!NOTE]
>Le modifiche ai criteri avranno effetto solo sui punti di ripristino creati nel quadro del processo di backup pianificato. Per i backup su richiesta, la conservazione è determinata dal valore **Conserva backup fino a**, specificato al momento dell'esecuzione del backup.

## <a name="next-steps"></a>Passaggi successivi

- [Risolvere i problemi durante il backup delle condivisioni file di Azure](troubleshoot-azure-files.md)
