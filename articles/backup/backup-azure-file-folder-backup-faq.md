---
title: Domande frequenti per eseguire il backup di file e cartelle con Backup di Azure
description: Affronta domande comuni sul backup di file e cartelle con Backup di Azure.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: dacurwin
ms.openlocfilehash: d4d1044a30d4ebc551cf1305993aba2a201c4c94
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514441"
---
# <a name="common-questions-about-backing-up-files-and-folders"></a>Domande frequenti sul backup di file e cartelle

Questo articolo offre risposte alle domande più comuni relativi ai backup dei file e cartelle con l'agente di Microsoft Azure Recovery Services (MARS) nei [Backup di Azure](backup-overview.md) servizio.

## <a name="general"></a>Generale

## <a name="configure-backups"></a>Configurare i backup

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>Dove è possibile scaricare la versione più recente dell'agente MARS?
L'agente MARS più recente utilizzato per eseguire il backup di macchine Windows Server, System Center DPM e server di Backup di Microsoft Azure è disponibile per [scaricare](https://aka.ms/azurebackup_agent).

### <a name="how-long-are-vault-credentials-valid"></a>Per quanto tempo sono le credenziali dell'insieme di credenziali valido?
Le credenziali dell'insieme di credenziali scadono dopo 48 ore. Se il file delle credenziali scade, scaricare nuovamente il file dal portale di Azure.

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>Da quali le unità è possibile eseguire il backup di file e cartelle?

È possibile eseguire il backup dei seguenti tipi di unità e i volumi:

* Supporti rimovibili: tutte le origini degli elementi di backup devono essere segnalate come corrette.
* Volumi di sola lettura: il volume deve essere accessibile in scrittura per garantire il funzionamento del servizio Copia Shadow del volume (VSS).
* Volumi offline: il volume deve essere online per garantire il funzionamento del servizio VSS.
* Condivisioni di rete: il volume deve essere in locale nel server per poter essere sottoposto a backup online.
* Volumi protetti da BitLocker: il volume deve essere sbloccato prima di poter eseguire il backup.
* Identificazione del file system: NTFS è l'unico file system supportato.

### <a name="what-file-and-folder-types-are-supported"></a>Quali tipi di file e cartelle sono supportati?

[Altre informazioni](backup-support-matrix-mars-agent.md#supported-file-types-for-backup) sui tipi di file e cartelle supportate per il backup.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>È possibile usare l'agente di MARS per eseguire il backup di file e cartelle in una macchina virtuale di Azure?  
Sì. Backup di Azure offre backup a livello di macchina virtuale per le macchine virtuali di Azure usando l'estensione macchina virtuale per l'agente di macchine Virtuali di Azure. Se si desidera eseguire il backup di file e cartelle nel sistema operativo Windows guest nella macchina virtuale, è possibile installare l'agente di MARS per eseguire questa operazione.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>È possibile usare l'agente di MARS per eseguire il backup di file e cartelle nell'archivio temporaneo per la macchina virtuale di Azure?
Sì. Installare l'agente MARS ed eseguire il backup di file e cartelle nel sistema operativo Windows guest nell'archivio temporaneo.

- I processi di backup non riescano a dati di archiviazione temporanea vengono cancellati.
- Se i dati di archiviazione temporanea viene eliminati, è possibile ripristinare solo all'archiviazione non volatili.

### <a name="how-do-i-register-a-server-to-another-region"></a>Come si registra un server in un'altra area?

I dati di backup viene inviati al Data Center dell'insieme di credenziali in cui è registrato il server. Il modo più semplice per modificare il Data Center consiste nel disinstallare e reinstallare l'agente e quindi registrare il computer a un nuovo insieme di credenziali nell'area è necessario.

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>Il supporto per l'agente MARS Windows Server 2012 esegue la deduplicazione?
Sì. L'agente MARS converte i dati deduplicati in dati normali quando si prepara l'operazione di backup. Quindi Ottimizza i dati per il backup, crittografa i dati e quindi invia i dati crittografati nell'insieme di credenziali.

## <a name="manage-backups"></a>Gestire i backup

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>Cosa accade se si rinomina un computer Windows configurato per il backup?

Quando si rinomina un computer Windows, tutti i backup attualmente configurati vengono arrestati.

- È necessario registrare il nuovo nome del computer con l'insieme di credenziali di Backup.
- Quando si registra il nome del nuovo insieme di credenziali, la prima operazione è un *completo* backup.
- Se devi ripristinare dati sottoposti a backup nell'insieme di credenziali con il nome del server precedente, usare l'opzione per ripristinare in un percorso alternativo nel ripristino guidato dei dati. [Altre informazioni](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>Che cos'è la lunghezza del percorso di file massime per il backup?
L'agente MARS si basa su NTFS e utilizza la specifica di lunghezza di filepath limitata dai [API Windows](/windows/desktop/FileIO/naming-a-file#fully-qualified-vs-relative-paths). Se i file da proteggere sono più lungo del valore consentito, eseguire il backup della cartella padre o l'unità disco.  

### <a name="what-characters-are-allowed-in-file-paths"></a>Quali caratteri sono consentiti nei percorsi di file?

L'agente MARS si basa su NTFS e consente [caratteri supportati](/windows/desktop/FileIO/naming-a-file#naming-conventions) nei nomi o percorsi di file.

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>Viene visualizzato l'avviso "I backup di Azure non sono stati configurati per questo server".
Questo avviso possa essere visualizzati anche se è stato configurato un criterio di backup, quando le impostazioni di pianificazione del backup archiviate nel server locale non corrispondono alle impostazioni archiviate nell'insieme di credenziali di backup.
- Quando il server o le impostazioni sono state ripristinate a uno stato noto soddisfacente, le pianificazioni di backup possono diventare non sincronizzate.
- Se si riceve questo avviso [configurare](backup-azure-manage-windows-server.md) il nuovo criterio di backup e backup su richiesta di esecuzione per risincronizzare il server locale con Azure.


## <a name="manage-the-backup-cache-folder"></a>Gestire la cartella della cache di backup

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>Qual è il requisito di dimensioni minime per la cartella della cache?
La dimensione della cartella della cache determina la quantità di dati sottoposti a backup.
- I volumi di cartella della cache devono avere spazio libero pari almeno al 5-10% delle dimensioni totali dei dati di backup.
- Se il volume è inferiore al 5% spazio libero, aumentare le dimensioni del volume, o spostare la cartella della cache in un volume con spazio sufficiente.
- Se si esegue il backup dello stato del sistema Windows, è necessario altri 30-35 GB di spazio libero nel volume che contiene la cartella della cache
### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>Come si modifica il percorso della cache per l'agente MARS?


1. Eseguire questo comando in un prompt dei comandi con privilegi elevati per arrestare il motore di Backup:

    ```PS C:\> Net stop obengine```

2. Non spostare i file. Al contrario, copiare la cartella di spazio della cache in un'altra unità con spazio sufficiente.
3. Aggiornare le voci del Registro di sistema seguenti con il percorso della nuova cartella della cache.<br/>

    | Percorso del Registro | Chiave del Registro | Value |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nuovo percorso della cartella della cache* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nuovo percorso della cartella della cache* |

4. Riavviare il motore di Backup in un prompt dei comandi con privilegi elevati:

    ```PS C:\> Net start obengine```

5. Al termine di backup completata utilizzando la nuova posizione, è possibile rimuovere la cartella della cache originale.


### <a name="where-should-the-cache-folder-be-located"></a>In cui deve essere collocata nella cartella della cache?

I percorsi seguenti per la cartella della cache non sono consigliati:

* Supporti rimovibili o condivisione di rete: la cartella della cache deve essere locale nel server di cui eseguire il backup con il backup online. I percorsi di rete o i supporti rimovibili, ad esempio le unità USB, non sono supportati
* Volumi offline: la cartella della cache deve essere online per il backup previsto con l'agente di Backup di Azure

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>Sono presenti tutti gli attributi della cartella della cache non sono supportati?
Per la cartella della cache non sono supportati gli attributi seguenti o le relative combinazioni:

* Crittografato
* De-duplicated
* Compresso
* Sparse
* Reparse-Point

La cartella della cache e il disco rigido virtuale dei metadati non hanno gli attributi necessari per l'agente di Backup di Azure.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>È possibile regolare la quantità di larghezza di banda usata per il backup?

Sì, è possibile usare la **modificare le proprietà** opzione nell'agente di MARS per regolare la larghezza di banda e temporizzazione. [Altre informazioni](backup-configure-vault.md#enable-network-throttling)

## <a name="restore"></a>Restore

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Cosa accade se si annulla un processo di ripristino in corso?

Se viene annullato un processo di ripristino in corso, si arresta il processo di ripristino. Tutti i file ripristinati prima dell'annullamento rimangono nell'istanza di destinazione configurato (percorso originale o alternativo), senza alcun rollback.


## <a name="next-steps"></a>Passaggi successivi

[Informazioni su](tutorial-backup-windows-server-to-azure.md) come eseguire il backup di un computer Windows.
