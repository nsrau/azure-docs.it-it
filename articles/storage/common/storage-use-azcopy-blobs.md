---
title: Trasferire i dati da o verso archiviazione Blob di Azure tramite AzCopy v10 | Microsoft Docs
description: Questo articolo contiene una raccolta di AzCopy esempio i comandi che consentono di creano contenitori, copiano i file e sincronizzano le cartelle tra i file System locali e i contenitori.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 98e33f838ee9b6f506bf1dc01e1dd61ad587aa05
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299398"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Trasferire dati con AzCopy e l'archiviazione Blob

AzCopy è un'utilità della riga di comando che è possibile usare per copiare i dati a, da o tra account di archiviazione. Questo articolo contiene esempi di comandi che funzionano con archivio Blob.

## <a name="get-started"></a>Attività iniziali

Vedere le [Introduzione ad AzCopy](storage-use-azcopy-v10.md) articolo per il download di AzCopy e informazioni sui metodi che è possibile fornire le credenziali di autorizzazione per il servizio di archiviazione.

> [!NOTE]
> Gli esempi in questo articolo presuppongono che l'utente è autenticato la tua identità usando il `AzCopy login` comando. AzCopy Usa quindi l'account Azure AD per autorizzare l'accesso ai dati nell'archiviazione Blob.
>
> Se si preferisce usare un token di firma di accesso condiviso per autorizzare l'accesso ai dati blob, è possibile aggiungere tale token per l'URL della risorsa in ogni comando di AzCopy.
>
> Ad esempio: `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>"`.

## <a name="create-a-container"></a>Creare un contenitore

È possibile usare AzCopy `make` comando per creare un contenitore. Gli esempi in questa sezione è creare un contenitore denominato `mycontainer`.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy make "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>` |
| **Esempio** | `azcopy make "https://mystorageaccount.blob.core.windows.net/mycontainer"` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy make "https://mystorageaccount.dfs.core.windows.net/mycontainer"` |

## <a name="upload-files"></a>Caricare file

È possibile usare AzCopy `copy` comando per caricare i file e cartelle dal computer locale.

Questa sezione contiene gli esempi seguenti:

> [!div class="checklist"]
> * Caricare un file
> * Caricare una cartella
> * Caricare file usando caratteri jolly

> [!NOTE]
> AzCopy non automaticamente calcolare e archiviare il codice hash md5 del file. Se si desidera che AzCopy a tale scopo, quindi aggiungere il `--put-md5` flag per ogni comando di copia. In questo modo, quando viene scaricato il blob, AzCopy viene calcolato un hash MD5 per i dati scaricati e verifica che l'hash MD5 archiviato nell'oggetto blob `Content-md5` proprietà corrisponda all'hash calcolato.

### <a name="upload-a-file"></a>Caricare un file

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>"` |
| **Esempio** | `azcopy copy "C:\myFolder\myTextFile.txt" "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy "C:\myFolder\myTextFile.txt" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt"` |

> [!NOTE]
> Per impostazione predefinita, AzCopy carica i dati in BLOB in blocchi. Per caricare i file come BLOB di accodamento o BLOB di pagine, usare il flag `--blob-type=[BlockBlob|PageBlob|AppendBlob]`.

### <a name="upload-a-folder"></a>Caricare una cartella

Questo esempio copia una cartella (e tutti i file in essa contenute) a un contenitore blob. Il risultato è una cartella nel contenitore con lo stesso nome.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Esempio** | `azcopy copy "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy "C:\myFolder" "https://mystorageaccount.dfs.core.windows.net/mycontainer" --recursive` |

Per copiare in una cartella all'interno del contenitore, è sufficiente specificare il nome della cartella nella stringa di comando.

|    |     |
|--------|-----------|
| **Esempio** | `azcopy copy "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder" --recursive` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy "C:\myFolder" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobFolder" --recursive` |

Se si specifica il nome di una cartella che non esiste nel contenitore, AzCopy crea una nuova cartella con lo stesso nome.

### <a name="upload-the-contents-of-a-folder"></a>Caricare il contenuto di una cartella

È possibile caricare il contenuto di una cartella senza copiare nella cartella del contenitore usando il carattere jolly asterisco (*).

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy "<local-folder-path>\*" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<folder-path>` |
| **Esempio** | `azcopy copy "C:\myFolder\*" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder"` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy "C:\myFolder\*" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobFolder"` |

> [!NOTE]
> Aggiungere il `--recursive` flag per caricare i file in tutte le sottocartelle.

## <a name="download-files"></a>Download dei file

È possibile usare AzCopy `copy` comando per scaricare i BLOB, cartelle e i contenitori nel computer locale.

Questa sezione contiene gli esempi seguenti:

> [!div class="checklist"]
> * Scaricare un file
> * Scaricare una cartella
> * Scaricare i file utilizzando caratteri jolly

> [!NOTE]
> Se il `Content-md5` valore della proprietà di un blob contiene un hash, AzCopy viene calcolato un hash MD5 per i dati scaricati e verifica che l'hash MD5 archiviato nell'oggetto blob `Content-md5` proprietà corrisponda all'hash calcolato. Se questi valori non corrispondono, il download ha esito negativo a meno che non si esegue l'override di questo comportamento aggiungendo `--check-md5=NoCheck` o `--check-md5=LogOnly` comando per la copia.

### <a name="download-a-file"></a>Scaricare un file

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>" "<local-file-path>"` |
| **Esempio** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "C:\myFolder\myTextFile.txt"` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt" "C:\myFolder\myTextFile.txt"` |

### <a name="download-a-folder"></a>Scaricare una cartella

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<folder-path>" "<local-folder-path>" --recursive` |
| **Esempio** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder "C:\myFolder"  --recursive` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobFolder "C:\myFolder"  --recursive` |

Questo esempio restituisce in una cartella denominata `C:\myFolder\myBlobFolder` che contiene tutti i file scaricati.

### <a name="download-the-contents-of-a-folder"></a>Scaricare il contenuto di una cartella

È possibile scaricare il contenuto di una cartella senza copiare nella cartella del contenitore usando il carattere jolly asterisco (*).

> [!NOTE]
> Attualmente, questo scenario è supportato solo per gli account che non hanno uno spazio dei nomi gerarchico.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-folder-path>/"` |
| **Esempio** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder/*" "C:\myFolder"` |

> [!NOTE]
> Aggiungere il `--recursive` flag per scaricare i file in tutte le sottocartelle.

## <a name="copy-blobs-between-storage-accounts"></a>Copiare i BLOB tra account di archiviazione

È possibile usare AzCopy per copiare i BLOB negli altri account di archiviazione. L'operazione di copia è sincrono quando termina, il comando che indica che tutti i file siano stati copiati.

> [!NOTE]
> Attualmente, questo scenario è supportato solo per gli account che non hanno uno spazio dei nomi gerarchico.

AzCopy Usa il [Put Block From URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API, in modo che i dati vengono copiati direttamente tra i server di archiviazione. Queste operazioni di copia non usano la larghezza di banda di rete del computer in uso.

Questa sezione contiene gli esempi seguenti:

> [!div class="checklist"]
> * Copiare un blob in un altro account di archiviazione
> * Copia una cartella a un altro account di archiviazione
> * Copiare un contenitori a un altro account di archiviazione
> * Copiare tutti i contenitori, cartelle e file in un altro account di archiviazione

### <a name="copy-a-blob-to-another-storage-account"></a>Copiare un blob in un altro account di archiviazione

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>"` |
| **Esempio** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |

### <a name="copy-a-folder-to-another-storage-account"></a>Copia una cartella a un altro account di archiviazione

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<folder-path>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<folder-path>" --recursive` |
| **Esempio** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobFolder" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myBlobFolder" --recursive` |

### <a name="copy-a-containers-to-another-storage-account"></a>Copiare un contenitori a un altro account di archiviazione

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **Esempio** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-all-containers-folders-and-files-to-another-storage-account"></a>Copiare tutti i contenitori, cartelle e file in un altro account di archiviazione

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/" "https://<destination-storage-account-name>.blob.core.windows.net/" --recursive"` |
| **Esempio** | `azcopy cp "https://mysourceaccount.blob.core.windows.net" "https://mydestinationaccount.blob.core.windows.net" --recursive` |

## <a name="synchronize-files"></a>Sincronizzare i file

È possibile sincronizzare il contenuto di un file system locale in un contenitore blob. È inoltre possibile sincronizzare un contenitore blob in un file system locale nel computer. La sincronizzazione è unidirezionale. In altre parole, decidere quali di questi due endpoint è l'origine e quello che rappresenta la destinazione.

> [!NOTE]
> La versione corrente di AzCopy non sincronizza tra le altre origini e destinazioni (ad esempio: Archiviazione di file o il bucket S3 di Amazon Web Services (AWS)).

Il `sync` comando consente di confrontare i nomi di file e i timestamp dell'ultima modifica. Impostare il `--delete-destination` flag facoltativo su un valore di `true` o `prompt` per eliminare i file nella cartella di destinazione se tali file non esistono più nella cartella di origine.

Se si impostano i `--delete-destination` flag per `true` AzCopy Elimina i file senza fornire un prompt dei comandi. Se si desidera un prompt dei comandi precedere AzCopy Elimina un file, impostare il `--delete-destination` flag `prompt`.

> [!NOTE]
> Per impedire eliminazioni accidentali, assicurarsi di abilitare la [eliminazione temporanea](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) funzionalità prima di usare il `--delete-destination=prompt|true` flag.

### <a name="synchronize-a-container-to-a-local-file-system"></a>Sincronizzare un contenitore per un file system locale

In questo caso, il file system locale diventa l'origine e il contenitore è la destinazione.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy sync "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Esempio** | `azcopy sync "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy sync "C:\myFolder" "https://<storage-account-name>.dfs.core.windows.net/mycontainer" --recursive` |


### <a name="synchronize-a-local-file-system-to-a-container"></a>Sincronizzare un file system locale a un contenitore

In questo caso, il contenitore diventa l'origine e nel file system locale rappresenta la destinazione.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy sync "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" "C:\myFolder" --recursive` |
| **Esempio** | `azcopy sync "https://mystorageaccount.blob.core.windows.net/mycontainer" "C:\myFolder" --recursive` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy sync "https://mystorageaccount.dfs.core.windows.net/mycontainer" "C:\myFolder" --recursive` |

## <a name="next-steps"></a>Passaggi successivi

Trovare altri esempi in questi articoli:

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)

- [Esercitazione: La migrazione dei dati in locale al cloud di archiviazione tramite AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Trasferire dati con AzCopy e archiviazione file](storage-use-azcopy-files.md)

- [Trasferire dati con AzCopy e Amazon S3 bucket](storage-use-azcopy-s3.md)

- [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)