---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/20/2019
ms.author: tamram
ms.openlocfilehash: 5ab03b682dd0ed1dc7b198e89c86e7a74c6275cd
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "67457397"
---
| Risorsa | Destinazione |
|----------|---------------|
| Dimensioni massime di una singola tabella | 500 TiB |
| Dimensioni massime di un'entità di tabella | 1 MiB |
| Numero massimo di proprietà in un'entità di tabella | 255, che include tre proprietà di sistema: PartitionKey, RowKey e timestamp |
| Dimensioni totali massime dei valori delle proprietà in un'entità | 1 MiB |
| Dimensioni totali massime di una singola proprietà in un'entità | Varia in base al tipo di proprietà. Per ulteriori informazioni, vedere la pagina relativa ai **tipi di proprietà** in [informazioni sul modello di dati del servizio tabelle](/rest/api/storageservices/understanding-the-table-service-data-model). |
| Numero massimo di criteri di accesso archiviati per tabella | 5 |
| Frequenza massima di richieste per account di archiviazione | 20.000 transazioni al secondo, che presuppone una dimensione dell'entità di 1 KiB |
| Velocità effettiva di destinazione per una singola partizione di tabella (1 KiB-entità) | Fino a 2.000 entità al secondo |