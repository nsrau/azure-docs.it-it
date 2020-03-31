---
title: Risolvere i problemi relativi ai caricamenti di dati usando i logTroubleshoot data uploads using logs
titleSuffix: Azure Data Box Disk
description: Descrive come usare i log e risolvere i problemi riscontrati durante il caricamento dei dati su Azure Data Box Disk.Describes how to use the logs and troubleshoot issues seen when uploading data to Azure Data Box Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: 7c14988706ef193ef5da868c55f6c4f55e7d98f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260137"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Informazioni sui log per la risoluzione dei problemi di caricamento dei dati in Azure Data Box DiskUnderstand logs to troubleshoot data upload issues in Azure Data Box Disk

Questo articolo si applica al disco di Microsoft Azure Data Box e descrive i problemi che si verificano quando si caricano dati in Azure.This article applies to Microsoft Azure Data Box Disk and describes the issues you see when you upload data to Azure.

## <a name="about-upload-logs"></a>Informazioni sui log di caricamento

Quando i dati vengono caricati in `_error.xml` `_verbose.xml` Azure nel data center e vengono generati file per ogni account di archiviazione. Questi log vengono caricati nello stesso account di archiviazione usato per caricare i dati. 

Entrambi i log sono nello stesso formato e contengono descrizioni XML degli eventi che si sono verificati durante la copia dei dati dal disco all'account di Archiviazione di Azure.Both logs are in the same format and contain XML descriptions of the events that occurred while copying the data from the disk to the Azure Storage account.

Il log dettagliato contiene informazioni complete sullo stato dell'operazione di copia per ogni BLOB o file, mentre il log degli errori contiene solo le informazioni per i BLOB o i file che hanno rilevato errori durante il caricamento.

Il log degli errori ha la stessa struttura del log dettagliato, ma esclude le operazioni riuscite.

## <a name="download-logs"></a>Scaricare i log

Attenersi alla seguente procedura per individuare i log di caricamento.

1. Se si verificano errori durante il caricamento dei dati in Azure, il portale visualizza un percorso alla cartella in cui si trovano i log di diagnostica.

    ![Collegamento ai log nel portale](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. Vai a **rinuncia**.

    ![errori e registri dettagliati](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

In ogni caso, vengono visualizzati i log degli errori e i log dettagliati. Selezionare ogni registro e scaricare una copia locale.

## <a name="sample-upload-logs"></a>Esempi di log di caricamento

Di seguito `_verbose.xml` è riportato un esempio di . In questo caso, l'ordine è stato completato correttamente senza errori.

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Blob Status="Completed">
    <BlobPath>$root/botetapageblob.vhd</BlobPath>
    <FilePath>\PageBlob\botetapageblob.vhd</FilePath>
    <Length>1073742336</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <PageRangeList>
      <PageRange Offset="0" Length="4194304" Status="Completed" />
      <PageRange Offset="4194304" Length="4194304" Status="Completed" />
      <PageRange Offset="8388608" Length="4194304" Status="Completed" />
      --------CUT-------------------------------------------------------
      <PageRange Offset="1061158912" Length="4194304" Status="Completed" />
      <PageRange Offset="1065353216" Length="4194304" Status="Completed" />
      <PageRange Offset="1069547520" Length="4194304" Status="Completed" />
      <PageRange Offset="1073741824" Length="512" Status="Completed" />
    </PageRangeList>
  </Blob>
  <Blob Status="Completed">
    <BlobPath>$root/botetablockblob.txt</BlobPath>
    <FilePath>\BlockBlob\botetablockblob.txt</FilePath>
    <Length>19</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <BlockList>
      <Block Offset="0" Length="19" Status="Completed" />
    </BlockList>
  </Blob>
  <File Status="Completed">
    <FileStoragePath>botetaazurefilesfolder/botetaazurefiles.txt</FileStoragePath>
    <FilePath>\AzureFile\botetaazurefilesfolder\botetaazurefiles.txt</FilePath>
    <Length>20</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <FileRangeList>
      <FileRange Offset="0" Length="20" Status="Completed" />
    </FileRangeList>
  </File>
  <Status>Completed</Status>
</DriveLog>
```

Per lo stesso ordine, `_error.xml` un esempio di è mostrato di seguito.

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Summary>
    <ValidationErrors>
      <None Count="3" />
    </ValidationErrors>
    <CopyErrors>
      <None Count="3" Description="No errors encountered" />
    </CopyErrors>
  </Summary>
  <Status>Completed</Status>
</DriveLog>
```

Un esempio `_error.xml` di è mostrato di seguito dove l'ordine completato con errori. 

Il file di errore `Summary` in questo caso ha una sezione e un'altra sezione che contiene tutti gli errori a livello di file. 

Il `Summary` contiene `ValidationErrors` il `CopyErrors`e il . In questo caso, 8 file o cartelle sono stati caricati in Azure e non si sono verificati errori di convalida. Quando i dati sono stati copiati nell'account di archiviazione di Azure, 5 file o cartelle caricati correttamente. I restanti 3 file o cartelle sono stati rinominati in base alle convenzioni di denominazione del contenitore di Azure e quindi caricati correttamente in Azure.The remaining 3 files or folders were renamed as by the Azure container naming conventions and then uploaded successfully to Azure.

Lo stato a `BlobStatus` livello di file si trova in questo articolo che descrive le azioni eseguite per caricare i BLOB. In questo caso, tre contenitori vengono rinominati perché le cartelle in cui sono stati copiati i dati non sono conformi alle convenzioni di denominazione di Azure per i contenitori. Per i BLOB caricati in tali contenitori, sono inclusi il nuovo nome del contenitore, il percorso del BLOB in Azure, il percorso del file non valido originale e le dimensioni del BLOB.
    
```xml
 <?xml version="1.0" encoding="utf-8"?>
    <DriveLog Version="2018-10-01">
      <DriveId>18041C582D7E</DriveId>
      <Summary>
     <!--Summary for validation and data copy to Azure -->
        <ValidationErrors>
          <None Count="8" />
        </ValidationErrors>
        <CopyErrors>
          <Completed Count="5" Description="No errors encountered" />
          <ContainerRenamed Count="3" Description="Renamed the container as the original container name does not follow Azure conventions." />
        </CopyErrors>
      </Summary>
    <!--List of renamed containers with the new names, new file path in Azure, original invalid file path, and size -->
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/private.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\private.vhd</OriginalFilePath>
        <SizeInBytes>10490880</SizeInBytes>
      </Blob>
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/resource.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\resource.vhd</OriginalFilePath>
        <SizeInBytes>71528448</SizeInBytes>
      </Blob>
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/role.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\role.vhd</OriginalFilePath>
        <SizeInBytes>10490880</SizeInBytes>
      </Blob>
      <Status>CompletedWithErrors</Status>
    </DriveLog>
```

## <a name="data-upload-errors"></a>Errori di caricamento dei dati

Gli errori generati durante il caricamento dei dati in Azure sono riepilogati nella tabella seguente.

| Codice di errore | Descrizione                   |
|-------------|------------------------------|
|`None` |  Completata.           |
|`Renamed` | Rinominato correttamente il BLOB.   |
|`CompletedWithErrors` | Caricamento completato con errori. I dettagli dei file in errore sono inclusi nel file di registro.  |
|`Corrupted`|Il CRC calcolato durante l'inserimento dei dati non corrisponde al CRC calcolato durante il caricamento.  |  
|`StorageRequestFailed` | Richiesta di archiviazione di Azure non riuscita.   |     
|`LeasePresent` | Questo elemento è in lizza e viene utilizzato da un altro utente. |
|`StorageRequestForbidden` |Impossibile caricare a causa di problemi di autenticazione. |
|`ManagedDiskCreationTerminalFailure` | Impossibile caricare come dischi gestiti. I file sono disponibili nell'account di archiviazione di gestione temporanea come BLOB di pagine. È possibile convertire manualmente i BLOB di pagine in dischi gestiti.  |
|`DiskConversionNotStartedTierInfoMissing` | Poiché il file VHD è stato copiato all'esterno delle cartelle di livello precreate, non è stato creato un disco gestito. Il file viene caricato come BLOB di pagine nell'account di archiviazione di gestione temporanea come specificato durante la creazione dell'ordine. È possibile convertirlo manualmente in un disco gestito.|
|`InvalidWorkitem` | Impossibile caricare i dati perché non sono conformi alle convenzioni di denominazione e limite di Azure.|
|`InvalidPageBlobUploadAsBlockBlob` | Caricato come BLOB di blocchi in `databoxdisk-invalid-pb-`un contenitore con prefisso .|
|`InvalidAzureFileUploadAsBlockBlob` | Caricato come BLOB di blocchi in `databoxdisk-invalid-af`un contenitore con prefisso -.|
|`InvalidManagedDiskUploadAsBlockBlob` | Caricato come BLOB di blocchi in `databoxdisk-invalid-md`un contenitore con prefisso -.|
|`InvalidManagedDiskUploadAsPageBlob` |Caricato come BLOB di pagine in `databoxdisk-invalid-md-`un contenitore con prefisso . |
|`MovedToOverflowShare` |I file caricati in una nuova condivisione quando la dimensione della condivisione originale ha superato il limite massimo di dimensioni di Azure.Uploaded files to a new share as the original share size exceeded the maximum Azure size limit. Il nuovo nome di condivisione file `-2`ha il nome originale suffisso .   |
|`MovedToDefaultAzureShare` |File caricati che non facevano parte di alcuna cartella in una condivisione predefinita. Il nome della `databox-`condivisione inizia con . |
|`ContainerRenamed` |Il contenitore per questi file non è conforme alle convenzioni di denominazione di Azure e viene rinominato. Il nuovo nome `databox-` inizia con e viene associato con l'hash SHA1 del nome originale |
|`ShareRenamed` |La condivisione per questi file non è conforme alle convenzioni di denominazione di Azure e viene rinominata. Il nuovo nome `databox-` inizia con e viene suffisso con l'hash SHA1 del nome originale. |
|`BlobRenamed` |Questi file non erano conformi alle convenzioni di denominazione di Azure e sono stati rinominati. Controllare `BlobPath` il campo per il nuovo nome. |
|`FileRenamed` |Questi file non erano conformi alle convenzioni di denominazione di Azure e sono stati rinominati. Controllare `FileStoragePath` il campo per il nuovo nome. |
|`DiskRenamed` |Questi file non erano conformi alle convenzioni di denominazione di Azure e sono stati rinominati. Controllare `BlobPath` il campo per il nuovo nome. |


## <a name="next-steps"></a>Passaggi successivi

- [Aprire un ticket](data-box-disk-contact-microsoft-support.md)di supporto per problemi relativi al Disco Data Box .
