---
title: Trasferire dati da e verso l'archiviazione BLOB di Azure tramite AzCopy V10 | Microsoft Docs
description: Questo articolo contiene una raccolta di comandi di esempio AzCopy che semplificano la creazione di contenitori, la copia di file e la sincronizzazione di directory tra i file System e i contenitori locali.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 294adce3dc312003d72336bd0752ba3aba5eaace
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792855"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Trasferire i dati con AzCopy e l'archiviazione BLOB

AzCopy è un'utilità da riga di comando che è possibile usare per copiare i dati in, da o tra account di archiviazione. Questo articolo contiene i comandi di esempio che funzionano con l'archivio BLOB.

> [!TIP]
> Gli esempi in questo articolo racchiudono gli argomenti del percorso con virgolette singole (''). Usare le virgolette singole in tutte le shell dei comandi eccetto la shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti del percorso con virgolette doppie ("") anziché virgolette singole ('').

## <a name="get-started"></a>Introduzione

Vedere l'articolo [Introduzione a AzCopy](storage-use-azcopy-v10.md) per scaricare AzCopy e informazioni sui modi in cui è possibile fornire le credenziali di autorizzazione al servizio di archiviazione.

> [!NOTE]
> Gli esempi in questo articolo presuppongono che l'identità sia stata autenticata tramite il `AzCopy login` comando. AzCopy usa quindi l'account Azure AD per autorizzare l'accesso ai dati nell'archivio BLOB.
>
> Se si preferisce usare un token di firma di accesso condiviso per autorizzare l'accesso ai dati BLOB, è possibile aggiungere tale token all'URL della risorsa in ogni comando AzCopy.
>
> Ad esempio: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="create-a-container"></a>Creare un contenitore

È possibile usare il comando [azcopy make](storage-ref-azcopy-make.md) per creare un contenitore. Negli esempi di questa sezione viene creato un contenitore denominato `mycontainer` .

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **Esempio** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

Per informazioni dettagliate sulla documentazione di riferimento, vedere [azcopy make](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Caricare i file

È possibile usare il comando [copy di azcopy](storage-ref-azcopy-copy.md) per caricare file e directory dal computer locale.

Questa sezione contiene gli esempi seguenti:

> [!div class="checklist"]
> * Caricare un file
> * Caricare una directory
> * Caricare il contenuto di una directory 
> * Carica file specifici

> [!TIP]
> È possibile modificare l'operazione di caricamento usando i flag facoltativi. Ecco alcuni esempi.
>
> |Scenario|Contrassegno|
> |---|---|
> |Caricare i file come BLOB di accodamento o BLOB di pagine.|**--BLOB-type** = \[ BlockBlob \| PageBlob \| AppendBlob\]|
> |Effettuare il caricamento in un livello di accesso specifico, ad esempio il livello archivio.|**--Block-BLOB-Tier** = \[ Nessun \| \| Archivio Hot Cool \|\]|
> 
> Per un elenco completo, vedere [Opzioni](storage-ref-azcopy-copy.md#options).

### <a name="upload-a-file"></a>Caricare un file

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **Esempio** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

È anche possibile caricare un file usando un carattere jolly (*) in qualsiasi punto del percorso o del nome file. Ad esempio: `'C:\myDirectory\*.txt'` o `C:\my*\*.txt` .

### <a name="upload-a-directory"></a>Caricare una directory

Questo esempio copia una directory (e tutti i file in tale directory) in un contenitore BLOB. Il risultato è una directory nel contenitore con lo stesso nome.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **Esempio** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

Per eseguire la copia in una directory all'interno del contenitore, è sufficiente specificare il nome della directory nella stringa di comando.

|    |     |
|--------|-----------|
| **Esempio** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

Se si specifica il nome di una directory che non esiste nel contenitore, AzCopy crea una nuova directory con tale nome.

### <a name="upload-the-contents-of-a-directory"></a>Caricare il contenuto di una directory

È possibile caricare il contenuto di una directory senza copiare la directory che lo contiene usando il carattere jolly (*).

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **Esempio** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |

> [!NOTE]
> Aggiungere il `--recursive` flag per caricare i file in tutte le sottodirectory.

### <a name="upload-specific-files"></a>Carica file specifici

È possibile caricare file specifici usando nomi di file completi, nomi parziali con caratteri jolly (*) o con date e ore.

#### <a name="specify-multiple-complete-file-names"></a>Specificare più nomi di file completi

Usare il comando [azcopy Copy](storage-ref-azcopy-copy.md) con l' `--include-path` opzione. Separare i singoli nomi di file usando un punto e virgola ( `;` ).

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **Esempio** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

In questo esempio, AzCopy trasferisce la `C:\myDirectory\photos` Directory e il `C:\myDirectory\documents\myFile.txt` file. È necessario includere l' `--recursive` opzione per trasferire tutti i file nella `C:\myDirectory\photos` Directory.

È anche possibile escludere i file utilizzando l' `--exclude-path` opzione. Per altre informazioni, vedere la documentazione di riferimento per la [copia di azcopy](storage-ref-azcopy-copy.md) .

#### <a name="use-wildcard-characters"></a>Usa caratteri jolly

Usare il comando [azcopy Copy](storage-ref-azcopy-copy.md) con l' `--include-pattern` opzione. Specificare i nomi parziali che includono i caratteri jolly. Separare i nomi con semicolin ( `;` ). 

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Esempio** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

È anche possibile escludere i file utilizzando l' `--exclude-pattern` opzione. Per altre informazioni, vedere la documentazione di riferimento per la [copia di azcopy](storage-ref-azcopy-copy.md) .

Le `--include-pattern` `--exclude-pattern` Opzioni e si applicano solo ai nomi file e non al percorso.  Se si desidera copiare tutti i file di testo presenti in un albero di directory, utilizzare l' `–recursive` opzione per ottenere l'intero albero di directory e quindi utilizzare `–include-pattern` e specificare `*.txt` per ottenere tutti i file di testo.

#### <a name="upload-files-that-were-modified-after-a-date-and-time"></a>Caricare i file che sono stati modificati dopo una data e un'ora 

Usare il comando [azcopy Copy](storage-ref-azcopy-copy.md) con l' `--include-after` opzione. Specificare una data e un'ora in formato ISO-8601 (ad esempio: `2020-08-19T15:04:00Z` ). 

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **Esempio** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory'   --include-after '2020-08-19T15:04:00Z'` |

Per informazioni di riferimento dettagliate, vedere la documentazione di riferimento per la [copia di azcopy](storage-ref-azcopy-copy.md) .

## <a name="download-files"></a>Scaricare i file

È possibile usare il comando [copy di azcopy](storage-ref-azcopy-copy.md) per scaricare i BLOB, le directory e i contenitori nel computer locale.

Questa sezione contiene gli esempi seguenti:

> [!div class="checklist"]
> * Scaricare un file
> * Scaricare una directory
> * Scarica il contenuto di una directory
> * Scarica file specifici

> [!TIP]
> È possibile modificare l'operazione di download usando i flag facoltativi. Ecco alcuni esempi.
>
> |Scenario|Contrassegno|
> |---|---|
> |Decomprime automaticamente i file.|**--Decomprimi**|
> |Specificare il modo in cui si desidera che le voci di log relative alla copia siano disponibili.|**--livello** = \[ di log informazioni sull'errore di avviso \| \| \| nessuno\]|
> |Specificare se e come sovrascrivere i BLOB e i file in conflitto nella destinazione.|**--Sovrascrivi** = \[ \| \| prompt ifSourceNewer true \| false\]|
> 
> Per un elenco completo, vedere [Opzioni](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> Se il `Content-md5` valore della proprietà di un BLOB contiene un hash, AzCopy calcola un hash MD5 per i dati scaricati e verifica che l'hash MD5 archiviato nella proprietà del BLOB `Content-md5` corrisponda all'hash calcolato. Se questi valori non corrispondono, il download ha esito negativo a meno che non si esegua l'override di questo comportamento accodando `--check-md5=NoCheck` o `--check-md5=LogOnly` al comando copy.

### <a name="download-a-file"></a>Scaricare un file

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **Esempio** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Scaricare una directory

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **Esempio** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

Questo esempio genera una directory denominata `C:\myDirectory\myBlobDirectory` che contiene tutti i file scaricati.

### <a name="download-the-contents-of-a-directory"></a>Scarica il contenuto di una directory

È possibile scaricare il contenuto di una directory senza copiare la directory che lo contiene usando il carattere jolly (*).

> [!NOTE]
> Attualmente, questo scenario è supportato solo per gli account che non dispongono di uno spazio dei nomi gerarchico.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **Esempio** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

> [!NOTE]
> Aggiungere il `--recursive` flag per scaricare i file in tutte le sottodirectory.

### <a name="download-specific-files"></a>Scarica file specifici

È possibile scaricare file specifici usando nomi di file completi, nomi parziali con caratteri jolly (*) o con date e ore. 

#### <a name="specify-multiple-complete-file-names"></a>Specificare più nomi di file completi

Usare il comando [azcopy Copy](storage-ref-azcopy-copy.md) con l' `--include-path` opzione. Separare i singoli nomi di file tramite semicolin ( `;` ).

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Esempio** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

In questo esempio, AzCopy trasferisce la `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` Directory e il `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` file. È necessario includere l' `--recursive` opzione per trasferire tutti i file nella `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` Directory.

È anche possibile escludere i file utilizzando l' `--exclude-path` opzione. Per altre informazioni, vedere la documentazione di riferimento per la [copia di azcopy](storage-ref-azcopy-copy.md) .

#### <a name="use-wildcard-characters"></a>Usa caratteri jolly

Usare il comando [azcopy Copy](storage-ref-azcopy-copy.md) con l' `--include-pattern` opzione. Specificare i nomi parziali che includono i caratteri jolly. Separare i nomi con semicolin ( `;` ).

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Esempio** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

È anche possibile escludere i file utilizzando l' `--exclude-pattern` opzione. Per altre informazioni, vedere la documentazione di riferimento per la [copia di azcopy](storage-ref-azcopy-copy.md) .

Le `--include-pattern` `--exclude-pattern` Opzioni e si applicano solo ai nomi file e non al percorso.  Se si desidera copiare tutti i file di testo presenti in un albero di directory, utilizzare l' `–recursive` opzione per ottenere l'intero albero di directory e quindi utilizzare `–include-pattern` e specificare `*.txt` per ottenere tutti i file di testo.

#### <a name="download-files-that-were-modified-after-a-date-and-time"></a>Scaricare i file che sono stati modificati dopo una data e un'ora 

Usare il comando [azcopy Copy](storage-ref-azcopy-copy.md) con l' `--include-after` opzione. Specificare una data e un'ora in formato ISO-8601 (ad esempio: `2020-08-19T15:04:00Z` ). 

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>/*' '<local-directory-path>' --include-after <Date-Time-in-ISO-8601-format>` |
| **Esempio** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |

Per informazioni di riferimento dettagliate, vedere la documentazione di riferimento per la [copia di azcopy](storage-ref-azcopy-copy.md) .

#### <a name="download-previous-versions-of-a-blob"></a>Scaricare le versioni precedenti di un BLOB

Se è stato abilitato il [controllo delle versioni dei BLOB](../blobs/versioning-enable.md), è possibile scaricare una o più versioni precedenti di un BLOB. 

Per prima cosa, creare un file di testo contenente un elenco di [ID versione](../blobs/versioning-overview.md). Ogni ID versione deve essere visualizzato in una riga separata. Ad esempio: 

```
2020-08-17T05:50:34.2199403Z
2020-08-17T05:50:34.5041365Z
2020-08-17T05:50:36.7607103Z
```

Usare quindi il comando [azcopy Copy](storage-ref-azcopy-copy.md) con l' `--list-of-versions` opzione. Specificare il percorso del file di testo che contiene l'elenco di versioni (ad esempio: `D:\\list-of-versions.txt` ).  

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-directory-path>' --list-of-versions '<list-of-versions-file>'`|
| **Esempio** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt' --list-of-versions 'D:\\list-of-versions.txt'` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt' --list-of-versions 'D:\\list-of-versions.txt'` |

Il nome di ogni file scaricato inizia con l'ID versione seguito dal nome del BLOB. 

## <a name="copy-blobs-between-storage-accounts"></a>Copiare i BLOB tra account di archiviazione

È possibile usare AzCopy per copiare i BLOB in altri account di archiviazione. L'operazione di copia è sincrona, quindi se il comando restituisce il risultato ciò indica che tutti i file sono stati copiati. 

AzCopy usa le [API](/rest/api/storageservices/put-page-from-url) [da server a server](/rest/api/storageservices/put-block-from-url) , quindi i dati vengono copiati direttamente tra i server di archiviazione. Queste operazioni di copia non utilizzano la larghezza di banda di rete del computer. È possibile aumentare la velocità effettiva di queste operazioni impostando il valore della `AZCOPY_CONCURRENCY_VALUE` variabile di ambiente. Per altre informazioni, vedere [ottimizzare la velocità effettiva](storage-use-azcopy-configure.md#optimize-throughput).

> [!NOTE]
> Questo scenario presenta le limitazioni seguenti nella versione corrente.
>
> - È necessario aggiungere un token SAS a ogni URL di origine. Se si forniscono le credenziali di autorizzazione usando Azure Active Directory (AD), è possibile omettere il token SAS solo dall'URL di destinazione. Assicurarsi di aver configurato i ruoli appropriati nell'account di destinazione. Vedere l' [opzione 1: usare Azure Active Directory](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory).
>-  Gli account di archiviazione BLOB in blocchi Premium non supportano i livelli di accesso. Omettere il livello di accesso di un BLOB dall'operazione di copia impostando `s2s-preserve-access-tier` su `false` (ad esempio: `--s2s-preserve-access-tier=false` ).

Questa sezione contiene gli esempi seguenti:

> [!div class="checklist"]
> * Copiare un BLOB in un altro account di archiviazione
> * Copiare una directory in un altro account di archiviazione
> * Copiare un contenitore in un altro account di archiviazione
> * Copia tutti i contenitori, le directory e i file in un altro account di archiviazione

Questi esempi funzionano anche con gli account che hanno uno spazio dei nomi gerarchico. L'accesso a più [protocolli su data Lake storage](../blobs/data-lake-storage-multi-protocol-access.md) consente di usare la stessa sintassi URL ( `blob.core.windows.net` ) per tali account.

> [!TIP]
> È possibile modificare l'operazione di copia usando i flag facoltativi. Ecco alcuni esempi.
>
> |Scenario|Contrassegno|
> |---|---|
> |Copiare i BLOB come blocchi, pagine o BLOB di Accodamento.|**--BLOB-type** = \[ BlockBlob \| PageBlob \| AppendBlob\]|
> |Copiare in un livello di accesso specifico, ad esempio il livello archivio.|**--Block-BLOB-Tier** = \[ Nessun \| \| Archivio Hot Cool \|\]|
> |Decomprime automaticamente i file.|**--Decomprimi** = \[ \|deflate gzip\]|
> 
> Per un elenco completo, vedere [Opzioni](storage-ref-azcopy-copy.md#options).

### <a name="copy-a-blob-to-another-storage-account"></a>Copiare un BLOB in un altro account di archiviazione

Utilizzare la stessa sintassi URL ( `blob.core.windows.net` ) per gli account che dispongono di uno spazio dei nomi gerarchico.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **Esempio** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Copiare una directory in un altro account di archiviazione

Utilizzare la stessa sintassi URL ( `blob.core.windows.net` ) per gli account che dispongono di uno spazio dei nomi gerarchico.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Esempio** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Esempio** (spazio dei nomi gerarchico)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-a-container-to-another-storage-account"></a>Copiare un contenitore in un altro account di archiviazione

Utilizzare la stessa sintassi URL ( `blob.core.windows.net` ) per gli account che dispongono di uno spazio dei nomi gerarchico.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Esempio** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Esempio** (spazio dei nomi gerarchico)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>Copiare tutti i contenitori, le directory e i BLOB in un altro account di archiviazione

Utilizzare la stessa sintassi URL ( `blob.core.windows.net` ) per gli account che dispongono di uno spazio dei nomi gerarchico.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **Esempio** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |
| **Esempio** (spazio dei nomi gerarchico)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

## <a name="synchronize-files"></a>Sincronizzare i file

È possibile sincronizzare il contenuto di un file system locale con un contenitore BLOB. È anche possibile sincronizzare i contenitori e le directory virtuali tra loro. La sincronizzazione è unidirezionale. In altre parole, è possibile scegliere quali di questi due endpoint sono l'origine e quella di destinazione. La sincronizzazione usa anche API da server a server. Gli esempi presentati in questa sezione funzionano anche con gli account che hanno uno spazio dei nomi gerarchico. 

> [!NOTE]
> La versione corrente di AzCopy non esegue la sincronizzazione tra altre origini e destinazioni, ad esempio file storage o Amazon Web Services (AWS) S3 bucket).

Il comando di [sincronizzazione](storage-ref-azcopy-sync.md) Confronta i nomi di file e i timestamp dell'Ultima modifica. Impostare il `--delete-destination` flag facoltativo sul valore `true` o `prompt` per eliminare i file nella directory di destinazione se tali file non sono più presenti nella directory di origine.

Se si imposta il `--delete-destination` flag su `true` AzCopy Elimina i file senza fornire un messaggio di richiesta. Se si desidera che venga visualizzata una richiesta prima che AzCopy elimini un file, impostare il `--delete-destination` flag su `prompt` .

> [!NOTE]
> Per evitare eliminazioni accidentali, assicurarsi di abilitare la funzionalità di [eliminazione](../blobs/soft-delete-blob-overview.md) temporanea prima di usare il `--delete-destination=prompt|true` flag.

> [!TIP]
> È possibile modificare l'operazione di sincronizzazione usando i flag facoltativi. Ecco alcuni esempi.
>
> |Scenario|Contrassegno|
> |---|---|
> |Consente di specificare il modo in cui devono essere convalidati gli hash MD5 durante il download.|**--Check-MD5** = \[ NOCHECK \| loginly \| FailIfDifferent \| FailIfDifferentOrMissing\]|
> |Escludere i file in base a un modello.|**--Exclude-Path**|
> |Specificare il modo in cui si desidera che le voci di log correlate alla sincronizzazione siano disponibili.|**--livello** = \[ di log informazioni sull'errore di avviso \| \| \| nessuno\]|
> 
> Per un elenco completo, vedere [Opzioni](storage-ref-azcopy-sync.md#options).

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Aggiornare un contenitore con modifiche a una file system locale

In questo caso, il contenitore è la destinazione e il file system locale è l'origine. 

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Esempio** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Aggiornare un file system locale con le modifiche apportate a un contenitore

In questo caso, il file system locale è la destinazione e il contenitore è l'origine.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **Esempio** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

### <a name="update-a-container-with-changes-in-another-container"></a>Aggiornare un contenitore con le modifiche in un altro contenitore

Il primo contenitore visualizzato in questo comando è l'origine. Il secondo è la destinazione.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Esempio** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Aggiornare una directory con le modifiche apportate a una directory in un'altra condivisione file

La prima directory visualizzata in questo comando è l'origine. Il secondo è la destinazione.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **Esempio** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="next-steps"></a>Passaggi successivi

Altri esempi sono disponibili in uno di questi articoli:

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)

- [Eseguire la migrazione di dati locali in una risorsa di archiviazione cloud tramite AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)

- [Trasferire dati con AzCopy e bucket Amazon S3](storage-use-azcopy-s3.md)

- [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)