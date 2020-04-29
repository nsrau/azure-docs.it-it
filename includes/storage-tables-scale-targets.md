---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/09/2020
ms.author: tamram
ms.openlocfilehash: 27617da97ced9ac775beae71e4c25202944b2aba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78941028"
---
Nella tabella seguente vengono descritti gli obiettivi di capacità, scalabilità e prestazioni per l'archiviazione tabelle.

| Risorsa | Destinazione |
|----------|---------------|
| Numero di tabelle in un account di archiviazione di Azure | Limitato solo dalla capacità dell'account di archiviazione |
| Numero di partizioni in una tabella | Limitato solo dalla capacità dell'account di archiviazione |
| Numero di entità in una partizione | Limitato solo dalla capacità dell'account di archiviazione |
| Dimensioni massime di una singola tabella | 500 TiB |
| Dimensione massima di una singola entità, inclusi tutti i valori delle proprietà | 1 MiB |
| Numero massimo di proprietà in un'entità di tabella | 255 (incluse le tre proprietà di sistema, **PartitionKey**, **RowKey**e **timestamp**) |
| Dimensioni totali massime di una singola proprietà in un'entità | Varia in base al tipo di proprietà. Per ulteriori informazioni, vedere la pagina relativa ai **tipi di proprietà** in [informazioni sul modello di dati del servizio tabelle](/rest/api/storageservices/understanding-the-table-service-data-model). |
| Dimensioni di **PartitionKey** | Una stringa con dimensioni massime di 1 KiB |
| Dimensioni di **RowKey** | Una stringa con dimensioni massime di 1 KiB |
| Dimensioni di una transazione del gruppo di entità | Una transazione può includere al massimo 100 entità e il payload deve avere una dimensione inferiore a 4 MiB. Una transazione del gruppo di entità può includere un aggiornamento a un'entità una sola volta. |
| Numero massimo di criteri di accesso archiviati per tabella | 5 |
| Frequenza massima di richieste per account di archiviazione | 20.000 transazioni al secondo, che presuppone una dimensione dell'entità di 1 KiB |
| Velocità effettiva di destinazione per una singola partizione di tabella (1 KiB-entità) | Fino a 2.000 entità al secondo |