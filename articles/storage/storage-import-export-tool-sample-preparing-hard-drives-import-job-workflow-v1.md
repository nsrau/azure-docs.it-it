---
title: Flusso di lavoro campione per preparare i dischi rigidi per un processo di importazione | Microsoft Docs
description: "Viene illustrata una procedura dettagliata e completa su come preparare le unità per un processo di importazione nel servizio di Importazione/Esportazione di Azure"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 6eb1b1b7-c69f-4365-b5ef-3cd5e05eb72a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: fbcc295284d060b9a8bc5cf1cb9f8b2fca566db4
ms.openlocfilehash: 775deb2717e5062e8eb4be7f10dd5bf3c2682f1b


---

# <a name="sample-workflow-to-prepare-hard-drives-for-an-import-job"></a>Flusso di lavoro campione per preparare i dischi rigidi per un processo di importazione
In questo argomento viene illustrato il processo completo di preparazione delle unità per un processo di importazione.  
  
In questo esempio verranno importati i seguenti dati di un account di archiviazione Windows Azure denominato `mystorageaccount`:  
  
|Percorso|Descrizione|  
|--------------|-----------------|  
|H:\Video|Una raccolta di video, 5 TB in totale.|  
|H:\Photo|Una raccolta di foto, 30 GB in totale.|  
|K:\Temp\FavoriteMovie.ISO|Un'immagine di disco Blu-ray™, 25 GB.|  
|\\\bigshare\john\music|Una raccolta di file musicali in una condivisione di rete, 10 GB in totale.|  
  
Il processo importerà questi dati nelle seguenti destinazioni nell'account di archiviazione:  
  
|Sorgente|BLOB o directory virtuale di destinazione|  
|------------|-------------------------------------------|  
|H:\Video|https://mystorageaccount.blob.core.windows.net/video|  
|H:\Photo|https://mystorageaccount.blob.core.windows.net/photo|  
|K:\Temp\FavoriteMovie.ISO|https://mystorageaccount.blob.core.windows.net/favorite/FavoriteMovies.ISO|  
|\\\bigshare\john\music|https://mystorageaccount.blob.core.windows.net/music|  
  
Con questo mapping, il file `H:\Video\Drama\GreatMovie.mov` verrà importato nel BLOB `https://mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov`.  
  
Successivamente, per determinare il numero di dischi rigidi necessari, calcolare la dimensione dei dati:  
  
`5TB + 30GB + 25GB + 10GB = 5TB + 65GB`  
  
Per questo esempio, dovrebbero essere sufficienti due dischi rigidi da 3 TB. Tuttavia, poiché la directory di origine `H:\Video` include 5 TB di dati e la capacità del singolo disco rigido è di soli 3 TB, è necessario suddividere `H:\Video` in due directory di dimensioni minori prima di eseguire lo strumento di Importazione/Esportazione di Microsoft Azure: `H:\Video1` e `H:\Video2`. Questo passaggio genera le seguenti directory di origine:  
  
|Percorso|Dimensione|BLOB o directory virtuale di destinazione|  
|--------------|----------|-------------------------------------------|  
|H:\Video1|2,5 TB|https://mystorageaccount.blob.core.windows.net/video|  
|H:\Video2|2,5 TB|https://mystorageaccount.blob.core.windows.net/video|  
|H:\Photo|30 GB|https://mystorageaccount.blob.core.windows.net/photo|  
|K:\Temp\FavoriteMovies.ISO|25 GB|https://mystorageaccount.blob.core.windows.net/favorite/FavoriteMovies.ISO|  
|\\\bigshare\john\music|10 GB|https://mystorageaccount.blob.core.windows.net/music|  
  
 Si noti che anche se la directory `H:\Video` è stata suddivisa in due, queste ultime puntano alla stessa directory virtuale di destinazione nell'account di archiviazione. In questo modo, tutti i file video verranno conservati in un singolo contenitore `video` nell'account di archiviazione.  
  
 Successivamente, le directory di origine indicate in precedenza vengono distribuite uniformemente nei due dischi rigidi:  
  
||||  
|-|-|-|  
|Disco rigido|Directory di origine|Dimensioni totali|  
|Prima unità|H:\Video1|2,5 TB + 30 GB|  
||H:\Photo||  
|Seconda unità|H:\Video2|2,5 TB + 35 GB|  
||K:\Temp\BlueRay.ISO||  
||\\\bigshare\john\music||  
  
Inoltre, è possibile impostare i metadati seguenti per tutti i file:  
  
-   **UploadMethod:** servizio di Importazione/Esportazione di Windows Azure  
  
-   **DataSetName:** SampleData  
  
-   **CreationDate:** 10/1/2013  
  
Per impostare i metadati per i file importati, creare un file di testo, `c:\WAImportExport\SampleMetadata.txt`, con il contenuto seguente:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
    <UploadMethod>Windows Azure Import/Export Service</UploadMethod>  
    <DataSetName>SampleData</DataSetName>  
    <CreationDate>10/1/2013</CreationDate>  
</Metadata>  
```
  
È inoltre possibile impostare alcune proprietà per il BLOB `FavoriteMovie.ISO`:  
  
-   **Content-Type:** application/octet-stream  
  
-   **Content-MD5:** Q2hlY2sgSW50ZWdyaXR5IQ==  
  
-   **Cache-Control:** no-cache  
  
Per impostare queste proprietà, creare un file di testo, `c:\WAImportExport\SampleProperties.txt`:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
    <Content-Type>application/octet-stream</Content-Type>  
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>  
    <Cache-Control>no-cache</Cache-Control>  
</Properties>  
```
  
A questo punto è possibile eseguire lo strumento di Importazione/Esportazione di Azure per preparare i due dischi rigidi. Si noti che:  
  
-   La prima unità viene montata come unità X.  
  
-   La seconda unità viene montata come unità Y.  
  
-   La chiave per l'account di archiviazione `mystorageaccount` è `8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg==`.  

## <a name="preparing-disk-for-import-when-data-is-pre-loaded"></a>Preparazione del disco per l'importazione con dati precaricati
 
 Se i dati da importare sono già presenti sul disco, usare il flag /skipwrite. I valori di /t e /srcdir devono puntare entrambi al disco in preparazione per l'importazione. Se non tutti i dati su disco devono essere trasferiti nella stessa directory virtuale di destinazione o root dell'account di archiviazione, eseguire il medesimo comando separatamente per ciascuna directory, mantenendo identico il valore di /id nelle varie esecuzioni.

>[!NOTE] 
>Non specificare /format in quanto comporterà l'eliminazione dei dati su disco. È possibile specificare /encrypt o /bk a seconda che il disco sia già crittografato o meno. 
>

```
    When data is already present on the disk for each drive run the following command.
    WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:x:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt /skipwrite
```

Per la prima unità, eseguire due volte lo strumento di Importazione/Esportazione di Azure per copiare le due directory di origine:  
  
```
## First copy session for first drive  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:H:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```

```  
## Second copy session for first drive  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Photo /srcdir:H:\Photo /dstdir:photo/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt
```
  
Per la seconda unità, eseguire tre volte lo strumento di Importazione/Esportazione di Azure, una volta per ciascuna delle due directory di origine e una volta per il file di immagine Blu-Ray™ autonomo:  
  
```
## First copy session  
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:Video2 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:y /format /encrypt /srcdir:H:\Video2 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```
  
```
## Second copy session  
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:Music /srcdir:\\bigshare\john\music /dstdir:music/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```  
  
```
## Third copy session  
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:BlueRayIso /srcfile:K:\Temp\BlueRay.ISO /dstblob:favorite/BlueRay.ISO /MetadataFile:c:\WAImportExport\SampleMetadata.txt /PropertyFile:c:\WAImportExport\SampleProperties.txt  
```
  
Dopo aver completato le sessioni di copia, è possibile disconnettere le due unità dal computer di copia e spedirle al data center di Windows Azure appropriato. I due file journal, `FirstDrive.jrn` e `SecondDrive.jrn`, dovranno essere caricati durante la creazione del processo di importazione nel [portale di gestione di Windows Azure](https://manage.windowsazure.com/).  
  
## <a name="see-also"></a>Vedere anche  
[Preparing Hard Drives for an Import Job](storage-import-export-tool-preparing-hard-drives-import-v1.md)  (Preparazione dei dischi rigidi per un processo di importazione)  
[Quick Reference for Frequently Used Commands](storage-import-export-tool-quick-reference-v1.md) (Riferimento rapido per i comandi usati più di frequente) 



<!--HONumber=Dec16_HO3-->


