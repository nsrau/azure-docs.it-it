---
title: I problemi usando i log di caricamento dati sulla risoluzione dei problemi di Azure Data Box Disk | Microsoft Docs
description: Viene descritto come usare i log e risolvere gli errori riscontrati durante il caricamento dei dati in Azure Data Box Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: deaa9a220ee4d765650779b40742225e300ffdb7
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807510"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Comprendere i log per risolvere i problemi di caricamento dati in Azure Data Box Disk

Questo articolo si applica a Microsoft Azure Data Box Disk e vengono descritti i problemi che viene visualizzato quando si caricano dati in Azure.

## <a name="about-upload-logs"></a>Informazioni sui log di caricamento

Quando i dati vengono caricati in Azure presso il Data Center, `_error.xml` e `_verbose.xml` vengono generati file per ogni account di archiviazione. Questi log vengono caricati lo stesso account di archiviazione usato per caricare i dati. 

Entrambi i log sono nello stesso formato e contengono le descrizioni XML degli eventi che si sono verificati durante la copia dei dati dal disco nell'account di archiviazione di Azure.

Il log dettagliato contiene informazioni complete sullo stato dell'operazione di copia per ogni blob o file, mentre il log degli errori contiene solo le informazioni per i BLOB o file di cui si sono verificati errori durante il caricamento.

Il log degli errori presenta la stessa struttura come il log dettagliato, ma esclude le operazioni completate correttamente.

## <a name="download-logs"></a>Scaricare i log

Eseguire i passaggi seguenti per individuare i log di caricamento.

1. Se si verificano errori durante il caricamento dei dati in Azure, il portale Visualizza il percorso di cartella in cui si trovano i log di diagnostica.

    ![Collegamento ai log nel portale](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. Passare a **waies**.

    ![errore e i log dettagliati](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

In ogni caso, vengono visualizzati i log degli errori e i log dettagliati. Selezionare ogni log e scaricare una copia locale.

## <a name="sample-upload-logs"></a>Esempio carica i log

Un esempio del `_verbose.xml` è illustrato di seguito. In questo caso, l'ordine è stata completata senza errori.

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

Per l'ordine stesso, un campione del `_error.xml` è illustrato di seguito.

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

Un esempio del `_error.xml` dove di seguito è riportato l'ordine completata con errori. 

Il file degli errori in questo caso ha un `Summary` sezione e un'altra sezione che contiene tutti i file a livello di errori. 

Il `Summary` contiene il `ValidationErrors` e il `CopyErrors`. In questo caso, 8 file o cartelle sono state caricate in Azure e nessun errore di convalida. Quando i dati siano stati copiati in account di archiviazione di Azure, 5 file o cartelle caricato correttamente. Le restanti 3 file o cartelle sono state rinominate in base alle convenzioni di denominazione di contenitori di Azure e quindi caricate correttamente in Azure.

Lo stato a livello di file sono in `BlobStatus` che descrive eventuali azioni eseguite per caricare i BLOB. Tre contenitori vengono rinominati in questo caso, poiché le cartelle in cui è stati copiati i dati non sono conforme alle convenzioni di denominazione Azure per contenitori. Per i BLOB caricati in tali contenitori, sono inclusi il nuovo nome del contenitore, percorso del blob in Azure, il percorso file non valido originale e le dimensioni del blob.
    
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

Nella tabella seguente sono riepilogati gli errori generati durante il caricamento dei dati in Azure.

| Codice di errore | Descrizione                   |
|-------------|------------------------------|
|`None` |  Completato correttamente.           |
|`Renamed` | È stato rinominato il blob.   |
|`CompletedWithErrors` | Caricamento completato con errori. I dettagli dei file di errore sono incluse nel file di log.  |
|`Corrupted`|CRC calcolato durante l'inserimento di dati non corrisponde al CRC calcolato durante il caricamento.  |  
|`StorageRequestFailed` | Richiesta di archiviazione di Azure non è riuscita.   |     
|`LeasePresent` | Questo elemento è associato a un lease e viene utilizzato da un altro utente. |
|`StorageRequestForbidden` |Impossibile caricare a causa di problemi di autenticazione. |
|`ManagedDiskCreationTerminalFailure` | Non è stato possibile caricare come dischi gestiti. I file sono disponibili nell'account di archiviazione di staging come BLOB di pagine. È possibile convertire manualmente i BLOB di pagine in dischi gestiti.  |
|`DiskConversionNotStartedTierInfoMissing` | Poiché è stato copiato il file di disco rigido virtuale all'esterno delle cartelle di livello creati in precedenza, non è stato creato un disco gestito. Il file viene caricato come blob di pagine per l'account di archiviazione di staging come specificato durante la creazione dell'ordine. È possibile convertirlo manualmente in un disco gestito.|
|`InvalidWorkitem` | Impossibile caricare i dati perché non è conforme alla denominazione di Azure e limiti di convenzioni.|
|`InvalidPageBlobUploadAsBlockBlob` | Caricato come BLOB in blocchi in un contenitore con prefisso `databoxdisk-invalid-pb-`.|
|`InvalidAzureFileUploadAsBlockBlob` | Caricato come BLOB in blocchi in un contenitore con prefisso `databoxdisk-invalid-af`-.|
|`InvalidManagedDiskUploadAsBlockBlob` | Caricato come BLOB in blocchi in un contenitore con prefisso `databoxdisk-invalid-md`-.|
|`InvalidManagedDiskUploadAsPageBlob` |Caricato come BLOB di pagine in un contenitore con prefisso `databoxdisk-invalid-md-`. |
|`MovedToOverflowShare` |I file caricati in una nuova condivisione come le dimensioni della condivisione originale ha superato il limite massimo delle dimensioni di Azure. Il nuovo nome della condivisione file con il nome originale con suffisso `-2`.   |
|`MovedToDefaultAzureShare` |File caricati che non sono stati una parte di qualsiasi cartella da una condivisione predefinita. Il nome della condivisione inizia con `databox-`. |
|`ContainerRenamed` |Il contenitore per questi file non è conforme alle convenzioni di denominazione di Azure e viene rinominato. Il nuovo nome inizia con `databox-` e viene aggiunto il suffisso l'hash SHA1 del nome originale |
|`ShareRenamed` |La condivisione per questi file non è conforme alle convenzioni di denominazione di Azure e viene rinominata. Il nuovo nome inizia con `databox-` e viene aggiunto il suffisso l'hash SHA1 del nome originale. |
|`BlobRenamed` |Questi file non sono conforme alle convenzioni di denominazione di Azure e sono stati rinominati. Controllare il `BlobPath` campo per il nuovo nome. |
|`FileRenamed` |Questi file non sono conforme alle convenzioni di denominazione di Azure e sono stati rinominati. Controllare il `FileStoragePath` campo per il nuovo nome. |
|`DiskRenamed` |Questi file non sono conforme alle convenzioni di denominazione di Azure e sono stati rinominati. Controllare il `BlobPath` campo per il nuovo nome. |


## <a name="next-steps"></a>Passaggi successivi

- [Aprire un ticket di supporto per problemi di Data Box Disk](data-box-disk-contact-microsoft-support.md).
