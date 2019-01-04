---
title: Copiare o spostare dati in Archiviazione di Azure con AzCopy v10 (anteprima) | Microsoft Docs
description: Usare l'utilità AzCopy v10 (anteprima) per spostare o copiare dati da o verso BLOB, tabelle e contenuto di file. Copiare i dati in archiviazione di Azure da file locali o copiare i dati all'interno o tra account di archiviazione. Migrare facilmente i dati in archiviazione di Azure.
services: storage
author: artemuwka
ms.service: storage
ms.topic: article
ms.date: 10/09/2018
ms.author: artemuwka
ms.component: common
ms.openlocfilehash: 2ab933506ea03ae72198113d70888460e5001a6d
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2018
ms.locfileid: "52958415"
---
# <a name="transfer-data-with-the-azcopy-v10-preview"></a>Trasferire dati con AzCopy v10 (anteprima)

AzCopy v10 (anteprima) è l'utilità della riga di comando di prossima generazione per copiare dati da e verso Archiviazione file e Archiviazione BLOB di Microsoft Azure, che offre un'interfaccia della riga di comando riprogettata e una nuova architettura per trasferimenti dei dati affidabili e ad alte prestazioni. Con AzCopy è possibile copiare dati tra un file system e un account di archiviazione o tra più account di archiviazione.

## <a name="whats-new-in-azcopy-v10"></a>Novità di AzCopy v10

- Possibilità di sincronizzare un file system con BLOB di Azure o viceversa. Usare `azcopy sync <source> <destination>`. Ideale per scenari di copia incrementale.
- Supporta le API di Azure Data Lake Storage Gen2. Usare `myaccount.dfs.core.windows.net` come URI per chiamare le API di Azure Data Lake Storage Gen2.
- Supporta la copia di un intero account (solo servizio BLOB) in un altro account.
- La copia da account ad account usa ora le nuove API [Put from URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url). Non è necessario alcun trasferimento dei dati al client, pertanto il trasferimento è più rapido.
- Possibilità di elencare o rimuovere file e BLOB in un percorso specificato.
- Supporta i caratteri jolly in un percorso oltre ai flag --include ed --exclude.
- Resilienza migliorata: ogni istanza di AzCopy creerà un ordine di processo e un file di log correlato. È possibile visualizzare e riavviare i processi precedenti e riprendere quelli non riusciti. AzCopy ritenterà inoltre automaticamente un trasferimento dopo un errore.
- Miglioramenti delle prestazioni generali.

## <a name="download-and-install-azcopy"></a>Scaricare e installare AzCopy

### <a name="latest-preview-version-v10"></a>Versione di anteprima più recente (v10)

Scaricare la versione di anteprima più recente di AzCopy:
- [Windows](https://aka.ms/downloadazcopy-v10-windows)
- [Linux](https://aka.ms/downloadazcopy-v10-linux)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac)

### <a name="latest-production-version-v81"></a>Versione di produzione più recente (v8.1)

Scaricare la [versione di produzione più recente di AzCopy per Windows](https://aka.ms/downloadazcopy).

### <a name="azcopy-supporting-table-storage-service-v73"></a>AzCopy che supporta il servizio di Archiviazione tabelle (v7.3)

Scaricare [AzCopy v7.3 che supporta la copia dei dati da e verso il servizio di Archiviazione tabelle di Microsoft Azure](https://aka.ms/downloadazcopynet).

## <a name="post-installation-steps"></a>Passaggi successivi all'installazione

AzCopy v10 non richiede un'installazione. Aprire un'applicazione della riga di comando a scelta e passare alla cartella in cui si trova l'eseguibile `azcopy.exe`. Se si vuole, è possibile aggiungere il percorso della cartella di AzCopy al percorso di sistema.

## <a name="authentication-options"></a>Opzioni di autenticazione

AzCopy v10 consente di usare le opzioni seguenti durante l'autenticazione con Archiviazione di Azure:
- Azure Active Directory. Usare ```.\azcopy login``` per accedere tramite Azure Active Directory.  L'utente deve avere [assegnato il ruolo "Collaboratore ai dati del BLOB di archiviazione"](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) per scrivere nell'archivio BLOB usando l'autenticazione di Azure Active Directory.
- Token di firma di accesso condiviso che è necessario accodare al percorso del BLOB. È possibile generare token di firma di accesso condiviso tramite il portale di Azure, [Storage Explorer](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/), [PowerShell](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestorageblobsastoken?view=azurermps-6.9.0) o altri strumenti di propria scelta. Per altre informazioni, vedere gli [esempi](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2).

## <a name="getting-started"></a>Introduzione

AzCopy v10 ha una semplice sintassi autodocumentata. La sintassi generale è simile alla seguente:

```azcopy
.\azcopy <command> <arguments> --<flag-name>=<flag-value>
# Example:
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/containersastoken" --recursive=true
```

Ecco come ottenere un elenco di comandi disponibili:

```azcopy
.\azcopy -help
# Using the alias instead
.\azcopy -h
```

Per visualizzare la pagina della Guida ed esempi per un comando specifico, eseguire il comando seguente:

```azcopy
.\azcopy <cmd> -help
# Example:
.\azcopy cp -h
```

## <a name="create-a-file-system-azure-data-lake-storage-gen2-only"></a>Creare un file system (solo Azure Data Lake Storage Gen2)

Se nell'account di archiviazione BLOB sono stati abilitati gli spazi dei nomi gerarchici, è possibile usare il comando seguente per creare un nuovo file system e quindi caricare e scaricare file dal file system.

```azcopy
.\azcopy make "https://account.dfs.core.windows.net/top-level-resource-name" --recursive=true
```

La parte ``account`` di questa stringa è il nome dell'account di archiviazione. La parte ``top-level-resource-name`` di questa stringa è il nome del file system che si vuole creare.

## <a name="copy-data-to-azure-storage"></a>Copiare dati in Archiviazione di Azure

Usare il comando copy per trasferire i dati dall'origine alla destinazione. L'origine/destinazione può essere un:
- File system locale
- URI di BLOB di Azure/directory virtuale/contenitore
- URI di File di Azure/directory/condivisione file
- URI di file system/directory/file di Azure Data Lake Storage Gen2

> [!NOTE]
> Attualmente AzCopy v10 supporta solo la copia dei BLOB in blocchi tra due account di archiviazione.

```azcopy
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
# Using alias instead
.\azcopy cp <source path> <destination path> --<flag-name>=<flag-value>
```

Il comando seguente consente di caricare tutti i file nella cartella C:\local\path in modo ricorsivo nel contenitore "mycontainer1":

```azcopy
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

Se nell'account di archiviazione BLOB sono stati abilitati gli spazi dei nomi gerarchici, è possibile usare il comando seguente per caricare file nel file system:

```azcopy
.\azcopy cp "C:\local\path" "https://myaccount.dfs.core.windows.net/myfolder<sastoken>" --recursive=true
```

Il comando seguente consente di caricare tutti i file nella cartella C:\local\path (senza ricorsione nelle sottodirectory) nel contenitore "mycontainer1":

```azcopy
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/mycontainer1<sastoken>"
```

Se nell'account di archiviazione BLOB sono stati abilitati gli spazi dei nomi gerarchici, è possibile usare il comando seguente:

```azcopy
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/myfolder<sastoken>"
```

Per ottenere più esempi, usare il comando seguente:

```azcopy
.\azcopy cp -h
```

## <a name="copy-data-between-two-storage-accounts"></a>Copiare dati tra due account di archiviazione

La copia di dati tra due account di archiviazione usa l'API [Put Block From URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) e non utilizza larghezza di banda di rete del computer client. I dati vengono copiati direttamente tra due server di Archiviazione di Azure mentre AzCopy orchestra semplicemente l'operazione di copia. 

Per copiare i dati tra due account di archiviazione, usare il comando seguente:
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/<sastoken>" "https://myotheraccount.blob.core.windows.net/<sastoken>" --recursive=true
```

Per lavorare con gli account di archiviazione BLOB in cui sono abilitati gli spazi dei nomi gerarchici, sostituire la stringa ``blob.core.windows.net`` con ``dfs.core.windows.net`` in questi esempi.

> [!NOTE]
> Il comando enumererà tutti i contenitori BLOB e li copierà nell'account di destinazione. Attualmente AzCopy v10 supporta solo la copia dei BLOB in blocchi tra due account di archiviazione. Tutti gli altri oggetti degli account di archiviazione (BLOB di accodamento, BLOB di pagine, file, tabelle e code) verranno ignorati.

## <a name="copy-a-vhd-image-to-a-storage-account"></a>Copiare un'immagine di disco rigido virtuale in un account di archiviazione

Per impostazione predefinita, AzCopy v10 carica i dati in BLOB in blocchi. Tuttavia, se un file di origine ha l'estensione vhd, per impostazione predefinita AzCopy v10 lo caricherà in un BLOB di pagine. Questo comportamento non è configurabile.

## <a name="sync-incremental-copy-and-delete"></a>Sincronizzazione: eliminazione e copia incrementale

> [!NOTE]
> Il comando sync sincronizza il contenuto dall'origine alla destinazione, inclusa l'eliminazione dei file di destinazione se non sono presenti nell'origine. Assicurarsi di usare la destinazione che si intende sincronizzare.

Per eseguire la sincronizzazione del file system locale con un account di archiviazione, usare il comando seguente:

```azcopy
.\azcopy sync "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

Allo stesso modo è possibile sincronizzare un contenitore BLOB con un file system locale:

```azcopy
# If you're using Azure Active Directory authentication the sastoken is not required
.\azcopy sync "https://account.blob.core.windows.net/mycontainer1" "C:\local\path" --recursive=true
```

Il comando consente di sincronizzare in modo incrementale l'origine con la destinazione in base ai timestamp modificati più di recente. Se si aggiunge o si elimina un file nell'origine, AzCopy v10 eseguirà la stessa operazione nella destinazione.

[!NOTE] Per lavorare con gli account di archiviazione BLOB in cui sono abilitati gli spazi dei nomi gerarchici, sostituire la stringa ``blob.core.windows.net`` con ``dfs.core.windows.net`` in questi esempi.

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
```

## <a name="troubleshooting"></a>risoluzione dei problemi

AzCopy v10 crea file di log e file di piano per tutti i processi. È possibile usare i log per analizzare e risolvere eventuali problemi potenziali. Il log conterrà lo stato dell'errore (UPLOADFAILED, COPYFAILED e DOWNLOADFAILED), il percorso completo e il motivo dell'errore. I file di piano e i log dei processi si trovano nella cartella %USERPROFILE\\.azcopy.

### <a name="review-the-logs-for-errors"></a>Esaminare i log degli errori

Il comando seguente otterrà tutti gli errori con stato UPLOADFAILED dal log 04dc9ca9-158f-7945-5933-564021086c79:

```azcopy
cat 04dc9ca9-158f-7945-5933-564021086c79.log | grep -i UPLOADFAILED
```

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