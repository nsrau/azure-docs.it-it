---
title: Inserimento di dati nella cache HPC di Azure-msrsync
description: Come usare msrsync per spostare i dati in una destinazione di archiviazione BLOB nella cache HPC di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 3e5937a036763fab57f9e37494ace33e8452b1f2
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582265"
---
# <a name="azure-hpc-cache-data-ingest---msrsync-method"></a>Inserimento di dati nella cache HPC di Azure-metodo msrsync

Questo articolo fornisce istruzioni dettagliate per l'uso dell'utilità ``msrsync`` per copiare i dati in un contenitore di archiviazione BLOB di Azure da usare con la cache HPC di Azure.

Per altre informazioni sullo spostamento dei dati nell'archiviazione BLOB per la cache HPC di Azure, vedere [spostare i dati nell'archivio BLOB di Azure per la cache HPC](hpc-cache-ingest.md)di Azure.

Lo strumento ``msrsync`` può essere usato per spostare i dati in una destinazione di archiviazione back-end per la cache HPC di Azure. Questo strumento è progettato per ottimizzare l'utilizzo della larghezza di banda mediante l'esecuzione parallela di più processi ``rsync``. È disponibile da GitHub all'indirizzo https://github.com/jbd/msrsync.

``msrsync`` suddivide la directory di origine in contenitori separati e quindi esegue singoli processi ``rsync`` in ogni contenitore.

I test preliminari su una macchina virtuale con quattro core hanno indicato la massima efficienza utilizzando 64 processi. Usare l'opzione ``msrsync`` di ``-p`` per impostare il numero di processi su 64.

Si noti che ``msrsync`` può scrivere solo da e verso i volumi locali. L'origine e la destinazione devono essere accessibili come montaggi locali nella workstation utilizzata per eseguire il comando.

Seguire queste istruzioni per usare ``msrsync`` per popolare l'archivio BLOB di Azure con la cache HPC di Azure:

1. Installare ``msrsync`` e i relativi prerequisiti (``rsync`` e Python 2,6 o versione successiva)
1. Determinare il numero totale di file e directory da copiare.

   Ad esempio, usare l'utilità ``prime.py`` con argomenti ```prime.py --directory /path/to/some/directory``` (disponibile scaricando <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>).

   Se non si usa ``prime.py``, è possibile calcolare il numero di elementi con lo strumento GNU ``find``, come indicato di seguito:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Dividere il numero di elementi per 64 per determinare il numero di elementi per ogni processo. Usare questo numero con l'opzione ``-f`` per impostare le dimensioni dei contenitori quando si esegue il comando.

1. Eseguire il comando ``msrsync`` per copiare i file:

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Ad esempio, questo comando è progettato per spostare i file 11.000 in 64 processi da/test/source-repository a/mnt/hpccache/repository:

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/hpccache/repository``
