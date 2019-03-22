---
title: Copiare o spostare i dati in archiviazione di Azure tramite AzCopy v10 (anteprima) | Microsoft Docs
description: Usare il v10 AzCopy utilità della riga di comando (anteprima) per spostare o copiare dati da o verso blob, data lake e contenuto del file. Copiare i dati in archiviazione di Azure da file locali o copiare i dati all'interno o tra account di archiviazione. Migrare facilmente i dati in archiviazione di Azure.
services: storage
author: artemuwka
ms.service: storage
ms.topic: article
ms.date: 02/24/2019
ms.author: artemuwka
ms.subservice: common
ms.openlocfilehash: ca7081bdfedae3abb5ec426a9d3ec0a7867a2ef9
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337018"
---
# <a name="transfer-data-with-azcopy-v10-preview"></a>Trasferire dati con AzCopy v10 (anteprima)

AzCopy v10 (anteprima) è l'utilità della riga di comando per copiare dati da o verso Blob di Microsoft Azure e archiviazione File. AzCopy v10 offre un'interfaccia della riga di comando riprogettata e trasferisce nuova architettura per affidabile dei dati. Tramite AzCopy, è possibile copiare dati tra un file system e un account di archiviazione o tra account di archiviazione.

## <a name="whats-new-in-azcopy-v10"></a>Novità di AzCopy v10

- Sincronizza i file System all'archivio Blob di Azure o viceversa. Usare `azcopy sync <source> <destination>`. Ideale per scenari di copia incrementale.
- Supporta le API di Azure Data Lake Storage Gen2. Usare `myaccount.dfs.core.windows.net` come URI per chiamare le API di Data Lake Storage Gen2.
- Supporta la copia di un intero account (solo servizio BLOB) in un altro account.
- Utilizza il nuovo [Put Block dall'URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API per supportare account-account-alla copia. Il trasferimento dei dati è più veloce, poiché il trasferimento al client non è necessario.
- Elenca o rimuove i file e i BLOB in un percorso specificato.
- Supporta i modelli con caratteri jolly in un percorso e --exclude flag.
- Crea un ordine di processo e un file di log correlate con ogni istanza di AzCopy. È possibile visualizzare e riavviare i processi precedenti e riprendere i processi non riusciti. AzCopy ritenterà inoltre automaticamente un trasferimento dopo un errore.
- Miglioramenti delle prestazioni generali della funzionalità.

## <a name="download-and-install-azcopy"></a>Scaricare e installare AzCopy

### <a name="latest-preview-version-v10"></a>Versione di anteprima più recente (v10)

Scaricare la versione di anteprima più recente di AzCopy:
- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

### <a name="latest-production-version-v81"></a>Versione di produzione più recente (v8.1)

Scaricare la [versione di produzione più recente di AzCopy per Windows](https://aka.ms/downloadazcopy).

### <a name="azcopy-supporting-table-storage-service-v73"></a>AzCopy che supporta il servizio di Archiviazione tabelle (v7.3)

Scaricare [AzCopy v7.3 che supporta la copia dei dati da e verso il servizio di Archiviazione tabelle di Microsoft Azure](https://aka.ms/downloadazcopynet).

## <a name="post-installation-steps"></a>Passaggi successivi all'installazione

AzCopy v10 non richiede un'installazione. Aprire l'applicazione della riga di comando preferito e passare alla cartella in cui `azcopy.exe` si trova. Se necessario, è possibile aggiungere il percorso di cartella di AzCopy al percorso di sistema per la semplicità d'uso.

## <a name="authentication-options"></a>Opzioni di autenticazione

Durante l'autenticazione con archiviazione di Azure, AzCopy v10 supporta le seguenti opzioni:
- **Azure Active Directory** (1!s!per **i servizi Blob e Data Lake Storage Gen2**). Usare ```.\azcopy login``` effettuare l'accesso con Azure Active Directory.  L'utente deve avere [ruolo "Collaboratore ai dati di archiviazione Blob" assegnato](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) da scrivere nell'archiviazione Blob con l'autenticazione di Azure Active Directory. Per l'autenticazione tramite identità gestita per le risorse di Azure, usare `azcopy login --identity`.
- **Condividere i token di firma di accesso [supportati per i servizi Blob e File]**. Aggiungere il token di firma di accesso condiviso per il percorso del blob nella riga di comando per usarlo. È possibile generare i token di firma di accesso condiviso con il portale di Azure [Storage Explorer](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/), [PowerShell](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageblobsastoken), o altri strumenti di propria scelta. Per altre informazioni, vedere gli [esempi](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2).

## <a name="getting-started"></a>Introduzione

> [!TIP]
> **Se si preferisce un'interfaccia utente grafica,**
>
> [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/), un client desktop che semplifica la gestione dei dati di archiviazione di Azure, AzCopy viene ora utilizzato per accelerare il trasferimento dei dati e in uscita da archiviazione di Azure.
>
> Abilitare AzCopy in Storage Explorer con il **Preview** menu.
> ![Abilitare AzCopy come motore di trasferimento in Azure Storage Explorer](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

AzCopy v10 presenta una sintassi self-documentata. Quando hanno effettuato l'accesso ad Azure Active Directory, la sintassi generale simile al seguente:

```azcopy
.\azcopy <command> <arguments> --<flag-name>=<flag-value>

# Examples if you have logged into the Azure Active Directory:
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile"
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/container"

# Examples if you're using SAS tokens to authenticate:
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container?sastoken" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile?sastoken"
```

Ecco come ottenere un elenco di comandi disponibili:

```azcopy
.\azcopy --help
# To use the alias instead
.\azcopy -h
```

Per visualizzare la pagina della Guida ed esempi per un comando specifico, eseguire il comando seguente:

```azcopy
.\azcopy <cmd> --help
# Example:
.\azcopy cp -h
```

## <a name="create-a-blob-container-or-file-share"></a>Creare una condivisione di file o contenitore blob 

**Creare un contenitore blob**

```azcopy
.\azcopy make "https://account.blob.core.windows.net/container-name"
```

**Creare una condivisione file**

```azcopy
.\azcopy make "https://account.file.core.windows.net/share-name"
```

**Creare un contenitore blob usando Azure Data Lake Storage Gen2**

Se è stato abilitato spazi dei nomi gerarchici in account di archiviazione Blob, è possibile usare il comando seguente per creare un nuovo contenitore blob per il caricamento di file.

```azcopy
.\azcopy make "https://account.dfs.core.windows.net/top-level-resource-name"
```

## <a name="copy-data-to-azure-storage"></a>Copiare dati in Archiviazione di Azure

Usare il comando copy per trasferire i dati dall'origine alla destinazione. L'origine o la destinazione può essere costituito da:
- File system locale
- URI di BLOB di Azure/directory virtuale/contenitore
- URI di File di Azure/directory/condivisione file
- URI di file system/directory/file di Azure Data Lake Storage Gen2

```azcopy
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
# Using the alias instead 
.\azcopy cp <source path> <destination path> --<flag-name>=<flag-value>
```

Il comando seguente consente di caricare tutti i file nella cartella `C:\local\path` in modo ricorsivo nel contenitore `mycontainer1`creando `path` directory nel contenitore:

```azcopy
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

Il comando seguente consente di caricare tutti i file nella cartella `C:\local\path` (senza ricorsione nelle sottodirectory) nel contenitore `mycontainer1`:

```azcopy
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/mycontainer1<sastoken>"
```

Per trovare altri esempi, usare il comando seguente:

```azcopy
.\azcopy cp -h
```

## <a name="copy-data-between-two-storage-accounts"></a>Copiare dati tra due account di archiviazione

Copia di dati tra due account di archiviazione Usa la [Put Block From URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API e non utilizza larghezza di banda di rete del computer client. Copiare i dati tra due server di archiviazione di Azure direttamente, mentre AzCopy Orchestra semplicemente l'operazione di copia. Questa opzione è attualmente disponibile solo per l'archiviazione Blob.

Per copiare i dati tra due account di archiviazione, usare il comando seguente:
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/<sastoken>" "https://myotheraccount.blob.core.windows.net/<sastoken>" --recursive=true
```

> [!NOTE]
> Questo comando enumererà tutti i contenitori blob e copiarle per l'account di destinazione. A questo punto, v10 AzCopy supporta la copia solo i BLOB in blocchi tra due account di archiviazione. Ignorerà tutti gli altri oggetti di account di archiviazione (ad esempio accodare BLOB, BLOB di pagine, file, tabelle e code).

## <a name="copy-a-vhd-image-to-a-storage-account"></a>Copiare un'immagine di disco rigido virtuale in un account di archiviazione

Per impostazione predefinita, AzCopy v10 carica i dati in BLOB in blocchi. Tuttavia, se un file di origine ha un `.vhd` estensione, AzCopy v10 per impostazione predefinita sarà il caricamento in un blob di pagine. A questo punto, questa azione non è configurabile.

## <a name="sync-incremental-copy-and-delete-blob-storage-only"></a>Sincronizzazione: eliminazione e copia incrementale (solo archiviazione BLOB)

Il comando di sincronizzazione Sincronizza contenuto di una directory di origine in una directory nella destinazione, confrontare i nomi di file e i timestamp dell'ultima modifica. Questa operazione include l'eliminazione di file di destinazione facoltativo se non sono presenti nell'origine quando la `--delete-destination=prompt|true` flag viene fornito. Per impostazione predefinita, il comportamento di eliminazione è disabilitato. 

> [!NOTE] 
> Usare il `--delete-destination` flag con cautela. Abilitare la [eliminazione temporanea](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) funzionalità prima di abilitare la sincronizzazione per impedire eliminazioni accidentali nell'account di comportamento di eliminazione. 

> Quando si `--delete-destination` è impostato su true, AzCopy eliminerà i file che non esistono nell'origine dalla destinazione senza alcuna richiesta all'utente. Se si desidera essere richiesto di confermare, usare `--delete-destination=prompt`.

Per eseguire la sincronizzazione del file system locale con un account di archiviazione, usare il comando seguente:

```azcopy
.\azcopy sync "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

È anche possibile sincronizzare un contenitore blob fino a un file system locale:

```azcopy
# The SAS token isn't required for Azure Active Directory authentication.
.\azcopy sync "https://account.blob.core.windows.net/mycontainer1" "C:\local\path" --recursive=true
```

Questo comando consente di sincronizzare in modo incrementale l'origine alla destinazione in base l'ultimo timestamp modificato. Se si aggiunge o si elimina un file nell'origine, AzCopy v10 eseguirà la stessa operazione nella destinazione. Prima dell'eliminazione, AzCopy chiederà di confermare.

## <a name="advanced-configuration"></a>Configurazione avanzata

### <a name="configure-proxy-settings"></a>Configurare le impostazioni proxy

Per configurare le impostazioni proxy per AzCopy v10, impostare il https_proxy variabile di ambiente usando il comando seguente:

```cmd
# For Windows:
set https_proxy=<proxy IP>:<proxy port>
# For Linux:
export https_proxy=<proxy IP>:<proxy port>
# For MacOS
export https_proxy=<proxy IP>:<proxy port>
```

### <a name="optimize-throughput"></a>Ottimizzare la velocità effettiva

Impostare la variabile di ambiente AZCOPY_CONCURRENCY_VALUE per configurare il numero di richieste simultanee e per controllare la velocità effettiva delle prestazioni e utilizzo di risorse. Per impostazione predefinita, il valore è impostato su 300. Riducendo il valore si limiteranno la larghezza di banda e la CPU usate da AzCopy v10.

```cmd
# For Windows:
set AZCOPY_CONCURRENCY_VALUE=<value>
# For Linux:
export AZCOPY_CONCURRENCY_VALUE=<value>
# For MacOS
export AZCOPY_CONCURRENCY_VALUE=<value>
# To check the current value of the variable on all the platforms
.\azcopy env
# If the value is blank then the default value is currently in use
```

### <a name="change-the-location-of-the-log-files"></a>Modificare il percorso dei file di log

È possibile modificare il percorso dei file di log, se è necessario o se si vuole evitare di esaurire lo spazio sul disco del sistema operativo.

```cmd
# For Windows:
set AZCOPY_LOG_LOCATION=<value>
# For Linux:
export AZCOPY_LOG_LOCATION=<value>
# For MacOS
export AZCOPY_LOG_LOCATION=<value>
# To check the current value of the variable on all the platforms
.\azcopy env
# If the value is blank, then the default value is currently in use
```
### <a name="change-the-default-log-level"></a>Modificare il livello predefinito del log 

Per impostazione predefinita, AzCopy log livello è impostato su INFO. Se si vuole ridurre il livello di dettaglio del log per risparmiare spazio su disco, sovrascrivere l'impostazione usando l'opzione ``--log-level``. I livelli del log disponibili sono: DEBUG, INFO, avviso, errore, un metodo PANIC ed errore irreversibile.

### <a name="review-the-logs-for-errors"></a>Esaminare i log degli errori

Il comando seguente otterrà tutti gli errori con stato UPLOADFAILED dal log 04dc9ca9-158f-7945-5933-564021086c79:

```azcopy
cat 04dc9ca9-158f-7945-5933-564021086c79.log | grep -i UPLOADFAILED
```
## <a name="troubleshooting"></a>risoluzione dei problemi

AzCopy v10 crea file di log e file di piano per ogni processo. È possibile usare i log per analizzare e risolvere eventuali problemi potenziali. Il log conterrà lo stato dell'errore (UPLOADFAILED, COPYFAILED e DOWNLOADFAILED), il percorso completo e il motivo dell'errore. I file di piano e i log dei processi si trovano nella cartella %USERPROFILE\\.azcopy su Windows oppure in $HOME\\.azcopy su Mac e Linux.

> [!IMPORTANT]
> Quando si invia una richiesta di supporto tecnico Microsoft (o alla risoluzione del problema che interessa di terze parti), condividere la versione con alcune modifiche del comando da eseguire. In questo modo che la firma di accesso condiviso non viene accidentalmente condivise con altri. È possibile trovare la versione con modifiche all'inizio del file di log.

### <a name="view-and-resume-jobs"></a>Visualizzare e riprendere i processi

Ogni operazione di trasferimento creerà un processo di AzCopy. Usare il comando seguente per visualizzare la cronologia dei processi:

```azcopy
.\azcopy jobs list
```

Per visualizzare le statistiche del processo, usare il comando seguente:

```azcopy
.\azcopy jobs show <job-id>
```

Per filtrare i trasferimenti in base allo stato, usare il comando seguente:

```azcopy
.\azcopy jobs show <job-id> --with-status=Failed
```

Usare il comando seguente per riprendere un processo non riuscito/annullato. Questo comando Usa il relativo identificatore insieme al token di firma di accesso condiviso. Non è persistente per motivi di sicurezza:

```azcopy
.\azcopy jobs resume <jobid> --sourcesastokenhere --destinationsastokenhere
```

## <a name="next-steps"></a>Passaggi successivi

Se hai domande, problemi o commenti e suggerimenti generali, inviarli [su GitHub](https://github.com/Azure/azure-storage-azcopy.).


