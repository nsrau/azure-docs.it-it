---
title: Trasferire dati da o verso i file di Azure usando AzCopy v10 . Documenti Microsoft
description: Trasferire i dati con AzCopy e l'archiviazione dei file.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 59f5733009424c60f2b9c48e68d70bbc29ad7095
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263370"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Trasferire dati con AzCopy e l'archivio file 

AzCopy è un'utilità della riga di comando che è possibile usare per copiare BLOB o file in o da un account di archiviazione. Questo articolo contiene comandi di esempio che funzionano con File di Azure.This article contains example commands that work with Azure Files.

Prima di iniziare, vedere l'articolo Introduzione ad [AzCopy](storage-use-azcopy-v10.md) per scaricare AzCopy e acquisire familiarità con lo strumento.

> [!TIP]
> Gli esempi in questo articolo racchiudono gli argomenti di percorso tra virgolette singole (''). Utilizzare le virgolette singole in tutte le shell dei comandi, ad eccezione della shell dei comandi di Windows (cmd.exe). Se si utilizza una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti di percorso tra virgolette doppie ("") anziché virgolette singole ('').

## <a name="create-file-shares"></a>Creare condivisioni file

È possibile utilizzare il comando [azcopy make](storage-ref-azcopy-make.md) per creare una condivisione file. Nell'esempio riportato in questa `myfileshare`sezione viene creata una condivisione file denominata .

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>'` |
| **Esempio** | `azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

Per documenti di riferimento dettagliati, vedere [azcopy make](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Caricare file

È possibile utilizzare il comando [azcopy copy copy](storage-ref-azcopy-copy.md) per caricare file e directory dal computer locale.

Questa sezione contiene gli esempi seguenti:

> [!div class="checklist"]
> * Caricare un file
> * Caricare una directory
> * Caricare il contenuto di una directory
> * Caricare un file specifico

> [!TIP]
> È possibile modificare l'operazione di caricamento utilizzando flag facoltativi. Ecco alcuni esempi.
>
> |Scenario|Flag|
> |---|---|
> |Copiare gli elenchi di controllo di accesso (ACL) insieme ai file.|**--preserve-smb-permissions**=\[\|true false\]|
> |Copiare le informazioni sulle proprietà SMB insieme ai file.|**--preserve-smb-info**=\[\|true false\]|
> |Caricare i file come Aggiungi BLOB o BLOB di pagine.|**--blob-tipo**=\[BlockBlob\|\|PageBlob AppendBlob\]|
> |Caricare in un livello di accesso specifico (ad esempio il livello di archiviazione).|**--block-blob-tier**=\[Nessuno\|\|Hot\|Cool Archive\]|
> 
> Per un elenco completo, vedere [opzioni](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> AzCopy non calcola e archivia automaticamente il codice hash md5 del file. Se si desidera che AzCopy eseri, aggiungere il `--put-md5` flag a ogni comando di copia. In questo modo, quando il file viene scaricato, AzCopy calcola un hash MD5 per i dati `Content-md5` scaricati e verifica che l'hash MD5 archiviato nella proprietà del file corrisponda all'hash calcolato.

### <a name="upload-a-file"></a>Caricare un file

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name><SAS-token>'` |
| **Esempio** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

È inoltre possibile caricare un file utilizzando un carattere jolly (-) in qualsiasi punto del percorso o del nome del file. Ad esempio: `'C:\myDirectory\*.txt'` `C:\my*\*.txt`, o .

### <a name="upload-a-directory"></a>Caricare una directory

In questo esempio viene copiata una directory (e tutti i file in tale directory) in una condivisione file. Il risultato è una directory nella condivisione file con lo stesso nome.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Esempio** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Per copiare in una directory all'interno della condivisione file, è sufficiente specificare il nome di tale directory nella stringa di comando.

|    |     |
|--------|-----------|
| **Esempio** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Se specificate il nome di una directory che non esiste nella condivisione file, AzCopy crea una nuova directory con tale nome.

### <a name="upload-the-contents-of-a-directory"></a>Caricare il contenuto di una directory

È possibile caricare il contenuto di una directory senza copiare la directory che lo contiene utilizzando il simbolo di carattere jolly (-).

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>` |
| **Esempio** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Aggiungere `--recursive` il flag per caricare i file in tutte le sottodirectory.

### <a name="upload-specific-files"></a>Caricare file specifici

È possibile specificare nomi di file completi o utilizzare nomi parziali con caratteri jolly (-) .

#### <a name="specify-multiple-complete-file-names"></a>Specificare più nomi di file completi

Utilizzare il comando [azcopy copy](storage-ref-azcopy-copy.md) copy con l'opzione. `--include-path` Separare i singoli nomi di`;`file utilizzando un punto e virgola ( ).

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-path <semicolon-separated-file-list>` |
| **Esempio** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'` |

In questo esempio AzCopy `C:\myDirectory\photos` trasferisce `C:\myDirectory\documents\myFile.txt` la directory e il file. È necessario includere `--recursive` l'opzione per `C:\myDirectory\photos` trasferire tutti i file nella directory.

È inoltre possibile escludere `--exclude-path` i file utilizzando l'opzione . Per altre informazioni, vedere azcopy copy reference docs(documento di riferimento sulla [copia di atrattari).](storage-ref-azcopy-copy.md)

#### <a name="use-wildcard-characters"></a>Utilizzare caratteri jolly

Utilizzare il comando [azcopy copy](storage-ref-azcopy-copy.md) copy con l'opzione. `--include-pattern` Specificare nomi parziali che includono i caratteri jolly. Separare i nomi utilizzando`;`una semicolina ( ).

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Esempio** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'` |

È inoltre possibile escludere `--exclude-pattern` i file utilizzando l'opzione . Per altre informazioni, vedere azcopy copy reference docs(documento di riferimento sulla [copia di atrattari).](storage-ref-azcopy-copy.md)

Le `--include-pattern` `--exclude-pattern` opzioni e si applicano solo ai nomi di file e non al percorso.  Se si desidera copiare tutti i file di testo `–recursive` presenti in una struttura di directory, `–include-pattern` utilizzare `*.txt` l'opzione per ottenere l'intera struttura di directory, quindi utilizzare e specificare per ottenere tutti i file di testo.

## <a name="download-files"></a>Scaricare i file

È possibile utilizzare il comando [azcopy copy copy](storage-ref-azcopy-copy.md) per scaricare file, directory e condivisioni file nel computer locale.

Questa sezione contiene gli esempi seguenti:

> [!div class="checklist"]
> * Scaricare un file
> * Scaricare una directory
> * Scaricare il contenuto di una directory
> * Scaricare file specifici

> [!TIP]
> È possibile modificare l'operazione di download utilizzando flag facoltativi. Ecco alcuni esempi.
>
> |Scenario|Flag|
> |---|---|
> |Copiare gli elenchi di controllo di accesso (ACL) insieme ai file.|**--preserve-smb-permissions**=\[\|true false\]|
> |Copiare le informazioni sulle proprietà SMB insieme ai file.|**--preserve-smb-info**=\[\|true false\]|
> |Decomprimere automaticamente i file.|**--decomprimere**=\[gzip\|sgonfiare\]|
> 
> Per un elenco completo, vedere [opzioni](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> Se `Content-md5` il valore della proprietà di un file contiene un hash, AzCopy calcola un hash MD5 per `Content-md5` i dati scaricati e verifica che l'hash MD5 archiviato nella proprietà del file corrisponda all'hash calcolato. Se questi valori non corrispondono, il download non riesce a meno che non si esegua l'override di questo comportamento aggiungendo `--check-md5=NoCheck` o `--check-md5=LogOnly` al comando di copia.

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

In questo esempio viene `C:\myDirectory\myFileShareDirectory` visualizzata una directory denominata contenente tutti i file scaricati.

### <a name="download-the-contents-of-a-directory"></a>Scaricare il contenuto di una directory

È possibile scaricare il contenuto di una directory senza copiare la directory che lo contiene utilizzando il simbolo di carattere jolly (-).

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*<SAS-token>' '<local-directory-path>/'` |
| **Esempio** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'` |

> [!NOTE]
> Aggiungere `--recursive` il flag per scaricare i file in tutte le sottodirectory.

### <a name="download-specific-files"></a>Scaricare file specifici

È possibile specificare nomi di file completi o utilizzare nomi parziali con caratteri jolly (-) .

#### <a name="specify-multiple-complete-file-names"></a>Specificare più nomi di file completi

Utilizzare il comando [azcopy copy](storage-ref-azcopy-copy.md) copy con l'opzione. `--include-path` Separare i singoli nomi di`;`file utilizzando una semicolina ( ).

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Esempio** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |

In questo esempio AzCopy `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` trasferisce `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt` la directory e il file. È necessario includere `--recursive` l'opzione per `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` trasferire tutti i file nella directory.

È inoltre possibile escludere `--exclude-path` i file utilizzando l'opzione . Per altre informazioni, vedere azcopy copy reference docs(documento di riferimento sulla [copia di atrattari).](storage-ref-azcopy-copy.md)

#### <a name="use-wildcard-characters"></a>Utilizzare caratteri jolly

Utilizzare il comando [azcopy copy](storage-ref-azcopy-copy.md) copy con l'opzione. `--include-pattern` Specificare nomi parziali che includono i caratteri jolly. Separare i nomi utilizzando`;`una semicolina ( ).

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name><SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Esempio** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

È inoltre possibile escludere `--exclude-pattern` i file utilizzando l'opzione . Per altre informazioni, vedere azcopy copy reference docs(documento di riferimento sulla [copia di atrattari).](storage-ref-azcopy-copy.md)

Le `--include-pattern` `--exclude-pattern` opzioni e si applicano solo ai nomi di file e non al percorso.  Se si desidera copiare tutti i file di testo `–recursive` presenti in una struttura di directory, `–include-pattern` utilizzare `*.txt` l'opzione per ottenere l'intera struttura di directory, quindi utilizzare e specificare per ottenere tutti i file di testo.

## <a name="copy-files-between-storage-accounts"></a>Copiare file tra account di archiviazione

È possibile usare AzCopy per copiare i file in altri account di archiviazione. L'operazione di copia è sincrona, quindi quando il comando viene restituito, che indica che tutti i file sono stati copiati.

AzCopy utilizza [le API](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)da [server a server,](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) pertanto i dati vengono copiati direttamente tra i server di archiviazione. Queste operazioni di copia non utilizzano la larghezza di banda di rete del computer. È possibile aumentare la velocità effettiva di `AZCOPY_CONCURRENCY_VALUE` queste operazioni impostando il valore della variabile di ambiente. Per altre informazioni, vedere [Ottimizzare la velocità effettiva](storage-use-azcopy-configure.md#optimize-throughput).

Questa sezione contiene gli esempi seguenti:

> [!div class="checklist"]
> * Copiare un file in un altro account di archiviazioneCopy a file to another storage account
> * Copiare una directory in un altro account di archiviazioneCopy a directory to another storage account
> * Copiare una condivisione file in un altro account di archiviazioneCopy a file share to another storage account
> * Copiare tutte le condivisioni file, le directory e i file in un altro account di archiviazioneCopy all file shares, directories, and files to another storage account

> [!TIP]
> È possibile modificare l'operazione di copia utilizzando flag facoltativi. Ecco alcuni esempi.
>
> |Scenario|Flag|
> |---|---|
> |Copiare gli elenchi di controllo di accesso (ACL) insieme ai file.|**--preserve-smb-permissions**=\[\|true false\]|
> |Copiare le informazioni sulle proprietà SMB insieme ai file.|**--preserve-smb-info**=\[\|true false\]|
> |Copiare i file come Accoda BLOB o BLOB di pagine.|**--blob-tipo**=\[BlockBlob\|\|PageBlob AppendBlob\]|
> |Copiare in un livello di accesso specifico, ad esempio il livello di archiviazione.|**--block-blob-tier**=\[Nessuno\|\|Hot\|Cool Archive\]|
> 
> Per un elenco completo, vedere [opzioni](storage-ref-azcopy-copy.md#options).

### <a name="copy-a-file-to-another-storage-account"></a>Copiare un file in un altro account di archiviazioneCopy a file to another storage account

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>'` |
| **Esempio** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Copiare una directory in un altro account di archiviazioneCopy a directory to another storage account

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Esempio** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-a-file-share-to-another-storage-account"></a>Copiare una condivisione file in un altro account di archiviazioneCopy a file share to another storage account

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Esempio** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>Copiare tutte le condivisioni file, le directory e i file in un altro account di archiviazioneCopy all file shares, directories, and files to another storage account

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<SAS-token>' --recursive'` |
| **Esempio** | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="synchronize-files"></a>Sincronizzare i file

È possibile sincronizzare il contenuto di una condivisione file con un'altra condivisione file. È inoltre possibile sincronizzare il contenuto di una directory in una condivisione file con il contenuto di una directory che si trova in un'altra condivisione file. La sincronizzazione è unidirezionale. In altre parole, si sceglie quale di questi due endpoint è l'origine e quale è la destinazione. La sincronizzazione utilizza anche le API da server a server.

> [!NOTE]
> Attualmente, questo scenario è supportato solo per gli account che non dispongono di uno spazio dei nomi gerarchico. La versione corrente di AzCopy non viene sincronizzata tra File di Azure e Archiviazione BLOB.

Il comando [sync](storage-ref-azcopy-sync.md) confronta i nomi dei file e i timestamp dell'ultima modifica. Impostare `--delete-destination` il flag facoltativo `prompt` su un valore di o per eliminare i file nella directory di `true` destinazione se tali file non esistono più nella directory di origine.

Se si `--delete-destination` imposta `true` il flag su AzCopy elimina i file senza fornire una richiesta. Se desiderate che venga visualizzata una richiesta prima `--delete-destination` che `prompt`AzCopy elimini un file, impostate il flag su .

> [!TIP]
> È possibile modificare l'operazione di sincronizzazione utilizzando flag facoltativi. Ecco alcuni esempi.
>
> |Scenario|Flag|
> |---|---|
> |Specificare il modo in cui gli egli heheM devono essere convalidati durante il download.|**--check-md5**=\[NoCheck\|\|LogOnly\|FailIfDifferent FailIfDifferentOrMissing\]|
> |Escludere i file in base a un modello.|**--exclude-percorso**|
> |Specificare il livello di dettaglio delle voci di log relative alla sincronizzazione.|**--log-livello**=\[\|AVVISO\|\|ERRORE INFO NONE\]|
> 
> Per un elenco completo, vedere [opzioni](storage-ref-azcopy-sync.md#options).

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>Aggiornare una condivisione file con le modifiche apportate a un'altra condivisione fileUpdate a file share with changes to another file share

La prima condivisione file visualizzata in questo comando è l'origine. La seconda è la destinazione.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Esempio** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Aggiornare una directory con le modifiche apportate a una directory in un'altra condivisione file

La prima directory visualizzata in questo comando è l'origine. La seconda è la destinazione.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' --recursive` |
| **Esempio** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-file-share-to-match-the-contents-of-a-share-snapshot"></a>Aggiornare una condivisione file in modo che corrisponda al contenuto di uno snapshot di condivisioneUpdate a file share to match the contents of a share snapshot

La prima condivisione file visualizzata in questo comando è l'origine. Alla fine dell'URI, aggiungere `&sharesnapshot=` la stringa seguita dal valore **DateTime** dello snapshot. 

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>&sharesnapsot<snapshot-ID>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Esempio** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-03-03T20%3A24%3A13.0000000Z' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

Per altre informazioni sugli snapshot di condivisione, vedere [Panoramica degli snapshot di condivisione per i file](https://docs.microsoft.com/azure/storage/files/storage-snapshots-files)di Azure.

## <a name="next-steps"></a>Passaggi successivi

Trova altri esempi in uno di questi articoli:

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)

- [Trasferire dati con AzCopy e l'archivio BLOB](storage-use-azcopy-blobs.md)

- [Trasferire dati con AzCopy e bucket Amazon S3](storage-use-azcopy-s3.md)

- [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)
