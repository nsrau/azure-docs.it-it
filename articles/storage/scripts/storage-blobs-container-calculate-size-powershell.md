---
title: Esempio di script di Azure PowerShell - Calcolare le dimensioni del contenitore BLOB | Microsoft Docs
description: Calcolare le dimensioni di un contenitore dell'Archiviazione BLOB di Azure sommando le dimensioni di ognuno dei relativi BLOB.
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
ms.date: 10/23/2017
ms.author: fryu
ms.openlocfilehash: 46f3eac0129da41062caba2da090f9e532505d67
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2017
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Calcolare le dimensioni di un contenitore di archiviazione BLOB

Lo script consente di calcolare le dimensioni di un contenitore dell'Archiviazione BLOB di Azure sommando le dimensioni dei BLOB del contenitore.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="understand-the-size-of-blob-storage-container"></a>Informazioni sulla dimensione di un contenitore di archiviazione BLOB

La dimensione totale del contenitore di archiviazione BLOB include la dimensione del contenitore stesso e di tutti i BLOB in esso contenuti.

Di seguito viene illustrata la modalità di calcolo della capacità di archiviazione per i contenitori BLOB e i BLOB. Nell'esempio di seguito, Len(X) indica il numero di caratteri nella stringa.

### <a name="blob-containers"></a>Contenitori BLOB

Di seguito viene descritto come calcolare la quantità di spazio di archiviazione usato per il contenitore BLOB:

`
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
`

I dettagli sono indicati di seguito:
* 48 byte di sovraccarico per ogni contenitore include ora dell'ultima modifica, autorizzazioni, impostazioni pubbliche e alcuni metadati di sistema.
* Il nome del contenitore viene archiviato come Unicode; considerare quindi il numero di caratteri e moltiplicare per 2.
* Metadati del contenitore BLOB for-each per archiviare la lunghezza del nome (come ASCII), più la lunghezza del valore della stringa.
* 512 byte per identificatore firmato include il nome dell'identificatore firmato, l'ora di inizio, l'ora di scadenza e le autorizzazioni.

### <a name="blobs"></a>Blobs

Di seguito viene illustrato come calcolare la quantità di spazio di archiviazione usato per ogni BLOB:

* BLOB in blocchi (BLOB di base o snapshot)

`
124 bytes + Len(BlobName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
SizeInBytes(data in unique committed data blocks stored) +
SizeInBytes(data in uncommitted data blocks)
`

* BLOB di pagine (BLOB di base o snapshot)

`
124 bytes + Len(BlobName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
number of nonconsecutive page ranges with data * 12 bytes +
SizeInBytes(data in unique pages stored)
`

I dettagli sono indicati di seguito:

* 124 byte di sovraccarico per BLOB che include ora dell'ultima modifica, dimensioni, cache-control, content-type, content-language, content-encoding, content-MD5, autorizzazioni, informazioni sugli snapshot, lease e alcuni metadati di sistema.
* Il nome del BLOB viene archiviato come Unicode; considerare quindi il numero di caratteri e moltiplicare per 2.
* Quindi per i metadati for-each archiviati, la lunghezza del nome (archiviato come ASCII), più la lunghezza del valore della stringa.
* Quindi per i BLOB in blocchi
    * 8 byte per l'elenco di blocchi
    * Il numero di blocchi per le dimensioni dell'ID blocco in byte
    * Più le dimensioni dei dati in tutti blocchi con commit e senza commit. Si noti che quando vengono usati gli snapshot, questa dimensione include solo i dati univoci per il BLOB di base o snapshot. Se i blocchi senza commit non vengono usati dopo una settimana, saranno sottoposti a garbage collection e quindi da quel momento non saranno più conteggiati ai fini della fatturazione.
* Quindi per i BLOB di pagine
    * Numero di intervalli di pagine non consecutivi con i dati per 12 byte. Questo è il numero di intervalli di pagine univoche visualizzato quando si chiama l'API GetPageRanges.
    * Più le dimensioni dei dati in byte di tutte le pagine archiviate. Si noti che quando vengono usati gli snapshot, questa dimensione include solo le pagine univoche per il BLOB di base o snapshot in fase di conteggio.

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size-ex.ps1 "Calculate container size")]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla fatturazione dell'Archiviazione di Azure, vedere [Informazioni sulla fatturazione dell'Archiviazione di Microsoft Azure](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Sono disponibili altri esempi di script di archiviazione di PowerShell in [PowerShell samples for Azure Blob storage](../blobs/storage-samples-blobs-powershell.md) (Esempi di PowerShell per l'Archiviazione BLOB di Azure).
