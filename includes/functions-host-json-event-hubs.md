---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: b5d8f67a70961aab21312b6f241081dcb33f66fb
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133974"
---
```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|Proprietà  |Predefinito | DESCRIZIONE |
|---------|---------|---------| 
|maxBatchSize|64|Il numero massimo degli eventi ricevuto per ogni ciclo di ricezione.|
|prefetchCount|n/d|Il valore predefinito di PrefetchCount che verrà utilizzato dall’EventProcessorHost sottostante.| 
|batchCheckpointFrequency|1|Il numero di batch di eventi da elaborare prima di creare un checkpoint di cursore EventHub.| 
