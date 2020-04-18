---
title: Procedure consigliate per la definizione di JSON - Query di Azure Time Series Insights . Documenti Microsoft
description: Informazioni su come migliorare l'efficienza delle query di Azure Time Series Insights modellando JSON.
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 04/17/2020
ms.custom: seodec18
ms.openlocfilehash: 63a708f80ad18309269e37c354b047c304a260d3
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641302"
---
# <a name="shape-json-to-maximize-query-performance"></a>Modellare JSON per ottimizzare le prestazioni delle query

Questo articolo fornisce indicazioni su come modellare JSON per ottimizzare l'efficienza delle query di Azure Time Series Insights.This article provides guidance on how to shape JSON to maximize the efficiency of your Azure Time Series Insights queries.

## <a name="video"></a>Video

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>Informazioni sulle procedure consigliate per modellare JSON in base alle esigenze di archiviazione.</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>Procedure consigliate

Pensa a come invii gli eventi a Time Series Insights. Vale a dire, è sempre:

1. Inviare dati tramite rete nel modo più efficiente possibile.
1. Assicurarsi che i dati siano archiviati in modo da poter eseguire aggregazioni adatte allo scenario.
1. Assicurati di non raggiungere i limiti massimi di proprietà di Time Series Insights di:
   - 600 proprietà (colonne) per ambienti S1.
   - 800 proprietà (colonne) per ambienti S2.

> [!TIP]
> Esaminare [i limiti e](time-series-insights-update-plan.md) la pianificazione in Azure Time Series Insights Preview.Review limits and planning in Azure Time Series Insights Preview.

Le indicazioni seguenti consentono di garantire le migliori prestazioni possibili per le query:

1. Non utilizzare proprietà dinamiche, ad esempio un ID tag, come nome di proprietà. Questo uso contribuisce a raggiungere il limite massimo di proprietà.
1. Non inviare proprietà non necessarie. Se una proprietà di query non è necessaria, è consigliabile non inviarla. In questo modo si evitano limitazioni di archiviazione.
1. Utilizzare i dati di [riferimento](time-series-insights-add-reference-data-set.md) per evitare l'invio di dati statici in rete.
1. Condividere le proprietà delle dimensioni tra più eventi per inviare dati in rete in modo più efficiente.
1. Non usare un annidamento troppo profondo delle matrici. Time Series Insights supporta fino a due livelli di matrici nidificate che contengono oggetti. Time Series Insights appiattisce le matrici nei messaggi in più eventi con coppie di valori di proprietà.
1. Se sono disponibili solo alcune misure per tutti gli eventi o per la maggior parte degli eventi, è consigliabile inviare tali misure come proprietà separate nello stesso oggetto. L'invio di essi separatamente riduce il numero di eventi e potrebbe migliorare le prestazioni delle query perché è necessario elaborare un numero inferiore di eventi. Quando sono presenti più misure, l'invio come valori in una singola proprietà riduce al minimo la possibilità di raggiungere il limite massimo di proprietà.

## <a name="example-overview"></a>Panoramica di esempio

Nei due esempi seguenti viene illustrato come inviare eventi per evidenziare le raccomandazioni precedenti. Seguendo ogni esempio, è possibile esaminare come sono stati applicati i suggerimenti.

Gli esempi sono basati su uno scenario in cui più dispositivi inviano misure o segnali. Le misurazioni o i segnali possono essere Velocità di flusso, Pressione dell'olio motore, Temperatura e Umidità. Nel primo esempio sono presenti poche misure in tutti i dispositivi. Il secondo esempio ha molti dispositivi e ogni dispositivo invia molte misure univoche.

## <a name="scenario-one-only-a-few-measurements-exist"></a>Scenario uno: esistono solo poche misurazioni

> [!TIP]
> Si consiglia di inviare ogni misurazione o segnale come proprietà o colonna separata.

Nell'esempio seguente è disponibile un singolo messaggio dell'hub IoT di Azure in cui la matrice esterna contiene una sezione condivisa di valori di dimensione comuni. La matrice esterna usa dati di riferimento per incrementare l'efficienza del messaggio. I dati di riferimento contengono metadati del dispositivo che non cambiano con ogni evento, ma forniscono proprietà utili per l'analisi dei dati. L'invio in batch di valori di dimensione comuni e l'utilizzo di dati di riferimento consentono di risparmiare sui byte inviati in rete, il che rende il messaggio più efficiente.

Si consideri il payload JSON seguente inviato all'ambiente Time Series Insights GA usando un [oggetto Messaggio dispositivo IoT](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.message?view=azure-dotnet) che viene serializzato in JSON quando viene inviato al cloud di Azure:Consider the following JSON payload sent to your Time Series Insights GA environment using an IoT Device Message object that is serialized into JSON when sent to Azure cloud:


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
                "Flow Rate ft3/s": 0.58015072345733643,
                "Engine Oil Pressure psi ": 22.2
            }
        ]
    }
]
```

* Tabella dati di riferimento con la proprietà chiave **deviceId**:

   | deviceId | messageId | deviceLocation |
   | --- | --- | --- |
   | FXXX | LINE\_DATA | EU |
   | FYYY | LINE\_DATA | Stati Uniti |

* Tabella degli eventi Time Series Insights, dopo la conversione:

   | deviceId | messageId | deviceLocation | timestamp | series.Flow Rate ft3/s | series.Engine Oil Pressure psi |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | FYYY | LINE\_DATA | Stati Uniti | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

> [!NOTE]
> - La colonna **deviceId** viene usata come intestazione di colonna per i diversi dispositivi nell'insieme. Rendere il valore **deviceId** il proprio nome di proprietà limita il totale dei dispositivi a 595 (per gli ambienti S1) o 795 (per gli ambienti S2) con le altre cinque colonne.
> - Le proprietà non necessarie vengono evitate (ad esempio, le informazioni sulla marca e sul modello). Poiché le proprietà non verranno interrogate in futuro, l'eliminazione di esse consente una migliore efficienza di rete e archiviazione.
> - I dati di riferimento vengono usati per ridurre il numero di byte trasferiti in rete. I due attributi **messageId** e **deviceLocation** vengono uniti utilizzando la proprietà chiave **deviceId**. Questi dati vengono uniti ai dati di telemetria in fase di ingresso e vengono quindi archiviati in Time Series Insights per l'esecuzione di query.
> - Vengono utilizzati due livelli di annidamento, ovvero la quantità massima di annidamento supportata da Time Series Insights. È essenziale evitare matrici annidate profondamente.
> - Le misure vengono inviate come proprietà separate all'interno dello stesso oggetto perché sono presenti poche misure. In questo caso le colonne **series.Flow Rate psi** e **series.Engine Oil Pressure ft3/s** sono colonne univoche.

## <a name="scenario-two-several-measures-exist"></a>Scenario due: esistono diverse misure

> [!TIP]
> È consigliabile inviare le misure come tuple "tipo", "unità" e "valore".

Payload JSON di esempio:

```JSON
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 1.0172575712203979
            },
            {
                "tagId": "oilPressure",
                "value": 49.2
            },
            {
                "tagId": "pumpRate",
                "value": 2.445906400680542
            },
            {
                "tagId": "oilPressure",
                "value": 34.7
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 0.58015072345733643
            },
            {
                "tagId": "oilPressure",
                "value": 22.2
            }
        ]
    }
]
```

* Tabella dati di riferimento con le proprietà chiave **deviceId** e **series.tagId**:

   | deviceId | series.tagId | messageId | deviceLocation | type | unit |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINE\_DATA | EU | Velocità del flusso | ft3/s |
   | FXXX | oilPressure | LINE\_DATA | EU | Pressione dell'olio del motore | psi |
   | FYYY | pumpRate | LINE\_DATA | Stati Uniti | Velocità del flusso | ft3/s |
   | FYYY | oilPressure | LINE\_DATA | Stati Uniti | Pressione dell'olio del motore | psi |

* Tabella degli eventi Time Series Insights, dopo la conversione:

   | deviceId | series.tagId | messageId | deviceLocation | type | unit | timestamp | series.value |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINE\_DATA | EU | Velocità del flusso | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 | 
   | FXXX | oilPressure | LINE\_DATA | EU | Pressione dell'olio del motore | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX | pumpRate | LINE\_DATA | EU | Velocità del flusso | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 | 
   | FXXX | oilPressure | LINE\_DATA | EU | Pressione dell'olio del motore | psi | 2018-01-17T01:17:00Z | 49.2 |
   | FYYY | pumpRate | LINE\_DATA | Stati Uniti | Velocità del flusso | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | FYYY | oilPressure | LINE\_DATA | Stati Uniti | Pressione dell'olio del motore | psi | 2018-01-17T01:18:00Z | 22.2 |

> [!NOTE]
> - Le colonne **deviceId** e **series.tagId** fungono da intestazioni di colonna per i vari dispositivi e tag in un parco. L'utilizzo di ciascuno come proprio attributo limita la query a 594 (per gli ambienti S1) o 794 (per gli ambienti S2) dispositivi totali con le altre sei colonne.
> - Le proprietà non necessarie sono state evitate, per la ragione citata nel primo esempio.
> - I dati di riferimento vengono utilizzati per ridurre il numero di byte trasferiti in rete introducendo **deviceId**, utilizzato per la coppia univoca di **messageId** e **deviceLocation**. Il numero composto **di serie.tagId** viene utilizzato per la coppia univoca di **tipo** e **unità**. La chiave composita consente di utilizzare la coppia **deviceId** e **series.tagId** per fare riferimento a quattro valori: **messageId, deviceLocation, type** e **unit**. Questi dati vengono uniti ai dati di telemetria in fase di ingresso. Viene quindi archiviato in Time Series Insights per l'esecuzione di query.
> - Vengono utilizzati due strati di nidificazione, per la ragione citata nel primo esempio.

### <a name="for-both-scenarios"></a>Per entrambi gli scenari

Per una proprietà con un numero elevato di valori possibili, è consigliabile inviare come valori distinti all'interno di una singola colonna anziché creare una nuova colonna per ogni valore. Dai due esempi precedenti:

  - Nel primo esempio, alcune proprietà hanno diversi valori, pertanto è opportuno rendere ognuna una proprietà separata.
  - Nel secondo esempio, le misure non sono specificate come singole proprietà. Sono invece una matrice di valori o misure in una proprietà di serie comune. Viene inviato il nuovo **key tagId,** che crea la nuova colonna **series.tagId** nella tabella appiattita. Il nuovo **tipo** di proprietà e **l'unità** vengono creati utilizzando i dati di riferimento in modo che non venga raggiunto il limite di proprietà.

## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni sull'invio di messaggi del [dispositivo Dell'hub IoT al cloud](../iot-hub/iot-hub-devguide-messages-construct.md).

- Per altre informazioni sulla sintassi delle query per l'API REST per l'accesso ai dati di Time Series Insights, leggere la [sintassi della query Azure Time Series Insights.](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-syntax)

- Informazioni su [come modellare gli eventi](./time-series-insights-send-events.md).
