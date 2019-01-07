---
title: Definire la forma degli eventi con Anteprima di Azure Time Series Insights | Microsoft Docs
description: Informazioni su come definire la struttura degli eventi in Anteprima di Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: eb398ad621167ad9f9b245fb8aa98c6942b87668
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2018
ms.locfileid: "53557428"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Definire la struttura degli eventi con Anteprima di Time Series Insights

Questo articolo consente di definire la struttura del file JSON per massimizzare l'efficienza delle query di Anteprima di Azure Time Series Insights.

## <a name="best-practices"></a>Procedure consigliate

È importante esaminare la procedura per l'invio di eventi ad Anteprima di Time Series Insights. È in particolare necessario eseguire sempre queste operazioni:

* Inviare dati tramite rete nel modo più efficiente possibile.
* Archiviare i dati per poterli aggregare in modo più adeguato allo scenario.

Per ottenere prestazioni migliori possibili per le query, seguire questa procedura:

* Non inviare proprietà non necessarie. Anteprima di Time Series Insights prevede la fatturazione per l'utilizzo. È consigliabile archiviare ed elaborare i dati di cui si eseguiranno query.
* Usare i campi di istanza per i dati statici. Questa procedura consente di evitare l'invio di dati statici in rete. I campi di istanza, un componente del modello serie temporale, funzionano come i dati di riferimento nel servizio Time Series Insights disponibile a livello generale. Per altre informazioni sui Campi di istanza, vedere [Modelli di serie temporali](./time-series-insights-update-tsm.md).
* Condividere le proprietà delle dimensioni tra due o più eventi. Questa procedura consente di inviare i dati in rete in modo più efficiente.
* Non usare un annidamento troppo profondo delle matrici. Anteprima di Time Series Insights supporta fino a due livelli di matrici annidate contenenti oggetti. Anteprima di Time Series Insights rende flat le matrici nei messaggi in più eventi con coppie di valori delle proprietà.
* Se sono disponibili solo alcune misure per tutti gli eventi o per la maggior parte degli eventi, è consigliabile inviare tali misure come proprietà separate nello stesso oggetto. L'invio separato riduce il numero di eventi e, poiché è necessario elaborare meno eventi, la procedura potrebbe migliorare le prestazioni delle query .

## <a name="example"></a>Esempio

L'esempio seguente è basato su uno scenario in cui due o più dispositivi inviano misure o segnali. Le misure o i segnali possono essere ad esempio relativi a *velocità del flusso*, *pressione dell'olio nel motore*, *temperatura* e *umidità*.

Nell'esempio seguente è presente un singolo messaggio dell'hub IoT, in cui la matrice esterna contiene una sezione condivisa di valori di dimensione comuni. La matrice esterna usa i dati dell'istanza della serie temporale per incrementare l'efficienza del messaggio. I dati dell'istanza della serie temporale includono i metadati del dispositivo, che non vengono modificati in ogni evento, ma forniscono proprietà utili per l'analisi dei dati. Per risparmiare sui byte inviati in rete e rendere più efficiente il messaggio, prendere in considerazione l'invio in batch dei valori delle dimensioni comuni e l'uso dei metadati dell'istanza della serie temporale.

### <a name="example-json-payload"></a>Payload JSON di esempio

```JSON
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "Flow Rate ft3/s": 1.0172575712203979,
                "Engine Oil Pressure psi ": 34.7
            },
            {
                "Flow Rate ft3/s": 2.445906400680542,
                "Engine Oil Pressure psi ": 49.2
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "Flow Rate psi": 0.58015072345733643,
                "Engine Oil Pressure psi ": 22.2
            }
        ]
    }
]
```

### <a name="time-series-instance"></a>Istanza della serie temporale 
> [!NOTE]
> L'ID della serie temporale è *deviceId*.

```JSON
{
    "timeSeriesId": [
      "FXXX"
    ],
    "typeId": "17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds": [
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description": null,
    "instanceFields": {
      "L1": "REVOLT SIMULATOR",
      "L2": "Battery System",
    }
  },
  {
    "timeSeriesId": [
      "FYYY"
    ],
    "typeId": "17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds": [
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description": null,
    "instanceFields": {
      "L1": "COMMON SIMULATOR",
      "L2": "Battery System",
    }
  },
```

Anteprima di Time Series Insights crea un join di una tabella (dopo la trasformazione in flat) durante la fase di query. La tabella include colonne aggiuntive, ad esempio **Type**. L'esempio seguente illustra come è possibile [definire la struttura](./time-series-insights-send-events.md#json) dei dati di telemetria:

| deviceId  | type | L1 | L2 |  timestamp | series.Flow Rate ft3/s | series.Engine Oil Pressure psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | REVOLT SIMULATOR | Battery System | 2018-01-17T01:17:00Z |    1.0172575712203979 |    34.7 |
| `FXXX` | LINE_DATA    REVOLT | SIMULATOR |    Battery System |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA    COMMON | SIMULATOR |    Battery System |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

Nell'esempio precedente osservare quanto segue:

* Le proprietà statiche vengono archiviate in Anteprima di Time Series Insights per ottimizzare i dati inviati in rete.
* I dati di Anteprima di Time Series Insights vengono aggiunti in fase di query usando l'elemento *timeSeriesId* definito nell'istanza.
* Vengono usati due livelli di annidamento, ovvero il numero massimo supportato da Anteprima di Time Series Insights. È essenziale evitare matrici annidate profondamente.
* Poiché le misure sono poche, vengono inviate come proprietà separate nello stesso oggetto. Nell'esempio **series.Flow Rate psi**, **series.Engine Oil Pressure psi** e **series.Flow Rate ft3/s** sono colonne univoche.

>[!IMPORTANT]
> I campi di istanza non vengono archiviati con i dati di telemetria, ma con i metadati nel **modello di serie temporale**.
> La tabella precedente rappresenta la visualizzazione della query.

## <a name="next-steps"></a>Passaggi successivi

Per mettere in pratica queste linee guida, vedere [Sintassi delle query di Anteprima di Azure Time Series Insights](./time-series-insights-query-data-csharp.md). Verranno fornite altre informazioni sulla sintassi delle query per l'API REST di accesso ai dati di Anteprima di Time Series Insights.

Per informazioni sulle forme JSON supportate, vedere [Forme JSON supportate](./time-series-insights-send-events.md#json).
