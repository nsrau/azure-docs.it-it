---
title: Trasferire dati da e verso File di Azure usando AzCopy V10 | Microsoft Docs
description: Trasferire i dati con AzCopy e archiviazione file.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 27042809b0f60e1e4141d50d20acff1893c71fde
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158239"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Trasferire dati con AzCopy e archiviazione file 

AzCopy è un'utilità da riga di comando che è possibile usare per copiare i BLOB o i file da e verso un account di archiviazione. Questo articolo contiene comandi di esempio che funzionano con File di Azure.

Prima di iniziare, vedere l'articolo [Introduzione a AzCopy](storage-use-azcopy-v10.md) per scaricare AzCopy e acquisire familiarità con lo strumento.

## <a name="create-file-shares"></a>Crea condivisioni file

È possibile usare il comando [azcopy make](storage-ref-azcopy-make.md) per creare una condivisione file. Nell'esempio riportato in questa sezione viene creata una condivisione file denominata `myfileshare`.

> [!TIP]
> Gli esempi in questa sezione racchiudono gli argomenti del percorso con virgolette singole (''). Usare le virgolette singole in tutte le shell dei comandi eccetto la shell dei comandi di Windows (cmd. exe). Se si usa una shell dei comandi di Windows (cmd. exe), racchiudere gli argomenti del percorso con virgolette doppie ("") anziché virgolette singole ('').

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>'` |
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

> [!NOTE]
> AzCopy non calcola e archivia automaticamente il codice hash MD5 del file. Se si vuole eseguire questa operazione, aggiungere il flag di `--put-md5` a ogni comando copy. In questo modo, quando il file viene scaricato, AzCopy calcola un hash MD5 per i dati scaricati e verifica che l'hash MD5 archiviato nella proprietà `Content-md5` del file corrisponda all'hash calcolato.

Per i documenti di riferimento dettagliati, vedere [azcopy Copy](storage-ref-azcopy-copy.md).

> [!TIP]
> Gli esempi in questa sezione racchiudono gli argomenti del percorso con virgolette singole (''). Usare le virgolette singole in tutte le shell dei comandi eccetto la shell dei comandi di Windows (cmd. exe). Se si usa una shell dei comandi di Windows (cmd. exe), racchiudere gli argomenti del percorso con virgolette doppie ("") anziché virgolette singole ('').

### <a name="upload-a-file"></a>Caricare un file

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>'` |
| **Esempio** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

È anche possibile caricare un file usando un carattere jolly (*) in qualsiasi punto del percorso o del nome file. Ad esempio: `'C:\myDirectory\*.txt'`o `C:\my*\*.txt`.

### <a name="upload-a-directory"></a>Caricare una directory

Questo esempio copia una directory (e tutti i file in tale directory) in una condivisione file. Il risultato è una directory nella condivisione file con lo stesso nome.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' --recursive` |
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
| **Sintassi** | `azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>` |
| **Esempio** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Aggiungere il flag di `--recursive` per caricare i file in tutte le sottodirectory.

### <a name="upload-specific-files"></a>Carica file specifici

È possibile specificare nomi di file completi oppure utilizzare nomi parziali con caratteri jolly (*).

#### <a name="specify-multiple-complete-file-names"></a>Specificare più nomi di file completi

Usare il comando [azcopy Copy](storage-ref-azcopy-copy.md) con l'opzione `--include-path`. Separare i singoli nomi di file usando un punto e virgola (`;`).

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>?<SAS-token>' --include-path <semicolon-separated-file-list>` |
| **Esempio** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'` |

In questo esempio, AzCopy trasferisce la directory `C:\myDirectory\photos` e il file di `C:\myDirectory\documents\myFile.txt`. È necessario includere l'opzione `--recursive` per trasferire tutti i file nella directory `C:\myDirectory\photos`.

È anche possibile escludere i file utilizzando l'opzione `--exclude-path`. Per altre informazioni, vedere la documentazione di riferimento per la [copia di azcopy](storage-ref-azcopy-copy.md) .

#### <a name="use-wildcard-characters"></a>Usa caratteri jolly

Usare il comando [azcopy Copy](storage-ref-azcopy-copy.md) con l'opzione `--include-pattern`. Specificare i nomi parziali che includono i caratteri jolly. Separare i nomi usando un semicolin (`;`).

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>?<SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Esempio** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'` |

È anche possibile escludere i file utilizzando l'opzione `--exclude-pattern`. Per altre informazioni, vedere la documentazione di riferimento per la [copia di azcopy](storage-ref-azcopy-copy.md) .

Le opzioni `--include-pattern` e `--exclude-pattern` sono valide solo per i nomi file e non per il percorso.  Se si desidera copiare tutti i file di testo presenti in un albero di directory, utilizzare l'opzione `–recursive` per ottenere l'intero albero di directory, quindi utilizzare l'`–include-pattern` e specificare `*.txt` per ottenere tutti i file di testo.

## <a name="download-files"></a>Download dei file

È possibile usare il comando [copy di azcopy](storage-ref-azcopy-copy.md) per scaricare file, directory e condivisioni file nel computer locale.

Questa sezione contiene gli esempi seguenti:

> [!div class="checklist"]
> * Scaricare un file
> * Scaricare una directory
> * Scarica il contenuto di una directory
> * Scarica file specifici

> [!NOTE]
> Se il valore della proprietà `Content-md5` di un file contiene un hash, AzCopy calcola un hash MD5 per i dati scaricati e verifica che l'hash MD5 archiviato nella proprietà `Content-md5` del file corrisponda all'hash calcolato. Se questi valori non corrispondono, il download ha esito negativo a meno che non si esegua l'override di questo comportamento accodando `--check-md5=NoCheck` o `--check-md5=LogOnly` al comando copy.

Per i documenti di riferimento dettagliati, vedere [azcopy Copy](storage-ref-azcopy-copy.md).

> [!TIP]
> Gli esempi in questa sezione racchiudono gli argomenti del percorso con virgolette singole (''). Usare le virgolette singole in tutte le shell dei comandi eccetto la shell dei comandi di Windows (cmd. exe). Se si usa una shell dei comandi di Windows (cmd. exe), racchiudere gli argomenti del percorso con virgolette doppie ("") anziché virgolette singole ('').

### <a name="download-a-file"></a>Scaricare un file

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>' '<local-file-path>'` |
| **Esempio** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Scaricare una directory

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>' '<local-directory-path>' --recursive`|
| **Esempio** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive` |

Questo esempio genera una directory denominata `C:\myDirectory\myFileShareDirectory` che contiene tutti i file scaricati.

### <a name="download-the-contents-of-a-directory"></a>Scarica il contenuto di una directory

È possibile scaricare il contenuto di una directory senza copiare la directory che lo contiene usando il carattere jolly (*).

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>' '<local-directory-path>/'` |
| **Esempio** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'` |

> [!NOTE]
> Aggiungere il flag di `--recursive` per scaricare i file in tutte le sottodirectory.

### <a name="download-specific-files"></a>Scarica file specifici

È possibile specificare nomi di file completi oppure utilizzare nomi parziali con caratteri jolly (*).

#### <a name="specify-multiple-complete-file-names"></a>Specificare più nomi di file completi

Usare il comando [azcopy Copy](storage-ref-azcopy-copy.md) con l'opzione `--include-path`. Separare i singoli nomi di file usando un semicolin (`;`).

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>?<SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Esempio** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |

In questo esempio, AzCopy trasferisce la directory `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` e il file di `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt`. È necessario includere l'opzione `--recursive` per trasferire tutti i file nella directory `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos`.

È anche possibile escludere i file utilizzando l'opzione `--exclude-path`. Per altre informazioni, vedere la documentazione di riferimento per la [copia di azcopy](storage-ref-azcopy-copy.md) .

#### <a name="use-wildcard-characters"></a>Usa caratteri jolly

Usare il comando [azcopy Copy](storage-ref-azcopy-copy.md) con l'opzione `--include-pattern`. Specificare i nomi parziali che includono i caratteri jolly. Separare i nomi usando un semicolin (`;`).

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>?<SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Esempio** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

È anche possibile escludere i file utilizzando l'opzione `--exclude-pattern`. Per altre informazioni, vedere la documentazione di riferimento per la [copia di azcopy](storage-ref-azcopy-copy.md) .

Le opzioni `--include-pattern` e `--exclude-pattern` sono valide solo per i nomi file e non per il percorso.  Se si desidera copiare tutti i file di testo presenti in un albero di directory, utilizzare l'opzione `–recursive` per ottenere l'intero albero di directory, quindi utilizzare l'`–include-pattern` e specificare `*.txt` per ottenere tutti i file di testo.

## <a name="copy-files-between-storage-accounts"></a>Copiare i file tra gli account di archiviazione

È possibile usare AzCopy per copiare i file in altri account di archiviazione. L'operazione di copia è sincrona, quindi quando il comando restituisce, che indica che tutti i file sono stati copiati.

AzCopy usa le [API](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url) [da server a server](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) , quindi i dati vengono copiati direttamente tra i server di archiviazione. Queste operazioni di copia non utilizzano la larghezza di banda di rete del computer. È possibile aumentare la velocità effettiva di queste operazioni impostando il valore della variabile di ambiente `AZCOPY_CONCURRENCY_VALUE`. Per altre informazioni, vedere [ottimizzare la velocità effettiva](storage-use-azcopy-configure.md#optimize-throughput).

Questa sezione contiene gli esempi seguenti:

> [!div class="checklist"]
> * Copiare un file in un altro account di archiviazione
> * Copiare una directory in un altro account di archiviazione
> * Copiare una condivisione file in un altro account di archiviazione
> * Copia tutte le condivisioni file, le directory e i file in un altro account di archiviazione

Per informazioni dettagliate sulla documentazione di riferimento, vedere [azcopy Copy](storage-ref-azcopy-copy.md).

> [!TIP]
> Gli esempi in questa sezione racchiudono gli argomenti del percorso con virgolette singole (''). Usare le virgolette singole in tutte le shell dei comandi eccetto la shell dei comandi di Windows (cmd. exe). Se si usa una shell dei comandi di Windows (cmd. exe), racchiudere gli argomenti del percorso con virgolette doppie ("") anziché virgolette singole ('').

### <a name="copy-a-file-to-another-storage-account"></a>Copiare un file in un altro account di archiviazione

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>'` |
| **Esempio** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Copiare una directory in un altro account di archiviazione

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' --recursive` |
| **Esempio** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-a-file-share-to-another-storage-account"></a>Copiare una condivisione file in un altro account di archiviazione

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' --recursive` |
| **Esempio** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>Copia tutte le condivisioni file, le directory e i file in un altro account di archiviazione

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/?<SAS-token>' --recursive'` |
| **Esempio** | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="synchronize-files"></a>Sincronizzare i file

È possibile sincronizzare il contenuto di una condivisione file con un'altra condivisione file. È anche possibile sincronizzare il contenuto di una directory in una condivisione file con il contenuto di una directory che si trova in un'altra condivisione file. La sincronizzazione è unidirezionale. In altre parole, è possibile scegliere quali di questi due endpoint sono l'origine e quella di destinazione. La sincronizzazione usa anche API da server a server.

> [!NOTE]
> Attualmente, questo scenario è supportato solo per gli account che non dispongono di uno spazio dei nomi gerarchico. La versione corrente di AzCopy non viene sincronizzata tra File di Azure e archiviazione BLOB.

Il comando di [sincronizzazione](storage-ref-azcopy-sync.md) Confronta i nomi di file e i timestamp dell'Ultima modifica. Impostare il flag `--delete-destination` facoltativo sul valore `true` o `prompt` per eliminare i file nella directory di destinazione se tali file non sono più presenti nella directory di origine.

Se si imposta il flag di `--delete-destination` su `true` AzCopy Elimina i file senza fornire un prompt. Se si desidera che venga visualizzato un messaggio prima che AzCopy elimini un file, impostare il flag `--delete-destination` su `prompt`.

Per informazioni dettagliate sulla documentazione di riferimento, vedere [azcopy Sync](storage-ref-azcopy-sync.md).

> [!TIP]
> Gli esempi in questa sezione racchiudono gli argomenti del percorso con virgolette singole (''). Usare le virgolette singole in tutte le shell dei comandi eccetto la shell dei comandi di Windows (cmd. exe). Se si usa una shell dei comandi di Windows (cmd. exe), racchiudere gli argomenti del percorso con virgolette doppie ("") anziché virgolette singole ('').

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>Aggiornare una condivisione file con le modifiche apportate a un'altra condivisione file

La prima condivisione file visualizzata in questo comando è l'origine. Il secondo è la destinazione.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' --recursive` |
| **Esempio** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Aggiornare una directory con le modifiche apportate a una directory in un'altra condivisione file

La prima directory visualizzata in questo comando è l'origine. Il secondo è la destinazione.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name>?<SAS-token>' --recursive` |
| **Esempio** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="next-steps"></a>Passaggi successivi

Altri esempi sono disponibili in uno di questi articoli:

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)

- [Trasferire dati con AzCopy e l'archivio BLOB](storage-use-azcopy-blobs.md)

- [Trasferire dati con AzCopy e bucket Amazon S3](storage-use-azcopy-s3.md)

- [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)
