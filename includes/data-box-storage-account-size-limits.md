---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/24/2020
ms.author: alkohli
ms.openlocfilehash: 2868e5a53686cfa94dc206c1aab65fe866d19b6d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026362"
---
Di seguito vengono indicati i limiti sulle dimensioni dei dati copiati nell'account di archiviazione. Verificare che i dati caricati siano conformi a tali limiti. Per informazioni aggiornate su questi limiti, vedere [obiettivi di scalabilità e prestazioni per l'archiviazione BLOB](../articles/storage/blobs/scalability-targets.md) e [obiettivi di scalabilità e prestazioni file di Azure](../articles/storage/files/storage-files-scale-targets.md).

| Dimensioni dei dati copiati nell'account di archiviazione di Azure                      | Limite predefinito          |
|---------------------------------------------------------------------|------------------------|
| BLOB in blocchi e BLOB di pagine                                            | Il limite massimo corrisponde al limite di [archiviazione definito per la sottoscrizione di Azure](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits) e include i dati provenienti da tutte le origini, tra cui data box. |
| File di Azure                                                          | Data Box supporta le condivisioni file di grandi dimensioni (100TiB) se abilitate prima della creazione dell'ordine di Data Box. <br> Se non è abilitato prima della creazione dell'ordine, la dimensione massima della condivisione file supportata è 5 TiB. <br> Le condivisioni file Premium non sono ancora supportate.<br> Tutte le cartelle sotto *StorageAccount_AzureFiles* devono rispettare questo limite. <br> Per altre informazioni, vedere [abilitare e creare condivisioni file di grandi dimensioni](../articles/storage/files/storage-files-how-to-create-large-file-share.md)      |