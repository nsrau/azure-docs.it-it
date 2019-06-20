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
ms.openlocfilehash: f725f38a335972ae8e0a8b8402a99202caa54a70
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147076"
---
# <a name="use-logs-to-troubleshoot-validation-issues-in-azure-data-box-disk"></a>Usare i log per risolvere i problemi di convalida in Azure Data Box Disk

Questo articolo si applica a Microsoft Azure Data Box Disk. L'articolo descrive come usare i log per risolvere i problemi di convalida che si potrebbero verificare quando si distribuisce questa soluzione.

## <a name="validation-tool-log-files"></a>File di log dello strumento di convalida

Quando si convalidano i dati nei dischi usando il [lo strumento di convalida](data-box-disk-deploy-copy-data.md#validate-data), un *error.xml* viene generato per registrare eventuali errori. Il file di log si trova nel `Drive:\DataBoxDiskImport\logs` cartelle dell'unità. Quando si esegue la convalida, viene fornito un collegamento al log degli errori.

<!--![Validation tool with link to error log](media/data-box-disk-troubleshoot/validation-tool-link-error-log.png)-->

Se si eseguono più sessioni per la convalida, ogni sessione viene generato un log degli errori.

- Ecco un esempio di log degli errori quando i dati caricati nel `PageBlob` cartella non è allineati a 512 byte. Tutti i dati caricati in PageBlob devono essere di 512 byte allineate, ad esempio, un VHD o VHDX. Gli errori in questo file sono nel `<Errors>` e gli avvisi in `<Warnings>`.

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

- Ecco un esempio di log degli errori quando il nome del contenitore non è valido. La cartella creata in `BlockBlob`, `PageBlob`, o `AzureFile` cartelle sul disco diventa un contenitore nell'account di archiviazione di Azure. Il nome del contenitore deve seguire le [convenzioni di denominazione di Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions).

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

## <a name="validation-tool-errors"></a>Errori di convalida dello strumento

Gli errori contenuti nel *error.xml* con i corrispondenti, le azioni consigliate sono riepilogati nella tabella seguente.

| Codice di errore| Descrizione                       | Azioni consigliate               |
|------------|--------------------------|-----------------------------------|
| `None` | Corretta convalida i dati. | non è necessaria alcuna azione. |
| `InvalidXmlCharsInPath` |Impossibile creare un file manifesto come il percorso del file contiene caratteri non validi. | Rimuovi questi caratteri per continuare.  |
| `OpenFileForReadFailed`| Non è riuscito a elaborare il file. È possibile perché un accesso problema o un file system sono danneggiati.|Non è stato possibile leggere il file a causa di un errore. I dettagli dell'errore sono nell'eccezione. |
| `Not512Aligned` | Questo file non è presente in un formato valido per la cartella PageBlob.| Solo i dati di caricamento di 512 byte è allineato a `PageBlob` cartella. Rimuovere il file dalla cartella PageBlob o viene spostato nella cartella BlockBlob. Riprovare la convalida.|
| `InvalidBlobPath` | Percorso del file non viene mappato a un percorso blob valido nel cloud in base alle convenzioni di denominazione del Blob di Azure.|Seguire le convenzioni di denominazione di Azure per rinominare il percorso del file. |
| `EnumerationError` | Impossibile enumerare i file per la convalida. |Potrebbero esserci diversi motivi per correggere l'errore. Un motivo più probabile è l'accesso al file. |
| `ShareSizeExceeded` | Questo file ha causato la dimensione della condivisione file di Azure a superare il limite di Azure di 5 TB.|Ridurre le dimensioni dei dati nella condivisione in modo che sia conforme al [limiti di dimensioni di Azure oggetto](data-box-disk-limits.md#azure-object-size-limits). Riprovare la convalida. |
| `AzureFileSizeExceeded` | Dimensioni del file superano i limiti delle dimensioni di File di Azure.| Ridurre le dimensioni del file o i dati in modo che sia conforme al [limiti di dimensioni di Azure oggetto](data-box-disk-limits.md#azure-object-size-limits). Riprovare la convalida.|
| `BlockBlobSizeExceeded` | Dimensioni del file superano i limiti delle dimensioni di Blob in blocchi. | Ridurre le dimensioni del file o i dati in modo che sia conforme al [limiti di dimensioni di Azure oggetto](data-box-disk-limits.md#azure-object-size-limits). Riprovare la convalida. |
| `ManagedDiskSizeExceeded` | Dimensioni del file superano i limiti delle dimensioni di disco gestito di Azure. | Ridurre le dimensioni del file o i dati in modo che sia conforme al [limiti di dimensioni di Azure oggetto](data-box-disk-limits.md#azure-object-size-limits). Riprovare la convalida. |
| `PageBlobSizeExceeded` | Dimensioni del file superano i limiti delle dimensioni di disco gestito di Azure. | Ridurre le dimensioni del file o i dati in modo che sia conforme al [limiti di dimensioni di Azure oggetto](data-box-disk-limits.md#azure-object-size-limits). Riprovare la convalida. |
| `InvalidShareContainerFormat`  |I nomi delle directory non sono conformi alle convenzioni di denominazione di Azure per contenitori o le condivisioni.         |La prima cartella creata sotto le cartelle esistenti sul disco diventa un contenitore nell'account di archiviazione. Questo nome condivisione o il contenitore non è conforme alle convenzioni di denominazione Azure. Rinominare il file in modo che sia conforme allo [convenzioni di denominazione di Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Riprovare la convalida.   |
| `InvalidBlobNameFormat` | Percorso del file non viene mappato a un percorso blob valido nel cloud in base alle convenzioni di denominazione del Blob di Azure.|Rinominare il file in modo che sia conforme allo [convenzioni di denominazione di Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Riprovare la convalida. |
| `InvalidFileNameFormat` | Percorso del file non viene mappato a un percorso file valido nel cloud in base alle convenzioni di denominazione di File di Azure. |Rinominare il file in modo che sia conforme allo [convenzioni di denominazione di Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Riprovare la convalida. |
| `InvalidDiskNameFormat` | Percorso del file non viene mappato a un nome disco valida nel cloud in base alle convenzioni di denominazione di disco gestito di Azure. |Rinominare il file in modo che sia conforme allo [convenzioni di denominazione di Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Riprovare la convalida.       |
| `NotPartOfFileShare` | Impossibile caricare i file perché il percorso di caricamento non è valido. Caricare i file in una cartella nel file di Azure.   | Rimuovere i file in errore e caricare i file in una cartella creati in precedenza. Riprovare la convalida. |
| `NonVhdFileNotSupportedForManagedDisk` | Non è possibile caricare un file non-VHD come disco gestito. |Rimuovere i file non disco rigido virtuale perché non sono supportati. Riprovare la convalida. |


## <a name="next-steps"></a>Passaggi successivi

- Risoluzione dei problemi [errori di caricamento dati](data-box-disk-troubleshoot-upload.md).
