---
title: Trasferire dati da e verso File di Azure usando AzCopy V10 | Microsoft Docs
description: Trasferire i dati con AzCopy e archiviazione file. AzCopy è uno strumento da riga di comando per la copia di BLOB o file in o da un account di archiviazione. Usare AzCopy con File di Azure.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 793f3869a9534c71d860cc8dea7a1995f5ee278d
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88871242"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Trasferire dati con AzCopy e l'archivio file 

AzCopy è un'utilità della riga di comando che è possibile usare per copiare i BLOB o i file da e verso un account di archiviazione. Questo articolo contiene comandi di esempio che funzionano con File di Azure.

Prima di iniziare, vedere l'articolo [Introduzione a AzCopy](storage-use-azcopy-v10.md) per scaricare AzCopy e acquisire familiarità con lo strumento.

> [!TIP]
> Gli esempi in questo articolo racchiudono gli argomenti del percorso con virgolette singole (''). Usare le virgolette singole in tutte le shell dei comandi eccetto la shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti del percorso con virgolette doppie ("") anziché virgolette singole ('').

## <a name="create-file-shares"></a>Creare condivisioni file

È possibile usare il comando [azcopy make](storage-ref-azcopy-make.md) per creare una condivisione file. Nell'esempio riportato in questa sezione viene creata una condivisione file denominata `myfileshare` .

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>'` |
| **Esempio** | `azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

Per informazioni dettagliate sulla documentazione di riferimento, vedere [azcopy make](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Caricare file

È possibile usare il comando [copy di azcopy](storage-ref-azcopy-copy.md) per caricare file e directory dal computer locale.

Questa sezione contiene gli esempi seguenti:

> [!div class="checklist"]
> * Caricare un file
> * Caricare una directory
> * Caricare il contenuto di una directory
> * Carica un file specifico

> [!TIP]
> È possibile modificare l'operazione di caricamento usando i flag facoltativi. Ecco alcuni esempi.
>
> |Scenario|Flag|
> |---|---|
> |Copiare gli elenchi di controllo di accesso (ACL) insieme ai file.|**--Preserve-SMB-autorizzazioni** = \[ true \| false\]|
> |Copiare le informazioni sulle proprietà SMB insieme ai file.|**--Preserve-SMB-info** = \[ true \| false\]|
> |Caricare i file come BLOB di accodamento o BLOB di pagine.|**--BLOB-type** = \[ BlockBlob \| PageBlob \| AppendBlob\]|
> |Effettuare il caricamento in un livello di accesso specifico, ad esempio il livello archivio.|**--Block-BLOB-Tier** = \[ Nessun \| \| Archivio Hot Cool \|\]|
> 
> Per un elenco completo, vedere [Opzioni](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> AzCopy non calcola e archivia automaticamente il codice hash MD5 del file. Se si vuole eseguire questa operazione, aggiungere il `--put-md5` flag a ogni comando di copia di AzCopy. In questo modo, quando il file viene scaricato, AzCopy calcola un hash MD5 per i dati scaricati e verifica che l'hash MD5 archiviato nella proprietà del file `Content-md5` corrisponda all'hash calcolato.

### <a name="upload-a-file"></a>Caricare un file

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name><SAS-token>'` |
| **Esempio** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

È anche possibile caricare un file usando un carattere jolly (*) in qualsiasi punto del percorso o del nome file. Ad esempio: `'C:\myDirectory\*.txt'` o `C:\my*\*.txt` .

### <a name="upload-a-directory"></a>Caricare una directory

Questo esempio copia una directory (e tutti i file in tale directory) in una condivisione file. Il risultato è una directory nella condivisione file con lo stesso nome.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Esempio** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Per eseguire la copia in una directory all'interno della condivisione file, è sufficiente specificare il nome della directory nella stringa di comando.

|    |     |
|--------|-----------|
| **Esempio** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Se si specifica il nome di una directory che non esiste nella condivisione file, AzCopy crea una nuova directory con tale nome.

### <a name="upload-the-contents-of-a-directory"></a>Caricare il contenuto di una directory

È possibile caricare il contenuto di una directory senza copiare la directory che lo contiene usando il carattere jolly (*).

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>` |
| **Esempio** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Aggiungere il `--recursive` flag per caricare i file in tutte le sottodirectory.

### <a name="upload-specific-files"></a>Carica file specifici

È possibile caricare file specifici usando nomi di file completi, nomi parziali con caratteri jolly (*) o con date e ore.

#### <a name="specify-multiple-complete-file-names"></a>Specificare più nomi di file completi

Usare il comando [azcopy Copy](storage-ref-azcopy-copy.md) con l' `--include-path` opzione. Separare i singoli nomi di file usando un punto e virgola ( `;` ).

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-path <semicolon-separated-file-list>` |
| **Esempio** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'` |

In questo esempio, AzCopy trasferisce la `C:\myDirectory\photos` Directory e il `C:\myDirectory\documents\myFile.txt` file. È necessario includere l' `--recursive` opzione per trasferire tutti i file nella `C:\myDirectory\photos` Directory.

È anche possibile escludere i file utilizzando l' `--exclude-path` opzione. Per altre informazioni, vedere la documentazione di riferimento per la [copia di azcopy](storage-ref-azcopy-copy.md) .

#### <a name="use-wildcard-characters"></a>Usa caratteri jolly

Usare il comando [azcopy Copy](storage-ref-azcopy-copy.md) con l' `--include-pattern` opzione. Specificare i nomi parziali che includono i caratteri jolly. Separare i nomi con un punto e virgola ( `;` ).

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Esempio** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'` |

È anche possibile escludere i file utilizzando l' `--exclude-pattern` opzione. Per altre informazioni, vedere la documentazione di riferimento per la [copia di azcopy](storage-ref-azcopy-copy.md) .

Le `--include-pattern` `--exclude-pattern` Opzioni e si applicano solo ai nomi file e non al percorso.  Se si desidera copiare tutti i file di testo presenti in un albero di directory, utilizzare l' `–recursive` opzione per ottenere l'intero albero di directory e quindi utilizzare `–include-pattern` e specificare `*.txt` per ottenere tutti i file di testo.

#### <a name="upload-files-that-were-modified-after-a-date-and-time"></a>Caricare i file che sono stati modificati dopo una data e un'ora 

Usare il comando [azcopy Copy](storage-ref-azcopy-copy.md) con l' `--include-after` opzione. Specificare una data e un'ora in formato ISO 8601 (ad esempio: `2020-08-19T15:04:00Z` ). 

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **Esempio** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-after '2020-08-19T15:04:00Z'` |

Per informazioni di riferimento dettagliate, vedere la documentazione di riferimento per la [copia di azcopy](storage-ref-azcopy-copy.md) .

## <a name="download-files"></a>Scaricare i file

È possibile usare il comando [copy di azcopy](storage-ref-azcopy-copy.md) per scaricare file, directory e condivisioni file nel computer locale.

Questa sezione contiene gli esempi seguenti:

> [!div class="checklist"]
> * Scaricare un file
> * Scaricare una directory
> * Scarica il contenuto di una directory
> * Scarica file specifici

> [!TIP]
> È possibile modificare l'operazione di download usando i flag facoltativi. Ecco alcuni esempi.
>
> |Scenario|Flag|
> |---|---|
> |Copiare gli elenchi di controllo di accesso (ACL) insieme ai file.|**--Preserve-SMB-autorizzazioni** = \[ true \| false\]|
> |Copiare le informazioni sulle proprietà SMB insieme ai file.|**--Preserve-SMB-info** = \[ true \| false\]|
> |Decomprime automaticamente i file.|**--Decomprimi**|
> 
> Per un elenco completo, vedere [Opzioni](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> Se il `Content-md5` valore della proprietà di un file contiene un hash, AzCopy calcola un hash MD5 per i dati scaricati e verifica che l'hash MD5 archiviato nella proprietà del file `Content-md5` corrisponda all'hash calcolato. Se questi valori non corrispondono, il download ha esito negativo a meno che non si esegua l'override di questo comportamento accodando `--check-md5=NoCheck` o `--check-md5=LogOnly` al comando copy.

### <a name="download-a-file"></a>Scaricare un file

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' '<local-file-path>'` |
| **Esempio** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Scaricare una directory

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' '<local-directory-path>' --recursive` |
| **Esempio** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive` |

Questo esempio genera una directory denominata `C:\myDirectory\myFileShareDirectory` che contiene tutti i file scaricati.

### <a name="download-the-contents-of-a-directory"></a>Scarica il contenuto di una directory

È possibile scaricare il contenuto di una directory senza copiare la directory che lo contiene usando il carattere jolly (*).

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*<SAS-token>' '<local-directory-path>/'` |
| **Esempio** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'` |

> [!NOTE]
> Aggiungere il `--recursive` flag per scaricare i file in tutte le sottodirectory.

### <a name="download-specific-files"></a>Scarica file specifici

È possibile scaricare file specifici usando nomi di file completi, nomi parziali con caratteri jolly (*) o con date e ore.

#### <a name="specify-multiple-complete-file-names"></a>Specificare più nomi di file completi

Usare il comando [azcopy Copy](storage-ref-azcopy-copy.md) con l' `--include-path` opzione. Separare i singoli nomi di file usando un punto e virgola ( `;` ).

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Esempio** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |

In questo esempio, AzCopy trasferisce la `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` Directory e il `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt` file. È necessario includere l' `--recursive` opzione per trasferire tutti i file nella `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` Directory.

È anche possibile escludere i file utilizzando l' `--exclude-path` opzione. Per altre informazioni, vedere la documentazione di riferimento per la [copia di azcopy](storage-ref-azcopy-copy.md) .

#### <a name="use-wildcard-characters"></a>Usa caratteri jolly

Usare il comando [azcopy Copy](storage-ref-azcopy-copy.md) con l' `--include-pattern` opzione. Specificare i nomi parziali che includono i caratteri jolly. Separare i nomi con un punto e virgola ( `;` ).

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name><SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Esempio** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

È anche possibile escludere i file utilizzando l' `--exclude-pattern` opzione. Per altre informazioni, vedere la documentazione di riferimento per la [copia di azcopy](storage-ref-azcopy-copy.md) .

Le `--include-pattern` `--exclude-pattern` Opzioni e si applicano solo ai nomi file e non al percorso.  Se si desidera copiare tutti i file di testo presenti in un albero di directory, utilizzare l' `–recursive` opzione per ottenere l'intero albero di directory e quindi utilizzare `–include-pattern` e specificare `*.txt` per ottenere tutti i file di testo.

#### <a name="download-files-that-were-modified-after-a-date-and-time"></a>Scaricare i file che sono stati modificati dopo una data e un'ora 

Usare il comando [azcopy Copy](storage-ref-azcopy-copy.md) con l' `--include-after` opzione. Specificare una data e un'ora in formato ISO-8601 (ad esempio: `2020-08-19T15:04:00Z` ). 

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>/*<SAS-token>' '<local-directory-path>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **Esempio** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/*?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory' --include-after '2020-08-19T15:04:00Z'` |


Per informazioni di riferimento dettagliate, vedere la documentazione di riferimento per la [copia di azcopy](storage-ref-azcopy-copy.md) .

## <a name="copy-files-between-storage-accounts"></a>Copiare file tra account di archiviazione

È possibile usare AzCopy per copiare i file in altri account di archiviazione. L'operazione di copia è sincrona, quindi se il comando restituisce il risultato ciò indica che tutti i file sono stati copiati.

AzCopy usa le [API](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url) [da server a server](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) , quindi i dati vengono copiati direttamente tra i server di archiviazione. Queste operazioni di copia non utilizzano la larghezza di banda di rete del computer. È possibile aumentare la velocità effettiva di queste operazioni impostando il valore della `AZCOPY_CONCURRENCY_VALUE` variabile di ambiente. Per altre informazioni, vedere [ottimizzare la velocità effettiva](storage-use-azcopy-configure.md#optimize-throughput).

Questa sezione contiene gli esempi seguenti:

> [!div class="checklist"]
> * Copiare un file in un altro account di archiviazione
> * Copiare una directory in un altro account di archiviazione
> * Copiare una condivisione file in un altro account di archiviazione
> * Copia tutte le condivisioni file, le directory e i file in un altro account di archiviazione

> [!TIP]
> È possibile modificare l'operazione di copia usando i flag facoltativi. Ecco alcuni esempi.
>
> |Scenario|Flag|
> |---|---|
> |Copiare gli elenchi di controllo di accesso (ACL) insieme ai file.|**--Preserve-SMB-autorizzazioni** = \[ true \| false\]|
> |Copiare le informazioni sulle proprietà SMB insieme ai file.|**--Preserve-SMB-info** = \[ true \| false\]|
> |Copia i file come BLOB di accodamento o BLOB di pagine.|**--BLOB-type** = \[ BlockBlob \| PageBlob \| AppendBlob\]|
> |Copiare in un livello di accesso specifico, ad esempio il livello archivio.|**--Block-BLOB-Tier** = \[ Nessun \| \| Archivio Hot Cool \|\]|
> 
> Per un elenco completo, vedere [Opzioni](storage-ref-azcopy-copy.md#options).

### <a name="copy-a-file-to-another-storage-account"></a>Copiare un file in un altro account di archiviazione

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>'` |
| **Esempio** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Copiare una directory in un altro account di archiviazione

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Esempio** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-a-file-share-to-another-storage-account"></a>Copiare una condivisione file in un altro account di archiviazione

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Esempio** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>Copia tutte le condivisioni file, le directory e i file in un altro account di archiviazione

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<SAS-token>' --recursive'` |
| **Esempio** | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="synchronize-files"></a>Sincronizzare i file

È possibile sincronizzare il contenuto di una condivisione file con un'altra condivisione file. È anche possibile sincronizzare il contenuto di una directory in una condivisione file con il contenuto di una directory che si trova in un'altra condivisione file. La sincronizzazione è unidirezionale. In altre parole, è possibile scegliere quali di questi due endpoint sono l'origine e quella di destinazione. La sincronizzazione usa anche API da server a server.

> [!NOTE]
> Attualmente, questo scenario è supportato solo per gli account che non dispongono di uno spazio dei nomi gerarchico. La versione corrente di AzCopy non viene sincronizzata tra File di Azure e archiviazione BLOB.

Il comando di [sincronizzazione](storage-ref-azcopy-sync.md) Confronta i nomi di file e i timestamp dell'Ultima modifica. Impostare il `--delete-destination` flag facoltativo sul valore `true` o `prompt` per eliminare i file nella directory di destinazione se tali file non sono più presenti nella directory di origine.

Se si imposta il `--delete-destination` flag su `true` AzCopy Elimina i file senza fornire un messaggio di richiesta. Se si desidera che venga visualizzata una richiesta prima che AzCopy elimini un file, impostare il `--delete-destination` flag su `prompt` .

> [!TIP]
> È possibile modificare l'operazione di sincronizzazione usando i flag facoltativi. Ecco alcuni esempi.
>
> |Scenario|Flag|
> |---|---|
> |Copiare gli elenchi di controllo di accesso (ACL) insieme ai file.|**--Preserve-SMB-autorizzazioni** = \[ true \| false\]|
> |Copiare le informazioni sulle proprietà SMB insieme ai file.|**--Preserve-SMB-info** = \[ true \| false\]|
> |Escludere i file in base a un modello.|**--Exclude-Path**|
> |Specificare il modo in cui si desidera che le voci di log correlate alla sincronizzazione siano disponibili.|**--livello** = \[ di log informazioni sull'errore di avviso \| \| \| nessuno\]|
> 
> Per un elenco completo, vedere [Opzioni](storage-ref-azcopy-sync.md#options).

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>Aggiornare una condivisione file con le modifiche apportate a un'altra condivisione file

La prima condivisione file visualizzata in questo comando è l'origine. Il secondo è la destinazione.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Esempio** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Aggiornare una directory con le modifiche apportate a una directory in un'altra condivisione file

La prima directory visualizzata in questo comando è l'origine. Il secondo è la destinazione.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' --recursive` |
| **Esempio** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-file-share-to-match-the-contents-of-a-share-snapshot"></a>Aggiornare una condivisione file in modo che corrisponda al contenuto di uno snapshot di condivisione

La prima condivisione file visualizzata in questo comando è l'origine. Alla fine dell'URI, aggiungere la stringa `&sharesnapshot=` seguita dal valore **DateTime** dello snapshot. 

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>&sharesnapsot<snapshot-ID>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Esempio** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-03-03T20%3A24%3A13.0000000Z' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

Per altre informazioni sugli snapshot di condivisione, vedere [Panoramica degli snapshot di condivisione per file di Azure](https://docs.microsoft.com/azure/storage/files/storage-snapshots-files).

## <a name="next-steps"></a>Passaggi successivi

Altri esempi sono disponibili in uno di questi articoli:

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)

- [Trasferire dati con AzCopy e l'archivio BLOB](storage-use-azcopy-blobs.md)

- [Trasferire dati con AzCopy e bucket Amazon S3](storage-use-azcopy-s3.md)

- [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)
