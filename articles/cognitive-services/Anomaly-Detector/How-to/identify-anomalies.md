---
title: Come utilizzare l'API Anomaly Detector sui dati della serie temporale
titleSuffix: Azure Cognitive Services
description: Informazioni su come rilevare le anomalie nei dati come batch o sui dati in streaming.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: aahi
ms.openlocfilehash: ca93de71f64efaf21c78b37b9c9aee193d13b28d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "71840231"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>Procedura: Utilizzare l'API Anomaly Detector sui dati della serie temporaleHow to: Use the Anomaly Detector API on your time series data  

[L'API Rilevatore](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) di anomalie fornisce due metodi di rilevamento delle anomalie. È possibile rilevare le anomalie come batch durante le serie di periodi o come i dati vengono generati rilevando lo stato di anomalia del punto dati più recente. Il modello di rilevamento restituisce risultati di anomalia insieme al valore previsto di ogni punto dati e ai limiti di rilevamento delle anomalie superiore e inferiore. è possibile utilizzare questi valori per visualizzare l'intervallo di valori normali e anomalie nei dati.

## <a name="anomaly-detection-modes"></a>Modalità di rilevamento delle anomalie 

L'API Anomaly Detector fornisce modalità di rilevamento: batch e streaming.

> [!NOTE]
> Gli URL di richiesta seguenti devono essere combinati con l'endpoint appropriato per la sottoscrizione. Ad esempio: `https://<your-custom-subdomain>.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>Rilevamento in batch

Per rilevare anomalie in un batch di punti dati in un determinato intervallo di tempo, utilizzare il seguente URI di richiesta con i dati della serie temporale: 

`/timeseries/entire/detect`. 

Inviando i dati della serie temporale contemporaneamente, l'API genererà un modello usando l'intera serie e analizzerà ogni punto dati con esso.  

### <a name="streaming-detection"></a>Rilevamento dello streaming

Per rilevare continuamente le anomalie nei dati in streaming, utilizzare il seguente URI di richiesta con il punto dati più recente: 

`/timeseries/last/detect'`. 

Inviando nuovi punti dati durante la generazione, è possibile monitorare i dati in tempo reale. Verrà generato un modello con i punti dati inviati e l'API determinerà se l'ultimo punto della serie temporale è un'anomalia.

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>Regolazione dei limiti di rilevamento delle anomalie inferiori e superiori

Per impostazione predefinita, i limiti superiore e inferiore `expectedValue`per `upperMargin`il `lowerMargin`rilevamento delle anomalie vengono calcolati utilizzando , , e . Se sono necessari limiti diversi, è `marginScale` `upperMargin` consigliabile applicare un file a o `lowerMargin`. I confini verrebbero calcolati come segue:

|Limite  |Calcolo  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

Gli esempi seguenti mostrano un risultato dell'API Rilevatore di anomalie con sensibilità diverse.

### <a name="example-with-sensitivity-at-99"></a>Esempio con sensibilità a 99

![Sensibilità predefinita](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>Esempio con sensibilità a 95

![99 Sensibilità](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>Esempio con sensibilità a 85

![85 Sensibilità](../media/sensitivity_85.png)

## <a name="next-steps"></a>Passaggi successivi

* [Che cos'è l'API Rilevatore di anomalie?](../overview.md)
* [Guida introduttiva: Rilevare le anomalie nei dati della serie temporale usando l'API REST di Rilevamento anomalie](../quickstarts/detect-data-anomalies-csharp.md)
