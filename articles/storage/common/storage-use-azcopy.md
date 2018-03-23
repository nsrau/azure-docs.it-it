---
title: Copiare o spostare dati in Archiviazione di Microsoft Azure con AzCopy in Windows | Microsoft Docs
description: Usare l'utilità AzCopy in Windows per spostare o copiare dati da o verso BLOB, tabelle e contenuto di file. Copiare i dati in archiviazione di Azure da file locali o copiare i dati all'interno o tra account di archiviazione. Migrare facilmente i dati in archiviazione di Azure.
services: storage
documentationcenter: ''
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: aa155738-7c69-4a83-94f8-b97af4461274
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: seguler
ms.openlocfilehash: 13e09a3081c9dfa2d88625489a82c687d6722f20
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="transfer-data-with-the-azcopy-on-windows"></a>Trasferire dati con AzCopy in Windows
AzCopy è un'utilità della riga di comando progettata la copia dei dati in/dall'archiviazione di oggetti BLOB, file e tabelle di Microsoft Azure usando semplici comandi progettati per garantire prestazioni ottimali. È possibile copiare dati tra un file system e un account di archiviazione o tra più account di archiviazione.  

Esistono due versioni di AzCopy che è possibile scaricare. AzCopy in Windows viene compilato con .NET Framework e offre opzioni della riga di comando in stile Windows. [AzCopy in Linux](storage-use-azcopy-linux.md) viene compilato con .NET Framework per le piattaforme Linux e offre opzioni della riga di comando in stile POSIX. Questo articolo descrive AzCopy in Windows.

## <a name="download-and-install-azcopy-on-windows"></a>Scaricare e installare AzCopy in Windows

Scaricare la [versione più recente di AzCopy in Windows](http://aka.ms/downloadazcopy).

Dopo l'installazione di AzCopy in Windows mediante il programma di installazione, aprire una finestra di comando e passare alla directory di installazione di AzCopy contenente il file eseguibile `AzCopy.exe`. Se si vuole, è possibile aggiungere il percorso di installazione di AzCopy al percorso di sistema. Per impostazione predefinita, AzCopy viene installato in `%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy` o `%ProgramFiles%\Microsoft SDKs\Azure\AzCopy`.

## <a name="writing-your-first-azcopy-command"></a>Scrittura del primo comando AzCopy 

La sintassi di base per i comandi di AzCopy è la seguente:

```azcopy
AzCopy /Source:<source> /Dest:<destination> [Options]
```

Gli esempi seguenti illustrano vari scenari per la copia dei dati da e in e da BLOB, file e tabelle di Microsoft Azure. Per una spiegazione dettagliata dei parametri usati in ogni esempio, vedere la sezione [Parametri di AzCopy](#azcopy-parameters) .

## <a name="download-blobs-from-blob-storage"></a>Scaricare BLOB da Archiviazione BLOB

Esistono diversi modi per scaricare i BLOB tramite AzCopy.

### <a name="download-a-single-blob"></a>Scaricare un singolo BLOB

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:"abc.txt"
```

Se la cartella `C:\myfolder` non esiste ancora, AzCopy la crea e scarica `abc.txt ` nella nuova cartella.

### <a name="download-a-single-blob-from-the-secondary-region"></a>Scaricare un singolo BLOB dall'area secondaria

```azcopy
AzCopy /Source:https://myaccount-secondary.blob.core.windows.net/mynewcontainer /Dest:C:\myfolder /SourceKey:key /Pattern:"abc.txt"
```

È necessario che sia abilitata l'archiviazione con ridondanza geografica e accesso in lettura per accedere all'area secondaria.

### <a name="download-all-blobs-in-a-container"></a>Scaricare tutti i BLOB in un contenitore

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /S
```

Si supponga che i BLOB seguenti risiedano nel contenitore specificato:  

    abc.txt
    abc1.txt
    abc2.txt
    vd1\a.txt
    vd1\abcd.txt

Dopo l'operazione di download, la directory `C:\myfolder` include i file seguenti:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\vd1\a.txt
    C:\myfolder\vd1\abcd.txt

Se non si specifica l'opzione `/S`, non verrà scaricato alcun BLOB.

### <a name="download-blobs-with-a-specific-prefix"></a>Scaricare i BLOB con un prefisso specifico

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:a /S
```

Si supponga che i BLOB seguenti risiedano nel contenitore specificato. Vengono scaricati tutti i BLOB che iniziano con il prefisso `a`:

    abc.txt
    abc1.txt
    abc2.txt
    xyz.txt
    vd1\a.txt
    vd1\abcd.txt

Dopo l'operazione di download, la cartella `C:\myfolder` include i file seguenti:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

Il prefisso si applica alla directory virtuale che forma la prima parte del nome del BLOB. Nell'esempio precedente, la directory virtuale non corrisponde al prefisso specificato, quindi non viene scaricata. Se inoltre l'opzione `/S` non è specificata, AzCopy non scarica alcun BLOB.

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>Impostare l'ora dell'ultima modifica dei file esportati sulla stessa ora dei BLOB di origine

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT
```

È anche possibile escludere BLOB dall'operazione di download in base all'ora dell'ultima modifica. Se ad esempio si vogliono escludere i BLOB in cui l'ora dell'ultima modifica è uguale o successiva a quella del file di destinazione, aggiungere l'opzione `/XN` .

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN
```

Se si vogliono escludere i BLOB in cui l'ora dell'ultima modifica è uguale o precedente a quella del file di destinazione, aggiungere l'opzione `/XO`:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO
```

## <a name="upload-blobs-to-blob-storage"></a>Caricare BLOB in Archiviazione BLOB

Esistono diversi modi per caricare i BLOB tramite AzCopy.

### <a name="upload-a-single-blob"></a>Caricare un singolo BLOB

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:"abc.txt"
```

Se il contenitore di destinazione specificato non esiste, AzCopy ne crea uno e vi carica il file.

### <a name="upload-a-single-blob-to-a-virtual-directory"></a>Caricare un singolo BLOB in una directory virtuale

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer/vd /DestKey:key /Pattern:abc.txt
```

Se la directory virtuale specificata non esiste, AzCopy carica il file in modo da includere la directory virtuale nel nome del BLOB, *ovvero* `vd/abc.txt` nell'esempio precedente.

### <a name="upload-all-blobs-in-a-folder"></a>Caricare tutti i BLOB in una cartella

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S
```

Se si specifica l'opzione `/S`, il contenuto della directory specificata viene caricato nell'archiviazione BLOB in modo ricorsivo, ovvero vengono caricati anche tutte le sottocartelle e i relativi file. Si supponga ad esempio che i file seguenti risiedano nella cartella `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Dopo l'operazione di caricamento, il contenitore include i file seguenti:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Se non si specifica l'opzione `/S`, AzCopy non carica i file in modo ricorsivo. Dopo l'operazione di caricamento, il contenitore include i file seguenti:

    abc.txt
    abc1.txt
    abc2.txt

### <a name="upload-blobs-matching-a-specific-pattern"></a>Caricare i BLOB corrispondenti a un criterio specifico

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:a* /S
```

Si supponga che i file seguenti risiedano nella cartella `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\xyz.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Dopo l'operazione di caricamento, il contenitore include i file seguenti:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Se non si specifica l'opzione `/S`, AzCopy carica solo i BLOB che non si trovano in una directory virtuale:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>Specificare il tipo di contenuto MIME di un BLOB di destinazione

Per impostazione predefinita, AzCopy imposta il tipo di contenuto di un BLOB di destinazione su `application/octet-stream`. A partire dalla versione 3.1.0, è possibile specificare il tipo di contenuto in modo esplicito tramite l'opzione `/SetContentType:[content-type]`. Questa sintassi imposta il tipo di contenuto per tutti i BLOB in un'operazione di caricamento.

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType:video/mp4
```

Se si specifica `/SetContentType` senza fornire un valore, AzCopy imposta il tipo di contenuto di ogni BLOB o file in base alla loro estensione.

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType
```

## <a name="copy-blobs-in-blob-storage"></a>Copiare BLOB in Archiviazione BLOB

Esistono diversi modi per copiare i BLOB da una posizione all'altra tramite AzCopy.

### <a name="copy-a-single-blob-from-one-container-to-another-within-the-same-storage-account"></a>Copiare un singolo BLOB da un contenitore all'altro nello stesso account di archiviazione 

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt
```

Quando si copia un BLOB all'interno di un account di archiviazione, viene eseguita un'operazione di [copia sul lato server](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) .

### <a name="copy-a-single-blob-from-one-storage-account-to-another"></a>Copiare un singolo BLOB da un account di archiviazione all'altro

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
```

Quando si copia un BLOB tra account di archiviazione, viene eseguita un'operazione di [copia sul lato server](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) .

### <a name="copy-a-single-blob-from-the-secondary-region-to-the-primary-region"></a>Copiare un singolo BLOB dall'area secondaria all'area primaria

```azcopy
AzCopy /Source:https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 /Dest:https://myaccount2.blob.core.windows.net/mynewcontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
```

È necessario che sia abilitata l'archiviazione con ridondanza geografica e accesso in lettura per accedere all'archiviazione secondaria.

### <a name="copy-a-single-blob-and-its-snapshots-from-one-storage-account-to-another"></a>Copiare un singolo BLOB e i relativi snapshot da un account di archiviazione all'altro

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot
```

Dopo l'operazione di copia, il contenitore di destinazione include il BLOB e i relativi snapshot. Supponendo che il BLOB dell'esempio precedente contenga due snapshot, nel contenitore saranno presenti il BLOB e gli snapshot seguenti:

    abc.txt
    abc (2013-02-25 080757).txt
    abc (2014-02-21 150331).txt

### <a name="copy-all-blobs-in-a-container-to-another-storage-account"></a>Copiare tutti i BLOB in un contenitore in un altro account di archiviazione 

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 
/Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /S
```

Specificando l'opzione /S, il contenuto del contenitore specificato viene caricato in modo ricorsivo. Vedere [Caricare tutti i BLOB in una cartella](#upload-all-blobs-in-a-folder) per altre informazioni e un esempio.

### <a name="synchronously-copy-blobs-from-one-storage-account-to-another"></a>Copiare BLOB in modo asincrono da un account di archiviazione all'altro

Per impostazione predefinita, AzCopy copia i dati tra due endpoint di archiviazione in modo asincrono. L'operazione di copia viene quindi eseguita in background sfruttando la capacità di larghezza di banda disponibile non limitata da alcun contratto di servizio relativo alla velocità di copia di un BLOB. AzCopy, inoltre, controlla periodicamente lo stato della copia fino a quando questa non viene completata o risulta non riuscita.

L'opzione `/SyncCopy` garantisce che l'operazione di copia avvenga a velocità costante. AzCopy esegue la copia sincrona scaricando i BLOB da copiare dall'origine specificata nella memoria locale, per poi caricarli nella destinazione dell'archiviazione BLOB.

```azcopy
AzCopy /Source:https://myaccount1.blob.core.windows.net/myContainer/ /Dest:https://myaccount2.blob.core.windows.net/myContainer/ /SourceKey:key1 /DestKey:key2 /Pattern:ab /SyncCopy
```

`/SyncCopy` potrebbe generare costi di uscita aggiuntivi rispetto alla copia asincrona. Per evitare costi di uscita, è quindi consigliabile usare questa opzione in una VM di Azure che si trova nella stessa area dell'account di archiviazione di origine.

## <a name="download-files-from-file-storage"></a>Scaricare file da Archiviazione file

Esistono diversi modi per scaricare i file tramite AzCopy.

### <a name="download-a-single-file"></a>Scaricare un singolo file

```azcopy
AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt
```

Se l'origine specificata è una condivisione file di Azure, è necessario specificare il nome file esatto, *ad esempio* `abc.txt`, per copiare un solo file oppure specificare l'opzione `/S` per copiare tutti i file nella condivisione in modo ricorsivo. Se si specificano sia il criterio del file che l'opzione `/S` contemporaneamente, verrà generato un errore.

### <a name="download-all-files-in-a-directory"></a>Scaricare tutti i file in una directory

```azcopy
AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S
```

Le cartelle vuote non vengono scaricate.

## <a name="upload-files-to-an-azure-file-share"></a>Caricare file in una condivisione File di Azure

Esistono diversi modi per caricare i file tramite AzCopy.

### <a name="upload-a-single-file"></a>Caricare un singolo file

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:abc.txt
```

### <a name="upload-all-files-in-a-folder"></a>Caricare tutti i file in una cartella

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S
```

Le cartelle vuote non vengono caricate.

### <a name="upload-files-matching-a-specific-pattern"></a>Caricare i file corrispondenti a un criterio specifico

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S
```

## <a name="copy-files-in-file-storage"></a>Copiare file in Archiviazione file

Esistono diversi modi per copiare i file in una condivisione file di Azure tramite AzCopy.

### <a name="copy-from-one-file-share-to-another"></a>Copiare da una condivisione file all'altra

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S
```
Quando si copia un file tra condivisioni file, viene eseguita un'operazione di [copia sul lato server](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx).

### <a name="copy-from-an-azure-file-share-to-blob-storage"></a>Copiare da una condivisione file di Azure in Archiviazione BLOB

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare/ /Dest:https://myaccount2.blob.core.windows.net/mycontainer/ /SourceKey:key1 /DestKey:key2 /S
```
Quando si copia un file dalla condivisione file al BLOB, viene eseguita un'operazione di [copia sul lato server](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx).

### <a name="copy-a-blob-from-blob-storage-to-an-azure-file-share"></a>Copiare un BLOB da Archiviazione BLOB in una condivisione file di Azure

```azcopy
AzCopy /Source:https://myaccount1.blob.core.windows.net/mycontainer/ /Dest:https://myaccount2.file.core.windows.net/myfileshare/ /SourceKey:key1 /DestKey:key2 /S
```
Quando si copia un file da un BLOB a una condivisione file, viene eseguita un'operazione di [copia sul lato server](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx).

### <a name="synchronously-copy-files"></a>Copiare file in modo sincrono

È possibile specificare l'opzione `/SyncCopy` per copiare i dati da archiviazione file ad archiviazione file, da archiviazione file ad archivio BLOB e da archivio BLOB ad archiviazione file in modo sincrono. AzCopy esegue questa operazione scaricando i dati di origine nella memoria locale e caricandoli di nuovo nella destinazione. Vengono applicati i costi in uscita standard.

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy
```

Durante la copia da Archiviazione file ad Archiviazione BLOB, il tipo di BLOB predefinito è il BLOB in blocchi. Per modificare il tipo di BLOB di destinazione, è possibile specificare l'opzione `/BlobType:page`.

`/SyncCopy` può generare costi aggiuntivi in uscita rispetto alla copia asincrona. Per evitare costi in uscita, è consigliabile quindi usare questa opzione nella macchina virtuale di Azure che si trova nella stessa area dell'account di archiviazione di origine.

## <a name="export-data-from-table-storage"></a>Esportare dati da Archiviazione tabelle

Verrà ora illustrata l'esportazione di dati da Archiviazione tabelle di Azure tramite AzCopy.

### <a name="export-a-table"></a>Esportare una tabella

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key
```

AzCopy scrive un file manifesto nella cartella di destinazione specificata. Il file manifesto viene usato dal processo di importazione per trovare i file di dati necessari ed eseguire la convalida di dati. Il file manifesto usa per impostazione predefinita la convenzione di denominazione seguente:

    <account name>_<table name>_<timestamp>.manifest

È possibile anche specificare l'opzione `/Manifest:<manifest file name>` per impostare il nome del file manifesto.

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /Manifest:abc.manifest
```

### <a name="split-an-export-from-table-storage-into-multiple-files"></a>Suddividere un'esportazione da Archiviazione tabelle in più file

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/mytable/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100
```

AzCopy usa un *indice di volumi* nei nomi file di dati divisi per distinguere più file. L'indice di volumi è costituito da due parti, un *indice di intervalli di chiavi di partizione* e un *indice di file divisi*. Entrambi gli indici sono in base zero.

L'indice dell'intervallo di chiavi di partizione è 0 se l'utente non specifica l'opzione `/PKRS`.

Si supponga, ad esempio, che AzCopy generi due file di dati dopo che l'utente ha specificato l'opzione. `/SplitSize`. I nomi file di dati risultanti potrebbero essere:

    myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
    myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

Tenere presente che il valore minimo possibile per l'opzione `/SplitSize` è di 32 MB. Se la destinazione specificata è l'archiviazione BLOB, AzCopy divide il file di dati quando le dimensioni raggiungono il limite delle dimensioni BLOB (200 GB), indipendentemente dal fatto che l'opzione `/SplitSize` sia stata specificata o meno dall'utente.

### <a name="export-a-table-to-json-or-csv-data-file-format"></a>Esportare una tabella nel formato di file di dati JSON o CSV

Per impostazione predefinita, AzCopy esporta le tabelle in file di dati JSON. È possibile specificare l'opzione `/PayloadFormat:JSON|CSV` per esportare le tabelle come JSON o CSV.

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PayloadFormat:CSV
```

Quando si specifica il formato di payload CSV, AzCopy genera anche un file di schema con estensione `.schema.csv` per ogni file di dati.

### <a name="export-table-entities-concurrently"></a>Esportare entità tabella simultaneamente

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PKRS:"aa#bb"
```

AzCopy avvia operazioni simultanee per esportare le entità quando l'utente specifica l'opzione `/PKRS`. Ogni operazione esporta un intervallo di chiavi di partizione.

Si noti che il numero di operazioni simultanee viene controllato anche dall'opzione `/NC`. AzCopy usa il numero di processori core come valore predefinito di `/NC`durante la copia di entità della tabella, anche se `/NC` non è stato specificato. Quando l'utente specifica l'opzione `/PKRS`, AzCopy usa il valore più basso dei due (intervalli di chiavi di partizione e operazioni simultanee specificate in modo implicito o esplicito) per determinare il numero di operazioni simultanee da avviare. Per ulteriori dettagli, digitare `AzCopy /?:NC` nella riga di comando.

### <a name="export-a-table-to-blob-storage"></a>Esportare una tabella in Archiviazione BLOB

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2
```

AzCopy genera un file di dati JSON nel contenitore BLOB con la convenzione di denominazione seguente:

    <account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

Il file di dati JSON generato segue il formato di payload per i metadati minimi. Per i dettagli su questo formato di payload, vedere [Formato di payload per le operazioni del servizio tabelle](http://msdn.microsoft.com/library/azure/dn535600.aspx).

Quando si esportano tabelle in file BLOB, AzCopy scarica le entità tabella in file di dati temporanei locali e quindi le carica nei file BLOB. Questi file di dati temporanei vengono inseriti nella cartella di file journal con il percorso predefinito "<code>%LocalAppData%\Microsoft\Azure\AzCopy</code>". È possibile specificare l'opzione /Z:[journal-file-folder] per modificare il percorso della cartella di file journal e quindi modificare il percorso dei file di dati temporanei. Le dimensioni dei file di dati temporanei dipendono dalle dimensioni delle entità tabella e dalle dimensioni specificate con l'opzione /SplitSize, anche se il file di dati temporanei sul disco locale viene eliminato immediatamente dopo essere stato caricato nel BLOB. Verificare che lo spazio disponibile sul disco locale sia sufficiente per archiviare i file di dati temporanei prima che vengano eliminati.

## <a name="import-data-into-table-storage"></a>Importare dati in Archiviazione tabelle

Verrà ora illustrata l'importazione di dati da Archiviazione tabelle di Azure tramite AzCopy.

### <a name="import-a-table"></a>Importare una tabella

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

L'opzione `/EntityOperation` indica come inserire le entità nella tabella. I valori possibili sono:

* `InsertOrSkip`: ignora un'entità esistente oppure ne inserisce una nuova se non esiste nella tabella.
* `InsertOrMerge`: unisce un'entità esistente oppure ne inserisce una nuova se non esiste nella tabella.
* `InsertOrReplace`: sostituisce un'entità esistente oppure ne inserisce una nuova se non esiste nella tabella.

Si noti che non è possibile specificare l'opzione `/PKRS`nello scenario di importazione. Diversamente dallo scenario di esportazione, in cui è necessario specificare l'opzione `/PKRS` per avviare operazioni simultanee, quando si importa una tabella, AzCopy avvia l'esecuzione di operazioni simultanee per impostazione predefinita. Il numero predefinito di operazioni simultanee avviate è uguale al numero di processori core. Tuttavia, è possibile specificare un numero diverso di operazioni simultanee con l'opzione`/NC`. Per ulteriori dettagli, digitare `AzCopy /?:NC` nella riga di comando.

Si noti che AzCopy supporta solo l'importazione per JSON, non per CSV. AzCopy non supporta l'importazione di tabelle da file JSON e file manifesto creati dall'utente. Entrambi questi file devono provenire da un'esportazione di tabella di AzCopy. Per evitare errori, non modificare il file JSON o il file manifesto esportato.

### <a name="import-entities-into-a-table-from-blob-storage"></a>Importare entità in una tabella da Archiviazione BLOB

Si supponga che in un contenitore BLOB siano presenti: un file JSON che rappresenta una tabella di Azure e il relativo file manifesto associato.

    myaccount_mytable_20140103T112020.manifest
    myaccount_mytable_20140103T112020_0_0_0AF395F1DC42E952.json

È possibile eseguire il comando seguente per importare le entità in una tabella usando il file manifesto disponibile nel contenitore BLOB:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:https://myaccount.table.core.windows.net/mytable /SourceKey:key1 /DestKey:key2 /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:"InsertOrReplace"
```

## <a name="other-azcopy-features"></a>Altre funzionalità di AzCopy

Verranno ora illustrate alcune altre funzionalità di AzCopy.

### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>Copiare solo i dati che non esistono nella destinazione

I parametri `/XO` e `/XN` consentono di escludere dalla copia, rispettivamente, le risorse di origine precedenti o più recenti. Per copiare solo e risorse di origine che non esistono nella destinazione, è possibile specificare entrambi i parametri nel comando AzCopy:

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /XO /XN

    /Source:C:\myfolder /Dest:http://myaccount.file.core.windows.net/myfileshare /DestKey:<destkey> /S /XO /XN

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:http://myaccount.blob.core.windows.net/mycontainer1 /SourceKey:<sourcekey> /DestKey:<destkey> /S /XO /XN

Si noti che questi parametri non sono supportati quando l'origine o la destinazione è una tabella.

### <a name="use-a-response-file-to-specify-command-line-parameters"></a>Usare un file di risposta per specificare i parametri della riga di comando

```azcopy
AzCopy /@:"C:\responsefiles\copyoperation.txt"
```

È possibile includere i parametri della riga di comando di AzCopy in un file di risposta. AzCopy elabora i parametri nel file come se fossero stati specificati nella riga di comando, eseguendo una sostituzione diretta con i contenuti del file.

Si supponga che sia presente un file di risposta denominato `copyoperation.txt`, che contiene le righe seguenti. Ogni parametro di AzCopy può essere specificato su una singola riga

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

o su righe separate:

    /Source:http://myaccount.blob.core.windows.net/mycontainer
    /Dest:C:\myfolder
    /SourceKey:<sourcekey>
    /S
    /Y

Se il parametro viene suddiviso su due righe, l'operazione di AzCopy ha esito negativo, come mostrato di seguito per il parametro `/sourcekey`:

    http://myaccount.blob.core.windows.net/mycontainer
     C:\myfolder
    /sourcekey:
    <sourcekey>
    /S
    /Y

### <a name="use-multiple-response-files-to-specify-command-line-parameters"></a>Usare più file di risposta per specificare i parametri della riga di comando

Si supponga che siano presenti un file di risposta denominato `source.txt` , che specifica un contenitore di origine:

    /Source:http://myaccount.blob.core.windows.net/mycontainer

Un file di risposta denominato `dest.txt` , che specifica una cartella di destinazione nel file system:

    /Dest:C:\myfolder

E un file di risposta denominato `options.txt` , che specifica le opzioni per AzCopy:

    /S /Y

Per chiamare AzCopy con questi file di risposta, che risiedono in una directory `C:\responsefiles`, usare il comando seguente:

```azcopy
AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /SourceKey:<sourcekey> /@:"C:\responsefiles\options.txt"   
```

AzCopy elabora il comando come se fossero stati inclusi tutti i singoli parametri nella riga di comando:

```azcopy
AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y
```

### <a name="specify-a-shared-access-signature-sas"></a>Specificare una firma di accesso condiviso

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceSAS:SAS1 /DestSAS:SAS2 /Pattern:abc.txt
```

È anche possibile specificare una firma di accesso condiviso per il contenitore URI:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken /Dest:C:\myfolder /S
```

### <a name="journal-file-folder"></a>Cartella del file journal

Ogni volta che si invia un comando ad AzCopy, l'utilità verifica se il file journal è presente nella cartella predefinita o in una cartella specificata dall'utente tramite questa opzione. Se il file journal non viene trovato in tali percorsi, AzCopy considera l'operazione come nuova e quindi crea un nuovo file journal.

Se il file journal esiste, AzCopy verifica se la riga di comando di input corrisponde alla riga di comando nel file journal. Se le righe di comando corrispondono, AzCopy avvia la ripresa dell'operazione incompleta. Se non corrispondono, viene chiesto se si vuole sovrascrivere il file journal per avviare una nuova operazione o annullare l'operazione corrente.

Se si vuole usare il percorso predefinito per il file journal:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z
```

Se si omette l'opzione`/Z` o si specifica l'opzione `/Z` senza il percorso della cartella, come illustrato sopra, AzCopy creerà il file journal nel percorso predefinito, ovvero `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`. Se il file journal esiste già, AzCopy riprenderà l'operazione in base al file journal.

Se si vuole specificare un percorso personalizzato per il file journal:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\
```

In questo esempio viene creato il file journal, nel caso in cui non esista già: Se esiste, AzCopy riprenderà l'operazione in base al file journal.

Se si vuole riprendere un'operazione di AzCopy:

```azcopy
AzCopy /Z:C:\journalfolder\
```

In questo esempio viene ripresa l'ultima operazione che potrebbe non essere stata completata a causa di errori.

### <a name="generate-a-log-file"></a>Generare un file di log

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V
```

Se si specifica l'opzione `/V`senza fornire il percorso del file di log dettagliato, AzCopy creerà il file journal nel percorso predefinito, ovvero`%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`.

In caso contrario, è possibile creare un file di log in un percorso personalizzato:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log
```

Si noti che se si specifica un percorso relativo per l'opzione`/V` , ad esempio`/V:test/azcopy1.log`, il log dettagliato verrà creato nella directory di lavoro corrente all'interno di una sottocartella denominata `test`.

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>Specificare il numero di operazioni simultanee da avviare

L'opzione `/NC` specifica il numero di operazioni di copia simultanee. Per impostazione predefinita, AzCopy avvia un determinato numero di operazioni simultanee per aumentare la velocità effettiva di trasferimento dei dati. Per le operazioni su tabella il numero di operazioni simultanee è uguale al numero di processori disponibili. Per le operazioni su BLOB e file il numero di operazioni simultanee è pari a 8 volte il numero di processori disponibili. Se AzCopy è in esecuzione in una rete con larghezza di banda ridotta, è possibile specificare un numero inferiore per il parametro /NC in modo da evitare errori generati dalla competizione tra le risorse.

### <a name="run-azcopy-against-the-azure-storage-emulator"></a>Eseguire AzCopy nell'emulatore di archiviazione di Azure

È possibile eseguire AzCopy nell'[emulatore di archiviazione di Azure](storage-use-emulator.md) per i BLOB:

```azcopy
AzCopy /Source:https://127.0.0.1:10000/myaccount/mycontainer/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S
```

È possibile eseguirlo anche per le tabelle:

```azcopy
AzCopy /Source:https://127.0.0.1:10002/myaccount/mytable/ /Dest:C:\myfolder /SourceKey:key /SourceType:Table
```

## <a name="azcopy-parameters"></a>Parametri di AzCopy

I parametri per AzCopy sono descritti nella tabella seguente. È anche possibile digitare uno dei comandi seguenti dalla riga di comando per assistenza nell'uso di AzCopy:

* Per assistenza dettagliata della riga di comando per AzCopy: `AzCopy /?`
* Per assistenza dettagliata con un parametro di AzCopy: `AzCopy /?:SourceKey`
* Per esempi della riga di comando: `AzCopy /?:Sample`

### <a name="sourcesource"></a>/Source: "source"

Specifica i dati di origine da cui eseguire la copia. L'origine può essere una directory di file system, un contenitore BLOB, una directory virtuale BLOB, una condivisione file di archiviazione, una directory file di archiviazione o una tabella di Azure.

**Applicabile a:** BLOB, file, tabelle

### <a name="destdestination"></a>/Dest:"destination"

Specifica la destinazione in cui eseguire la copia. La destinazione può essere una directory di file system, un contenitore BLOB, una directory virtuale BLOB, una condivisione file di archiviazione, una directory file di archiviazione o una tabella di Azure.

**Applicabile a:** BLOB, file, tabelle

### <a name="patternfile-pattern"></a>/Pattern:"file-pattern"

Specifica un criterio file indicante i file da copiare. Il comportamento del parametro /Pattern è determinato dal percorso dei dati di origine e dalla presenza dell'opzione relativa alla modalità ricorsiva. È possibile specificare la modalità ricorsiva tramite l'opzione /S.

Se l'origine specificata è una directory nel file system, è possibile usare i caratteri jolly standard e viene cercata una corrispondenza del criterio del file specificato con i file inclusi nella directory. Se è specificata l'opzione /S, AzCopy cerca anche la corrispondenza del criterio specificato con tutti i file inclusi nelle sottocartelle della directory.

Se l'origine specificata è un contenitore o una directory virtuale BLOB, non è possibile applicare i caratteri jolly. Se è specificata l'opzione /S, AzCopy interpreta i criteri del file specificato come prefisso del BLOB. Se non è specificata l'opzione /S, AzCopy cerca una corrispondenza esatta dei criteri con i nomi dei BLOB.

Se l'origine specificata è una condivisione file di Azure, è necessario specificare il nome file esatto (ad esempio, abc.txt) per copiare un solo file oppure specificare l'opzione /S per copiare tutti i file nella condivisione in modo ricorsivo. Se si specificano contemporaneamente il criterio del file e l'opzione /S, viene generato un errore.

AzCopy utilizza la corrispondenza tra maiuscole e minuscole quando l’opzione /Source è un contenitore BLOB o una directory virtuale di BLOB e non utilizza la corrispondenza tra maiuscole e minuscole in tutti gli altri casi.

Il criterio predefinito per i file, se non specificato in modo esplicito, è *.* per un percorso del file system o un prefisso vuoto per un percorso di Archiviazione di Azure. Non è consentito specificare più criteri file.

**Applicabile a:** BLOB, file

### <a name="destkeystorage-key"></a>/DestKey:"storage-key"

Specifica la chiave dell'account di archiviazione per la risorsa di destinazione.

**Applicabile a:** BLOB, file, tabelle

### <a name="destsassas-token"></a>/DestSAS:"sas-token"

Specifica una firma di accesso condiviso con autorizzazioni di LETTURA e SCRITTURA per la destinazione (se applicabile). Racchiudere la firma di accesso condiviso tra virgolette doppie, in quanto potrebbe contenere caratteri della riga di comando speciali.

Se la risorsa di destinazione è una tabella, una condivisione file o un contenitore BLOB, è possibile specificare questa opzione seguita dal token di accesso condiviso o specificare la firma di accesso condiviso come parte dell'URI della tabella, della condivisione file o del contenitore BLOB, senza questa opzione.

Se l'origine e la destinazione sono entrambi BLOB, il BLOB di destinazione deve essere nello stesso account di archiviazione del BLOB di origine.

**Applicabile a:** BLOB, file, tabelle

### <a name="sourcekeystorage-key"></a>/SourceKey:"storage-key"

Specifica la chiave dell'account di archiviazione per la risorsa di origine.

**Applicabile a:** BLOB, file, tabelle

### <a name="sourcesassas-token"></a>/SourceSAS:"sas-token"

Specifica una firma di accesso condiviso con autorizzazioni di LETTURA ed ELENCO per l’origine (se applicabile). Racchiudere la firma di accesso condiviso tra virgolette doppie, in quanto potrebbe contenere caratteri della riga di comando speciali.

Se la risorsa di origine è un contenitore BLOB e non viene fornita né una chiave né una firma di accesso condiviso, il contenitore BLOB viene letto tramite accesso anonimo.

Se l'origine è una tabella o una condivisione file, è necessario specificare una chiave o una firma di accesso condiviso.

**Applicabile a:** BLOB, file, tabelle

### <a name="s"></a>/S

Specifica la modalità ricorsiva per le operazioni di copia. In modalità ricorsiva, AzCopy copia tutti i BLOB o i file corrispondenti al criterio di file specificato, inclusi quelli nelle sottocartelle.

**Applicabile a:** BLOB, file

### <a name="blobtypeblock--page--append"></a>/BlobType:"block" | "page" | "append"

Specifica se il BLOB di destinazione è un BLOB in blocchi, un BLOB di pagine o un BLOB di accodamento. Questa opzione è applicabile solo per l'upload di un BLOB. In caso contrario, viene generato un errore. Se la destinazione è un BLOB e questa opzione non è specificata, per impostazione predefinita AzCopy crea un BLOB in blocchi.

**Applicabile a:** BLOB

### <a name="checkmd5"></a>/CheckMD5

Calcola un hash MD5 per i dati scaricati e verifica che l'hash MD5 archiviato nella proprietà Content-MD5 del BLOB o del file corrisponda all'hash calcolato. Il controllo MD5 è disattivato per impostazione predefinita, quindi è necessario specificare questa opzione per eseguire il controllo MD5 al download dei dati.

Si noti che Archiviazione di Azure non garantisce che l'hash MD5 archiviato per il BLOB o il file sia aggiornato. È responsabilità del client aggiornare l'hash MD5 ogni volta che il BLOB o il file viene modificato.

AzCopy imposta sempre la proprietà Content-MD5 per un BLOB o un file di Azure dopo averlo caricato nel servizio.  

**Applicabile a:** BLOB, file

### <a name="snapshot"></a>/Snapshot

Indica se trasferire gli snapshot o meno. Questa opzione è valida solo quando l'origine è un BLOB.

Gli snapshot di BLOB trasferiti vengono rinominati nel formato seguente: nome-BLOB (ora-snapshot).estensione.

Per impostazione predefinita, gli snapshot non vengono copiati.

**Applicabile a:** BLOB

### <a name="vverbose-log-file"></a>/V:[verbose-log-file]

Esegue l'output dei messaggi di stato dettagliati in un file di log.

Per impostazione predefinita, al file di log dettagliato viene assegnato il nome AzCopyVerbose.log in `%LocalAppData%\Microsoft\Azure\AzCopy`. Se per questa opzione si specifica un percorso di file esistente, al file viene aggiunto il log dettagliato.  

**Applicabile a:** BLOB, file, tabelle

### <a name="zjournal-file-folder"></a>/Z:[journal-file-folder]

Specifica la cartella del file journal per la ripresa di un'operazione.

AzCopy supporta sempre la ripresa di un'operazione interrotta.

Se questa opzione non è specificata o se è specificata senza un percorso di cartella, AzCopy crea il file journal nel percorso predefinito, ovvero %LocalAppData%\Microsoft\Azure\AzCopy.

Ogni volta che si invia un comando ad AzCopy, l'utilità verifica se il file journal è presente nella cartella predefinita o in una cartella specificata dall'utente tramite questa opzione. Se il file journal non viene trovato in tali percorsi, AzCopy considera l'operazione come nuova e quindi crea un nuovo file journal.

Se il file journal esiste, AzCopy verifica se la riga di comando di input corrisponde alla riga di comando nel file journal. Se le righe di comando corrispondono, AzCopy avvia la ripresa dell'operazione incompleta. Se non corrispondono, viene chiesto se si vuole sovrascrivere il file journal per avviare una nuova operazione o annullare l'operazione corrente.

Se l'operazione viene completata senza errori, il file journal viene eliminato.

Si noti che la ripresa di un'operazione da un file journal creato da una versione precedente di AzCopy non è supportata.

**Applicabile a:** BLOB, file, tabelle

### <a name="parameter-file"></a>/@:"parameter-file"

Specifica un file contenente parametri. AzCopy elabora i parametri nel file come se fossero stati specificati nella riga di comando.

In un file di risposta è possibile specificare più parametri su una sola riga o specificare un parametro su ogni riga. Si noti che un singolo parametro non può estendersi su più righe.

I file di risposta possono includere righe di commento che iniziano con il simbolo #.

È possibile specificare più file di risposta. Tuttavia, tenere presente che AzCopy non supporta file di risposta annidati.

**Applicabile a:** BLOB, file, tabelle

### <a name="y"></a>/Y

Elimina tutte le richieste di conferma di AzCopy. Questa opzione consente anche l'uso di token di firma di accesso condiviso in sola scrittura per scenari di caricamento dei dati, se non sono specificate le opzioni /XO e /XN.

**Applicabile a:** BLOB, file, tabelle

### <a name="l"></a>/L

Specifica solo un'operazione di elenco, non viene copiato alcun dato.

AzCopy interpreta l'utilizzo di questa opzione come una simulazione per l'esecuzione della riga di comando senza questa opzione /L e conta il numero di oggetti copiati. È possibile specificare contemporaneamente l'opzione /V per controllare quali oggetti vengono copiati nel log dettagliato.

Il comportamento di questa opzione è determinato dal percorso dei dati di origine e dalla presenza dell'opzione relativa alla modalità ricorsiva /S e dell’opzione di modello file /Pattern.

AzCopy richiede l'autorizzazione ELENCO e LETTURA per questo percorso di origine quando si utilizza questa opzione.

**Applicabile a:** BLOB, file

### <a name="mt"></a>/MT

Imposta l'ora dell'ultima modifica di un file scaricato sulla stessa ora del file o del BLOB di origine.

**Applicabile a:** BLOB, file

### <a name="xn"></a>/XN

Esclude una risorsa di origine più recente. La risorsa non viene copiata se l'ora dell'ultima modifica dell'origine è uguale alla destinazione o più recente.

**Applicabile a:** BLOB, file

### <a name="xo"></a>/XO
Esclude una risorsa di origine meno recente. La risorsa non viene copiata se l'ora dell'ultima modifica dell'origine è uguale alla destinazione o meno recente.

**Applicabile a:** BLOB, file

### <a name="a"></a>/A

Carica solo i file per i quali è impostato l'attributo Archive.

**Applicabile a:** BLOB, file

### <a name="iarashcnetoi"></a>/IA:[RASHCNETOI]

Carica solo i file per i quali sono impostati gli attributi specificati.

Gli attributi disponibili sono:

* R = File di sola lettura
* A = File pronti per l'archiviazione
* S = File di sistema
* H = File nascosti
* C = File compressi
* N = File normali
* E = File crittografati
* T = File temporanei
* O = File offline
* I = File non indicizzati

**Applicabile a:** BLOB, file

### <a name="xarashcnetoi"></a>/XA:[RASHCNETOI]

Esclude i file per i quali sono impostati gli attributi specificati.

Gli attributi disponibili sono:

* R = File di sola lettura
* A = File pronti per l'archiviazione
* S = File di sistema
* H = File nascosti
* C = File compressi
* N = File normali
* E = File crittografati
* T = File temporanei
* O = File offline
* I = File non indicizzati

**Applicabile a:** BLOB, file

### <a name="delimiterdelimiter"></a>/Delimiter:"delimiter"

Indica il delimitatore usato per delimitare le directory virtuali in un nome BLOB.

Per impostazione predefinita, AzCopy usa il carattere / come delimitatore. Tuttavia, a questo scopo supporta anche l'uso di caratteri comuni, ad esempio @, # o %. Se è necessario includere uno di questi caratteri speciali nella riga di comando, racchiudere il nome del file tra virgolette doppie.

Questa opzione si applica solo per il download di BLOB.

**Applicabile a:** BLOB

### <a name="ncnumber-of-concurrent-operations"></a>/NC:"number-of-concurrent-operations"

Specifica il numero di operazioni simultanee.

Per impostazione predefinita, AzCopy avvia un determinato numero di operazioni simultanee per aumentare la velocità effettiva di trasferimento dei dati. Si noti che un numero elevato di operazioni simultanee in un ambiente con una larghezza di banda ridotta potrebbe sovraccaricare la connessione di rete e impedire il corretto completamento delle operazioni. Limitare le operazioni simultanee in base alla larghezza di banda di rete effettivamente disponibile.

Il limite massimo per le operazioni simultanee è 512.

**Applicabile a:** BLOB, file, tabelle

### <a name="sourcetypeblob--table"></a>/SourceType:"Blob" | "Table"

Indica che la risorsa `source` è un BLOB disponibile nell'ambiente di sviluppo locale, in esecuzione nell'emulatore di archiviazione.

**Applicabile a:** BLOB, tabelle

### <a name="desttypeblob--table"></a>/DestType:"Blob" | "Table"

Indica che la risorsa `destination` è un BLOB disponibile nell'ambiente di sviluppo locale, in esecuzione nell'emulatore di archiviazione.

**Applicabile a:** BLOB, tabelle

### <a name="pkrskey1key2key3"></a>/PKRS:"key1#key2#key3#..."

Divide l'intervallo di chiavi di partizione per consentire l'esportazione dei dati della tabella in parallelo, aumentando la velocità dell'operazione di esportazione.

Se questa opzione non è specificata, AzCopy usa un solo thread per esportare le entità della tabella. Ad esempio, se l'utente specifica /PKRS:"aa#bb", AzCopy avvia tre operazioni simultanee.

Ogni operazione esporta uno dei tre intervalli di chiavi di partizione, nel modo seguente:

  [first-partition-key, aa)

  [aa, bb)

  [bb, last-partition-key]

**Applicabile a:** tabelle

### <a name="splitsizefile-size"></a>/SplitSize:"file-size"

Specifica la dimensione del file esportato diviso in MB, il valore minimo consentito è 32.

Se questa opzione non è specificata, AzCopy esporta i dati della tabella in un solo file.

Se i dati della tabella vengono esportati in un BLOB e le dimensioni del file esportato raggiungono il limite di 200 GB per le dimensioni del BLOB, AzCopy divide il file esportato, anche se questa opzione non viene specificata.

**Applicabile a:** tabelle

### <a name="entityoperationinsertorskip--insertormerge--insertorreplace"></a>/EntityOperation:"InsertOrSkip" | "InsertOrMerge" | "InsertOrReplace"

Specifica il comportamento di importazione dei dati della tabella.

* InsertOrSkip - Ignora un'entità esistente oppure ne inserisce una nuova se non esiste nella tabella.
* InsertOrMerge - Unisce un'entità esistente oppure ne inserisce una nuova se non esiste nella tabella.
* InsertOrReplace - Sostituisce un'entità esistente oppure ne inserisce una nuova se non esiste nella tabella.

**Applicabile a:** tabelle

### <a name="manifestmanifest-file"></a>/Manifest:"manifest-file"

Specifica il file manifesto per l'operazione di importazione ed esportazione della tabella.

Questa opzione è facoltativa durante l'operazione di esportazione. Se non viene specificata, AzCopy genera un file manifesto con il nome predefinito.

Questa opzione è necessaria durante l'operazione di importazione per individuare i file di dati.

**Applicabile a:** tabelle

### <a name="synccopy"></a>/SyncCopy

Indica se si desidera copiare BLOB o file in modo sincrono da un endpoint di Archiviazione di Azure a un altro.

Per impostazione predefinita, AzCopy esegue la copia in modo asincrono sul lato server. Specificare questa opzione per eseguire una copia sincrona, che scarica BLOB o file nella memoria locale e quindi li carica in Archiviazione di Azure.

È possibile utilizzare questa opzione quando si copiano file all'interno dell'archiviazione BLOB o dell'archiviazione file oppure quando i file vengono copiati dall'archiviazione BLOB all'archiviazione file o viceversa.

**Applicabile a:** BLOB, file

### <a name="setcontenttypecontent-type"></a>/SetContentType:"content-type"

Specifica il tipo di contenuto MIME per i BLOB o i file di destinazione.

Per impostazione predefinita, AzCopy imposta il tipo di contenuto per un BLOB o un file su application/octet-stream. Se si desidera impostare il tipo di contenuto per tutti i BLOB o i file, è possibile specificare in modo esplicito un valore per questa opzione.

Se si specifica questa opzione senza fornire un valore, AzCopy imposta il tipo di contenuto di ogni BLOB o file in base alla loro estensione.

**Applicabile a:** BLOB, file

### <a name="payloadformatjson--csv"></a>/PayloadFormat:"JSON" | "CSV"

Specifica il formato del file di dati tabella esportato.

Se questa opzione non è specificata, per impostazione predefinita AzCopy esporta il file di dati tabella nel formato JSON.

**Applicabile a:** tabelle

## <a name="known-issues-and-best-practices"></a>Problemi noti e procedure consigliate

Verranno ora illustrati alcuni problemi noti e procedure consigliate.

### <a name="limit-concurrent-writes-while-copying-data"></a>Limitare le scritture simultanee durante la copia dei dati

Durante la copia di BLOB o file con AzCopy, tenere presente che altre applicazioni potrebbero modificare i dati mentre è in corso la copia. Se possibile, assicurarsi che i dati copiati non vengano modificati durante l'operazione di copia. Ad esempio, se si copia un file VHD associato a una macchina virtuale di Azure, verificare che altre applicazioni non stiano scrivendo nel file VHD durante la copia. Un buon metodo per eseguire questa operazione è tramite leasing della risorsa da copiare. In alternativa, è prima possibile creare uno snapshot del file VHD e quindi copiare lo snapshot.

Se non è possibile evitare che altre applicazioni scrivano nei BLOB o nei file durante l'operazione di copia, tenere presente che al termine del processo la parità delle risorse copiate con le risorse di origine potrebbe non essere completa.

### <a name="run-one-azcopy-instance-on-one-machine"></a>Eseguire un'istanza di AzCopy su un computer.

AzCopy è progettato per massimizzare l'utilizzo della risorsa del computer per accelerare il trasferimento dei dati, è consigliabile eseguire solo un'istanza di AzCopy in un computer e specificare l'opzione `/NC` se sono necessarie più operazioni simultanee. Per ulteriori dettagli, digitare `AzCopy /?:NC` nella riga di comando.

### <a name="enable-fips-compliant-md5-algorithms-for-azcopy-when-you-use-fips-compliant-algorithms-for-encryption-hashing-and-signing"></a>Abilitare gli algoritmi MD5 conformi a FIPS per AzCopy quando "si usano algoritmi FIPS compatibili per crittografia, hash e firma".

Per impostazione predefinita, AzCopy usa l'implementazione di .NET MD5 per calcolare l'algoritmo MD5 durante la copia di oggetti, ma esistono alcuni requisiti di sicurezza per cui è necessario abilitare in AzCopy l'impostazione MD5 FIPS compatibile.

È possibile creare un file app.config `AzCopy.exe.config` con la proprietà `AzureStorageUseV1MD5` e conservarlo con AzCopy.exe.

    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
      <appSettings>
        <add key="AzureStorageUseV1MD5" value="false"/>
      </appSettings>
    </configuration>

Per la proprietà "AzureStorageUseV1MD5":

* True: valore predefinito, AzCopy usa l'implementazione di .NET MD5.
* False: AzCopy usa l'algoritmo MD5 FIPS compatibile.

Gli algoritmi FIPS compatibili sono disabilitati per impostazione predefinita in Windows. È possibile modificare questa impostazione dei criteri per il computer in uso. Nella finestra Esegui (Windows+R) digitare secpol.msc per aprire la finestra **Criteri di sicurezza locali**. Nella finestra **Impostazioni di sicurezza** passare a **Impostazioni di sicurezza** > **Criteri locali** > **Opzioni di sicurezza**. Individuare il criterio **Crittografia di sistema: utilizza algoritmi FIPS compatibili per crittografia, hash e firma**. Fare doppio clic sul criterio per visualizzare il valore nella colonna **Impostazione di sicurezza**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Archiviazione di Azure e AzCopy, vedere le risorse seguenti:

### <a name="azure-storage-documentation"></a>Documentazione di Archiviazione di Azure
* [Introduzione ad Archiviazione di Azure](../storage-introduction.md)
* [Come usare l'archiviazione BLOB da .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Come usare l'archiviazione file da .NET](../storage-dotnet-how-to-use-files.md)
* [Come usare l'archiviazione tabelle da .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Come creare, gestire o eliminare un account di archiviazione](../storage-create-storage-account.md)
* [Trasferire dati con AzCopy in Linux](storage-use-azcopy-linux.md)

### <a name="azure-storage-blog-posts"></a>Post del blog di Archiviazione di Azure:
* [Introduzione alla versione di anteprima della libreria per lo spostamento dei dati di Archiviazione di Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
* [AzCopy: introduzione alla copia sincrona e al tipo di contenuto personalizzato](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
* [AzCopy: annuncio della disponibilità per tutti di AzCopy 3.0 oltre alla versione di anteprima di AzCopy 4.0 con il supporto di tabelle e file](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
* [AzCopy: ottimizzazione per gli scenari di copia su larga scala](http://go.microsoft.com/fwlink/?LinkId=507682)
* [AzCopy:supporto per l'archiviazione con ridondanza geografica e accesso in lettura](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
* [AzCopy:trasferimento di dati con modalità riavviabile e token di firma di accesso condiviso](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
* [AzCopy: uso del comando di copia dei BLOB tra account](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
* [AzCopy: Caricamento e download di file per BLOB di Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)
