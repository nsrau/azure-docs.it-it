---
title: Procedure consigliate per definire la struttura del codice JSON nelle query di Azure Time Series Insights | Microsoft Docs
description: Informazioni su come migliorare l'efficienza delle query di Azure Time Series Insights.
services: time-series-insights
author: ashannon7
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 05/24/2018
ms.author: anshan
ms.custom: seodec18
ms.openlocfilehash: c076d425a7740bd0eb6398e6b8720fa873f2fc30
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54201262"
---
# <a name="how-to-shape-json-to-maximize-query-performance"></a>Come definire la struttura del codice JSON per massimizzare le prestazioni delle query 

Questo articolo fornisce indicazioni per la definizione della struttura del codice JSON per massimizzare l'efficienza delle query di Azure Time Series Insights (TSI).

## <a name="video"></a>Video: 

### <a name="in-this-video-we-cover-best-practices-around-shaping-json-to-meet-your-storage-needsbr"></a>In questo video vengono illustrate le procedure consigliate per definire la struttura del codice JSON in modo da soddisfare le esigenze di archiviazione.</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>Procedure consigliate

È importante esaminare la procedura per l'invio di eventi a Time Series Insights. È in particolare necessario eseguire sempre queste operazioni:

1. Inviare dati tramite rete nel modo più efficiente possibile.
2. Assicurarsi che i dati vengano archiviati in modo da consentire l'esecuzione delle aggregazioni ottimali per lo scenario specifico.
3. Assicurarsi di non raggiungere il limite massimo di proprietà di Time Series Insights pari a:
   - 600 proprietà (colonne) per ambienti S1.
   - 800 proprietà (colonne) per ambienti S2.

Le indicazioni seguenti consentono di assicurare le prestazioni migliori possibili per le query:

1. Non usare proprietà dinamiche, ad esempio gli ID tag, come nome di proprietà, poiché ciò contribuisce al raggiungimento del limite massimo di proprietà.
2. Non inviare proprietà non necessarie. Se una proprietà di una query non è obbligatoria, è consigliabile non inviarla ed evitare le limitazioni di archiviazione.
3. Usare i [dati di riferimento](time-series-insights-add-reference-data-set.md) per evitare l'invio di dati statici in rete.
4. Condividere le proprietà delle dimensioni tra più eventi per inviare dati in rete in modo più efficiente.
5. Non usare un annidamento troppo profondo delle matrici. Time Series Insights supporta al massimo due livelli di matrici annidate che contengono oggetti. Time Series Insights rende flat le matrici nei messaggi in più eventi con coppie di valori delle proprietà.
6. Se sono disponibili solo alcune misure per tutti gli eventi o per la maggior parte degli eventi, è consigliabile inviare tali misure come proprietà separate nello stesso oggetto. L'invio separato riduce il numero di eventi e potrebbe migliorare le prestazioni delle query perché è necessario elaborare meno eventi. Quando sono presenti diverse misure, l'invio sotto forma di valori in una singola proprietà riduce al minimo la possibilità di raggiungere il limite massimo di proprietà.

## <a name="examples"></a>Esempi

I due esempi seguenti illustrano l'invio di eventi, per una dimostrazione dei consigli precedenti. Dopo ogni esempio è possibile verificare il modo in cui i consigli sono stati applicati.

Gli esempi sono basati su uno scenario in cui più dispositivi inviano misure o segnali. Le misure o i segnali possono essere ad esempio relativi a velocità del flusso, pressione dell'olio nel motore, temperatura e umidità. Nel primo esempio sono presenti poche misure in tutti i dispositivi. Nel secondo esempio sono presenti molti dispositivi e ogni dispositivo invia molte misure univoche.

### <a name="scenario-only-a-few-measurementssignals-exist"></a>Scenario: esistono solo alcune misure o alcuni segnali

**Consiglio:** inviare ogni misura come proprietà/colonna separata.

Nell'esempio seguente è presente un singolo messaggio dell'hub IoT, in cui la matrice esterna contiene una sezione condivisa di valori di dimensione comuni. La matrice esterna usa dati di riferimento per incrementare l'efficienza del messaggio. I dati di riferimento includono i metadati del dispositivo, che non vengono modificati in ogni evento, ma forniscono proprietà utili per l'analisi dei dati. L'invio in batch dei valori di dimensione comuni e l'uso dei dati di riferimento consentono di ridurre la quantità di byte trasmessi in rete, rendendo quindi più efficiente il messaggio.

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
                "Flow Rate psi": 0.58015072345733643,
                "Engine Oil Pressure psi ": 22.2
            }
        ]
    }
]
```

Tabella di dati di riferimento (la proprietà chiave è deviceId):

| deviceId | messageId | deviceLocation |
| --- | --- | --- |
| FXXX | LINE\_DATA | EU |
| FYYY | LINE\_DATA | Stati Uniti |

Tabella di eventi di Time Series Insights (dopo la trasformazione in flat):

| deviceId | messageId | deviceLocation |  timestamp | series.Flow Rate ft3/s | series.Engine Oil Pressure psi |
| --- | --- | --- | --- | --- | --- |
| FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
| FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
| FYYY | LINE\_DATA | Stati Uniti | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

Si noti quanto segue nell'esempio precedente:

- La colonna **deviceId** viene usata come intestazione di colonna per i diversi dispositivi nell'insieme. Un tentativo di usare il valore deviceId come nome di proprietà comporterebbe un totale limitato di dispositivi pari a 595 (ambienti S1) o 795 (ambienti S2), con altre cinque colonne.

- Le proprietà non necessarie vengono evitate, ad esempio le informazioni sul produttore e sul modello e così via. Poiché non verranno sottoposte a query in futuro, l'eliminazione di queste proprietà consente di ottenere una maggiore efficienza di rete e di archiviazione.

- I dati di riferimento vengono usati per ridurre il numero di byte trasferiti in rete. Viene eseguito il join di due attributi, **messageId** e **deviceLocation**, con la proprietà chiave, **deviceId**. Viene eseguito il join di questi dati con i dati di telemetria in fase di ingresso e i dati vengono successivamente archiviati in Time Series Insights per l'esecuzione di query.

- Vengono usati due livelli di annidamento, ovvero la quantità massima di annidamento supportata da Time Series Insights. È essenziale evitare matrici annidate profondamente.

- Le misure vengono inviate come proprietà separate entro lo stesso oggetto, poiché sono presenti poche misure. In questo caso le colonne **series.Flow Rate psi** e **series.Engine Oil Pressure ft3/s** sono colonne univoche.

### <a name="scenario-several-measures-exist"></a>Scenario: sono presenti alcune misure

**Consiglio:** inviare le misure sotto forma di tuple di tipo "type", "unit", "value".

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

Dati di riferimento (le proprietà chiave sono deviceId e series.tagId):

| deviceId | series.tagId | messageId | deviceLocation | type | unit |
| --- | --- | --- | --- | --- | --- |
| FXXX | pumpRate | LINE\_DATA | EU | Velocità del flusso | ft3/s |
| FXXX | oilPressure | LINE\_DATA | EU | Pressione dell'olio del motore | psi |
| FYYY | pumpRate | LINE\_DATA | Stati Uniti | Velocità del flusso | ft3/s |
| FYYY | oilPressure | LINE\_DATA | Stati Uniti | Pressione dell'olio del motore | psi |

Tabella di eventi di Time Series Insights (dopo la trasformazione in flat):

| deviceId | series.tagId | messageId | deviceLocation | type | unit |  timestamp | series.value |
| --- | --- | --- | --- | --- | --- | --- | --- |
| FXXX | pumpRate | LINE\_DATA | EU | Velocità del flusso | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 |
| FXXX | oilPressure | LINE\_DATA | EU | Pressione dell'olio del motore | psi | 2018-01-17T01:17:00Z | 34.7 |
| FXXX | pumpRate | LINE\_DATA | EU | Velocità del flusso | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 |
| FXXX | oilPressure | LINE\_DATA | EU | Pressione dell'olio del motore | Psi | 2018-01-17T01:17:00Z | 49.2 |
| FYYY | pumpRate | LINE\_DATA | Stati Uniti | Velocità del flusso | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
| FYYY | oilPressure | LINE\_DATA | Stati Uniti | Pressione dell'olio del motore | psi | 2018-01-17T01:18:00Z | 22.2 |

Si noti quanto segue nell'esempio precedente, analogamente al primo esempio:

- Le colonne **deviceId** e **series.tagId** vengono usate come intestazioni di colonna per i diversi dispositivi e tag in un insieme. L'uso di ogni colonna come rispettivo attributo avrebbe limitato la query a 594 (ambienti S1) o 794 (ambienti S2) dispositivi totali con le altre sei colonne.

- Le proprietà non necessarie sono state evitate, per i motivi indicati nel primo esempio.

- I dati di riferimento vengono usati per ridurre il numero di byte trasferiti in rete introducendo **deviceId** per una coppia univoca di **messageId** e **deviceLocation**. Viene usata una chiave composta, **series.tagId**, per la coppia univoca di **type** e **unit**. La chiave composta consente l'uso della coppia **deviceId** e **series.tagId** per fare riferimento a quattro valori: **messageId, deviceLocation, type** e **unit**. Viene eseguito il join di questi dati con i dati di telemetria in fase di ingresso e i dati vengono successivamente archiviati in Time Series Insights per l'esecuzione di query.

- Vengono usati due livelli di annidamento per il motivo citato nel primo esempio.

### <a name="for-both-scenarios"></a>Per entrambi gli scenari

Se è presente una proprietà con un numero elevato di valori possibili, è consigliabile inviarli come valori distinti in una singola colonna, invece di creare una nuova colonna per ogni valore. Dai due esempi precedenti:
  - Nel primo esempio sono presenti alcune proprietà con diversi valori, quindi è opportuno impostare ogni proprietà come proprietà separata. 
  - Nel secondo esempio, tuttavia, si può notare che le misure non sono specificate come singole proprietà, ma come una matrice di valori/misure in una proprietà per la serie comune. Viene inviata una nuova chiave, **tagId**, che crea una nuova colonna, **series.tagId** nella tabella resa flat. Vengono create nuove proprietà, **type** e **unit**, usando dati di riferimento e impedendo quindi il raggiungimento del limite di proprietà.

## <a name="next-steps"></a>Passaggi successivi

Per mettere in pratica queste indicazioni, vedere [Azure Time Series Insights query syntax](/rest/api/time-series-insights/ga-query-syntax) (Sintassi delle query di Azure Time Series Insights) per ottenere altre informazioni sulla sintassi delle query per l'API REST di accesso ai dati di Time Series Insights.