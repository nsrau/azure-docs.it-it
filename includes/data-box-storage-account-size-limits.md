---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 3e1f9c225a57e7d41f85c2a92dac989453057c51
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2020
ms.locfileid: "82736978"
---
Di seguito vengono indicati i limiti sulle dimensioni dei dati copiati nell'account di archiviazione. Verificare che i dati caricati siano conformi a tali limiti. Per informazioni aggiornate su questi limiti, vedere [obiettivi di scalabilità e prestazioni per l'archiviazione BLOB](../articles/storage/blobs/scalability-targets.md) e [obiettivi di scalabilità e prestazioni file di Azure](../articles/storage/files/storage-files-scale-targets.md).

| Dimensioni dei dati copiati nell'account di archiviazione di Azure                      | Limite predefinito          |
|---------------------------------------------------------------------|------------------------|
| BLOB in blocchi e BLOB di pagine                                            | 2 PB per gli Stati Uniti e l'Europa.<br>500 TB per tutte le altre aree, inclusa la UK.  <br> Sono inclusi i dati da tutte le origini, tra cui Data Box.|
| File di Azure                                                          | Dimensioni massime delle condivisioni file standard 100TiB *, 5 TB, condivisioni file Premium 100TiB per condivisione.<br> Tutte le cartelle sotto *StorageAccount_AzureFiles* devono rispettare questo limite.       |
