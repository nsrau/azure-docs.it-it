---
title: Trasferire dati da e verso l'archiviazione BLOB di Azure tramite AzCopy V10 | Microsoft Docs
description: Questo articolo contiene una raccolta di comandi di esempio AzCopy che semplificano la creazione di contenitori, la copia di file e la sincronizzazione di directory tra i file System e i contenitori locali.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: a95e643d6561f425484a21b5032b1df585d0460b
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900335"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Trasferire i dati con AzCopy e l'archiviazione BLOB

AzCopy è un'utilità da riga di comando che è possibile usare per copiare i dati in, da o tra account di archiviazione. Questo articolo contiene i comandi di esempio che funzionano con l'archivio BLOB.

## <a name="get-started"></a>Attività iniziali

Vedere l'articolo [Introduzione a AzCopy](storage-use-azcopy-v10.md) per scaricare AzCopy e informazioni sui modi in cui è possibile fornire le credenziali di autorizzazione al servizio di archiviazione.

> [!NOTE]
> Gli esempi in questo articolo presuppongono che l'identità sia stata autenticata tramite `AzCopy login` il comando. AzCopy usa quindi l'account Azure AD per autorizzare l'accesso ai dati nell'archivio BLOB.
>
> Se si preferisce usare un token di firma di accesso condiviso per autorizzare l'accesso ai dati BLOB, è possibile aggiungere tale token all'URL della risorsa in ogni comando AzCopy.
>
> Ad esempio: `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>"`.

## <a name="create-a-container"></a>Creare un contenitore

È possibile usare il comando `make` AzCopy per creare un contenitore. Negli esempi di questa sezione viene creato un contenitore `mycontainer`denominato.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy make "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>` |
| **Esempio** | `azcopy make "https://mystorageaccount.blob.core.windows.net/mycontainer"` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy make "https://mystorageaccount.dfs.core.windows.net/mycontainer"` |

## <a name="upload-files"></a>Caricare file

È possibile usare il comando `copy` AzCopy per caricare file e directory dal computer locale.

Questa sezione contiene gli esempi seguenti:

> [!div class="checklist"]
> * Carica un file
> * Caricare una directory
> * Caricare file usando caratteri jolly

> [!NOTE]
> AzCopy non calcola e archivia automaticamente il codice hash MD5 del file. Se si vuole eseguire questa operazione, aggiungere il `--put-md5` flag a ogni comando di copia di AzCopy. In questo modo, quando viene scaricato il BLOB, AzCopy calcola un hash MD5 per i dati scaricati e verifica che l'hash MD5 archiviato nella `Content-md5` proprietà del BLOB corrisponda all'hash calcolato.

### <a name="upload-a-file"></a>Carica un file

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy "<local-file-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>"` |
| **Esempio** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt"` |

> [!NOTE]
> Per impostazione predefinita, AzCopy carica i dati in BLOB in blocchi. Per caricare i file come BLOB di accodamento o BLOB di pagine `--blob-type=[BlockBlob|PageBlob|AppendBlob]`, usare il flag.

### <a name="upload-a-directory"></a>Caricare una directory

Questo esempio copia una directory (e tutti i file in tale directory) in un contenitore BLOB. Il risultato è una directory nel contenitore con lo stesso nome.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Esempio** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer" --recursive` |

Per eseguire la copia in una directory all'interno del contenitore, è sufficiente specificare il nome della directory nella stringa di comando.

|    |     |
|--------|-----------|
| **Esempio** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory" --recursive` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory" --recursive` |

Se si specifica il nome di una directory che non esiste nel contenitore, AzCopy crea una nuova directory con tale nome.

### <a name="upload-the-contents-of-a-directory"></a>Caricare il contenuto di una directory

È possibile caricare il contenuto di una directory senza copiare la directory che lo contiene usando il carattere jolly (*).

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy "<local-directory-path>\*" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>` |
| **Esempio** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory"` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory"` |

> [!NOTE]
> Aggiungere il `--recursive` flag per caricare i file in tutte le sottodirectory.

## <a name="download-files"></a>Download dei file

È possibile usare il comando `copy` AzCopy per scaricare i BLOB, le directory e i contenitori nel computer locale.

Questa sezione contiene gli esempi seguenti:

> [!div class="checklist"]
> * Scarica file
> * Scaricare una directory
> * Scaricare i file utilizzando caratteri jolly

> [!NOTE]
> Se il `Content-md5` valore della proprietà di un BLOB contiene un hash, AzCopy calcola un hash MD5 per i dati scaricati e verifica che l'hash MD5 archiviato nella `Content-md5` proprietà del BLOB corrisponda all'hash calcolato. Se questi valori non corrispondono, il download ha esito negativo a meno che non si esegua `--check-md5=LogOnly` l'override di questo comportamento accodando `--check-md5=NoCheck` o al comando copy.

### <a name="download-a-file"></a>Scarica file

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>" "<local-file-path>"` |
| **Esempio** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>Scaricare una directory

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>" "<local-directory-path>" --recursive` |
| **Esempio** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |

Questo esempio genera una directory denominata `C:\myDirectory\myBlobDirectory` che contiene tutti i file scaricati.

### <a name="download-the-contents-of-a-directory"></a>Scarica il contenuto di una directory

È possibile scaricare il contenuto di una directory senza copiare la directory che lo contiene usando il carattere jolly (*).

> [!NOTE]
> Attualmente, questo scenario è supportato solo per gli account che non dispongono di uno spazio dei nomi gerarchico.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"` |
| **Esempio** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*" "C:\myDirectory"` |

> [!NOTE]
> Aggiungere il `--recursive` flag per scaricare i file in tutte le sottodirectory.

## <a name="copy-blobs-between-storage-accounts"></a>Copiare i BLOB tra gli account di archiviazione

È possibile usare AzCopy per copiare i BLOB in altri account di archiviazione. L'operazione di copia è sincrona, quindi quando il comando restituisce, che indica che tutti i file sono stati copiati.

AzCopy usa le [API](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url) [da server a server](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) , quindi i dati vengono copiati direttamente tra i server di archiviazione. Queste operazioni di copia non utilizzano la larghezza di banda di rete del computer. È possibile aumentare la velocità effettiva di queste operazioni impostando il valore della `AZCOPY_CONCURRENCY_VALUE` variabile di ambiente. Per altre informazioni, vedere [ottimizzare la velocità effettiva](storage-use-azcopy-configure.md#optimize-throughput).

> [!NOTE]
> Questo scenario presenta le limitazioni seguenti nella versione corrente.
>
> - Sono supportati solo gli account che non dispongono di uno spazio dei nomi gerarchico.
> - È necessario aggiungere un token SAS a ogni URL di origine. Se si forniscono le credenziali di autorizzazione usando Azure Active Directory (AD), è possibile omettere il token SAS solo dall'URL di destinazione.
>-  Gli account di archiviazione BLOB in blocchi Premium non supportano i livelli di accesso. Omettere il livello di accesso di un BLOB dall'operazione di `s2s-preserve-access-tier` copia impostando su `false` (ad esempio `--s2s-preserve-access-tier=false`:).

Questa sezione contiene gli esempi seguenti:

> [!div class="checklist"]
> * Copiare un BLOB in un altro account di archiviazione
> * Copiare una directory in un altro account di archiviazione
> * Copiare un contenitore in un altro account di archiviazione
> * Copia tutti i contenitori, le directory e i file in un altro account di archiviazione

### <a name="copy-a-blob-to-another-storage-account"></a>Copiare un BLOB in un altro account di archiviazione

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>"` |
| **Esempio** | `azcopy copy "https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |

### <a name="copy-a-directory-to-another-storage-account"></a>Copiare una directory in un altro account di archiviazione

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **Esempio** | `azcopy copy "https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-a-containers-to-another-storage-account"></a>Copiare un contenitore in un altro account di archiviazione

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy "https://<source-storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **Esempio** | `azcopy copy "https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>Copiare tutti i contenitori, le directory e i BLOB in un altro account di archiviazione

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy "https://<source-storage-account-name>.blob.core.windows.net/?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/" --recursive"` |
| **Esempio** | `azcopy copy "https://mysourceaccount.blob.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net" --recursive` |

## <a name="synchronize-files"></a>Sincronizzare i file

È possibile sincronizzare il contenuto di un file system locale con un contenitore BLOB. La sincronizzazione è unidirezionale. In altre parole, è possibile scegliere quali di questi due endpoint sono l'origine e quella di destinazione. La sincronizzazione usa anche API da server a server.

> [!NOTE]
> Attualmente, questo scenario è supportato solo per gli account che non dispongono di uno spazio dei nomi gerarchico. La versione corrente di AzCopy non viene sincronizzata tra altre origini e destinazioni, ad esempio: Bucket di archiviazione file o Amazon Web Services (AWS) S3).

Il `sync` comando Confronta i nomi di file e i timestamp dell'Ultima modifica. Impostare il `--delete-destination` flag facoltativo sul `true` valore o `prompt` per eliminare i file nella directory di destinazione se tali file non sono più presenti nella directory di origine.

Se si imposta il `--delete-destination` flag su `true` AzCopy Elimina i file senza fornire un messaggio di richiesta. Se si desidera che venga visualizzata una richiesta prima che AzCopy elimini un file `--delete-destination` , impostare `prompt`il flag su.

> [!NOTE]
> Per evitare eliminazioni accidentali, assicurarsi di abilitare la funzionalità di [eliminazione](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) temporanea prima di usare `--delete-destination=prompt|true` il flag.

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Aggiornare un contenitore con modifiche a una file system locale

In questo caso, il contenitore è la destinazione e la file system locale è l'origine.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy sync "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Esempio** | `azcopy sync "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Aggiornare un file system locale con le modifiche apportate a un contenitore

In questo caso, il file system locale è la destinazione e il contenitore è l'origine.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy sync "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" "C:\myDirectory" --recursive` |
| **Esempio** | `azcopy sync "https://mystorageaccount.blob.core.windows.net/mycontainer" "C:\myDirectory" --recursive` |
|

## <a name="next-steps"></a>Passaggi successivi

Altri esempi sono disponibili in uno di questi articoli:

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)

- [Esercitazione: Eseguire la migrazione di dati locali nell'archiviazione cloud usando AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)

- [Trasferire dati con AzCopy e bucket Amazon S3](storage-use-azcopy-s3.md)

- [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)
