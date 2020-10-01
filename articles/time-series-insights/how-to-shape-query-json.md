---
title: Procedure consigliate per il data shaping di query Azure Time Series Insights JSON | Microsoft Docs
description: Informazioni su come migliorare l'efficienza delle query Azure Time Series Insights tramite il data shaping JSON.
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.service: time-series-insights
ms.topic: article
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: b5723433cca20c934d4cb5b129d77c1c6d65feef
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91617112"
---
# <a name="shape-json-to-maximize-query-performance-in-your-gen1-environment"></a>Formato JSON per ottimizzare le prestazioni delle query nell'ambiente Gen1

> [!CAUTION]
> Questo è un articolo di Gen1.

Questo articolo fornisce indicazioni su come eseguire la modellazione di JSON per ottimizzare l'efficienza delle query Azure Time Series Insights.

## <a name="video"></a>Video

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>Informazioni sulle procedure consigliate per il data shaping di JSON per soddisfare le esigenze di archiviazione.</br>

> [!VIDEO <https://www.youtube.com/embed/b2BD5hwbg5I>]

## <a name="best-practices"></a>Procedure consigliate

Si pensi al modo in cui si inviano gli eventi a Azure Time Series Insights. In particolare, è sempre:

1. Inviare dati tramite rete nel modo più efficiente possibile.
1. Assicurarsi che i dati vengano archiviati in modo che sia possibile eseguire le aggregazioni appropriate per lo scenario in uso.
1. Assicurarsi di non raggiungere i limiti di proprietà massimi Azure Time Series Insights di:
   - 600 proprietà (colonne) per ambienti S1.
   - 800 proprietà (colonne) per ambienti S2.

Le linee guida seguenti consentono di ottenere le migliori prestazioni possibili per le query:

1. Non usare proprietà dinamiche, ad esempio un ID tag, come nome di proprietà. Questo utilizzo contribuisce a raggiungere il limite massimo di proprietà.
1. Non inviare proprietà non necessarie. Se una proprietà di query non è obbligatoria, è preferibile non inviarla. In questo modo si evitano limitazioni di archiviazione.
1. Usare i [dati di riferimento](time-series-insights-add-reference-data-set.md) per evitare di inviare dati statici in rete.
1. Condividere le proprietà della dimensione tra più eventi per inviare i dati sulla rete in modo più efficiente.
1. Non usare un annidamento troppo profondo delle matrici. Azure Time Series Insights supporta fino a due livelli di matrici annidate contenenti oggetti. Azure Time Series Insights rende Flat le matrici nei messaggi in più eventi con le coppie di valori di proprietà.
1. Se sono disponibili solo alcune misure per tutti gli eventi o per la maggior parte degli eventi, è consigliabile inviare tali misure come proprietà separate nello stesso oggetto. Inviarli separatamente riduce il numero di eventi e potrebbe migliorare le prestazioni delle query perché è necessario elaborare un numero inferiore di eventi. Quando sono presenti diverse misure, l'invio come valori in una singola proprietà riduce al minimo la possibilità di raggiungere il limite massimo di proprietà.

## <a name="example-overview"></a>Panoramica di esempio

Nei due esempi seguenti viene illustrato come inviare eventi per evidenziare le raccomandazioni precedenti. Seguendo ogni esempio, è possibile esaminare il modo in cui sono state applicate le raccomandazioni.

Gli esempi sono basati su uno scenario in cui più dispositivi inviano misure o segnali. Le misurazioni o i segnali possono essere la velocità del flusso, la pressione del petrolio, la temperatura e l'umidità del motore. Nel primo esempio sono presenti poche misure in tutti i dispositivi. Il secondo esempio ha molti dispositivi e ogni dispositivo invia molte misurazioni univoche.

## <a name="scenario-one-only-a-few-measurements-exist"></a>Scenario uno: esistono solo alcune misurazioni

> [!TIP]
> Si consiglia di inviare ogni misura o segnale come proprietà o colonna separata.

Nell'esempio seguente è presente un singolo messaggio dell'hub dell'area di Azure in cui la matrice esterna contiene una sezione condivisa dei valori delle dimensioni comuni. La matrice esterna usa dati di riferimento per incrementare l'efficienza del messaggio. I dati di riferimento contengono metadati del dispositivo che non cambiano con ogni evento, ma fornisce proprietà utili per l'analisi dei dati. L'invio in batch dei valori delle dimensioni comuni e l'utilizzo dei dati di riferimento vengono salvati sui byte inviati in rete, il che rende più efficiente il messaggio.

Si consideri il payload JSON seguente inviato all'ambiente Azure Time Series Insights GA usando un [oggetto messaggio del dispositivo](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.message?view=azure-dotnet) Internet che viene serializzato in JSON quando viene inviato al cloud di Azure:

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

- Tabella dati di riferimento con la proprietà chiave **DeviceID**:

   | deviceId | messageId | deviceLocation |
   | --- | --- | --- |
   | FXXX | LINE\_DATA | EU |
   | FYYY | LINE\_DATA | US |

- Azure Time Series Insights tabella eventi, dopo l'appiattimento:

   | deviceId | messageId | deviceLocation | timestamp | series.Flow Rate ft3/s | series.Engine Oil Pressure psi |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | FYYY | LINE\_DATA | US | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

> [!NOTE]

> - La colonna **deviceId** viene usata come intestazione di colonna per i diversi dispositivi nell'insieme. Impostando il valore di **DeviceID** , il nome della proprietà limita i dispositivi totali a 595 (per gli ambienti S1) o 795 (per gli ambienti S2) con le altre cinque colonne.
> - Sono state evitate proprietà non necessarie, ad esempio le informazioni su marca e modello. Poiché le proprietà non verranno sottoposte a query in futuro, l'eliminazione consente di migliorare l'efficienza di archiviazione e rete.
> - I dati di riferimento vengono usati per ridurre il numero di byte trasferiti in rete. I due attributi **MessageID** e **deviceLocation** vengono Uniti usando la proprietà chiave **DeviceID**. Questi dati vengono uniti in join con i dati di telemetria in fase di ingresso e vengono quindi archiviati in Azure Time Series Insights per l'esecuzione di query.
> - Vengono utilizzati due livelli di annidamento, ovvero la quantità massima di nidificazione supportata da Azure Time Series Insights. È essenziale evitare matrici annidate profondamente.
> - Le misure vengono inviate come proprietà separate all'interno dello stesso oggetto perché sono presenti poche misure. In questo caso le colonne **series.Flow Rate psi** e **series.Engine Oil Pressure ft3/s** sono colonne univoche.

## <a name="scenario-two-several-measures-exist"></a>Scenario due: esistono diverse misure

> [!TIP]
> È consigliabile inviare misure come tuple "tipo," "unità" e "valore".

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

- Tabella dati di riferimento con le proprietà chiave **DeviceID** e **Series. TagId**:

   | deviceId | series.tagId | messageId | deviceLocation | tipo | unit |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINE\_DATA | EU | Velocità del flusso | ft3/s |
   | FXXX | oilPressure | LINE\_DATA | EU | Pressione dell'olio del motore | psi |
   | FYYY | pumpRate | LINE\_DATA | US | Velocità del flusso | ft3/s |
   | FYYY | oilPressure | LINE\_DATA | US | Pressione dell'olio del motore | psi |

- Azure Time Series Insights tabella eventi, dopo l'appiattimento:

   | deviceId | series.tagId | messageId | deviceLocation | tipo | unit | timestamp | series.value |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINE\_DATA | EU | Velocità del flusso | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 |
   | FXXX | oilPressure | LINE\_DATA | EU | Pressione dell'olio del motore | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX | pumpRate | LINE\_DATA | EU | Velocità del flusso | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 |
   | FXXX | oilPressure | LINE\_DATA | EU | Pressione dell'olio del motore | psi | 2018-01-17T01:17:00Z | 49.2 |
   | FYYY | pumpRate | LINE\_DATA | US | Velocità del flusso | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | FYYY | oilPressure | LINE\_DATA | US | Pressione dell'olio del motore | psi | 2018-01-17T01:18:00Z | 22.2 |

> [!NOTE]

> - Le colonne **DeviceID** e **Series. TagId** vengono utilizzate come intestazioni di colonna per i vari dispositivi e tag in una flotta. L'uso di ogni attributo come rispettivo attributo limita la query a 594 (per gli ambienti S1) o 794 (per gli ambienti S2) il totale dei dispositivi con le altre sei colonne.
> - Sono state evitate proprietà non necessarie per il motivo menzionato nel primo esempio.
> - I dati di riferimento vengono usati per ridurre il numero di byte trasferiti in rete introducendo **DeviceID**, che viene usato per la coppia univoca di **MessageID** e **deviceLocation**. La serie di chiavi composite **. TagId** viene utilizzata per la coppia univoca di **tipo** e **unità**. La chiave composta consente di usare la coppia  **DeviceID** e **Series. TagId** per fare riferimento a quattro valori: **MessageID, deviceLocation, Type** e **unit**. Questi dati vengono uniti in join con i dati di telemetria in fase di ingresso. Viene quindi archiviato in Azure Time Series Insights per l'esecuzione di query.
> - Vengono utilizzati due livelli di annidamento, per il motivo citato nel primo esempio.

### <a name="for-both-scenarios"></a>Per entrambi gli scenari

Per una proprietà con un numero elevato di valori possibili, è preferibile inviare come valori distinti all'interno di una singola colonna anziché creare una nuova colonna per ogni valore. Dai due esempi precedenti:

- Nel primo esempio, alcune proprietà hanno diversi valori, quindi è opportuno creare ogni proprietà distinta.
- Nel secondo esempio, le misure non vengono specificate come singole proprietà. Si tratta invece di una matrice di valori o misure in una proprietà della serie comune. Viene inviata la nuova chiave **TagId** , che crea la nuova serie di colonne **. TagId** nella tabella bidimensionale. Il **tipo** e l' **unità** delle nuove proprietà vengono creati utilizzando i dati di riferimento in modo che non venga raggiunto il limite della proprietà.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sull'invio [di messaggi del dispositivo dell'hub Internet al cloud](../iot-hub/iot-hub-devguide-messages-construct.md).

- Per altre informazioni sulla sintassi di query per l'API REST di accesso ai dati Azure Time Series Insights, vedere [Azure Time Series Insights sintassi di query](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query-syntax) .

- Informazioni [su come eseguire la forma degli eventi](./time-series-insights-send-events.md).
