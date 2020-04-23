---
title: Domande frequenti sul backup di file di Azure
description: In questo articolo vengono fornite le risposte alle domande comuni su come proteggere le condivisioni file di Azure con il servizio Backup di Azure.
ms.date: 04/22/2020
ms.topic: conceptual
ms.openlocfilehash: b89e4a55bc8bc4ef2f4cdb50059537fe7708b6a6
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82101162"
---
# <a name="questions-about-backing-up-azure-files"></a>Domande sul backup di file di Azure

Questo articolo risponde a domande comuni sul backup di file di Azure. Alcune risposte includono collegamenti ad articoli con informazioni complete. È anche possibile inserire le domande sul servizio Backup di Azure nel [forum di discussione](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

Per analizzare rapidamente le sezioni di questo articolo, usare i collegamenti riportati a destra sotto **In questo articolo**.

## <a name="configuring-the-backup-job-for-azure-files"></a>Configurazione del processo di backup per i file di Azure

### <a name="why-cant-i-see-some-of-my-storage-accounts-that-i-want-to-protect-which-contain-valid-azure-file-shares"></a>Perché non è possibile visualizzare alcuni degli account di archiviazione che si vuole proteggere, che contengono condivisioni file di Azure valide?

Per assicurarsi che l'account di archiviazione appartenga a uno dei tipi di account di archiviazione supportati, vedere la [matrice di supporto per il backup di condivisioni file di Azure](azure-file-share-support-matrix.md) . È anche possibile che l'account di archiviazione che si sta cercando sia già protetto o registrato con un altro insieme di credenziali. [Annullare la registrazione dell'account di archiviazione](manage-afs-backup.md#unregister-a-storage-account) dall'insieme di credenziali per individuare l'account di archiviazione in altri insiemi di credenziali per la protezione.

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup"></a>Perché non vengono visualizzate alcune delle condivisioni file di Azure nell'account di archiviazione quando si prova a configurare il backup?

Verificare se la condivisione file di Azure è già protetta nello stesso insieme di credenziali di Servizi di ripristino oppure se è stata eliminata di recente.

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync"></a>È possibile proteggere condivisioni file connesse a un gruppo di sincronizzazione in Sincronizzazione file di Azure?

Sì. La protezione delle condivisioni file di Azure connesse a gruppi di sincronizzazione è abilitata.

### <a name="when-trying-to-back-up-file-shares-i-clicked-on-a-storage-account-for-discovering-the-file-shares-in-it-however-i-didnt-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>Durante un tentativo di backup delle condivisioni file, è stato selezionato un account di archiviazione per individuare le condivisioni file presenti. Tuttavia, non sono stati protetti. Ricerca per categorie proteggere queste condivisioni file con altri insiemi di credenziali?

Quando si tenta di eseguire il backup, selezionando un account di archiviazione per individuare le condivisioni file in essa viene registrato l'account di archiviazione con l'insieme di credenziali da cui viene eseguita questa operazione. Se si sceglie di proteggere le condivisioni file con un insieme di credenziali diverso, [annullare la registrazione](manage-afs-backup.md#unregister-a-storage-account) dell'account di archiviazione scelto da questo insieme di credenziali.

### <a name="can-i-change-the-vault-to-which-i-back-up-my-file-shares"></a>È possibile cambiare l'insieme di credenziali in cui si esegue il backup delle condivisioni file?

Sì. Tuttavia, è necessario [arrestare la protezione nella condivisione file](manage-afs-backup.md#stop-protection-on-a-file-share) dall'insieme di credenziali connesso, [annullare la registrazione](manage-afs-backup.md#unregister-a-storage-account) dell'account di archiviazione e quindi proteggerlo da un insieme di credenziali diverso.

### <a name="how-many-azure-file-shares-can-i-protect-in-a-vault"></a>Quante condivisioni file di Azure si possono proteggere in un insieme di credenziali?

È possibile proteggere le condivisioni file di Azure da un massimo di 50 account di archiviazione per ogni insieme di credenziali. È possibile proteggere fino a 200 condivisioni file di Azure in un singolo insieme di credenziali.

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>È possibile proteggere due condivisioni file diverse dallo stesso account di archiviazione in insiemi di credenziali diversi?

No. Tutte le condivisioni file di un account di archiviazione possono essere protette solo dallo stesso insieme di credenziali.

## <a name="backup"></a>Backup

### <a name="what-should-i-do-if-my-backups-start-failing-due-to-the-maximum-limit-reached-error"></a>Cosa fare se i backup non vengono avviati a causa dell'errore limite massimo raggiunto?

È previsto un limite di 200 snapshot per ogni condivisione file in qualsiasi momento. Il limite include gli snapshot creati da Backup di Azure in base ai criteri definiti. Se l'avvio dei backup non riesce dopo il raggiungimento del limite, eliminare gli snapshot su richiesta per i backup futuri.

## <a name="restore"></a>Restore

### <a name="can-i-recover-from-a-deleted-azure-file-share"></a>È possibile recuperare una condivisione file di Azure eliminata?

Quando viene eliminata una condivisione file di Azure, viene visualizzato l'elenco dei backup che verranno eliminati e viene richiesta una conferma. Attualmente, non è possibile ripristinare una condivisione file di Azure eliminata.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share"></a>È possibile eseguire il ripristino dai backup se è stata interrotta la protezione in una condivisione file di Azure?

Sì. Se si è scelto **Conserva i dati di backup** quando è stata interrotta la protezione, sarà possibile eseguire il ripristino da tutti i punti di ripristino esistenti.

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Cosa accade se si annulla un processo di ripristino in corso?

Se si annulla un processo di ripristino in corso, il processo si interrompe e tutti i file ripristinati prima dell'annullamento rimangono nella destinazione configurata (posizione originale o alternativa) senza rollback.

## <a name="manage-backup"></a>Gestire il backup

### <a name="can-i-use-powershell-to-configuremanagerestore-backups-of-azure-file-shares"></a>È possibile usare PowerShell per configurare/gestire/ripristinare i backup di condivisioni file di Azure?

Sì. Vedere la documentazione dettagliata [qui](backup-azure-afs-automation.md).

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-them"></a>È possibile accedere agli snapshot eseguiti da backup di Azure e montarli?

Per accedere a tutti gli snapshot eseguiti da backup di Azure, è possibile visualizzare gli snapshot nel portale, in PowerShell o nell'interfaccia della riga di comando. Per altre informazioni sugli snapshot di condivisione per File di Azure, vedere [Panoramica degli snapshot di condivisione per File di Azure (anteprima)](../storage/files/storage-snapshots-files.md).

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups"></a>Qual è il periodo di conservazione massimo configurabile per i backup?

Per informazioni dettagliate sul periodo di conservazione massimo, vedere la [matrice di supporto](azure-file-share-support-matrix.md) . Backup di Azure esegue un calcolo in tempo reale del numero di snapshot quando si immettono i valori di conservazione durante la configurazione dei criteri di backup. Non appena il numero di snapshot corrispondenti ai valori di conservazione definiti è superiore a 200, nel portale verrà visualizzato un avviso che richiede di modificare i valori di conservazione. In modo da non superare il limite del numero massimo di snapshot supportato da File di Azure per qualsiasi condivisione file in un momento qualsiasi.

### <a name="what-happens-when-i-change-the-backup-policy-for-an-azure-file-share"></a>Cosa accade quando si modificano i criteri di backup per una condivisione file di Azure?

Quando vengono applicati nuovi criteri a una o più condivisioni file, vengono seguite la pianificazione e la conservazione stabilite dai nuovi criteri. Se il periodo di conservazione viene esteso, i punti di ripristino esistenti vengono contrassegnati in modo che vengano mantenuti in base ai nuovi criteri. Se il periodo di conservazione viene ridotto, vengono contrassegnati per l'eliminazione nel processo di pulizia successivo e vengono eliminati.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su altre aree di Backup di Azure, vedere queste domande frequenti sul backup:

- [Domande frequenti sull'insieme di credenziali di Servizi di ripristino](backup-azure-backup-faq.md)
- [Domande frequenti sul backup delle macchine virtuali di Azure](backup-azure-vm-backup-faq.md)
- [Domande frequenti sull'agente di Backup di Azure](backup-azure-file-folder-backup-faq.md)
