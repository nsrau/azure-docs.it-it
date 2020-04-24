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
# <a name="use-logs-to-troubleshoot-validation-issues-in-azure-data-box-disk"></a>Usare i log per risolvere i problemi di convalida in Azure Data Box Disk

Questo articolo si applica a Microsoft Azure Data Box Disk. Questo articolo descrive come usare i log per risolvere i problemi di convalida che si possono verificare quando si distribuisce questa soluzione.

## <a name="validation-tool-log-files"></a>File di log dello strumento di convalida

Quando si convalidano i dati sui dischi utilizzando lo [strumento di convalida](data-box-disk-deploy-copy-data.md#validate-data), viene generato un *errore. XML* per registrare eventuali errori. Il file di log si trova nella `Drive:\DataBoxDiskImport\logs` cartella dell'unità. Quando si esegue la convalida, viene fornito un collegamento al log degli errori di.

<!--![Validation tool with link to error log](media/data-box-disk-troubleshoot/validation-tool-link-error-log.png)-->

Se si eseguono più sessioni per la convalida, viene generato un log degli errori per sessione.

- Di seguito è riportato un esempio del log degli errori quando i dati caricati `PageBlob` nella cartella non sono allineati a 512 byte. Tutti i dati caricati in PageBlob devono essere allineati a 512 byte, ad esempio un VHD o VHDX. Gli errori in questo file si trovano negli `<Errors>` avvisi e in `<Warnings>`.

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

- Di seguito è riportato un esempio del log degli errori quando il nome del contenitore non è valido. La cartella creata in cartelle `BlockBlob`, `PageBlob`o `AzureFile` sul disco diventa un contenitore nell'account di archiviazione di Azure. Il nome del contenitore deve seguire le [convenzioni di denominazione di Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions).

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

Nella tabella seguente sono riepilogati gli errori contenuti nel *file Error. XML* con le azioni consigliate corrispondenti.

| Codice errore| Descrizione                       | Azioni consigliate               |
|------------|--------------------------|-----------------------------------|
| `None` | I dati sono stati convalidati. | Non è richiesta alcuna azione. |
| `InvalidXmlCharsInPath` |Non è stato possibile creare un file manifesto perché il percorso del file contiene caratteri non validi. | Rimuovere questi caratteri per continuare.  |
| `OpenFileForReadFailed`| Non è stato possibile elaborare il file. La causa potrebbe essere un problema di accesso o file system danneggiamento.|Impossibile leggere il file a causa di un errore. I dettagli dell'errore si trovano nell'eccezione. |
| `Not512Aligned` | Il formato del file non è valido per la cartella PageBlob.| Caricare solo i dati di 512 byte allineati alla `PageBlob` cartella. Rimuovere il file dalla cartella PageBlob o spostarlo nella cartella BlockBlob. Ripetere la convalida.|
| `InvalidBlobPath` | Il percorso del file non è mappato a un percorso BLOB valido nel cloud in base alle convenzioni di denominazione dei BLOB di Azure.|Seguire le linee guida per la denominazione di Azure per rinominare il percorso del file. |
| `EnumerationError` | Impossibile enumerare il file per la convalida. |Questo errore può essere dovuto a diversi motivi. Un motivo più probabile è l'accesso al file. |
| `ShareSizeExceeded` | Questo file ha causato il superamento del limite di 5 TB per le dimensioni della condivisione file di Azure.|Ridurre le dimensioni dei dati nella condivisione in modo che siano conformi ai limiti delle [dimensioni degli oggetti di Azure](data-box-disk-limits.md#azure-object-size-limits). Ripetere la convalida. |
| `AzureFileSizeExceeded` | Le dimensioni del file superano i limiti delle dimensioni dei file di Azure.| Ridurre le dimensioni del file o dei dati in modo che siano conformi ai [limiti delle dimensioni degli oggetti di Azure](data-box-disk-limits.md#azure-object-size-limits). Ripetere la convalida.|
| `BlockBlobSizeExceeded` | Le dimensioni del file superano i limiti delle dimensioni dei BLOB in blocchi di Azure. | Ridurre le dimensioni del file o dei dati in modo che siano conformi ai [limiti delle dimensioni degli oggetti di Azure](data-box-disk-limits.md#azure-object-size-limits). Ripetere la convalida. |
| `ManagedDiskSizeExceeded` | Le dimensioni del file superano i limiti delle dimensioni del disco gestito di Azure. | Ridurre le dimensioni del file o dei dati in modo che siano conformi ai [limiti delle dimensioni degli oggetti di Azure](data-box-disk-limits.md#azure-object-size-limits). Ripetere la convalida. |
| `PageBlobSizeExceeded` | Le dimensioni del file superano i limiti delle dimensioni del disco gestito di Azure. | Ridurre le dimensioni del file o dei dati in modo che siano conformi ai [limiti delle dimensioni degli oggetti di Azure](data-box-disk-limits.md#azure-object-size-limits). Ripetere la convalida. |
| `InvalidShareContainerFormat`  |I nomi di directory non sono conformi alle convenzioni di denominazione di Azure per contenitori o condivisioni.         |La prima cartella creata con le cartelle preesistenti sul disco diventa un contenitore nell'account di archiviazione. Il nome della condivisione o del contenitore non è conforme alle convenzioni di denominazione di Azure. Rinominare il file in modo che sia conforme alle [convenzioni di denominazione di Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Ripetere la convalida.   |
| `InvalidBlobNameFormat` | Il percorso del file non è mappato a un percorso BLOB valido nel cloud in base alle convenzioni di denominazione dei BLOB di Azure.|Rinominare il file in modo che sia conforme alle [convenzioni di denominazione di Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Ripetere la convalida. |
| `InvalidFileNameFormat` | Il percorso del file non è mappato a un percorso di file valido nel cloud in base alle convenzioni di denominazione dei file di Azure. |Rinominare il file in modo che sia conforme alle [convenzioni di denominazione di Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Ripetere la convalida. |
| `InvalidDiskNameFormat` | Il percorso del file non è mappato a un nome di disco valido nel cloud in base alle convenzioni di denominazione dei dischi gestiti di Azure. |Rinominare il file in modo che sia conforme alle [convenzioni di denominazione di Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Ripetere la convalida.       |
| `NotPartOfFileShare` | Il percorso di caricamento per i file non è valido. Caricare i file in una cartella File di Azure.   | Rimuovere i file in errore e caricare i file in una cartella precreata. Ripetere la convalida. |
| `NonVhdFileNotSupportedForManagedDisk` | Non è possibile caricare un file non VHD come disco gestito. |Rimuovere i file non VHD dalla `ManagedDisk` cartella perché non sono supportati o spostare questi file in una `PageBlob` cartella. Ripetere la convalida. |


## <a name="next-steps"></a>Passaggi successivi

- Risolvere gli [errori di caricamento dei dati](data-box-disk-troubleshoot-upload.md).
