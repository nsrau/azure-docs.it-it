---
title: Copiare o spostare dati in Archiviazione di Azure con AzCopy v10 (anteprima) | Microsoft Docs
description: Usare l'utilità AzCopy v10 (anteprima) per spostare o copiare dati da o verso BLOB, Data Lake e contenuto di file. Copiare i dati in archiviazione di Azure da file locali o copiare i dati all'interno o tra account di archiviazione. Migrare facilmente i dati in archiviazione di Azure.
services: storage
author: artemuwka
ms.service: storage
ms.topic: article
ms.date: 02/24/2019
ms.author: artemuwka
ms.subservice: common
ms.openlocfilehash: 111c24c1cd608542a5ef7da85f93ca22082af6d9
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726720"
---
# <a name="transfer-data-with-the-azcopy-v10-preview"></a>Trasferire dati con AzCopy v10 (anteprima)

AzCopy v10 (anteprima) è l'utilità della riga di comando di prossima generazione per copiare dati da e verso Archiviazione file e Archiviazione BLOB di Microsoft Azure, che offre un'interfaccia della riga di comando riprogettata e una nuova architettura per trasferimenti dei dati affidabili e ad alte prestazioni. Con AzCopy è possibile copiare dati tra un file system e un account di archiviazione o tra più account di archiviazione.

## <a name="whats-new-in-azcopy-v10"></a>Novità di AzCopy v10

- Possibilità di sincronizzare un file system con BLOB di Azure o viceversa. Usare `azcopy sync <source> <destination>`. Ideale per scenari di copia incrementale.
- Supporta le API di Azure Data Lake Storage Gen2. Usare `myaccount.dfs.core.windows.net` come URI per chiamare le API di Azure Data Lake Storage Gen2.
- Supporta la copia di un intero account (solo servizio BLOB) in un altro account.
- Ora Usa il nuovo account alla copia di account [Put Block dall'URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API. Non è necessario alcun trasferimento dei dati al client, pertanto il trasferimento è più rapido.
- Possibilità di elencare o rimuovere file e BLOB in un percorso specificato.
- Supporta i modelli con caratteri jolly in un percorso anche come in-- esclude flag.
- Resilienza migliorata: ogni istanza di AzCopy creerà un ordine di processo e un file di log correlato. È possibile visualizzare e riavviare i processi precedenti e riprendere quelli non riusciti. AzCopy ritenterà inoltre automaticamente un trasferimento dopo un errore.
- Miglioramenti delle prestazioni generali.

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

AzCopy v10 non richiede un'installazione. Aprire un'applicazione della riga di comando preferita e passare alla cartella in cui `azcopy.exe` (Windows) o `azcopy` Trova file eseguibile (Linux). Se si vuole, è possibile aggiungere il percorso della cartella di AzCopy al percorso di sistema.

## <a name="authentication-options"></a>Opzioni di autenticazione

AzCopy v10 consente di usare le opzioni seguenti durante l'autenticazione con Archiviazione di Azure:
- **Azure Active Directory [supportato per i servizi BLOB e ADLS Gen2]**. Usare ```.\azcopy login``` per accedere tramite Azure Active Directory.  L'utente deve avere [assegnato il ruolo "Collaboratore ai dati del BLOB di archiviazione"](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) per scrivere nell'archivio BLOB usando l'autenticazione di Azure Active Directory. Per l'autenticazione usando Managed Service Identity (MSI), usare `azcopy login --identity` dopo aver concesso il ruolo di collaboratore data l'istanza di calcolo di Azure.
- **Token di firma di accesso condiviso [supportato per i servizi BLOB e File]**. Per usarlo, aggiungere il token di firma di accesso condiviso al percorso del BLOB nella riga di comando. È possibile generare token di firma di accesso condiviso tramite il portale di Azure, [Storage Explorer](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/), [PowerShell](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageblobsastoken) o altri strumenti di propria scelta. Per altre informazioni, vedere gli [esempi](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2).

## <a name="getting-started"></a>Introduzione

> [!TIP]
> **Se si preferisce un'interfaccia utente grafica,**
>
> Provare [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/), un client desktop che semplifica la gestione dei dati di archiviazione di Azure, e **ora USA AzCopy** per accelerare il trasferimento dei dati e in uscita da archiviazione di Azure.
>
> È sufficiente abilitare la funzionalità di AzCopy in Storage Explorer nel menu 'Anteprima'. Storage Explorer userà AzCopy fase di caricamento e download dei dati nell'archiviazione Blob per migliorare le prestazioni.
> ![Abilitare AzCopy come motore di trasferimento in Azure Storage Explorer](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

AzCopy v10 ha una semplice sintassi autodocumentata. La sintassi generale è simile alla seguente quando si è connessi ad Azure Active Directory:

```azcopy
.\azcopy <command> <arguments> --<flag-name>=<flag-value>
# Examples if you have logged into the Azure Active Directory:
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile"
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/container"

# Examples if you are using SAS tokens to authenticate:
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container?sastoken" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile?sastoken"
```

Ecco come ottenere un elenco di comandi disponibili:

```azcopy
.\azcopy --help
# Using the alias instead
.\azcopy -h
```

Per visualizzare la pagina della Guida ed esempi per un comando specifico, eseguire il comando seguente:

```azcopy
.\azcopy <cmd> --help
# Example:
.\azcopy cp -h
```

## <a name="create-a-blob-container-or-file-share"></a>Creare un contenitore BLOB o una condivisione file 

**Creare un contenitore BLOB**

```azcopy
.\azcopy make "https://account.blob.core.windows.net/container-name"
```

**Creare una condivisione file**

```azcopy
.\azcopy make "https://account.file.core.windows.net/share-name"
```

**Creare un contenitore BLOB tramite ADLS Gen2**

Se nell'account di archiviazione BLOB sono stati abilitati gli spazi dei nomi gerarchici, è possibile usare il comando seguente per creare un nuovo file system (contenitore BLOB) e quindi caricare file in tale file system.

```azcopy
.\azcopy make "https://account.dfs.core.windows.net/top-level-resource-name"
```

## <a name="copy-data-to-azure-storage"></a>Copiare dati in Archiviazione di Azure

Usare il comando copy per trasferire i dati dall'origine alla destinazione. L'origine/destinazione può essere un:
- File system locale
- URI di BLOB di Azure/directory virtuale/contenitore
- URI di File di Azure/directory/condivisione file
- URI di file system/directory/file di Azure Data Lake Storage Gen2

```azcopy
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
# Using alias instead
.\azcopy cp <source path> <destination path> --<flag-name>=<flag-value>
```

Il comando seguente consente di caricare tutti i file nella cartella `C:\local\path` in modo ricorsivo nel contenitore `mycontainer1`, creando la directory `path` nel contenitore:

```azcopy
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

Il comando seguente consente di caricare tutti i file nella cartella `C:\local\path` (senza ricorsione nelle sottodirectory) nel contenitore `mycontainer1`:

```azcopy
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/mycontainer1<sastoken>"
```

Per ottenere più esempi, usare il comando seguente:

```azcopy
.\azcopy cp -h
```

## <a name="copy-data-between-two-storage-accounts"></a>Copiare dati tra due account di archiviazione

La copia di dati tra due account di archiviazione usa l'API [Put Block From URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) e non utilizza larghezza di banda di rete del computer client. I dati vengono copiati direttamente tra due server di Archiviazione di Azure mentre AzCopy orchestra semplicemente l'operazione di copia. Questa opzione è attualmente disponibile solo per Archiviazione BLOB.

Per copiare i dati tra due account di archiviazione, usare il comando seguente:
```azcopy
.\azcopy cp "https://account.blob.core.windows.net/<sastoken>" "https://otheraccount.blob.core.windows.net/<sastoken>" --recursive=true
```

> [!NOTE]
> Il comando enumererà tutti i contenitori BLOB e li copierà nell'account di destinazione. Attualmente AzCopy v10 supporta solo la copia dei BLOB in blocchi tra due account di archiviazione. Tutti gli altri oggetti degli account di archiviazione (BLOB di accodamento, BLOB di pagine, file, tabelle e code) verranno ignorati.

## <a name="copy-a-vhd-image-to-a-storage-account"></a>Copiare un'immagine di disco rigido virtuale in un account di archiviazione

Usare `--blob-type=PageBlob` per caricare un'immagine del disco in archiviazione Blob come un Blob di pagine.

```azcopy
.\azcopy cp "C:\myimages\diskimage.vhd" "https://account.blob.core.windows.net/mycontainer/diskimage.vhd<sastoken>" --blob-type=PageBlob
```

## <a name="sync-incremental-copy-and-optional-delete-blob-storage-only"></a>Sincronizzazione: (facoltativo) eliminare (solo archiviazione Blob) e copia incrementale

Comando di sincronizzazione viene sincronizzato contenuto di una directory di origine in una directory il confronto dei nomi file di destinazione e l'ultimo timestamp modificato. Facoltativamente, questa operazione include l'eliminazione di file di destinazione se non sono presenti nell'origine quando `--delete-destination=prompt|true` flag viene fornito. Per impostazione predefinita è disabilitato il comportamento di eliminazione.

> [!NOTE]
> Usare `--delete-destination` flag con cautela. Abilitare [eliminazione temporanea](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) funzionalità prima di abilitare la sincronizzazione per impedire eliminazioni accidentali nell'account di comportamento di eliminazione.
>
> Quando si `--delete-destination` è impostato su true, AzCopy eliminerà i file che non esistono nell'origine dalla destinazione senza alcuna richiesta all'utente. Se vuoi essere richiesto di confermare, usare `--delete-destination=prompt`.

Per eseguire la sincronizzazione del file system locale con un account di archiviazione, usare il comando seguente:

```azcopy
.\azcopy sync "C:\local\path" "https://account.blob.core.windows.net/mycontainer<sastoken>"
```

Allo stesso modo è possibile sincronizzare un contenitore BLOB con un file system locale:

```azcopy
# If you're using Azure Active Directory authentication the sastoken is not required
.\azcopy sync "https://account.blob.core.windows.net/mycontainer" "C:\local\path"
```

Il comando consente di sincronizzare in modo incrementale l'origine con la destinazione in base ai timestamp modificati più di recente. Se si aggiunge o si elimina un file nell'origine, AzCopy v10 eseguirà la stessa operazione nella destinazione. Se il comportamento di eliminazione è abilitato nel comando Sincronizza, AzCopy eliminerà i file dalla destinazione, se non esistono nell'origine più.

## <a name="advanced-configuration"></a>Configurazione avanzata

### <a name="configure-proxy-settings"></a>Configurare le impostazioni proxy

Per configurare le impostazioni proxy per AzCopy v10, impostare la variabile di ambiente https_proxy usando il comando seguente:

```cmd
# For Windows:
set https_proxy=<proxy IP>:<proxy port>
# For Linux:
export https_proxy=<proxy IP>:<proxy port>
# For MacOS
export https_proxy=<proxy IP>:<proxy port>
```

### <a name="optimize-throughput"></a>Ottimizzare la velocità effettiva

Impostare la variabile di ambiente AZCOPY_CONCURRENCY_VALUE per configurare il numero di richieste simultanee e controllare il consumo di risorse e le prestazioni per la velocità effettiva. Per impostazione predefinita, il valore è impostato su 300. Riducendo il valore si limiteranno la larghezza di banda e la CPU usate da AzCopy v10.

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
# If the value is blank then the default value is currently in use
```

### <a name="change-the-default-log-level"></a>Modificare il livello predefinito del log

Per impostazione predefinita, il livello del log di AzCopy è impostato su INFO. Se si vuole ridurre il livello di dettaglio del log per risparmiare spazio su disco, sovrascrivere l'impostazione usando l'opzione ``--log-level``. I livelli del log disponibili sono: DEBUG, INFO, WARNING, ERROR, PANIC e FATAL

## <a name="troubleshooting"></a>risoluzione dei problemi

AzCopy v10 crea file di log e file di piano per tutti i processi. È possibile usare i log per analizzare e risolvere eventuali problemi potenziali. Il log conterrà lo stato dell'errore (UPLOADFAILED, COPYFAILED e DOWNLOADFAILED), il percorso completo e il motivo dell'errore. I file di piano e i log dei processi si trovano nella cartella % USERPROFILE %\\cartella .azcopy su Windows o $HOME\\cartella .azcopy su Mac e Linux.

> [!IMPORTANT]
> Quando si invia una richiesta al supporto tecnico Microsoft (o si risolve il problema coinvolgendo terze parti), condividere la versione con alcune modifiche del comando che si sta provando a eseguire per essere sicuri che la firma di accesso condiviso non venga accidentalmente condivisa con altri. È possibile trovare la versione con modifiche all'inizio del file di log.

### <a name="review-the-logs-for-errors"></a>Esaminare i log degli errori

Il comando seguente otterrà tutti gli errori con stato UPLOADFAILED dal log 04dc9ca9-158f-7945-5933-564021086c79:

```azcopy
cat 04dc9ca9-158f-7945-5933-564021086c79.log | grep -i UPLOADFAILED
```

In alternativa è possibile visualizzare i nomi dei file che non sono stati trasferiti tramite `azcopy jobs show <jobid> --with-status=Failed` comando.

### <a name="view-and-resume-jobs"></a>Visualizzare e riprendere i processi

Ogni operazione di trasferimento creerà un processo di AzCopy. È possibile visualizzare la cronologia dei processi usando il comando seguente:

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

È possibile riprendere un processo non riuscito/annullato usando il relativo identificatore insieme al token di firma di accesso condiviso (non persistente per motivi di sicurezza):

```azcopy
.\azcopy jobs resume <jobid> --sourcesastokenhere --destinationsastokenhere
```

## <a name="next-steps"></a>Passaggi successivi

I commenti degli utenti sono sempre molto apprezzati. Eventuali domande, problemi o commenti di carattere generale devono essere inoltrati a https://github.com/Azure/azure-storage-azcopy. Grazie!
