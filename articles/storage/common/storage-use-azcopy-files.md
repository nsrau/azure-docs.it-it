---
title: Trasferire dati da e verso File di Azure usando AzCopy V10 | Microsoft Docs
description: Trasferire i dati con AzCopy e archiviazione file.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: eeac35cb96f5001e9ad318d8fe03927d0cd9394e
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68844801"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Trasferire dati con AzCopy e archiviazione file 

AzCopy è un'utilità da riga di comando che è possibile usare per copiare i BLOB o i file da e verso un account di archiviazione. Questo articolo contiene comandi di esempio che funzionano con File di Azure.

Prima di iniziare, vedere l'articolo [Introduzione a AzCopy](storage-use-azcopy-v10.md) per scaricare AzCopy e acquisire familiarità con lo strumento.

## <a name="create-file-shares"></a>Crea condivisioni file

È possibile usare il comando `make` AzCopy per creare una condivisione file. Nell'esempio riportato in questa sezione viene creata una condivisione `myfileshare`file denominata.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy make "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>"` |
| **Esempio** | `azcopy make "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

## <a name="upload-files"></a>Caricare file

È possibile usare il comando `copy` AzCopy per caricare file e directory dal computer locale.

Questa sezione contiene gli esempi seguenti:

> [!div class="checklist"]
> * Carica un file
> * Caricare una directory
> * Caricare file usando caratteri jolly

> [!NOTE]
> AzCopy non calcola e archivia automaticamente il codice hash MD5 del file. Se si vuole eseguire questa operazione, aggiungere il `--put-md5` flag a ogni comando di copia di AzCopy. In questo modo, quando il file viene scaricato, AzCopy calcola un hash MD5 per i dati scaricati e verifica che l'hash MD5 archiviato nella `Content-md5` proprietà del file corrisponda all'hash calcolato.

### <a name="upload-a-file"></a>Carica un file

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>"` |
| **Esempio** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

### <a name="upload-a-directory"></a>Caricare una directory

Questo esempio copia una directory (e tutti i file in tale directory) in una condivisione file. Il risultato è una directory nella condivisione file con lo stesso nome.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>" --recursive` |
| **Esempio** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Per eseguire la copia in una directory all'interno della condivisione file, è sufficiente specificare il nome della directory nella stringa di comando.

|    |     |
|--------|-----------|
| **Esempio** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Se si specifica il nome di una directory che non esiste nella condivisione file, AzCopy crea una nuova directory con tale nome.

### <a name="upload-the-contents-of-a-directory"></a>Caricare il contenuto di una directory

È possibile caricare il contenuto di una directory senza copiare la directory che lo contiene usando il carattere jolly (*).

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy "<local-directory-path>/*" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>` |
| **Esempio** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Aggiungere il `--recursive` flag per caricare i file in tutte le sottodirectory.

## <a name="download-files"></a>Download dei file

È possibile usare il comando `copy` AzCopy per scaricare file, directory e condivisioni file nel computer locale.

Questa sezione contiene gli esempi seguenti:

> [!div class="checklist"]
> * Scarica file
> * Scaricare una directory
> * Scaricare i file utilizzando caratteri jolly

> [!NOTE]
> Se il `Content-md5` valore della proprietà di un file contiene un hash, AzCopy calcola un hash MD5 per i dati scaricati e verifica che l'hash MD5 archiviato nella `Content-md5` proprietà del file corrisponda all'hash calcolato. Se questi valori non corrispondono, il download ha esito negativo a meno che non si esegua `--check-md5=LogOnly` l'override di questo comportamento accodando `--check-md5=NoCheck` o al comando copy.

### <a name="download-a-file"></a>Scarica file

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>" "<local-file-path>"` |
| **Esempio** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>Scaricare una directory

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>" "<local-directory-path>" --recursive` |
| **Esempio** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"  --recursive` |

Questo esempio genera una directory denominata `C:\myDirectory\myFileShareDirectory` che contiene tutti i file scaricati.

### <a name="download-the-contents-of-a-directory"></a>Scarica il contenuto di una directory

È possibile scaricare il contenuto di una directory senza copiare la directory che lo contiene usando il carattere jolly (*).

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>" "<local-directory-path>/"` |
| **Esempio** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"` |

> [!NOTE]
> Aggiungere il `--recursive` flag per scaricare i file in tutte le sottodirectory.

## <a name="next-steps"></a>Passaggi successivi

Altri esempi sono disponibili in uno di questi articoli:

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)

- [Trasferire dati con AzCopy e l'archivio BLOB](storage-use-azcopy-blobs.md)

- [Trasferire dati con AzCopy e bucket Amazon S3](storage-use-azcopy-s3.md)

- [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)