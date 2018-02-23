---
title: 'Esempio di script di Azure PowerShell: calcolare le dimensioni di fatturazione totali di un contenitore BLOB | Microsoft Docs'
description: Calcolare le dimensioni totali di un contenitore nell'archivio BLOB di Azure ai fini della fatturazione.
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
ms.date: 11/07/2017
ms.author: fryu
ms.openlocfilehash: c37b416578a76e9b12e29d68e413d851796ccc6f
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="calculate-the-total-billing-size-of-a-blob-container"></a>Calcolare le dimensioni di fatturazione totali di un contenitore BLOB

Questo script calcola le dimensioni di un contenitore nell'archivio BLOB di Azure allo scopo di stimare i costi per la fatturazione. Lo script somma le dimensioni dei BLOB nel contenitore.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Questo script di PowerShell calcola le dimensioni di un contenitore ai fini della fatturazione. In caso di calcolo delle dimensioni del contenitore per altri scopi, vedere l'articolo su come [calcolare le dimensioni totali di un contenitore di archiviazione BLOB](../scripts/storage-blobs-container-calculate-size-powershell.md) per uno script più semplice che offre una stima.

## <a name="determine-the-size-of-the-blob-container"></a>Determinare le dimensioni di un contenitore BLOB

La dimensione totale del contenitore BLOB include la dimensione del contenitore stesso e di tutti i BLOB in esso contenuti.

Nelle sezioni seguenti viene illustrata la modalità di calcolo della capacità di archiviazione per i contenitori BLOB e i BLOB. Nella sezione seguente Len(X) indica il numero di caratteri nella stringa.

### <a name="blob-containers"></a>Contenitori BLOB

Nel calcolo seguente viene descritto come calcolare la quantità di spazio di archiviazione usato per il contenitore BLOB:

`
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
`

I dettagli sono indicati di seguito:
* 48 byte di sovraccarico per ogni contenitore include ora dell'ultima modifica, autorizzazioni, impostazioni pubbliche e alcuni metadati di sistema.

* Il nome del contenitore viene archiviato come Unicode. Considerare quindi il numero di caratteri e moltiplicare per 2.

* Per ogni blocco di metadati del contenitore BLOB archiviato, viene archiviata la lunghezza del nome (ASCII) più la lunghezza del valore della stringa.

* 512 byte per identificatore firmato include il nome dell'identificatore firmato, l'ora di inizio, l'ora di scadenza e le autorizzazioni.

### <a name="blobs"></a>Blobs

Nei calcoli seguenti viene illustrato come calcolare la quantità di spazio di archiviazione usato per ogni BLOB.

* BLOB in blocchi (BLOB di base o snapshot):

   `
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
   SizeInBytes(data in unique committed data blocks stored) +
   SizeInBytes(data in uncommitted data blocks)
   `

* BLOB di pagine (BLOB di base o snapshot):

   `
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   number of nonconsecutive page ranges with data * 12 bytes +
   SizeInBytes(data in unique pages stored)
   `

I dettagli sono indicati di seguito:

* 124 byte di sovraccarico per BLOB, che include:
    - Ora ultima modifica
    - Dimensione
    - Cache-Control
    - Content-Type
    - Content-Language
    - Content-Encoding
    - Content-MD5
    - Autorizzazioni
    - Informazioni sugli snapshot
    - Lease
    - Alcuni metadati del file system

* Il nome del BLOB viene archiviato come Unicode. Considerare quindi il numero di caratteri e moltiplicare per 2.

* Per ogni blocco di metadati archiviato aggiungere la lunghezza del nome (archiviato come ASCII) alla lunghezza del valore della stringa.

* Per i BLOB in blocchi:
    * 8 byte per l'elenco di blocchi.
    * Il numero di blocchi moltiplicato per le dimensioni dell'ID blocco in byte.
    * Le dimensioni dei dati in tutti blocchi con commit e senza commit. 
    
    >[!NOTE]
    >Quando vengono usati gli snapshot, questa dimensione include solo i dati univoci per il BLOB di base o il BLOB snapshot. Se i blocchi senza commit non vengono usati dopo una settimana, sono sottoposto a Garbage Collection. In seguito non vengono considerati per la fatturazione.

* Per i BLOB di pagine:
    * Numero di intervalli di pagine non consecutivi con i dati per 12 byte. Questo è il numero di intervalli di pagine univoche visualizzato quando si chiama l'API **GetPageRanges**.

    * Le dimensioni dei dati in byte di tutte le pagine archiviate. 
    
    >[!NOTE]
    >Quando vengono usati gli snapshot, questa dimensione include solo le pagine univoche per il BLOB di base o snapshot in fase di conteggio.

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size-ex.ps1 "Calculate container size")]

## <a name="next-steps"></a>Passaggi successivi

- Vedere l'articolo su come [calcolare le dimensioni totali di un contenitore di archiviazione BLOB](../scripts/storage-blobs-container-calculate-size-powershell.md) per un semplice script che offre una stima delle dimensioni del contenitore.

- Per altre informazioni sulla fatturazione dell'Archiviazione di Azure, vedere [Informazioni sulla fatturazione dell'Archiviazione di Microsoft Azure](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

- Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.4.1).

- Sono disponibili altri esempi di script di archiviazione di PowerShell in [PowerShell samples for Azure Blob storage](../blobs/storage-samples-blobs-powershell.md) (Esempi di PowerShell per Archiviazione di Azure).
