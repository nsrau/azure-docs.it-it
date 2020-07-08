---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 2604a1608f21d7239db755027e15b8198fb3f9f2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81791633"
---
### <a name="functions-2x-and-higher"></a>Funzioni 2.x e versioni successive

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            }
        }
    }
}  
```

|Proprietà  |Predefinito | Descrizione |
|---------|---------|---------|
|maxBatchSize|10|Il numero massimo degli eventi ricevuto per ogni ciclo di ricezione.|
|prefetchCount|300|Conteggio predefinito di pre-recupero utilizzato dall'oggetto sottostante `EventProcessorHost` .|
|batchCheckpointFrequency|1|Il numero di batch di eventi da elaborare prima di creare un checkpoint di cursore EventHub.|

> [!NOTE]
> Per informazioni di riferimento su host.jsin funzioni di Azure 2. x e versioni successive, vedere [host.jsdi riferimento per funzioni di Azure](../articles/azure-functions/functions-host-json.md).

### <a name="functions-1x"></a>Funzioni 1.x

```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|Proprietà  |Predefinito | Descrizione |
|---------|---------|---------| 
|maxBatchSize|64|Il numero massimo degli eventi ricevuto per ogni ciclo di ricezione.|
|prefetchCount|n/d|Pre-recupero predefinito che verrà utilizzato dall'oggetto sottostante `EventProcessorHost` .| 
|batchCheckpointFrequency|1|Il numero di batch di eventi da elaborare prima di creare un checkpoint di cursore EventHub.| 

> [!NOTE]
> Per informazioni di riferimento su host.jsin funzioni di Azure 1. x, vedere [host.jsdi riferimento per le funzioni di Azure 1. x](../articles/azure-functions/functions-host-json-v1.md).

