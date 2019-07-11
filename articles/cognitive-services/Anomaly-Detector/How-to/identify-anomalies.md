---
title: Come utilizzare l'API rilevatore di anomalie nei dati delle serie temporali
titleSuffix: Azure Cognitive Services
description: Informazioni su come rilevare le anomalie nei dati, come un batch o nel flusso di dati.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 551196815004cb047680e2ae2f8dbe32186c1a0c
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721785"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>Procedura: Usare l'API rilevatore di anomalie in dati delle serie temporali  

Il [API rilevatore di anomalie](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) fornisce due metodi di rilevamento delle anomalie. È possibile rilevare le anomalie sia come batch durante i tempi di serie, o i dati vengono generati da rilevare lo stato delle anomalie del punto dati più recente. Il modello di rilevamento restituisce risultati di anomalie insieme a valore previsto di ogni punto dati e le anomalie superiore e inferiore dei limiti di rilevamento. è possibile usare questi valori per visualizzare l'intervallo di valori normali e le anomalie nei dati.

## <a name="anomaly-detection-modes"></a>Modalità di rilevamento anomalie 

L'API rilevatore di anomalie fornisce modalità di rilevamento: batch e streaming.

> [!NOTE]
> La richiesta seguente che gli URL devono essere combinati con l'endpoint appropriato per la sottoscrizione. Ad esempio: `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>Rilevamento in batch

Per rilevare le anomalie in tutta una serie di punti dati in un intervallo di tempo specifico, usare il seguente URI richiesta con i dati delle serie temporali: 

[https://login.microsoftonline.com/consumers/](`/timeseries/entire/detect`). 

Mediante l'invio di dati delle serie temporali in una sola volta, l'API genera un modello usando l'intera serie e analizzare ciascun punto dati con esso.  

### <a name="streaming-detection"></a>Rilevamento di streaming

Per rilevare in modo continuo anomalie nei dati di streaming, usare il seguente URI richiesta con il punto dati più recente: 

[https://login.microsoftonline.com/common/](`/timeseries/last/detect'`). 

Inviando i nuovi punti dati come generarli, è possibile monitorare i dati in tempo reale. Verrà generato un modello con i punti dati inviati e l'API consente di verificare se l'ultimo punto nella serie temporale è un'anomalia.

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>Regolare i limiti di rilevamento delle anomalie inferiore e superiore

Per impostazione predefinita, i limiti superiori e inferiori per il rilevamento anomalie vengono calcolati utilizzando `expectedValue`, `upperMargin`, e `lowerMargin`. Se sono necessari diversi limiti, si consiglia di applicare una `marginScale` al `upperMargin` o `lowerMargin`. I limiti verrebbe calcolati come segue:

|Limite  |Calcolo  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

Gli esempi seguenti mostrano il risultato di un'API di rilevamento delle anomalie a sensibilità diversi.

### <a name="example-with-sensitivity-at-99"></a>Esempio con sensibilità a 99

![Sensibilità predefinita](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>Esempio con sensibilità 95

![Sensibilità 99](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>Esempio con sensibilità 85

![Sensibilità 85](../media/sensitivity_85.png)

## <a name="next-steps"></a>Fasi successive

* [Che cos'è l'API rilevatore di anomalie?](../overview.md)
* [Avvio rapido: Rilevare le anomalie nei dati delle serie temporali utilizzando l'API REST di rilevatore di anomalie](../quickstarts/detect-data-anomalies-csharp.md)