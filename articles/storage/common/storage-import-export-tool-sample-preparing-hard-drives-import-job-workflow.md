---
title: Flusso di lavoro di esempio per la preparazione dei dischi rigidi per un processo di importazione in Importazione/Esportazione di Azure | Documentazione Microsoft
description: "Viene illustrata una procedura dettagliata e completa per la preparazione delle unità per un processo di importazione nel servizio Importazione/Esportazione di Azure."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: muralikk
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 83cb82b9807718e7a509312d159eb766a5da1d2c
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---

# <a name="sample-workflow-to-prepare-hard-drives-for-an-import-job"></a>Flusso di lavoro campione per preparare i dischi rigidi per un processo di importazione

In questo articolo viene illustrato il processo completo di preparazione delle unità per un processo di importazione.

## <a name="sample-data"></a>Dati di esempio

In questo esempio verranno importati i seguenti dati di un account di archiviazione Azure denominato `mystorageaccount`:

|Percorso|Descrizione|Dimensioni dei dati|
|--------------|-----------------|-----|
|H:\Video\ |Una raccolta di video|12 TB|
|H:\Photo\ |Una raccolta di foto|30 GB|
|K:\Temp\FavoriteMovie.ISO|Un'immagine di disco Blu-ray™|25 GB|
|\\\bigshare\john\music\ |Una raccolta di file musicali in una condivisione di rete|10 GB|

## <a name="storage-account-destinations"></a>Destinazioni degli account di archiviazione

Il processo importerà i dati nelle seguenti destinazioni nell'account di archiviazione:

|Sorgente|BLOB o directory virtuale di destinazione|
|------------|-------------------------------------------|
|H:\Video\ |video/|
|H:\Photo\ |photo/|
|K:\Temp\FavoriteMovie.ISO|favorite/FavoriteMovies.ISO|
|\\\bigshare\john\music\ |music|

Con questo mapping, il file `H:\Video\Drama\GreatMovie.mov` verrà importato nel BLOB `https://mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov`.

## <a name="determine-hard-drive-requirements"></a>Determinare i requisiti del disco rigido

Successivamente, per determinare il numero di dischi rigidi necessari, calcolare la dimensione dei dati:

`12TB + 30GB + 25GB + 10GB = 12TB + 65GB`

Per questo esempio, dovrebbero essere sufficienti due dischi rigidi da 8 TB. Poiché tuttavia la directory di origine `H:\Video` contiene 12 TB di dati e la capacità del singolo disco rigido è di soli 8 TB, sarà possibile specificarlo nel modo seguente nel file **driveset.csv**:

```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
X,Format,SilentMode,Encrypt,
Y,Format,SilentMode,Encrypt,
```
Lo strumento distribuirà dati nei due dischi rigidi in modo ottimizzato.

## <a name="attach-drives-and-configure-the-job"></a>Collegare le unità e configurare il processo
Si collegheranno entrambi i dischi alla macchina e si creeranno i volumi. Creare quindi il file **dataset.csv**:
```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
H:\Video\,video/,BlockBlob,rename,None,H:\mydirectory\properties.xml
H:\Photo\,photo/,BlockBlob,rename,None,H:\mydirectory\properties.xml
K:\Temp\FavoriteVideo.ISO,favorite/FavoriteVideo.ISO,BlockBlob,rename,None,H:\mydirectory\properties.xml
\\myshare\john\music\,music/,BlockBlob,rename,None,H:\mydirectory\properties.xml
```

Inoltre, è possibile impostare i metadati seguenti per tutti i file:

* **UploadMethod:** servizio Importazione/Esportazione di Windows Azure
* **DataSetName:** SampleData
* **CreationDate:** 10/1/2013

Per impostare i metadati per i file importati, creare un file di testo, `c:\WAImportExport\SampleMetadata.txt`, con il contenuto seguente:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Metadata>
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>
    <DataSetName>SampleData</DataSetName>
    <CreationDate>10/1/2013</CreationDate>
</Metadata>
```

È inoltre possibile impostare alcune proprietà per il BLOB `FavoriteMovie.ISO`:

* **Content-Type:** application/octet-stream
* **Content-MD5:** Q2hlY2sgSW50ZWdyaXR5IQ==
* **Cache-Control:** no-cache

Per impostare queste proprietà, creare un file di testo, `c:\WAImportExport\SampleProperties.txt`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Properties>
    <Content-Type>application/octet-stream</Content-Type>
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>
    <Cache-Control>no-cache</Cache-Control>
</Properties>
```

## <a name="run-the-azure-importexport-tool-waimportexportexe"></a>Eseguire lo strumento di Importazione/Esportazione di Azure (WAImportExport.exe)

A questo punto è possibile eseguire lo strumento di Importazione/Esportazione di Azure per preparare i due dischi rigidi.

**Per la prima sessione:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

Se è necessario aggiungere altri dati, creare un altro file di set di dati (stesso formato di Initialdataset).

**Per la seconda sessione:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

Dopo aver completato le sessioni di copia, è possibile disconnettere le due unità dal computer di copia e spedirle al data center di Azure appropriato. I due file journal, `<FirstDriveSerialNumber>.xml` e `<SecondDriveSerialNumber>.xml`, dovranno essere caricati durante la creazione del processo di importazione nel portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

* [Preparing hard drives for an import job](../storage-import-export-tool-preparing-hard-drives-import.md) (Preparazione dei dischi rigidi per un processo di importazione)
* [Quick reference for frequently used commands](../storage-import-export-tool-quick-reference.md) (Riferimento rapido per i comandi usati più di frequente)

