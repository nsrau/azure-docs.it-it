---
title: Procedure consigliate per definire la struttura del codice JSON nelle query di Azure Time Series Insights | Microsoft Docs
description: Informazioni su come migliorare l'efficienza delle query di Azure Time Series Insights.
services: time-series-insights
author: ashannon7
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 05/09/2019
ms.author: anshan
ms.custom: seodec18
ms.openlocfilehash: 535fe9a7920db89e434b4cc1b66aa38bf72a7829
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790071"
---
# <a name="how-to-shape-json-to-maximize-query-performance"></a>Come definire la struttura del codice JSON per massimizzare le prestazioni delle query 

Questo articolo offre materiale sussidiario per il data shaping JSON, per migliorare l'efficienza delle query di Azure Time Series Insights.

## <a name="video"></a>Video

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>Informazioni sulle procedure consigliate per il data shaping per soddisfare le esigenze di archiviazione JSON.</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>Procedure consigliate

È importante esaminare la procedura per l'invio di eventi a Time Series Insights. È in particolare necessario eseguire sempre queste operazioni:

1. Inviare dati tramite rete nel modo più efficiente possibile.
1. Verificare che i dati sono archiviati in modo che ti permette di eseguire aggregazioni adatte per il proprio scenario.
1. Verificare che si non raggiungono i limiti di Time Series Insights proprietà numero massimo di:
   - 600 proprietà (colonne) per ambienti S1.
   - 800 proprietà (colonne) per ambienti S2.

Le indicazioni seguenti consentono di assicurare le prestazioni migliori possibili per le query:

1. Non usare proprietà dinamiche, ad esempio gli ID tag, come nome di proprietà, poiché ciò contribuisce al raggiungimento del limite massimo di proprietà.
1. Non inviare proprietà non necessarie. Se una proprietà di una query non è obbligatoria, è consigliabile non inviarla ed evitare le limitazioni di archiviazione.
1. Usare i [dati di riferimento](time-series-insights-add-reference-data-set.md) per evitare l'invio di dati statici in rete.
1. Condividere le proprietà delle dimensioni tra più eventi per inviare dati in rete in modo più efficiente.
1. Non usare un annidamento troppo profondo delle matrici. Time Series Insights supporta fino a due livelli di matrici nidificate che contengono oggetti. Time Series Insights appiattisce le matrici nei messaggi in più eventi con le coppie di valore di proprietà.
1. Se sono disponibili solo alcune misure per tutti gli eventi o per la maggior parte degli eventi, è consigliabile inviare tali misure come proprietà separate nello stesso oggetto. L'invio separato riduce il numero di eventi e potrebbe migliorare le prestazioni delle query perché è necessario elaborare meno eventi. Quando sono presenti diverse misure, l'invio sotto forma di valori in una singola proprietà riduce al minimo la possibilità di raggiungere il limite massimo di proprietà.

## <a name="example-overview"></a>Cenni preliminari sull'esempio

I due esempi seguenti illustrano l'invio di eventi, per una dimostrazione dei consigli precedenti. Dopo ogni esempio è possibile verificare il modo in cui i consigli sono stati applicati.

Gli esempi sono basati su uno scenario in cui più dispositivi inviano misure o segnali. Le misure o i segnali possono essere ad esempio relativi a velocità del flusso, pressione dell'olio nel motore, temperatura e umidità. Nel primo esempio sono presenti poche misure in tutti i dispositivi. Nel secondo esempio sono presenti molti dispositivi e ogni dispositivo invia molte misure univoche.

## <a name="scenario-one-only-a-few-measurements-exist"></a>Scenario uno: esistono solo alcune misurazioni

> [!TIP]
> **Raccomandazione**: inviare ogni misura/segnale come una proprietà/colonna separata.

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
                "Flow Rate ft3/s": 0.58015072345733643,
                "Engine Oil Pressure psi ": 22.2
            }
        ]
    }
]
```

* Tabella dati di riferimento (proprietà chiave viene **deviceId**):

   | deviceId | messageId | deviceLocation |
   | --- | --- | --- |
   | FXXX | LINE\_DATA | EU |
   | FYYY | LINE\_DATA | Stati Uniti |

* Tabella di eventi di Time Series Insights (dopo la trasformazione in flat):

   | deviceId | messageId | deviceLocation |  timestamp | series.Flow Rate ft3/s | series.Engine Oil Pressure psi |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | FYYY | LINE\_DATA | Stati Uniti | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

In precedenza:

- La colonna **deviceId** viene usata come intestazione di colonna per i diversi dispositivi nell'insieme. Un tentativo di usare il valore deviceId come nome di proprietà comporterebbe un totale limitato di dispositivi pari a 595 (ambienti S1) o 795 (ambienti S2), con altre cinque colonne.

- Le proprietà non necessarie vengono evitate, ad esempio le informazioni sul produttore e sul modello e così via. Poiché non verranno sottoposte a query in futuro, l'eliminazione di queste proprietà consente di ottenere una maggiore efficienza di rete e di archiviazione.

- I dati di riferimento vengono usati per ridurre il numero di byte trasferiti in rete. Due attributi **messageId** e **deviceLocation**, vengono aggiunti tramite la proprietà chiave, ovvero **deviceId**. Questi dati vengano uniti con i dati di telemetria in fase di ingresso e successivamente archiviati in Time Series Insights per l'esecuzione di query.

- Vengono utilizzati due livelli di annidamento, ovvero la quantità massima di annidamento supportato da Time Series Insights. È essenziale evitare matrici annidate profondamente.

- Le misure vengono inviate come proprietà separate entro lo stesso oggetto, poiché sono presenti poche misure. In questo caso le colonne **series.Flow Rate psi** e **series.Engine Oil Pressure ft3/s** sono colonne univoche.

## <a name="scenario-two-several-measures-exist"></a>Scenario due: diverse misure esistano

> [!TIP]
> **Consiglio:** inviare le misure sotto forma di tuple di tipo "type", "unit", "value".

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

* I dati di riferimento (proprietà chiave siano **deviceId** e **series.tagId**):

   | deviceId | series.tagId | messageId | deviceLocation | tipo | unit |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINE\_DATA | EU | Velocità del flusso | ft3/s |
   | FXXX | oilPressure | LINE\_DATA | EU | Pressione dell'olio del motore | psi |
   | FYYY | pumpRate | LINE\_DATA | Stati Uniti | Velocità del flusso | ft3/s |
   | FYYY | oilPressure | LINE\_DATA | Stati Uniti | Pressione dell'olio del motore | psi |

* Tabella di eventi di Time Series Insights (dopo la trasformazione in flat):

   | deviceId | series.tagId | messageId | deviceLocation | tipo | unit |  timestamp | series.value |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINE\_DATA | EU | Velocità del flusso | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 | 
   | FXXX | oilPressure | LINE\_DATA | EU | Pressione dell'olio del motore | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX | pumpRate | LINE\_DATA | EU | Velocità del flusso | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 | 
   | FXXX | oilPressure | LINE\_DATA | EU | Pressione dell'olio del motore | Psi | 2018-01-17T01:17:00Z | 49.2 |
   | FYYY | pumpRate | LINE\_DATA | Stati Uniti | Velocità del flusso | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | FYYY | oilPressure | LINE\_DATA | Stati Uniti | Pressione dell'olio del motore | psi | 2018-01-17T01:18:00Z | 22.2 |

In precedenza:

- Le colonne **deviceId** e **series.tagId** fungono da intestazioni di colonna per i diversi dispositivi e i tag in una flotta. L'uso di ogni colonna come rispettivo attributo avrebbe limitato la query a 594 (ambienti S1) o 794 (ambienti S2) dispositivi totali con le altre sei colonne.

- proprietà non necessarie vengono evitate, per il motivo indicato nel primo esempio.

- i dati di riferimento consente di ridurre il numero di byte trasferiti in rete introducendo **deviceId**, per una coppia univoca **messageId** e **deviceLocation**. Viene usata una chiave composta, **series.tagId**, per la coppia univoca di **type** e **unit**. La chiave composta consente l'uso della coppia **deviceId** e **series.tagId** per fare riferimento a quattro valori: **messageId, deviceLocation, type** e **unit**. Questi dati vengano uniti con i dati di telemetria in fase di ingresso e successivamente archiviati in Time Series Insights per l'esecuzione di query.

- vengono utilizzati due livelli di annidamento, per il motivo indicato nel primo esempio.

### <a name="for-both-scenarios"></a>Per entrambi gli scenari

Se è presente una proprietà con un numero elevato di valori possibili, è consigliabile inviarli come valori distinti in una singola colonna, invece di creare una nuova colonna per ogni valore. Dai due esempi precedenti:

  - Nel primo esempio sono presenti alcune proprietà con diversi valori, quindi è opportuno impostare ogni proprietà come proprietà separata.
  - Nel secondo esempio, tuttavia, si può notare che le misure non sono specificate come singole proprietà, ma come una matrice di valori/misure in una proprietà per la serie comune. Viene inviata una nuova chiave, **tagId**, che crea una nuova colonna, **series.tagId** nella tabella resa flat. Vengono create nuove proprietà, **type** e **unit**, usando dati di riferimento e impedendo quindi il raggiungimento del limite di proprietà.

## <a name="next-steps"></a>Passaggi successivi

- Lettura [sintassi di query di Azure Time Series Insights](/rest/api/time-series-insights/ga-query-syntax) per altre informazioni sulla sintassi della query per l'API REST di accesso ai dati di Time Series Insights.

- Scopri [come eventi di forma](./time-series-insights-send-events.md).
