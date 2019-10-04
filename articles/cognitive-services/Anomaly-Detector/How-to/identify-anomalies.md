---
title: Come usare l'API del rilevatore di anomalie nei dati delle serie temporali
titleSuffix: Azure Cognitive Services
description: Informazioni su come rilevare le anomalie nei dati come batch o in streaming di dati.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: aahi
ms.openlocfilehash: ca93de71f64efaf21c78b37b9c9aee193d13b28d
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71840231"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>Procedura: Usare l'API del rilevatore di anomalie nei dati delle serie temporali  

L' [API rilevatore di anomalie](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) fornisce due metodi di rilevamento delle anomalie. È possibile rilevare le anomalie come batch in tutta la serie di volte o quando i dati vengono generati rilevando lo stato anomalie del punto dati più recente. Il modello di rilevamento restituisce risultati di anomalie insieme al valore previsto di ogni punto dati e ai limiti di rilevamento di anomalie superiori e inferiori. è possibile usare questi valori per visualizzare l'intervallo di valori normali e le anomalie nei dati.

## <a name="anomaly-detection-modes"></a>Modalità di rilevamento anomalie 

L'API rilevatore di anomalie fornisce le modalità di rilevamento: batch e flusso.

> [!NOTE]
> Gli URL di richiesta seguenti devono essere combinati con l'endpoint appropriato per la sottoscrizione. Ad esempio: `https://<your-custom-subdomain>.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>Rilevamento in batch

Per rilevare le anomalie in un batch di punti dati in un intervallo di tempo specifico, usare l'URI della richiesta seguente con i dati della serie temporale: 

`/timeseries/entire/detect` (Indici per tabelle con ottimizzazione per la memoria). 

Inviando contemporaneamente i dati delle serie temporali, l'API genererà un modello usando l'intera serie e analizzerà ogni punto dati.  

### <a name="streaming-detection"></a>Rilevamento del flusso

Per rilevare continuamente le anomalie nei dati di streaming, usare l'URI della richiesta seguente con il punto dati più recente: 

`/timeseries/last/detect'`. 

Inviando nuovi punti dati durante la generazione, è possibile monitorare i dati in tempo reale. Verrà generato un modello con i punti dati inviati e l'API determinerà se il punto più recente della serie temporale è un'anomalia.

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>Regolazione dei limiti di rilevamento di anomalie inferiori e superiori

Per impostazione predefinita, i limiti superiore e inferiore per il rilevamento delle anomalie vengono calcolati con `expectedValue`, `upperMargin` e `lowerMargin`. Se sono necessari limiti diversi, è consigliabile applicare una `marginScale` a `upperMargin` o `lowerMargin`. I limiti verranno calcolati come segue:

|Confine  |Calcolo  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

Gli esempi seguenti mostrano un risultato dell'API del rilevamento delle anomalie con sensibilità diverse.

### <a name="example-with-sensitivity-at-99"></a>Esempio con sensibilità a 99

![Sensibilità predefinita](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>Esempio con sensibilità a 95

![sensibilità 99](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>Esempio con sensibilità a 85

![sensibilità 85](../media/sensitivity_85.png)

## <a name="next-steps"></a>Passaggi successivi

* [Che cos'è l'API rilevatore di anomalie?](../overview.md)
* [Avvio rapido: Rilevare le anomalie nei dati delle serie temporali tramite l'API REST Rilevamento anomalie](../quickstarts/detect-data-anomalies-csharp.md)
