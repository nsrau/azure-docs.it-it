---
title: Trasferire i dati da o verso file di Azure tramite AzCopy v10 | Microsoft Docs
description: Trasferire dati con AzCopy e archiviazione file.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 5d11d5c0da350b9abf2e2b06a11a1690cf3f6922
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247110"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Trasferire dati con AzCopy e archiviazione file 

AzCopy è un'utilità della riga di comando che è possibile usare per copiare BLOB o file da o verso un account di archiviazione. Questo articolo contiene esempi di comandi che funzionano con i file di Azure.

Prima di iniziare, vedere la [Introduzione ad AzCopy](storage-use-azcopy-v10.md) articolo AzCopy di scaricare e acquisire familiarità con lo strumento.

## <a name="create-file-shares"></a>Creare condivisioni file

È possibile usare AzCopy `make` comando per creare una condivisione file. L'esempio in questa sezione viene creata una condivisione file denominata `myfileshare`.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy make "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>"` |
| **Esempio** | `azcopy make "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

## <a name="upload-files"></a>Caricare file

È possibile usare AzCopy `copy` comando per caricare i file e cartelle dal computer locale.

Questa sezione contiene gli esempi seguenti:

> [!div class="checklist"]
> * Caricare un file
> * Caricare una cartella
> * Caricare file usando caratteri jolly

> [!NOTE]
> AzCopy non automaticamente calcolare e archiviare il codice hash md5 del file. Se si desidera che AzCopy a tale scopo, quindi aggiungere il `--put-md5` flag per ogni comando di copia. In questo modo, quando viene scaricato il file, AzCopy viene calcolato un hash MD5 per i dati scaricati e verifica che l'hash MD5 archiviato il file `Content-md5` proprietà corrisponda all'hash calcolato.

### <a name="upload-a-file"></a>Caricare un file

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>"` |
| **Esempio** | `azcopy copy "C:\myFolder\myTextFile.txt" "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

### <a name="upload-a-folder"></a>Caricare una cartella

Questo esempio copia una cartella (e tutti i file in essa contenute) a una condivisione file. Il risultato è una cartella nella condivisione di file con lo stesso nome.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy "<local-folder-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>" --recursive` |
| **Esempio** | `azcopy copy "C:\myFolder" "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Per copiare in una cartella all'interno della condivisione file, è sufficiente specificare il nome della cartella nella stringa di comando.

|    |     |
|--------|-----------|
| **Esempio** | `azcopy copy "C:\myFolder" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Se si specifica il nome di una cartella che non esiste nella condivisione file, AzCopy crea una nuova cartella con lo stesso nome.

### <a name="upload-the-contents-of-a-folder"></a>Caricare il contenuto di una cartella

È possibile caricare il contenuto di una cartella senza copiare nella cartella del contenitore usando il carattere jolly asterisco (*).

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy "<local-folder-path>/*" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<folder-path>?<SAS-token>` |
| **Esempio** | `azcopy copy "C:\myFolder\*" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Aggiungere il `--recursive` flag per caricare i file in tutte le sottocartelle.

## <a name="download-files"></a>Download dei file

È possibile usare AzCopy `copy` condivide i comando per scaricare i file, cartelle e file nel computer locale.

Questa sezione contiene gli esempi seguenti:

> [!div class="checklist"]
> * Scaricare un file
> * Scaricare una cartella
> * Scaricare i file utilizzando caratteri jolly

> [!NOTE]
> Se il `Content-md5` valore della proprietà di un file contiene un hash, AzCopy viene calcolato un hash MD5 per i dati scaricati e verifica che l'hash MD5 archiviato il file `Content-md5` proprietà corrisponda all'hash calcolato. Se questi valori non corrispondono, il download ha esito negativo a meno che non si esegue l'override di questo comportamento aggiungendo `--check-md5=NoCheck` o `--check-md5=LogOnly` comando per la copia.

### <a name="download-a-file"></a>Scaricare un file

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>" "<local-file-path>"` |
| **Esempio** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myFolder\myTextFile.txt"` |

### <a name="download-a-folder"></a>Scaricare una cartella

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<folder-path>?<SAS-token>" "<local-folder-path>" --recursive` |
| **Esempio** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myFolder"  --recursive` |

Questo esempio restituisce in una cartella denominata `C:\myFolder\myFileShareFolder` che contiene tutti i file scaricati.

### <a name="download-the-contents-of-a-folder"></a>Scaricare il contenuto di una cartella

È possibile scaricare il contenuto di una cartella senza copiare nella cartella del contenitore usando il carattere jolly asterisco (*).

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>" "<local-folder-path>/"` |
| **Esempio** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myFolder"` |

> [!NOTE]
> Aggiungere il `--recursive` flag per scaricare i file in tutte le sottocartelle.

## <a name="next-steps"></a>Passaggi successivi

Trovare altri esempi in questi articoli:

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)

- [Trasferire dati con AzCopy e l'archiviazione blob](storage-use-azcopy-blobs.md)

- [Trasferire dati con AzCopy e Amazon S3 bucket](storage-use-azcopy-s3.md)

- [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)