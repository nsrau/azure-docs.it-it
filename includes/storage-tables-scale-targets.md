---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/09/2020
ms.author: tamram
ms.openlocfilehash: 27617da97ced9ac775beae71e4c25202944b2aba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78941028"
---
Nella tabella seguente vengono descritti gli obiettivi di capacità, scalabilità e prestazioni per l'archiviazione tabelle.

| Risorsa | Destinazione |
|----------|---------------|
| Numero di tabelle in un account di archiviazione di Azure | Limitato solo dalla capacità dell'account di archiviazione |
| Numero di partizioni in una tabella | Limitato solo dalla capacità dell'account di archiviazione |
| Numero di entità in una partizione | Limitato solo dalla capacità dell'account di archiviazione |
| Dimensione massima di una singola tabella | 500 TiB |
| Dimensione massima di una singola entità, inclusi tutti i valori di proprietàMaximum size of a single entity, including all property values | 1 MiB |
| Numero massimo di proprietà in un'entità tabella | 255 (incluse le tre proprietà di sistema, **PartitionKey**, **RowKey**e **Timestamp**) |
| Dimensione totale massima di una singola proprietà in un'entitàMaximum total size of an individual property in an entity | Varia in base al tipo di proprietà. Per ulteriori informazioni, vedere **Tipi di proprietà** in Informazioni sul modello di dati del servizio [tabelle](/rest/api/storageservices/understanding-the-table-service-data-model). |
| Dimensioni di **PartitionKey** | Una stringa di dimensioni fino a 1 KiB |
| Dimensioni di **RowKey** | Una stringa di dimensioni fino a 1 KiB |
| Dimensioni di una transazione del gruppo di entità | Una transazione può includere al massimo 100 entità e il payload deve essere inferiore a 4 MiB. Una transazione del gruppo di entità può includere un aggiornamento a un'entità una sola volta. |
| Numero massimo di criteri di accesso archiviati per tabellaMaximum number of stored access policies per table | 5 |
| Frequenza massima di richieste per account di archiviazione | 20.000 transazioni al secondo, che presuppone una dimensione di entità 1-KiB |
| Velocità effettiva di destinazione per una singola partizione di tabella (1 KiB-entità)Target throughput for a single table partition (1 KiB-entities) | Fino a 2.000 entità al secondo |