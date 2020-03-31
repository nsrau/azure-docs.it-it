---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 6a9c31eb46a457433834d5940b3f7e190ebe1476
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75466559"
---
Di seguito vengono indicati i limiti sulle dimensioni dei dati copiati nell'account di archiviazione. Verificare che i dati caricati siano conformi a tali limiti. Per le informazioni più aggiornate su questi limiti, vedere Obiettivi di [scalabilità e prestazioni per l'archiviazione BLOB](../articles/storage/blobs/scalability-targets.md) e gli obiettivi di scalabilità e prestazioni di File di [Azure.](../articles/storage/files/storage-files-scale-targets.md)

| Dimensioni dei dati copiati nell'account di archiviazione di Azure                      | Limite predefinito          |
|---------------------------------------------------------------------|------------------------|
| BLOB in blocchi e BLOB di pagine                                            | 2 PB per gli Stati Uniti e l'Europa.<br>500 TB per tutte le altre regioni, tra cui il Regno Unito.  <br> Sono inclusi i dati da tutte le origini, tra cui Data Box.|
| File di Azure                                                          | 5 TB per azione.<br> Tutte le cartelle sotto *StorageAccount_AzureFiles* devono rispettare questo limite.       |
