---
title: Risoluzione dei problemi di Azure Data Box Disk | Microsoft Docs
description: Descrive come risolvere i problemi riscontrati in Azure Data Box Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: f8116ec0836623adf803991017950ddc7f960923
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67805715"
---
# <a name="use-logs-to-troubleshoot-validation-issues-in-azure-data-box-disk"></a>Usare i log per risolvere i problemi di convalida nel disco di Azure Data BoxUse logs to troubleshoot validation issues in Azure Data Box Disk

Questo articolo si applica al disco di Microsoft Azure Data Box.This article applies to Microsoft Azure Data Box Disk. In questo articolo viene descritto come utilizzare i log per risolvere i problemi di convalida che è possibile verificare quando si distribuisce questa soluzione.

## <a name="validation-tool-log-files"></a>File di log dello strumento di convalida

Quando si convalidano i dati sui dischi utilizzando [lo strumento](data-box-disk-deploy-copy-data.md#validate-data)di convalida , viene generato un *file error.xml* per registrare eventuali errori. Il file di registro si trova nella cartella dell'unità. `Drive:\DataBoxDiskImport\logs` Quando si esegue la convalida, viene fornito un collegamento al log degli errori.

<!--![Validation tool with link to error log](media/data-box-disk-troubleshoot/validation-tool-link-error-log.png)-->

Se si eseguono più sessioni per la convalida, viene generato un log degli errori per sessione.

- Di seguito è riportato un esempio del `PageBlob` log degli errori quando i dati caricati nella cartella non sono allineati a 512 byte. Tutti i dati caricati in PageBlob devono essere allineati a 512 byte, ad esempio un disco rigido virtuale o VHDX.Any data uploaded to PageBlob must be 512-bytes aligned, for example, a VHD o VHDX. Gli errori in questo `<Errors>` file sono `<Warnings>`negli avvisi e in .

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
            <SessionId>session#1</SessionId>
            <ItemType>PageBlob</ItemType>
            <SourceDirectory>D:\Dataset\TestDirectory</SourceDirectory>
            <Errors>
                <Error Code="Not512Aligned">
                    <Description>The file is not 512 bytes aligned.</Description>
                    <List>
                        <File Path="\Practice\myScript.ps1" />
                    </List>
                    <Count>1</Count>
                </Error>
            </Errors>
            <Warnings />
        </ErrorLog>
    ```

- Di seguito è riportato un esempio del log degli errori quando il nome del contenitore non è valido. La cartella creata `BlockBlob`in `PageBlob`, `AzureFile` o le cartelle sul disco diventa un contenitore nell'account di Archiviazione di Azure. Il nome del contenitore deve seguire le convenzioni di denominazione di [Azure.](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)

    ```xml
        <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
          <SessionId>bbsession</SessionId>
          <ItemType>BlockBlob</ItemType>
          <SourceDirectory>E:\BlockBlob</SourceDirectory>
          <Errors>
            <Error Code="InvalidShareContainerFormat">
              <List>
                <Container Name="Azu-reFile" />
                <Container Name="bbcont ainer1" />
              </List>
              <Count>2</Count>
            </Error>
          </Errors>
          <Warnings />
    </ErrorLog>
    ```

## <a name="validation-tool-errors"></a>Errori dello strumento di convalida

Gli errori contenuti nel file *error.xml* con le azioni consigliate corrispondenti sono riepilogati nella tabella seguente.

| Codice di errore| Descrizione                       | Azioni consigliate               |
|------------|--------------------------|-----------------------------------|
| `None` | Convalida dei dati completata. | Non è richiesta alcuna azione. |
| `InvalidXmlCharsInPath` |Impossibile creare un file manifesto come percorso del file contiene caratteri non validi. | Rimuovi questi caratteri per procedere.  |
| `OpenFileForReadFailed`| Impossibile elaborare il file. Ciò potrebbe essere dovuto a un problema di accesso o al danneggiamento del file system.|Impossibile leggere il file a causa di un errore. I dettagli dell'errore sono nell'eccezione. |
| `Not512Aligned` | Questo file non è in un formato valido per la cartella PageBlob.| Caricare solo i dati allineati alla `PageBlob` cartella da 512 byte. Rimuovere il file dalla cartella PageBlob o spostarlo nella cartella BlockBlob. Ripetere la convalida.|
| `InvalidBlobPath` | Percorso del file non esegue il mapping a un percorso BLOB valido nel cloud in base alle convenzioni di denominazione BLOB di Azure.File path doesn't map to a valid blob path in cloud as by the Azure Blob naming conventions.|Seguire le linee guida per la denominazione di Azure per rinominare il percorso del file. |
| `EnumerationError` | Impossibile enumerare il file per la convalida. |Questo errore può essere motivato da diversi motivi. Un motivo più probabile è l'accesso al file. |
| `ShareSizeExceeded` | Questo file ha causato il superamento del limite di Azure di 5 TB per le dimensioni della condivisione file di Azure.This file caused the Azure file size to exceed the Azure limit of 5 TB.|Ridurre le dimensioni dei dati nella condivisione in modo che siano conformi ai limiti delle dimensioni degli oggetti di [Azure.](data-box-disk-limits.md#azure-object-size-limits) Ripetere la convalida. |
| `AzureFileSizeExceeded` | La dimensione del file supera i limiti delle dimensioni del file di Azure.File size exceeds the Azure File size limits.| Ridurre le dimensioni del file o dei dati in modo che siano conformi ai limiti delle dimensioni degli oggetti di [Azure.](data-box-disk-limits.md#azure-object-size-limits) Ripetere la convalida.|
| `BlockBlobSizeExceeded` | La dimensione del file supera i limiti delle dimensioni dei BLOB bloccati di Azure.File size exceeds the Azure Block Blob size limits. | Ridurre le dimensioni del file o dei dati in modo che siano conformi ai limiti delle dimensioni degli oggetti di [Azure.](data-box-disk-limits.md#azure-object-size-limits) Ripetere la convalida. |
| `ManagedDiskSizeExceeded` | La dimensione del file supera i limiti delle dimensioni del disco gestito di Azure.File size exceeds the Azure Managed Disk size limits. | Ridurre le dimensioni del file o dei dati in modo che siano conformi ai limiti delle dimensioni degli oggetti di [Azure.](data-box-disk-limits.md#azure-object-size-limits) Ripetere la convalida. |
| `PageBlobSizeExceeded` | La dimensione del file supera i limiti delle dimensioni del disco gestito di Azure.File size exceeds the Azure Managed Disk size limits. | Ridurre le dimensioni del file o dei dati in modo che siano conformi ai limiti delle dimensioni degli oggetti di [Azure.](data-box-disk-limits.md#azure-object-size-limits) Ripetere la convalida. |
| `InvalidShareContainerFormat`  |I nomi delle directory non sono conformi alle convenzioni di denominazione di Azure per contenitori o condivisioni.         |La prima cartella creata nelle cartelle preesistenti sul disco diventa un contenitore nell'account di archiviazione. Questo nome di condivisione o contenitore non è conforme alle convenzioni di denominazione di Azure.This share or container name does not conform to the Azure naming conventions. Rinominare il file in modo che sia conforme alle convenzioni di denominazione di [Azure.](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) Ripetere la convalida.   |
| `InvalidBlobNameFormat` | Percorso del file non esegue il mapping a un percorso BLOB valido nel cloud in base alle convenzioni di denominazione BLOB di Azure.File path doesn't map to a valid blob path in cloud as by the Azure Blob naming conventions.|Rinominare il file in modo che sia conforme alle convenzioni di denominazione di [Azure.](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) Ripetere la convalida. |
| `InvalidFileNameFormat` | Percorso del file non esegue il mapping a un percorso di file valido nel cloud in base alle convenzioni di denominazione dei file di Azure.File path doesn't map to a valid file path in cloud as by the Azure File naming conventions. |Rinominare il file in modo che sia conforme alle convenzioni di denominazione di [Azure.](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) Ripetere la convalida. |
| `InvalidDiskNameFormat` | Percorso del file non esegue il mapping a un nome di disco valido nel cloud in base alle convenzioni di denominazione del disco gestito di Azure.File path doesn't map to a valid disk name in cloud as by the Azure Managed Disk naming conventions. |Rinominare il file in modo che sia conforme alle convenzioni di denominazione di [Azure.](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) Ripetere la convalida.       |
| `NotPartOfFileShare` | Il percorso di caricamento dei file non è valido. Caricare i file in una cartella in File di Azure.Upload the files to a folder in Azure Files.   | Rimuovere i file in errore e caricarli in una cartella precreata. Ripetere la convalida. |
| `NonVhdFileNotSupportedForManagedDisk` | Un file non VHD non può essere caricato come disco gestito. |Rimuovere i file non `ManagedDisk` VHD dalla cartella in quanto questi `PageBlob` non sono supportati o spostare questi file in una cartella. Ripetere la convalida. |


## <a name="next-steps"></a>Passaggi successivi

- Risolvere gli errori di [caricamento dei dati](data-box-disk-troubleshoot-upload.md).
