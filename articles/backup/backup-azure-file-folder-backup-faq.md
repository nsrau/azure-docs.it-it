---
title: Backup di file e cartelle - domande comuni
description: Risolve le domande comuni sul backup di file e cartelle con Backup di Azure.Addresses common questions about backing up files and folders with Azure Backup.
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: adcbf5c3b404de46634423f8f59c4798d44bebe0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273423"
---
# <a name="common-questions-about-backing-up-files-and-folders"></a>Domande frequenti sul backup di file e cartelle

Questo articolo risponde alle domande comuni abbondano il backup di file e cartelle con l'agente di Microsoft Azure Recovery Services (MARS) nel servizio Backup di Azure.This article answers common questions abbound backup files and folders with the Microsoft Azure Recovery Services (MARS) Agent in the [Azure Backup](backup-overview.md) service.

## <a name="configure-backups"></a>Configurare i backup

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>Dove posso scaricare l'ultima versione dell'agente MARS?

L'agente MARS più recente utilizzato durante il backup di computer Windows Server, System Center DPM e server Microsoft Azure Backup è disponibile per [il download.](https://aka.ms/azurebackup_agent)

### <a name="how-long-are-vault-credentials-valid"></a>Per quanto tempo sono valide per le credenziali dell'insieme di credenziali?

Le credenziali dell'insieme di credenziali scadono dopo 48 ore. Se il file delle credenziali scade, scaricare di nuovo il file dal portale di Azure.If the credentials file expires, download the file again from the Azure portal.

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>Da quali unità è possibile eseguire il backup di file e cartelle?

Non è possibile eseguire il backup dei seguenti tipi di unità e volumi:

* Supporti rimovibili: tutte le origini degli elementi di backup devono essere dichiarate come fisse.
* Volumi di sola lettura: il volume deve essere scrivibile per il funzionamento del servizio Copia Shadow del volume (VSS).
* Volumi offline: il volume deve essere online per il funzionamento di VSS.
* Condivisioni di rete: il volume deve essere locale per il server di cui eseguire il backup tramite il backup online.
* Volumi protetti da BitLocker: il volume deve essere sbloccato prima che possa essere eseguito il backup.
* Identificazione del file system: NTFS è l'unico file system supportato.

### <a name="what-file-and-folder-types-are-supported"></a>Quali tipi di file e cartelle sono supportati?

[Ulteriori informazioni](backup-support-matrix-mars-agent.md#supported-file-types-for-backup) sui tipi di file e cartelle supportati per il backup.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>È possibile usare l'agente MARS per eseguire il backup di file e cartelle in una macchina virtuale di Azure?  

Sì. Backup di Azure offre il backup a livello di macchina virtuale per le macchine virtuali di Azure usando l'estensione della macchina virtuale per l'agente della macchina virtuale di Azure.Azure Backup provides VM-level backup for Azure VMs using the VM extension for the Azure VM agent. Se si desidera eseguire il backup di file e cartelle nel sistema operativo Windows guest nella macchina virtuale, è possibile installare l'agente MARS a tale scopo.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>È possibile usare l'agente MARS per eseguire il backup di file e cartelle nell'archiviazione temporanea per la macchina virtuale di Azure?

Sì. Installare l'agente MARS ed eseguire il backup di file e cartelle nel sistema operativo Windows guest nell'archiviazione temporanea.

* I processi di backup hanno esito negativo quando i dati di archiviazione temporanei vengono cancellati.
* Se i dati di archiviazione temporanei vengono eliminati, è possibile ripristinare solo l'archiviazione non volatile.

### <a name="how-do-i-register-a-server-to-another-region"></a>Come si registra un server in un'altra regione?

I dati di backup vengono inviati al data center dell'insieme di credenziali in cui è registrato il server. Il modo più semplice per modificare il data center consiste nel disinstallare e reinstallare l'agente e quindi registrare il computer in un nuovo insieme di credenziali nell'area necessaria.

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>L'agente MARS supporta la deduplicazione di Windows Server 2012?

Sì. L'agente MARS converte i dati deduplicati in dati normali durante la preparazione dell'operazione di backup. Quindi ottimizza i dati per il backup, crittografa i dati e quindi invia i dati crittografati all'insieme di credenziali.

## <a name="manage-backups"></a>Gestire i backup

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>Cosa succede se si rinomina una macchina Windows configurata per il backup?

Quando si rinomina un computer Windows, tutti i backup attualmente configurati vengono interrotti.

* È necessario registrare il nuovo nome del computer con l'insieme di credenziali di backup.
* Quando si registra il nuovo nome con l'insieme di credenziali, la prima operazione è un backup *completo.*
* Se è necessario ripristinare i dati di cui è stato eseguito il backup nell'insieme di credenziali con il nome del server precedente, utilizzare l'opzione per eseguire il ripristino in una posizione alternativa nel Ripristino guidato dati. [Scopri di più](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>Qual è la lunghezza massima del percorso del file per il backup?

L'agente MARS si basa su NTFS e utilizza la specifica della lunghezza del percorso del file limitata [dall'API di Windows.](/windows/desktop/FileIO/naming-a-file#fully-qualified-vs-relative-paths) Se i file che si desidera proteggere sono più lunghi del valore consentito, eseguire il backup della cartella padre o dell'unità disco.  

### <a name="what-characters-are-allowed-in-file-paths"></a>Quali caratteri sono consentiti nei percorsi dei file?

L'agente MARS si basa su NTFS e consente [i caratteri supportati](/windows/desktop/FileIO/naming-a-file#naming-conventions) nei nomi/percorsi di file.

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>Viene visualizzato l'avviso "Backup di Azure non sono stati configurati per questo server"

Questo avviso può essere visualizzato anche se è stato configurato un criterio di backup, quando le impostazioni di pianificazione del backup archiviate nel server locale non sono le stesse delle impostazioni archiviate nell'insieme di credenziali di backup.

* Quando il server o le impostazioni sono state ripristinate a uno stato valido noto, le pianificazioni di backup possono diventare non sincronizzate.
* Se viene visualizzato questo avviso, [configurare](backup-azure-manage-windows-server.md) nuovamente i criteri di backup e quindi eseguire un backup su richiesta per risincronizzare il server locale con Azure.If you receive this warning, configure the backup policy again, and then run an on-demand backup to resynchronize the local server with Azure.

## <a name="manage-the-backup-cache-folder"></a>Gestire la cartella della cache di backup

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>Qual è il requisito di dimensioni minime per la cartella della cache?

La dimensione della cartella della cache determina la quantità di dati sottoposti a backup.

* I volumi della cartella cache devono avere spazio libero pari ad almeno il 5-10% della dimensione totale dei dati di backup.
* Se il volume dispone di meno del 5% di spazio libero, aumentare le dimensioni del volume o spostare la cartella della cache in un volume con spazio sufficiente attenendosi alla [seguente procedura.](#how-do-i-change-the-cache-location-for-the-mars-agent)
* Se si eseguva lo stato di sistema di Windows, sono necessari ulteriori 30-35 GB di spazio libero nel volume contenente la cartella della cache.

### <a name="how-to-check-if-scratch-folder-is-valid-and-accessible"></a>Come verificare se la cartella scratch è valida e accessibile?

1. Per impostazione predefinita, la cartella scratch si trova in`\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
2. Assicurarsi che il percorso della cartella scratch corrisponda ai valori delle voci della chiave del Registro di sistema riportate di seguito:

    | Percorso del Registro | Chiave del Registro | valore |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nuovo percorso della cartella della cache* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nuovo percorso della cartella della cache* |

### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>Come si modifica il percorso della cache per l'agente MARS?

1. Eseguire questo comando in un prompt dei comandi con privilegi elevati per arrestare il motore di backup:

    ```Net stop obengine```
2. Se è stato configurato il backup dello stato del sistema, aprire Gestione `"CBSSBVol_<ID>"`disco e smontare i dischi con nomi nel formato .
3. Per impostazione predefinita, la cartella scratch si trova in`\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
4. Copiare `\Scratch` l'intera cartella in un'unità diversa con spazio sufficiente. Assicurarsi che il contenuto venga copiato, non spostato.
5. Aggiornare le seguenti voci del Registro di sistema con il percorso della cartella scratch appena spostata.

    | Percorso del Registro | Chiave del Registro | valore |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nuovo percorso della cartella scratch* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nuovo percorso della cartella scratch* |

6. Riavviare il motore di Backup da un prompt dei comandi con privilegi elevati:

    ```command
    Net stop obengine

    Net start obengine
    ```

7. Eseguire un backup su richiesta. Al termine del backup utilizzando la nuova posizione, è possibile rimuovere la cartella della cache originale.

### <a name="where-should-the-cache-folder-be-located"></a>Dove deve trovarsi la cartella della cache?

I percorsi seguenti per la cartella della cache non sono consigliati:

* Condivisione di rete/supporti rimovibili: la cartella della cache deve essere locale al server che deve essere eseguito il backup tramite backup in linea. I percorsi di rete o i supporti rimovibili come le unità USB non sono supportati.
* Volumi offline: la cartella della cache deve essere online per il backup previsto usando l'agente di Backup di Azure

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>Esistono attributi della cartella della cache non supportati?

Per la cartella della cache non sono supportati gli attributi seguenti o le relative combinazioni:

* Crittografato
* De-duplicated
* Compresso
* Sparse
* Reparse-Point

La cartella della cache e il disco rigido virtuale dei metadati non dispongono degli attributi necessari per l'agente di Backup di Azure.The cache folder and the metadata VHD don't have the necessary attributes for the Azure Backup agent.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>Esiste un modo per regolare la larghezza di banda utilizzata per il backup?

Sì, è possibile utilizzare l'opzione **Cambia proprietà** nell'agente MARS per regolare la larghezza di banda e la temporizzazione. [Scopri di più](backup-windows-with-mars-agent.md#enable-network-throttling).

## <a name="restore"></a>Restore

### <a name="manage"></a>Gestione

**Posso recuperare se ho dimenticato la mia passphrase?**
L'agente di Backup di Azure richiede una passphrase (fornita durante la registrazione) per decrittografare i dati di cui è stato eseguito il backup durante il ripristino. Esaminare gli scenari seguenti per comprendere le opzioni per la gestione di una passphrase persa:Review the scenarios below to understand your options for handling a lost passphrase:

| Macchina originale <br> *(computer di origine in cui sono stati eseguiti i backup)* | Passphrase | Opzioni disponibili |
| --- | --- | --- |
| Disponibile |Lost |Se il computer originale (in cui sono stati eseguiti i backup) è disponibile e ancora registrato nello stesso insieme di credenziali di Servizi di ripristino, è possibile rigenerare la passphrase attenendosi alla [seguente procedura.](https://docs.microsoft.com/azure/backup/backup-azure-manage-mars#re-generate-passphrase)  |
| Lost |Lost |Non è possibile recuperare i dati o i dati non è disponibile |

Prendere in considerazione le condizioni seguenti:

* Se si disinstalla e si registra nuovamente l'agente sullo stesso computer originale con
  * *Stessa passphrase*, sarà possibile ripristinare i dati di cui è stato eseguito il backup.
  * *Passphrase diversa,* non sarà possibile ripristinare i dati di cui è stato eseguito il backup.
* Se si installa l'agente su un *altro computer* con
  * *Stessa passphrase* (utilizzata nel computer originale), quindi sarà possibile ripristinare i dati di cui è stato eseguito il backup.
  * *Passphrase diversa,* non sarà possibile ripristinare i dati di cui è stato eseguito il backup.
* Se il computer originale è danneggiato (impedendo la rigenerazione della passphrase tramite la console MARS), ma è possibile ripristinare o accedere alla cartella scratch originale utilizzata dall'agente MARS, potrebbe essere possibile eseguire il ripristino (se si è dimenticata la password). Per ulteriore assistenza, contattare l'Assistenza clienti.

**Come posso eseguire il ripristino in caso di smarrimento della macchina originale (dove sono stati eseguiti i backup)?**

Se si dispone della stessa passphrase (fornita durante la registrazione) del computer originale, è possibile ripristinare i dati di cui è stato eseguito il backup in un computer alternativo. Esaminare gli scenari seguenti per comprendere le opzioni di ripristino.

| Macchina originale | Passphrase | Opzioni disponibili |
| --- | --- | --- |
| Lost |Disponibile |È possibile installare e registrare l'agente MARS su un altro computer con la stessa passphrase fornita durante la registrazione del computer originale. Scegliere Recovery Option Another location **(Opzione** > di**ripristino) In un'altra posizione** per eseguire il ripristino. Per ulteriori informazioni, vedere questo [articolo](https://docs.microsoft.com/azure/backup/backup-azure-restore-windows-server#use-instant-restore-to-restore-data-to-an-alternate-machine).
| Lost |Lost |Non è possibile recuperare i dati o i dati non è disponibile |


### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Cosa accade se si annulla un processo di ripristino in corso?

Se un processo di ripristino in corso viene annullato, il processo di ripristino viene interrotto. Tutti i file ripristinati prima dell'annullamento rimangono nella destinazione configurata (posizione originale o alternativa), senza alcun rollback.

### <a name="does-the-mars-agent-back-up-and-restore-acls-set-on-files-folders-and-volumes"></a>L'agente MARS esegue il backup e il ripristino degli ACL impostati su file, cartelle e volumi?

* L'agente MARS esegue il backup degli ACL impostati su file, cartelle e volumi
* Per l'opzione di ripristino del volume, l'agente MARS offre un'opzione per ignorare il ripristino delle autorizzazioni ACL per il file o la cartella da ripristinare
* Per l'opzione di ripristino di singoli file e cartelle, l'agente MARS verrà ripristinato con autorizzazioni ACL (non è possibile ignorare il ripristino degli ACL).

## <a name="next-steps"></a>Passaggi successivi

[Scopri](tutorial-backup-windows-server-to-azure.md) come eseguire il backup di un computer Windows.
