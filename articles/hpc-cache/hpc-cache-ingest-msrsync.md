---
title: Inserimento dati cache HPC di Azure - msrsyncAzure HPC Cache data ingest - msrsync
description: Come usare msrsync per spostare i dati in una destinazione di archiviazione BLOB nella cache HPC di AzureHow to use msrsync to move data to a Blob storage target in Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 4f8863d706d623d613ac156cf202c3b7b12f2ae0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168436"
---
# <a name="azure-hpc-cache-data-ingest---msrsync-method"></a>Inserimento dati cache HPC di Azure - metodo msrsyncAzure HPC Cache data ingest - msrsync method

Questo articolo fornisce istruzioni dettagliate per usare l'utilità per copiare i ``msrsync`` dati in un contenitore di archiviazione BLOB di Azure da usare con la cache HPC di Azure.This article gives detailed instructions for using the utility to copy data to an Azure Blob storage container for use with Azure HPC Cache.

Per altre informazioni sullo spostamento dei dati nell'archivio BLOB per la cache HPC di Azure, vedere [Spostare i dati nell'archiviazione BLOB](hpc-cache-ingest.md)di Azure.To learn more about moving data to Blob storage for your Azure HPC Cache, read Move data to Azure Blob storage .

Lo ``msrsync`` strumento può essere usato per spostare i dati in una destinazione di archiviazione back-end per la cache HPC di Azure.The tool can be used to move data to a backend storage target for the Azure HPC Cache. Questo strumento è progettato per ottimizzare l'utilizzo della larghezza di banda mediante l'esecuzione parallela di più processi ``rsync``. È disponibile da GitHub all'indirizzo https://github.com/jbd/msrsync.

``msrsync`` suddivide la directory di origine in contenitori separati e quindi esegue singoli processi ``rsync`` in ogni contenitore.

I test preliminari su una macchina virtuale con quattro core hanno indicato la massima efficienza utilizzando 64 processi. Usare l'opzione ``-p`` di ``msrsync`` per impostare il numero di processi su 64.

Si noti che ``msrsync`` può scrivere solo da e verso i volumi locali. L'origine e la destinazione devono essere accessibili come supporti locali sulla workstation utilizzata per eseguire il comando.

Seguire queste istruzioni ``msrsync`` per usare per popolare l'archiviazione BLOB di Azure con la cache HPC di Azure:Follow these instructions to use to populate Azure Blob storage with Azure HPC Cache:

1. Installazione ``msrsync`` e relativi``rsync`` prerequisiti (e Python 2.6 o versioni successive)
1. Determinare il numero totale di file e directory da copiare.

   Ad esempio, utilizzare ``prime.py`` l'utilità con ```prime.py --directory /path/to/some/directory``` <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>argomenti (disponibile scaricando ).

   In caso ``prime.py``contrario, è possibile calcolare ``find`` il numero di elementi con lo strumento GNU come segue:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Dividere il numero di elementi per 64 per determinare il numero di elementi per ogni processo. Usare questo numero con l'opzione ``-f`` per impostare le dimensioni dei contenitori quando si esegue il comando.

1. Eseguire ``msrsync`` il comando per copiare i file:

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Ad esempio, questo comando è progettato per spostare 11.000 file in 64 processi da /test/source-repository a /mnt/hpccache/repository:

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/hpccache/repository``
