---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 8f3a58d3a7470867ab23249bbd645289e010ad89
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997307"
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
|prefetchCount|300|Il conteggio predefinito di pre-recupero usato dall'elemento `EventProcessorHost` sottostante. Il valore minimo consentito è 10.|
|batchCheckpointFrequency|1|Il numero di batch di eventi da elaborare prima di creare un checkpoint di cursore EventHub.|

> [!NOTE]
> Per informazioni di riferimento su host.json in Funzioni di Azure 2.x e versioni successive, vedere [Informazioni di riferimento su host.json per Funzioni di Azure](../articles/azure-functions/functions-host-json.md).

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
|prefetchCount|n/d|Il conteggio predefinito di pre-recupero che verrà usato dall'elemento `EventProcessorHost` sottostante.| 
|batchCheckpointFrequency|1|Il numero di batch di eventi da elaborare prima di creare un checkpoint di cursore EventHub.| 

> [!NOTE]
> Per informazioni di riferimento su host.json in Funzioni di Azure 1.x, vedere [Informazioni di riferimento su host.json per Funzioni di Azure 1.x](../articles/azure-functions/functions-host-json-v1.md).
