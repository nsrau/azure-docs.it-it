---
title: Backup di file e cartelle con backup di Azure-Domande frequenti
description: Risolve le domande frequenti sul backup di file e cartelle con backup di Azure.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: dacurwin
ms.openlocfilehash: c30b918be5e4185d6fb4fdd2fcfc47f8dd4d25ef
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969157"
---
# <a name="common-questions-about-backing-up-files-and-folders"></a>Domande frequenti sul backup di file e cartelle

Questo articolo contiene le risposte alle domande più comuni che comportano il backup di file e cartelle con l'agente di Servizi di ripristino di Microsoft Azure (MARS) nel servizio [backup di Azure](backup-overview.md) .

## <a name="general"></a>Informazioni di carattere generale

## <a name="configure-backups"></a>Configurare i backup

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>Dove è possibile scaricare la versione più recente dell'agente MARS?

L'agente MARS più recente usato per il backup di computer Windows Server, System Center DPM e Backup di Microsoft Azure server è disponibile per il [download](https://aka.ms/azurebackup_agent).

### <a name="how-long-are-vault-credentials-valid"></a>Per quanto tempo sono valide le credenziali dell'insieme di credenziali?

Le credenziali dell'insieme di credenziali scadono dopo 48 ore. Se il file delle credenziali scade, scaricare di nuovo il file dalla portale di Azure.

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>Da quali unità è possibile eseguire il backup di file e cartelle?

Non è possibile eseguire il backup dei seguenti tipi di unità e volumi:

* Supporti rimovibili: tutte le origini degli elementi di backup devono essere segnalate come fisse.
* Volumi di sola lettura: il volume deve essere accessibile in scrittura per il funzionamento del servizio Copia Shadow del volume (VSS).
* Volumi offline: il volume deve essere online per il funzionamento di VSS.
* Condivisioni di rete: il volume deve essere locale nel server di cui eseguire il backup tramite il backup online.
* Volumi protetti da BitLocker: il volume deve essere sbloccato prima che il backup possa essere eseguito.
* Identificazione del file system: NTFS è l'unico file system supportato.

### <a name="what-file-and-folder-types-are-supported"></a>Quali tipi di file e cartelle sono supportati?

[Altre](backup-support-matrix-mars-agent.md#supported-file-types-for-backup) informazioni sui tipi di file e cartelle supportati per il backup.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>È possibile usare l'agente MARS per eseguire il backup di file e cartelle in una macchina virtuale di Azure?  

Sì. Backup di Azure fornisce il backup a livello di VM per le VM di Azure usando l'estensione della macchina virtuale per l'agente di macchine virtuali di Azure. Se si vuole eseguire il backup di file e cartelle nel sistema operativo Windows guest nella macchina virtuale, è possibile installare l'agente MARS a tale scopo.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>È possibile usare l'agente MARS per eseguire il backup di file e cartelle nell'archiviazione temporanea per la VM di Azure?

Sì. Installare l'agente MARS ed eseguire il backup di file e cartelle nel sistema operativo Windows guest in un archivio temporaneo.

* I processi di backup hanno esito negativo quando i dati di archiviazione temporanea vengono cancellati.
* Se i dati di archiviazione temporanea vengono eliminati, è possibile eseguire il ripristino solo in un archivio non volatile.

### <a name="how-do-i-register-a-server-to-another-region"></a>Ricerca per categorie registrare un server in un'altra area?

I dati di backup vengono inviati al data center dell'insieme di credenziali in cui è registrato il server. Il modo più semplice per modificare il Data Center consiste nel disinstallare e reinstallare l'agente e quindi registrare il computer in un nuovo insieme di credenziali nell'area di cui si ha bisogno.

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>L'agente MARS supporta la deduplicazione di Windows Server 2012?

Sì. L'agente MARS converte i dati deduplicati in dati normali quando prepara l'operazione di backup. Consente quindi di ottimizzare i dati per il backup, crittografare i dati e quindi inviare i dati crittografati all'insieme di credenziali.

## <a name="manage-backups"></a>Gestire i backup

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>Cosa accade se si rinomina un computer Windows configurato per il backup?

Quando si rinomina un computer Windows, tutti i backup attualmente configurati vengono arrestati.

* È necessario registrare il nuovo nome del computer con l'insieme di credenziali per il backup.
* Quando si registra il nuovo nome con l'insieme di credenziali, la prima operazione è un backup *completo* .
* Se è necessario ripristinare i dati di cui è stato eseguito il backup nell'insieme di credenziali con il nome del server precedente, usare l'opzione per eseguire il ripristino in un percorso alternativo nella procedura guidata Ripristina dati. [Altre informazioni](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>Qual è la lunghezza massima del percorso file per il backup?

L'agente MARS si basa su NTFS e utilizza la specifica della lunghezza del FilePath limitata dall' [API Windows](/windows/desktop/FileIO/naming-a-file#fully-qualified-vs-relative-paths). Se i file che si desidera proteggere sono più lunghi del valore consentito, eseguire il backup della cartella padre o dell'unità disco.  

### <a name="what-characters-are-allowed-in-file-paths"></a>Quali caratteri sono consentiti nei percorsi di file?

L'agente MARS si basa su NTFS e supporta i [caratteri supportati](/windows/desktop/FileIO/naming-a-file#naming-conventions) nei nomi file e nei percorsi.

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>Viene visualizzato l'avviso "backup di Azure non è stato configurato per questo server".

Questo avviso può essere visualizzato anche se sono stati configurati criteri di backup, quando le impostazioni di pianificazione del backup archiviate nel server locale non sono le stesse delle impostazioni archiviate nell'insieme di credenziali di backup.

* Quando il server o le impostazioni sono state ripristinate in uno stato valido noto, le pianificazioni di backup possono non essere sincronizzate.
* Se viene visualizzato questo avviso, [configurare](backup-azure-manage-windows-server.md) di nuovo il criterio di backup e quindi eseguire un backup su richiesta per risincronizzare il server locale con Azure.

## <a name="manage-the-backup-cache-folder"></a>Gestire la cartella della cache di backup

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>Qual è il requisito di dimensioni minime per la cartella della cache?

La dimensione della cartella della cache determina la quantità di dati sottoposti a backup.

* Lo spazio disponibile nei volumi della cartella della cache è uguale almeno al 5-10% delle dimensioni totali dei dati di backup.
* Se lo spazio disponibile nel volume è inferiore al 5%, aumentare la dimensione del volume oppure spostare la cartella della cache in un volume con spazio sufficiente.
* Se si esegue il backup dello stato del sistema Windows, sarà necessario disporre di un numero aggiuntivo di 30-35 GB di spazio libero nel volume contenente la cartella della cache.

### <a name="how-to-check-if-scratch-folder-is-valid-and-accessible"></a>Come verificare se la cartella Scratch è valida e accessibile

1. Per impostazione predefinita, la cartella Scratch si trova in `\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
2. Verificare che il percorso della cartella dei file temporanei corrisponda ai valori delle voci della chiave del registro di sistema mostrate di seguito:

  | Percorso del Registro | Chiave del Registro | Value |
  | --- | --- | --- |
  | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nuovo percorso della cartella della cache* |
  | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nuovo percorso della cartella della cache* |

### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>Ricerca per categorie modificare il percorso della cache per l'agente MARS?

1. Eseguire questo comando in un prompt dei comandi con privilegi elevati per arrestare il motore di backup:

    ```PS C:\> Net stop obengine```

2. Se è stato configurato il backup dello stato del sistema, aprire Gestione disco e smontare i dischi con nomi nel formato `"CBSSBVol_<ID>"`.
3. Non spostare i file. Copiare invece la cartella dello spazio della cache in un'unità diversa con spazio sufficiente.
4. Aggiornare le voci del registro di sistema seguenti con il percorso della nuova cartella della cache.<br/>

    | Percorso del Registro | Chiave del Registro | Value |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nuovo percorso della cartella della cache* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nuovo percorso della cartella della cache* |

5. Riavviare il motore di backup a un prompt dei comandi con privilegi elevati:

    ```PS C:\> Net stop obengine```

    ```PS C:\> Net start obengine```

6. Eseguire un backup ad hoc. Al termine del backup con il nuovo percorso, è possibile rimuovere la cartella della cache originale.

### <a name="where-should-the-cache-folder-be-located"></a>Dove si trova la cartella della cache?

I percorsi seguenti per la cartella della cache non sono consigliati:

* Condivisione di rete/supporti rimovibili: la cartella della cache deve essere locale nel server di cui è necessario eseguire il backup tramite il backup online. I percorsi di rete o i supporti rimovibili, ad esempio le unità USB, non sono supportati
* Volumi offline: la cartella della cache deve essere online per il backup previsto con l'agente di backup di Azure

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>Sono presenti attributi della cartella della cache che non sono supportati?

Per la cartella della cache non sono supportati gli attributi seguenti o le relative combinazioni:

* Crittografato
* De-duplicated
* Compresso
* Sparse
* Reparse-Point

La cartella della cache e il disco rigido virtuale dei metadati non hanno gli attributi necessari per l'agente di Backup di Azure.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>È possibile modificare la quantità di larghezza di banda usata per il backup?

Sì, è possibile usare l'opzione **modifica proprietà** nell'agente Mars per regolare la larghezza di banda e l'intervallo di tempo. [Altre informazioni](backup-configure-vault.md#enable-network-throttling).

## <a name="restore"></a>Ripristinare

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Cosa accade se si annulla un processo di ripristino in corso?

Se un processo di ripristino in corso viene annullato, il processo di ripristino viene arrestato. Tutti i file ripristinati prima dell'annullamento rimanere nella destinazione configurata (percorso originale o alternativo), senza rollback.

## <a name="next-steps"></a>Passaggi successivi

[Informazioni](tutorial-backup-windows-server-to-azure.md) su come eseguire il backup di un computer Windows.
