---
ms.openlocfilehash: 0bae8946b7d81fbf45698cefe7c8b00bbc94d00c
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168112"
---
#### <a name="determine-memory-usage"></a>Determinare l'utilizzo della memoria 

In **monitoraggio**selezionare **log (Analytics)**, quindi copiare la query di telemetria seguente e incollarla nella finestra di query e selezionare **Esegui**. Questa query restituisce l'utilizzo totale della memoria per ogni tempo campione.

```
performanceCounters
| where name == "Private Bytes"
| project timestamp, name, value
```

I risultati sono simili all'esempio seguente:

| timestamp \[ UTC\]          | name          | Valore       |
|----------------------------|---------------|-------------|
| 9/12/2019, 1:05:14 \. 947 am | Private Bytes | 209.932.288 |
| 9/12/2019, 1:06:14 \. 994 am | Private Bytes | 212.189.184 |
| 9/12/2019, 1:06:30 \. 010 | Private Bytes | 231.714.816 |
| 9/12/2019, 1:07:15 \. 040 am | Private Bytes | 210.591.744 |
| 9/12/2019, 1:12:16 \. 285 am | Private Bytes | 216.285.184 |
| 9/12/2019, 1:12:31 \. 376 am | Private Bytes | 235.806.720 |

#### <a name="determine-duration"></a>Determinazione della durata 

Monitoraggio di Azure tiene traccia delle metriche a livello di risorsa, che per le funzioni è l'app per le funzioni. Application Insights integrazione genera metriche per ogni singola funzione. Di seguito è riportato un esempio di query di analisi per ottenere la durata media di una funzione:

```
customMetrics
| where name contains "Duration"
| extend averageDuration = valueSum / valueCount
| summarize averageDurationMilliseconds=avg(averageDuration) by name
```

| name                       | averageDurationMilliseconds |
|----------------------------|-----------------------------|
| AvgDurationMs QueueTrigger | 16 \. 087                     |
| MaxDurationMs QueueTrigger | 90 \. 249                     |
| MinDurationMs QueueTrigger | 8 \. 522                      |
