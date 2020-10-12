---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/09/2020
ms.author: tamram
ms.openlocfilehash: 27617da97ced9ac775beae71e4c25202944b2aba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "78941028"
---
La tabella seguente descrive i limiti di capacità, scalabilità e prestazioni per l'archiviazione tabelle.

| Risorsa | Destinazione |
|----------|---------------|
| Numero di tabelle in un account di archiviazione di Azure | Limitato solo dalla capacità dell'account di archiviazione |
| Numero di partizioni in una tabella | Limitato solo dalla capacità dell'account di archiviazione |
| Numero di entità in una partizione | Limitato solo dalla capacità dell'account di archiviazione |
| Dimensioni massime di una singola tabella | 500 TiB |
| Dimensioni massime di una singola entità, inclusi tutti i valori delle proprietà | 1 MiB |
| Numero massimo di proprietà di un'entità di tabella | 255 (incluse 3 proprietà di sistema: **PartitionKey**, **RowKey** e **Timestamp**) |
| Dimensioni massime totali di una singola proprietà in un'entità | Varia in base al tipo di proprietà. Per altre informazioni, vedere **Tipi di proprietà** in [Informazioni sul modello di dati del servizio tabelle](/rest/api/storageservices/understanding-the-table-service-data-model). |
| Dimensioni di **PartitionKey** | Una stringa di dimensioni fino a 1 KiB |
| Dimensioni di **RowKey** | Una stringa di dimensioni fino a 1 KiB |
| Dimensioni di una transazione di gruppi di entità | Una transazione può includere al massimo 100 entità e le dimensioni del payload devono essere inferiori a 4 MiB. Una transazione di gruppi di entità può includere un aggiornamento di un'entità una sola volta. |
| Numero massimo di criteri di accesso archiviati per ogni tabella | 5 |
| Frequenza massima di richieste per account di archiviazione | 20.000 transazioni al secondo, supponendo una dimensione delle entità di 1 KiB |
| Velocità effettiva da raggiungere per partizione di tabella singola (entità di 1 KiB) | Fino a 2.000 entità al secondo |