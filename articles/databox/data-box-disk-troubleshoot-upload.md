---
title: Risolvere i problemi di caricamento dei dati tramite log
titleSuffix: Azure Data Box Disk
description: Viene descritto come usare i log e risolvere i problemi riscontrati durante il caricamento dei dati in Azure Data Box Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: 7c14988706ef193ef5da868c55f6c4f55e7d98f9
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471976"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Informazioni sui log per la risoluzione dei problemi di caricamento dei dati in Azure Data Box Disk

Questo articolo si applica a Microsoft Azure Data Box Disk e descrive i problemi che si verificano durante il caricamento dei dati in Azure.

## <a name="about-upload-logs"></a>Informazioni sui log di caricamento

Quando i dati vengono caricati in Azure nel Data Center, i file di `_error.xml` e di `_verbose.xml` vengono generati per ogni account di archiviazione. Questi log vengono caricati nello stesso account di archiviazione usato per caricare i dati. 

Entrambi i registri hanno lo stesso formato e contengono le descrizioni XML degli eventi che si sono verificati durante la copia dei dati dal disco all'account di archiviazione di Azure.

Il log dettagliato contiene informazioni complete sullo stato dell'operazione di copia per ogni BLOB o file, mentre il log degli errori contiene solo le informazioni per i BLOB o i file che hanno rilevato errori durante il caricamento.

Il log degli errori presenta la stessa struttura del log dettagliato, ma filtra le operazioni riuscite.

## <a name="download-logs"></a>Scaricare i log

Per individuare i log di caricamento, seguire questa procedura.

1. Se si verificano errori durante il caricamento dei dati in Azure, nel portale viene visualizzato il percorso della cartella in cui si trovano i log di diagnostica.

    ![Collegamento ai log nel portale](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. Passare a **waies**.

    ![log di errore e dettagliati](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

In ogni caso vengono visualizzati i log degli errori e i log dettagliati. Selezionare ogni log e scaricare una copia locale.

## <a name="sample-upload-logs"></a>Esempio di log di caricamento

Di seguito è riportato un esempio di `_verbose.xml`. In questo caso, l'ordine è stato completato correttamente senza errori.

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

Per lo stesso ordine, di seguito è riportato un esempio di `_error.xml`.

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

Di seguito è riportato un esempio di `_error.xml`, in cui l'ordine è stato completato con errori. 

Il file degli errori in questo caso include una sezione `Summary` e un'altra sezione che contiene tutti gli errori a livello di file. 

Il `Summary` contiene i `ValidationErrors` e il `CopyErrors`. In questo caso, 8 file o cartelle sono stati caricati in Azure e non si sono verificati errori di convalida. Quando i dati sono stati copiati nell'account di archiviazione di Azure, 5 file o cartelle sono stati caricati correttamente. I 3 file o le cartelle rimanenti sono stati rinominati in base alle convenzioni di denominazione dei contenitori di Azure e quindi caricati in Azure.

Lo stato a livello di file si trova in `BlobStatus` che descrive le azioni eseguite per caricare i BLOB. In questo caso, tre contenitori vengono rinominati perché le cartelle in cui sono stati copiati i dati non sono conformi alle convenzioni di denominazione di Azure per i contenitori. Per i BLOB caricati in questi contenitori, il nome del nuovo contenitore, il percorso del BLOB in Azure, il percorso file originale non valido e le dimensioni del BLOB sono inclusi.
    
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
|`None` |  Operazione completata.           |
|`Renamed` | Il BLOB è stato rinominato.   |
|`CompletedWithErrors` | Caricamento completato con errori. I dettagli dei file in errore sono inclusi nel file di log.  |
|`Corrupted`|Il CRC calcolato durante l'inserimento dei dati non corrisponde al CRC calcolato durante il caricamento.  |  
|`StorageRequestFailed` | La richiesta di archiviazione di Azure non è riuscita.   |     
|`LeasePresent` | Questo elemento è in lease e viene usato da un altro utente. |
|`StorageRequestForbidden` |Non è stato possibile caricare a causa di problemi di autenticazione. |
|`ManagedDiskCreationTerminalFailure` | Non è stato possibile caricare come dischi gestiti. I file sono disponibili nell'account di archiviazione di staging come BLOB di pagine. È possibile convertire manualmente i BLOB di pagine in dischi gestiti.  |
|`DiskConversionNotStartedTierInfoMissing` | Poiché il file VHD è stato copiato al di fuori delle cartelle di livello PreCreate, non è stato creato un disco gestito. Il file viene caricato come BLOB di pagine nell'account di archiviazione di staging come specificato durante la creazione dell'ordine. È possibile convertirlo manualmente in un disco gestito.|
|`InvalidWorkitem` | Non è stato possibile caricare i dati perché non sono conformi alle convenzioni di denominazione e limiti di Azure.|
|`InvalidPageBlobUploadAsBlockBlob` | Caricato come BLOB in blocchi in un contenitore con prefisso `databoxdisk-invalid-pb-`.|
|`InvalidAzureFileUploadAsBlockBlob` | Caricato come BLOB in blocchi in un contenitore con prefisso `databoxdisk-invalid-af`-.|
|`InvalidManagedDiskUploadAsBlockBlob` | Caricato come BLOB in blocchi in un contenitore con prefisso `databoxdisk-invalid-md`-.|
|`InvalidManagedDiskUploadAsPageBlob` |Caricati come BLOB di pagine in un contenitore con prefisso `databoxdisk-invalid-md-`. |
|`MovedToOverflowShare` |I file caricati in una nuova condivisione come dimensioni della condivisione originale superano il limite massimo di dimensioni di Azure. Il nome originale del nuovo nome della condivisione file è suffisso con `-2`.   |
|`MovedToDefaultAzureShare` |File caricati che non facevano parte di alcuna cartella in una condivisione predefinita. Il nome della condivisione inizia con `databox-`. |
|`ContainerRenamed` |Il contenitore per questi file non è conforme alle convenzioni di denominazione di Azure ed è stato rinominato. Il nuovo nome inizia con `databox-` ed è suffisso con l'hash SHA1 del nome originale |
|`ShareRenamed` |La condivisione per questi file non è conforme alle convenzioni di denominazione di Azure ed è stata rinominata. Il nuovo nome inizia con `databox-` e con il suffisso hash SHA1 del nome originale. |
|`BlobRenamed` |Questi file non sono conformi alle convenzioni di denominazione di Azure e sono stati rinominati. Selezionare il campo `BlobPath` per il nuovo nome. |
|`FileRenamed` |Questi file non sono conformi alle convenzioni di denominazione di Azure e sono stati rinominati. Selezionare il campo `FileStoragePath` per il nuovo nome. |
|`DiskRenamed` |Questi file non sono conformi alle convenzioni di denominazione di Azure e sono stati rinominati. Selezionare il campo `BlobPath` per il nuovo nome. |


## <a name="next-steps"></a>Passaggi successivi

- [Aprire un ticket di supporto per data box disk problemi](data-box-disk-contact-microsoft-support.md).
