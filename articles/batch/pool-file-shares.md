---
title: Condivisione file di Azure per i pool di Azure Batch
description: Come montare una condivisione di File di Azure dai nodi di calcolo in un pool Linux o Windows in Azure Batch.
ms.topic: how-to
ms.date: 05/24/2018
ms.openlocfilehash: 6bbaba20512a17de563e74ba095057c5c3f71f6b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87986424"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Usare una condivisione file di Azure con un pool di Batch

[File di Azure](../storage/files/storage-files-introduction.md) offre condivisioni file completamente gestite nel cloud, accessibili tramite il protocollo SMB (Server Message Block). Questo articolo fornisce informazioni ed esempi di codice per il montaggio e l'uso di una condivisione file di Azure nei nodi di calcolo del pool.

## <a name="considerations-for-use-with-batch"></a>Considerazioni per l'uso con Batch

* Si consiglia di usare una condivisione file di Azure quando sono presenti pool che eseguono un numero relativamente basso di attività parallele se si usano File di Azure non Premium. Esaminare gli [obiettivi di scalabilità e prestazioni](../storage/files/storage-files-scale-targets.md) per determinare se usare File di Azure (che usa un account di archiviazione di Azure), considerate le dimensioni del pool previste e il numero di file di asset. 

* Le condivisioni file di Azure sono [convenienti](https://azure.microsoft.com/pricing/details/storage/files/) e possono essere configurate con la replica dei dati in un'altra area e sono quindi ridondanti a livello globale. 

* È possibile montare simultaneamente una condivisione file di Azure da un computer locale. Tuttavia, assicurarsi di comprendere le [implicazioni della concorrenza](../storage/common/storage-concurrency.md) , soprattutto quando si usano le API REST.

* Vedere anche le [considerazioni sulla pianificazione](../storage/files/storage-files-planning.md) generali per le condivisioni file di Azure.


## <a name="create-a-file-share"></a>Creare una condivisione file

[Creare una condivisione file](../storage/files/storage-how-to-create-file-share.md) in un account di archiviazione collegato all'account Batch o in un account di archiviazione separato.

## <a name="mount-an-azure-file-share-on-a-batch-pool"></a>Montare una condivisione file di Azure in un pool di batch

Per informazioni su come [montare un file system virtuale in un pool di batch](virtual-file-mount.md), vedere la documentazione.

## <a name="next-steps"></a>Passaggi successivi

* Per altre opzioni per leggere e scrivere i dati in Batch, vedere [Rendere persistente l'output di processi e attività](batch-task-output.md).
* Per distribuire file system per carichi di lavoro di Batch vedere anche il toolkit [Batch Shipyard](https://github.com/Azure/batch-shipyard), che include [Shipyard Recipes](https://github.com/Azure/batch-shipyard/tree/master/recipes).
