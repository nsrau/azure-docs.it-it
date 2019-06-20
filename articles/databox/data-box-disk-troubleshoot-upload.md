---
title: I problemi usando i log di caricamento dati sulla risoluzione dei problemi di Azure Data Box Disk | Microsoft Docs
description: Viene descritto come usare i log e risolvere gli errori riscontrati durante il caricamento dei dati in Azure Data Box Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: 6f3ac38c3eac968bd2f7ec2aada435466d3ff279
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148309"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Comprendere i log per risolvere i problemi di caricamento dati in Azure Data Box Disk

Questo articolo si applica a Microsoft Azure Data Box Disk e vengono descritti i problemi che viene visualizzato quando si caricano dati in Azure.

## <a name="data-upload-logs"></a>I log di caricamento dei dati

Quando i dati vengono caricati in Azure presso il Data Center, `_error.xml` e `_verbose.xml` vengono generati i file. Questi log vengono caricati lo stesso account di archiviazione usato per caricare i dati. Un esempio del `_error.xml` è illustrato di seguito.
    
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

## <a name="download-logs"></a>Scaricare i log

Esistono due modi per individuare e scaricare i log di diagnostica.

- Se si verificano errori durante il caricamento dei dati in Azure, il portale Visualizza il percorso di cartella in cui si trovano i log di diagnostica.

    ![Collegamento ai log nel portale](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

- Passare all'account di archiviazione associato il tuo ordine di Data Box. Passare a **Servizio BLOB > Esplora BLOB** e cercare il BLOB corrispondente all'account di archiviazione. Passare a **waies**.

    ![Log di copia 2](./media/data-box-disk-troubleshoot/data-box-disk-copy-logs2.png)

In ogni caso, vengono visualizzati i log degli errori e i log dettagliati. Selezionare ogni log e scaricare una copia locale.


## <a name="data-upload-errors"></a>Errori di caricamento dei dati

Nella tabella seguente sono riepilogati gli errori generati durante il caricamento dei dati in Azure.

| Codice di errore | Descrizione                        |
|-------------|------------------------------|
|`None` |  Completato correttamente.           |
|`Renamed` | È stato rinominato il blob.  |                                                            |
|`CompletedWithErrors` | Caricamento completato con errori. I dettagli dei file di errore sono incluse nel file di log.  |
|`Corrupted`|CRC calcolato durante l'inserimento di dati non corrisponde al CRC calcolato durante il caricamento.  |  
|`StorageRequestFailed` | Richiesta di archiviazione di Azure non è riuscita.   |     |
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
