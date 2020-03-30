---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68881344"
---
#### <a name="built-in-log-streaming"></a>Streaming dei log integrato

Usare `logstream` l'opzione per iniziare a ricevere i log di streaming di un'app per le funzioni specifica in esecuzione in Azure, come nell'esempio seguente:Use the option to start receiving streaming logs of a specific function app running in Azure, as in the following example:

```bash
func azure functionapp logstream <FunctionAppName>
```

#### <a name="live-metrics-stream"></a>Flusso di metriche live

Puoi anche visualizzare live [metrics Stream](../articles/azure-monitor/app/live-stream.md) per la tua app `--browser` per le funzioni in una nuova finestra del browser includendo l'opzione, come nell'esempio seguente:

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
