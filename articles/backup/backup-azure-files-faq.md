---
title: Domande frequenti sul backup di file di Azure
description: Questo articolo fornisce informazioni dettagliate su come proteggere le condivisioni file di Azure.
services: backup
author: markgalioto
ms.author: markgal
ms.date: 2/21/2018
ms.topic: tutorial
ms.service: backup
manager: carmonm
ms.openlocfilehash: de91559d9c3626bdd07c2e497a8aa0b124f00b57
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2018
ms.locfileid: "37434841"
---
# <a name="questions-about-backing-up-azure-files"></a>Domande sul backup di file di Azure
Questo articolo risponde a domande comuni sul backup di file di Azure. Alcune risposte includono collegamenti ad articoli con informazioni complete. È anche possibile inserire le domande sul servizio Backup di Azure nel [forum di discussione](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

Per analizzare rapidamente le sezioni di questo articolo, usare i collegamenti riportati a destra sotto **In questo articolo**.

## <a name="configuring-the-backup-job-for-azure-files"></a>Configurazione del processo di backup per i file di Azure

### <a name="why-cant-i-see-some-of-my-storage-accounts-i-want-to-protect-that-contain-valid-azure-file-shares-br"></a>Perché non vengono visualizzati alcuni degli account di archiviazione da proteggere e che contengono condivisioni file di Azure valide? <br/>
Durante l'anteprima, il backup per le condivisioni file di Azure non supporta tutti i tipi di account di archiviazione. Vedere l'elenco di account di archiviazione supportati disponibile [qui](troubleshoot-azure-files.md#limitations-for-azure-file-share-backup-during-preview). È anche possibile che l'account di archiviazione che si sta cercando sia già protetto o registrato con un altro insieme di credenziali. [Annullare la registrazione](troubleshoot-azure-files.md#configuring-backup) dall'insieme di credenziali per individuare l'account di archiviazione in altri insiemi di credenziali per la protezione.

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup-br"></a>Perché non vengono visualizzate alcune delle condivisioni file di Azure nell'account di archiviazione quando si prova a configurare il backup? <br/>
Verificare se la condivisione file di Azure è già protetta nello stesso insieme di credenziali di Servizi di ripristino oppure se è stata eliminata di recente.

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync-br"></a>È possibile proteggere condivisioni file connesse a un gruppo di sincronizzazione in Sincronizzazione file di Azure? <br/>
Sì. La protezione di condivisioni file di Azure connesse a gruppi di sincronizzazione è abilitata ed è inclusa nell'anteprima pubblica.

### <a name="when-trying-to-back-up-file-shares-i-clicked-on-a-storage-account-for-discovering-the-file-shares-in-it-however-i-did-not-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>Durante un tentativo di backup delle condivisioni file, è stato selezionato un account di archiviazione per individuare le condivisioni file presenti. Le condivisioni non sono state tuttavia protette. Come si possono proteggere queste condivisioni file con altri insiemi di credenziali?
Durante un tentativo di backup, la selezione di un account di archiviazione per l'individuazione di condivisioni file incluse in tale account comporta la registrazione dell'account di archiviazione nell'insieme di credenziali da cui viene eseguita l'operazione. Se si sceglie di proteggere le condivisioni file con un insieme di credenziali diverso, [annullare la registrazione](troubleshoot-azure-files.md#configuring-backup) dell'account di archiviazione scelto da questo insieme di credenziali.

### <a name="can-i-change-the-vault-to-which-i-backup-my-file-shares"></a>È possibile cambiare l'insieme di credenziali in cui si esegue il backup delle condivisioni file?
Sì. Sarà tuttavia necessario [interrompere la protezione](backup-azure-files.md#stop-protecting-an-azure-file-share) dall'insieme di credenziali connesso, [annullare la registrazione](troubleshoot-azure-files.md#configuring-backup) dell'account di archiviazione e quindi proteggerlo da un insieme di credenziali diverso.

### <a name="in-which-geos-can-i-back-up-azure-file-shares-br"></a>In quali aree geografiche è possibile eseguire il backup di condivisioni file di Azure? <br/>
Il backup di condivisioni file di Azure è attualmente disponibile in anteprima e solo nelle aree geografiche seguenti: 
-   Australia orientale (AE) 
- Australia sud-orientale (ASE) 
- Brasile meridionale (BRS)
- Canada centrale (CNC)
-   Canada orientale (CE)
-   Stati Uniti centrali (CUS)
-   Asia orientale (EA)
-   Stati Uniti orientali (EUS)
-   Stati Uniti orientali 2 (EUS2)
- Giappone orientale (JPE)
- Giappone occidentale (JPW)
-   India centrale (INC) 
- India meridionale (INS)
- Corea del Sud centrale (KRC)
- Corea del Sud meridionale (KRS)
-   Stati Uniti centro-settentrionali (NCUS) 
-   Europa settentrionale (NE) 
-   Stati Uniti a centro-meridionali (SCUS) 
-   Asia sud-orientale (SEA)
-   Regno Unito meridionale (UKS) 
-   Regno Unito occidentale (UKW) 
-   Europa occidentale (OE) 
-   Stati Uniti occidentali (WUS)
-   Stati Uniti centro occidentali (WCUS)
-   Stati Uniti occidentali 2 (WUS 2)

Inviare un messaggio a [AskAzureBackupTeam@microsoft.com](email:askazurebackupteam@microsoft.com) se è necessario usare la funzionalità in un'area geografica non indicata nell'elenco.

### <a name="how-many-azure-file-shares-can-i-protect-in-a-vaultbr"></a>Quante condivisioni file di Azure si possono proteggere in un insieme di credenziali?<br/>
Durante l'anteprima è possibile proteggere condivisioni file di Azure di un massimo di 50 account di archiviazione per ogni insieme di credenziali. È possibile proteggere fino a 200 condivisioni file di Azure in un singolo insieme di credenziali.

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>È possibile proteggere due condivisioni file diverse dallo stesso account di archiviazione in insiemi di credenziali diversi?
No. Tutte le condivisioni file di un account di archiviazione possono essere protette solo dallo stesso insieme di credenziali.

## <a name="backup"></a>Backup

### <a name="how-many-on-demand-backups-can-i-take-per-file-share-br"></a>Quanti backup su richiesta è possibile eseguire per ogni condivisione file? <br/>
È previsto un limite di 200 snapshot per ogni condivisione file in qualsiasi momento. Il limite include gli snapshot creati da Backup di Azure in base ai criteri definiti. Se si iniziano a rilevare errori con i backup dopo aver raggiunto questo limite, eliminare i punti di ripristino su richiesta per poter completare i backup successivi.

### <a name="after-enabling-virtual-networks-on-my-storage-account-the-backup-of-file-shares-in-the-account-started-failing-why"></a>Dopo aver abilitato le reti virtuali nell'account di archiviazione, il backup di condivisioni file nell'account non riesce. Perché?
Il backup di condivisioni file di Azure non supporta attualmente gli account di archiviazione con reti virtuali abilitate. Disabilitare le reti virtuali negli account di archiviazione per consentire il backup. 

## <a name="restore"></a>Restore

### <a name="can-i-recover-from-a-deleted-azure-file-share-br"></a>È possibile recuperare una condivisione file di Azure eliminata? <br/>
Quando si elimina una condivisione file di Azure, viene visualizzato l'elenco dei backup che verranno eliminati se si procede, con la relativa richiesta di conferma. Non è possibile ripristinare una condivisione file di Azure eliminata.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share-br"></a>È possibile eseguire il ripristino dai backup se è stata interrotta la protezione in una condivisione file di Azure? <br/>
Sì. Se si è scelto **Conserva i dati di backup** quando è stata interrotta la protezione, sarà possibile eseguire il ripristino da tutti i punti di ripristino esistenti.

## <a name="manage-backup"></a>Gestire il backup

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-it-br"></a>È possibile accedere agli snapshot creati da backup di Azure e montarli? <br/>
È possibile accedere a tutti gli snapshot creati da Backup di Azure visualizzandoli nel portale, in PowerShell o nell'interfaccia della riga di comando. Per altre informazioni sugli snapshot di condivisione per File di Azure, vedere [Panoramica degli snapshot di condivisione per File di Azure (anteprima)](../storage/files/storage-snapshots-files.md).

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups-br"></a>Qual è il periodo di conservazione massimo configurabile per i backup? <br/>
Il backup di condivisioni file di Azure consente di conservare i backup giornalieri fino a 120 giorni.

### <a name="what-happens-when-i-change-the-backup-policy-for-an-azure-file-share-br"></a>Cosa accade quando si modificano i criteri di backup per una condivisione file di Azure? <br/>
Quando vengono applicati nuovi criteri a una o più condivisioni file, vengono seguite la pianificazione e la conservazione stabilite dai nuovi criteri. Se il periodo di conservazione viene esteso, i punti di ripristino esistenti vengono contrassegnati in modo che vengano mantenuti in base ai nuovi criteri. Se il periodo di conservazione viene ridotto, vengono contrassegnati per l'eliminazione nel processo di pulizia successivo e vengono eliminati.

## <a name="see-also"></a>Vedere anche 
Queste informazioni riguardano solo il backup di file di Azure. Per informazioni su altre aree di Backup di Azure, vedere queste domande frequenti sul backup:
-  [Domande frequenti sull'insieme di credenziali di Servizi di ripristino](backup-azure-backup-faq.md)
-  [Domande frequenti sul backup delle macchine virtuali di Azure](backup-azure-vm-backup-faq.md)
-  [Domande frequenti sull'agente di Backup di Azure](backup-azure-file-folder-backup-faq.md)
