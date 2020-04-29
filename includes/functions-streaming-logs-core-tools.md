---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2020
ms.locfileid: "68881344"
---
#### <a name="built-in-log-streaming"></a>Streaming di log predefinito

Usare l' `logstream` opzione per iniziare a ricevere i log in streaming di una specifica app per le funzioni in esecuzione in Azure, come nell'esempio seguente:

```bash
func azure functionapp logstream <FunctionAppName>
```

#### <a name="live-metrics-stream"></a>Flusso di metriche live

È anche possibile visualizzare l' [Live Metrics Stream](../articles/azure-monitor/app/live-stream.md) per l'app per le funzioni in una nuova finestra del browser `--browser` includendo l'opzione, come nell'esempio seguente:

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
