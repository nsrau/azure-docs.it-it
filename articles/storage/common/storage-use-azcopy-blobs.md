---
title: Trasferire dati da o verso l'archiviazione BLOB di Azure usando AzCopy v10 Documenti Microsoft
description: Questo articolo contiene una raccolta di comandi di esempio AzCopy che consentono di creare contenitori, copiare file e sincronizzare directory tra file system locali e contenitori.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: fbdb447905ae43fe92693dfe45c1add710f76355
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78933583"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Trasferire dati con AzCopy e l'archiviazione BLOBTransfer data with AzCopy and Blob storage

AzCopy è un'utilità della riga di comando che è possibile usare per copiare dati in, da o tra account di archiviazione. Questo articolo contiene comandi di esempio che funzionano con l'archiviazione BLOB.

## <a name="get-started"></a>Introduzione

Vedere l'articolo Introduzione a [AzCopy](storage-use-azcopy-v10.md) per scaricare AzCopy e informazioni sui modi in cui è possibile fornire le credenziali di autorizzazione al servizio di archiviazione.

> [!NOTE]
> Negli esempi di questo articolo si presuppone che l'identità sia stata autenticata tramite il `AzCopy login` comando. AzCopy usa quindi l'account Azure AD per autorizzare l'accesso ai dati nell'archivio BLOB.
>
> Se si preferisce usare un token di firma di accesso condiviso per autorizzare l'accesso ai dati BLOB, è possibile aggiungerlo all'URL della risorsa in ogni comando AzCopy.If you'd prefer use a SAS token to authorize access to blob data, then you can append that token to the resource URL in each AzCopy command.
>
> Ad esempio `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="create-a-container"></a>Creare un contenitore

> [!TIP]
> Gli esempi in questa sezione racchiudono gli argomenti di percorso tra virgolette singole (''). Utilizzare le virgolette singole in tutte le shell dei comandi, ad eccezione della shell dei comandi di Windows (cmd.exe). Se si utilizza una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti di percorso tra virgolette doppie ("") anziché virgolette singole ('').

È possibile utilizzare il comando [azcopy make](storage-ref-azcopy-make.md) per creare un contenitore. Negli esempi di questa sezione `mycontainer`viene creato un contenitore denominato .

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **Esempio** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

Per documenti di riferimento dettagliati, vedere [azcopy make](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Caricare file

È possibile utilizzare il comando [azcopy copy copy](storage-ref-azcopy-copy.md) per caricare file e directory dal computer locale.

Questa sezione contiene gli esempi seguenti:

> [!div class="checklist"]
> * Caricare un file
> * Caricare una directory
> * Caricare il contenuto di una directory 
> * Caricare file specifici

Per documenti di riferimento dettagliati, consultate [copia azcopy](storage-ref-azcopy-copy.md).

> [!TIP]
> Gli esempi in questa sezione racchiudono gli argomenti di percorso tra virgolette singole (''). Utilizzare le virgolette singole in tutte le shell dei comandi, ad eccezione della shell dei comandi di Windows (cmd.exe). Se si utilizza una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti di percorso tra virgolette doppie ("") anziché virgolette singole ('').

### <a name="upload-a-file"></a>Caricare un file

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **Esempio** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

È inoltre possibile caricare un file utilizzando un carattere jolly (-) in qualsiasi punto del percorso o del nome del file. Ad esempio: `'C:\myDirectory\*.txt'` `C:\my*\*.txt`, o .

> [!NOTE]
> AzCopy per impostazione predefinita carica i dati come BLOB in blocchi. Per caricare i file come BLOB di accodamento o BLOB di pagine, usare il flag `--blob-type=[BlockBlob|PageBlob|AppendBlob]`.
> AzCopy per impostazione predefinita carica i dati per ereditare il livello di accesso all'account. Per caricare file in un [livello](../blobs/storage-blob-storage-tiers.md) `--block-blob-tier=[Hot|Cool|Archive]`di accesso specifico, utilizzare il flag .

### <a name="upload-a-directory"></a>Caricare una directory

In questo esempio viene copiata una directory (e tutti i file in tale directory) in un contenitore BLOB. Il risultato è una directory nel contenitore con lo stesso nome.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **Esempio** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

Per copiare in una directory all'interno del contenitore, è sufficiente specificare il nome di tale directory nella stringa di comando.

|    |     |
|--------|-----------|
| **Esempio** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

Se si specifica il nome di una directory che non esiste nel contenitore, AzCopy crea una nuova directory con tale nome.

### <a name="upload-the-contents-of-a-directory"></a>Caricare il contenuto di una directory

È possibile caricare il contenuto di una directory senza copiare la directory che lo contiene utilizzando il simbolo di carattere jolly (-).

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **Esempio** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |

> [!NOTE]
> Aggiungere `--recursive` il flag per caricare i file in tutte le sottodirectory.

### <a name="upload-specific-files"></a>Caricare file specifici

È possibile specificare nomi di file completi o utilizzare nomi parziali con caratteri jolly (-) .

#### <a name="specify-multiple-complete-file-names"></a>Specificare più nomi di file completi

Utilizzare il comando [azcopy copy](storage-ref-azcopy-copy.md) copy con l'opzione. `--include-path` Separare i singoli nomi di`;`file utilizzando un punto e virgola ( ).

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **Esempio** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

In questo esempio AzCopy `C:\myDirectory\photos` trasferisce `C:\myDirectory\documents\myFile.txt` la directory e il file. È necessario includere `--recursive` l'opzione per `C:\myDirectory\photos` trasferire tutti i file nella directory.

È inoltre possibile escludere `--exclude-path` i file utilizzando l'opzione . Per altre informazioni, vedere azcopy copy reference docs(documento di riferimento sulla [copia di atrattari).](storage-ref-azcopy-copy.md)

#### <a name="use-wildcard-characters"></a>Utilizzare caratteri jolly

Utilizzare il comando [azcopy copy](storage-ref-azcopy-copy.md) copy con l'opzione. `--include-pattern` Specificare nomi parziali che includono i caratteri jolly. Separare i nomi utilizzando`;`una semicolina ( ). 

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Esempio** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

È inoltre possibile escludere `--exclude-pattern` i file utilizzando l'opzione . Per altre informazioni, vedere azcopy copy reference docs(documento di riferimento sulla [copia di atrattari).](storage-ref-azcopy-copy.md)

Le `--include-pattern` `--exclude-pattern` opzioni e si applicano solo ai nomi di file e non al percorso.  Se si desidera copiare tutti i file di testo `–recursive` presenti in una struttura di directory, `–include-pattern` utilizzare `*.txt` l'opzione per ottenere l'intera struttura di directory, quindi utilizzare e specificare per ottenere tutti i file di testo.

## <a name="download-files"></a>Scaricare i file

È possibile usare il comando [azcopy copy per](storage-ref-azcopy-copy.md) scaricare BLOB, directory e contenitori nel computer locale.

Questa sezione contiene gli esempi seguenti:

> [!div class="checklist"]
> * Scaricare un file
> * Scaricare una directory
> * Scaricare il contenuto di una directory
> * Scaricare file specifici

> [!NOTE]
> Se `Content-md5` il valore della proprietà di un BLOB contiene un hash, AzCopy calcola un hash MD5 per `Content-md5` i dati scaricati e verifica che l'hash MD5 archiviato nella proprietà del BLOB corrisponda all'hash calcolato. Se questi valori non corrispondono, il download non riesce a meno che non si esegua l'override di questo comportamento aggiungendo `--check-md5=NoCheck` o `--check-md5=LogOnly` al comando di copia.

Per documenti di riferimento dettagliati, consultate [copia azcopy](storage-ref-azcopy-copy.md).

> [!TIP]
> Gli esempi in questa sezione racchiudono gli argomenti di percorso tra virgolette singole (''). Utilizzare le virgolette singole in tutte le shell dei comandi, ad eccezione della shell dei comandi di Windows (cmd.exe). Se si utilizza una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti di percorso tra virgolette doppie ("") anziché virgolette singole ('').

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

In questo esempio viene `C:\myDirectory\myBlobDirectory` visualizzata una directory denominata contenente tutti i file scaricati.

### <a name="download-the-contents-of-a-directory"></a>Scaricare il contenuto di una directory

È possibile scaricare il contenuto di una directory senza copiare la directory che lo contiene utilizzando il simbolo di carattere jolly (-).

> [!NOTE]
> Attualmente, questo scenario è supportato solo per gli account che non dispongono di uno spazio dei nomi gerarchico.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **Esempio** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

> [!NOTE]
> Aggiungere `--recursive` il flag per scaricare i file in tutte le sottodirectory.

### <a name="download-specific-files"></a>Scaricare file specifici

È possibile specificare nomi di file completi o utilizzare nomi parziali con caratteri jolly (-) .

#### <a name="specify-multiple-complete-file-names"></a>Specificare più nomi di file completi

Utilizzare il comando [azcopy copy](storage-ref-azcopy-copy.md) copy con l'opzione. `--include-path` Separare i singoli nomi di`;`file utilizzando una semicolina ( ).

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Esempio** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

In questo esempio AzCopy `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` trasferisce `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` la directory e il file. È necessario includere `--recursive` l'opzione per `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` trasferire tutti i file nella directory.

È inoltre possibile escludere `--exclude-path` i file utilizzando l'opzione . Per altre informazioni, vedere azcopy copy reference docs(documento di riferimento sulla [copia di atrattari).](storage-ref-azcopy-copy.md)

#### <a name="use-wildcard-characters"></a>Utilizzare caratteri jolly

Utilizzare il comando [azcopy copy](storage-ref-azcopy-copy.md) copy con l'opzione. `--include-pattern` Specificare nomi parziali che includono i caratteri jolly. Separare i nomi utilizzando`;`una semicolina ( ).

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Esempio** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

È inoltre possibile escludere `--exclude-pattern` i file utilizzando l'opzione . Per altre informazioni, vedere azcopy copy reference docs(documento di riferimento sulla [copia di atrattari).](storage-ref-azcopy-copy.md)

Le `--include-pattern` `--exclude-pattern` opzioni e si applicano solo ai nomi di file e non al percorso.  Se si desidera copiare tutti i file di testo `–recursive` presenti in una struttura di directory, `–include-pattern` utilizzare `*.txt` l'opzione per ottenere l'intera struttura di directory, quindi utilizzare e specificare per ottenere tutti i file di testo.

## <a name="copy-blobs-between-storage-accounts"></a>Copiare i BLOB tra account di archiviazione

È possibile usare AzCopy per copiare BLOB in altri account di archiviazione. L'operazione di copia è sincrona, quindi quando il comando viene restituito, che indica che tutti i file sono stati copiati. 

AzCopy utilizza [le API](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)da [server a server,](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) pertanto i dati vengono copiati direttamente tra i server di archiviazione. Queste operazioni di copia non utilizzano la larghezza di banda di rete del computer. È possibile aumentare la velocità effettiva di `AZCOPY_CONCURRENCY_VALUE` queste operazioni impostando il valore della variabile di ambiente. Per altre informazioni, vedere [Ottimizzare la velocità effettiva](storage-use-azcopy-configure.md#optimize-throughput).

> [!NOTE]
> Questo scenario presenta le limitazioni seguenti nella versione corrente.
>
> - È necessario aggiungere un token di firma di accesso condiviso a ogni URL di origine. Se si forniscono le credenziali di autorizzazione usando Azure Active Directory (AD), è possibile omettere il token di firma di accesso condiviso solo dall'URL di destinazione.
>-  Gli account di archiviazione BLOB con blocchi Premium non supportano i livelli di accesso. Omettere il livello di accesso di un BLOB `s2s-preserve-access-tier` `false` dall'operazione `--s2s-preserve-access-tier=false`di copia impostando su (ad esempio: ).

Questa sezione contiene gli esempi seguenti:

> [!div class="checklist"]
> * Copiare un BLOB in un altro account di archiviazioneCopy a blob to another storage account
> * Copiare una directory in un altro account di archiviazioneCopy a directory to another storage account
> * Copiare un contenitore in un altro account di archiviazioneCopy a container to another storage account
> * Copiare tutti i contenitori, le directory e i file in un altro account di archiviazioneCopy all containers, directories, and files to another storage account

Per documenti di riferimento dettagliati, consultate [copia azcopy](storage-ref-azcopy-copy.md).

> [!TIP]
> Gli esempi in questa sezione racchiudono gli argomenti di percorso tra virgolette singole (''). Utilizzare le virgolette singole in tutte le shell dei comandi, ad eccezione della shell dei comandi di Windows (cmd.exe). Se si utilizza una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti di percorso tra virgolette doppie ("") anziché virgolette singole ('').

 Questi esempi funzionano anche con gli account che hanno uno spazio dei nomi gerarchico. [L'accesso multiprotocollo su Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) consente`blob.core.windows.net`di utilizzare la stessa sintassi URL ( ) in tali account. 

### <a name="copy-a-blob-to-another-storage-account"></a>Copiare un BLOB in un altro account di archiviazioneCopy a blob to another storage account

Utilizzare la stessa`blob.core.windows.net`sintassi URL ( ) per gli account con uno spazio dei nomi gerarchico.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **Esempio** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Copiare una directory in un altro account di archiviazioneCopy a directory to another storage account

Utilizzare la stessa`blob.core.windows.net`sintassi URL ( ) per gli account con uno spazio dei nomi gerarchico.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Esempio** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Esempio** (spazio dei nomi gerarchico)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-a-container-to-another-storage-account"></a>Copiare un contenitore in un altro account di archiviazioneCopy a container to another storage account

Utilizzare la stessa`blob.core.windows.net`sintassi URL ( ) per gli account con uno spazio dei nomi gerarchico.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Esempio** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Esempio** (spazio dei nomi gerarchico)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>Copiare tutti i contenitori, le directory e i BLOB in un altro account di archiviazioneCopy all containers, directories, and blobs to another storage account

Utilizzare la stessa`blob.core.windows.net`sintassi URL ( ) per gli account con uno spazio dei nomi gerarchico.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **Esempio** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |
| **Esempio** (spazio dei nomi gerarchico)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

## <a name="synchronize-files"></a>Sincronizzare i file

È possibile sincronizzare il contenuto di un file system locale con un contenitore BLOB. È inoltre possibile sincronizzare contenitori e directory virtuali tra loro. La sincronizzazione è unidirezionale. In altre parole, si sceglie quale di questi due endpoint è l'origine e quale è la destinazione. La sincronizzazione utilizza anche le API da server a server. Gli esempi presentati in questa sezione funzionano anche con gli account con uno spazio dei nomi gerarchico. 

> [!NOTE]
> La versione corrente di AzCopy non viene sincronizzata tra altre origini e destinazioni (ad esempio: archiviazione file o bucket Amazon Web Services (AWS) S3).

Il comando [sync](storage-ref-azcopy-sync.md) confronta i nomi dei file e i timestamp dell'ultima modifica. Impostare `--delete-destination` il flag facoltativo `prompt` su un valore di o per eliminare i file nella directory di `true` destinazione se tali file non esistono più nella directory di origine.

Se si `--delete-destination` imposta `true` il flag su AzCopy elimina i file senza fornire una richiesta. Se desiderate che venga visualizzata una richiesta prima `--delete-destination` che `prompt`AzCopy elimini un file, impostate il flag su .

> [!NOTE]
> Per evitare eliminazioni accidentali, assicurarsi di abilitare la funzionalità di [eliminazione temporanea](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) prima di utilizzare il `--delete-destination=prompt|true` flag.

Per documenti di riferimento dettagliati, consultate [sincronizzazione azcopy](storage-ref-azcopy-sync.md).

> [!TIP]
> Gli esempi in questa sezione racchiudono gli argomenti di percorso tra virgolette singole (''). Utilizzare le virgolette singole in tutte le shell dei comandi, ad eccezione della shell dei comandi di Windows (cmd.exe). Se si utilizza una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti di percorso tra virgolette doppie ("") anziché virgolette singole ('').

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Aggiornare un contenitore con le modifiche apportate a un file system localeUpdate a container with changes to a local file system

In questo caso, il contenitore è la destinazione e il file system locale è l'origine. 

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Esempio** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Aggiornare un file system locale con le modifiche apportate a un contenitoreUpdate a local file system with changes to a container

In questo caso, il file system locale è la destinazione e il contenitore è l'origine.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **Esempio** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

### <a name="update-a-container-with-changes-in-another-container"></a>Aggiornare un contenitore con le modifiche in un altro contenitoreUpdate a container with changes in another container

Il primo contenitore visualizzato in questo comando è l'origine. La seconda è la destinazione.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Esempio** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Aggiornare una directory con le modifiche apportate a una directory in un'altra condivisione file

La prima directory visualizzata in questo comando è l'origine. La seconda è la destinazione.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **Esempio** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="next-steps"></a>Passaggi successivi

Trova altri esempi in uno di questi articoli:

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)

- [Eseguire la migrazione di dati locali in una risorsa di archiviazione cloud tramite AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)

- [Trasferire dati con AzCopy e bucket Amazon S3](storage-use-azcopy-s3.md)

- [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)
