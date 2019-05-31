---
title: Procedure consigliate per definire la struttura del codice JSON nelle query di Azure Time Series Insights | Microsoft Docs
description: Informazioni su come migliorare l'efficienza delle query di Azure Time Series Insights.
services: time-series-insights
author: ashannon7
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 05/09/2019
ms.author: dpalled
ms.custom: seodec18
ms.openlocfilehash: 089285637bb740fea47f1fd07de0906dfe46662b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244468"
---
# <a name="shape-json-to-maximize-query-performance"></a>Forma JSON per ottimizzare le prestazioni delle query 

Questo articolo fornisce indicazioni su come forma JSON per ottimizzare l'efficienza delle query di Azure Time Series Insights.

## <a name="video"></a>Video

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>Informazioni sulle procedure consigliate per il data shaping per soddisfare le esigenze di archiviazione JSON.</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>Procedure consigliate
Considerare come si inviano eventi a Time Series Insights. Vale a dire, è sempre:

1. Inviare dati tramite rete nel modo più efficiente possibile.
1. Assicurarsi che i dati sono archiviati in un modo, in modo che sia possibile eseguire aggregazioni adatte per il proprio scenario.
1. Assicurarsi che non raggiungere i limiti della proprietà numero massimo di Time Series Insights di:
   - 600 proprietà (colonne) per ambienti S1.
   - 800 proprietà (colonne) per ambienti S2.

Il materiale sussidiario seguente consente di garantire le migliori prestazioni possibili:

1. Non usare le proprietà dinamiche, ad esempio un ID di tag, come un nome di proprietà. Questo utilizzo contribuisce al raggiungimento del limite massimo di proprietà.
1. Non inviare proprietà non necessarie. Se la proprietà di una query non è necessaria, è consigliabile non inviare quest ' ultimo. In questo modo è evitare limitazioni di archiviazione.
1. Uso [dati di riferimento](time-series-insights-add-reference-data-set.md) per evitare l'invio di dati statici in rete.
1. Condividere le proprietà di dimensione fra più eventi per inviare dati tramite la rete in modo più efficiente.
1. Non usare un annidamento troppo profondo delle matrici. Time Series Insights supporta fino a due livelli di matrici nidificate che contengono oggetti. Time Series Insights appiattisce le matrici nei messaggi in più eventi con le coppie di valore di proprietà.
1. Se sono disponibili solo alcune misure per tutti gli eventi o per la maggior parte degli eventi, è consigliabile inviare tali misure come proprietà separate nello stesso oggetto. Inviarle separatamente riduce il numero di eventi e potrebbe migliorare le prestazioni delle query in quanto un minor numero di eventi devono essere elaborati. Quando sono disponibili diverse misure, inviarli come valori in una singola proprietà riduce al minimo la possibilità di raggiungere il limite massimo di proprietà.

## <a name="example-overview"></a>Cenni preliminari sull'esempio

I due esempi seguenti illustrano come inviare eventi a evidenziare le raccomandazioni precedenti. In seguito ogni esempio, è possibile visualizzare come sono state applicate le raccomandazioni.

Gli esempi sono basati su uno scenario in cui più dispositivi inviano misure o segnali. Le misure o segnali possono essere flusso velocità, pressione olio motore, temperatura e umidità. Nel primo esempio sono presenti poche misure in tutti i dispositivi. Nel secondo esempio dispone di numerosi dispositivi e ogni dispositivo invia molti misurazioni univoche.

## <a name="scenario-one-only-a-few-measurements-exist"></a>Scenario uno: Esistono solo alcune misurazioni

> [!TIP]
> Si consiglia di inviare ogni misura o un segnale come una proprietà separata o una colonna.

Nell'esempio seguente, è presente un solo messaggio dell'IoT Hub di Azure in cui la matrice esterna contiene una sezione condivisa comune di valori di dimensione. La matrice esterna usa dati di riferimento per incrementare l'efficienza del messaggio. I dati di riferimento contengano i metadati del dispositivo che non cambia con ogni evento, ma fornisce le proprietà utile per analisi dei dati. L'invio in batch i valori della dimensione comune e su come usare riferimenti consente di salvare i dati in byte inviati in rete, che rende il messaggio più efficiente.

Payload JSON di esempio:

```json
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

* Tabella di dati di riferimento che dispone della proprietà chiave **deviceId**:

   | deviceId | messageId | deviceLocation |
   | --- | --- | --- |
   | FXXX | LINE\_DATA | EU |
   | FYYY | LINE\_DATA | Stati Uniti |

* Tabella di eventi il tempo Series Insights, dopo l'appiattimento:

   | deviceId | messageId | deviceLocation |  timestamp | series.Flow Rate ft3/s | series.Engine Oil Pressure psi |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | FYYY | LINE\_DATA | Stati Uniti | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

Note su queste due tabelle:

- La colonna **deviceId** viene usata come intestazione di colonna per i diversi dispositivi nell'insieme. Rendendo il valore di ID dispositivo il proprio nome di proprietà consente di limitare i dispositivi totali 595 (per S1 ambienti) o 795 (per gli ambienti di S2) con le altre cinque colonne.
- Proprietà non necessarie vengono evitate per le informazioni di esempio, la marca e modello. Poiché le proprietà non sarà possibile eseguire query in futuro, eliminando li abilita rete migliore e l'efficienza di archiviazione.
- I dati di riferimento vengono usati per ridurre il numero di byte trasferiti in rete. I due attributi **messageId** e **deviceLocation** vengono unite mediante la proprietà chiave **deviceId**. Questi dati sono unita in join con i dati di telemetria in fase di ingresso e vengono quindi archiviati in Time Series Insights per l'esecuzione di query.
- Vengono utilizzati due livelli di annidamento, ovvero la quantità massima di annidamento supportato da Time Series Insights. È essenziale evitare matrici annidate profondamente.
- Le misure vengono inviate come proprietà separate all'interno dell'oggetto stesso perché vi sono alcune misure. In questo caso le colonne **series.Flow Rate psi** e **series.Engine Oil Pressure ft3/s** sono colonne univoche.

## <a name="scenario-two-several-measures-exist"></a>Scenario due: Esistono diverse misure

> [!TIP]
> Si consiglia di inviare le misurazioni come "tipo", "unità" e "value" Tuple.

Payload JSON di esempio:

```json
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

* Tabella di dati di riferimento con le proprietà chiave **deviceId** e **series.tagId**:

   | deviceId | series.tagId | messageId | deviceLocation | type | unit |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINE\_DATA | EU | Velocità del flusso | ft3/s |
   | FXXX | oilPressure | LINE\_DATA | EU | Pressione dell'olio del motore | psi |
   | FYYY | pumpRate | LINE\_DATA | Stati Uniti | Velocità del flusso | ft3/s |
   | FYYY | oilPressure | LINE\_DATA | Stati Uniti | Pressione dell'olio del motore | psi |

* Tabella di eventi il tempo Series Insights, dopo l'appiattimento:

   | deviceId | series.tagId | messageId | deviceLocation | type | unit |  timestamp | series.value |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINE\_DATA | EU | Velocità del flusso | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 | 
   | FXXX | oilPressure | LINE\_DATA | EU | Pressione dell'olio del motore | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX | pumpRate | LINE\_DATA | EU | Velocità del flusso | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 | 
   | FXXX | oilPressure | LINE\_DATA | EU | Pressione dell'olio del motore | psi | 2018-01-17T01:17:00Z | 49.2 |
   | FYYY | pumpRate | LINE\_DATA | Stati Uniti | Velocità del flusso | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | FYYY | oilPressure | LINE\_DATA | Stati Uniti | Pressione dell'olio del motore | psi | 2018-01-17T01:18:00Z | 22.2 |

Note su queste due tabelle:

- Le colonne **deviceId** e **series.tagId** fungono da intestazioni di colonna per i diversi dispositivi e i tag in una flotta. Utilizzando ognuna come proprio attributo limita la query 594 (per S1 ambienti) o 794 (per S2 ambienti) totale di dispositivi con sei colonne.
- proprietà non necessarie vengono evitate, per il motivo indicato nel primo esempio.
- I dati di riferimento consente di ridurre il numero di byte trasferiti in rete introducendo **deviceId**, che viene usato per la coppia di univoco **messageId** e **deviceLocation**. Chiave composta **series.tagId** viene usato per la coppia di univoco **tipo** e **unità**. Chiave composta consente la **deviceId** e **series.tagId** coppia da utilizzare per fare riferimento a quattro valori: **messageId, deviceLocation, digitare** e **unit**. Questi dati viene aggiunto ai dati di telemetria in fase di ingresso. Viene quindi archiviato in Time Series Insights per l'esecuzione di query.
- vengono utilizzati due livelli di annidamento, per il motivo indicato nel primo esempio.

### <a name="for-both-scenarios"></a>Per entrambi gli scenari

Per una proprietà con un numero elevato di valori possibili, è consigliabile inviare come valori distinti in una singola colonna invece di crearne una nuova colonna per ogni valore. Dai due esempi precedenti:

  - Nel primo esempio, alcune proprietà hanno valori diversi, pertanto è opportuno apportare ognuna una proprietà separata.
  - Nel secondo esempio, le misure non sono specificate come proprietà singole. Al contrario, si tratta di una matrice di valori o le misure in una proprietà della serie comuni. La nuova chiave **tagId** viene inviata, che consente di creare la nuova colonna **series.tagId** nella tabella bidimensionale. Le nuove proprietà **tipo** e **unit** vengono creati utilizzando i dati di riferimento in modo che non è stato raggiunto il limite di proprietà.

## <a name="next-steps"></a>Passaggi successivi

- Lettura [sintassi di query di Azure Time Series Insights](/rest/api/time-series-insights/ga-query-syntax) per altre informazioni sulla sintassi della query per l'API REST di accesso ai dati di Time Series Insights.
- Scopri [come eventi di forma](./time-series-insights-send-events.md).
