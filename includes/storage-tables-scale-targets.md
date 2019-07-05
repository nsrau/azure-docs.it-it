---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/20/2019
ms.author: tamram
ms.openlocfilehash: 5ab03b682dd0ed1dc7b198e89c86e7a74c6275cd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67457397"
---
| Resource | Destinazione |
|----------|---------------|
| Dimensioni massime di una singola tabella | 500 TiB |
| Dimensioni massime di un'entità di tabella | 1 MiB |
| Numero massimo di proprietà di un'entità di tabella | 255, che include tre proprietà di sistema: PartitionKey, RowKey e Timestamp |
| Dimensione totale massima dei valori di proprietà in un'entità | 1 MiB |
| Dimensione massima di una singola proprietà in un'entità | Varia in base al tipo di proprietà. Per altre informazioni, vedere **tipi di proprietà** nelle [informazioni sul modello di dati del servizio tabella](/rest/api/storageservices/understanding-the-table-service-data-model). |
| Numero massimo di criteri di accesso archiviati per ogni tabella | 5 |
| Frequenza massima di richieste per account di archiviazione | 20.000 transazioni al secondo, in cui si presuppone una dimensione delle entità di 1 KiB |
| Velocità effettiva da raggiungere per una partizione singola tabella (-entità di 1 KB) | Fino a 2000 entità al secondo |