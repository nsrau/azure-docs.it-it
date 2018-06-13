---
title: Elaborare regole basate su soglie configurabili in Analisi di flusso di Azure
description: Questo articolo descrive come usare i dati di riferimento per ottenere una soluzione per la creazione di avvisi che disponga di regole basate su soglie configurabili in Analisi di flusso di Azure.
services: stream-analytics
author: zhongc
ms.author: zhongc
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 1c131c2c9ca12556c1d2cd52e7976d2f4272a0c8
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2018
ms.locfileid: "32768031"
---
# <a name="process-configurable-threshold-based-rules-in-azure-stream-analytics"></a>Elaborare regole basate su soglie configurabili in Analisi di flusso di Azure
Questo articolo descrive come usare i dati di riferimento per ottenere una soluzione per la creazione di avvisi che usi regole basate su soglie configurabili in Analisi di flusso di Azure.

## <a name="scenario-alerting-based-on-adjustable-rule-thresholds"></a>Scenario: Creazione di avvisi basati su soglie delle regole modificabili
Potrebbe essere necessario generare un avviso come output quando gli eventi trasmessi in ingresso hanno raggiunto un valore specifico oppure quando un valore aggregato basato sugli eventi trasmessi in ingresso supera una determinata soglia. È possibile configurare facilmente una query di Analisi di flusso per mettere a confronto un valore con una soglia statica fissa e predeterminata. Una soglia fissa può essere impostata come hardcoded nella sintassi della query di streaming usando confronti numerici semplici (maggiore di, minore di e uguale a).

In alcuni casi, i valori soglia devono poter essere più facilmente configurabili senza modificare la sintassi della query ogni volta che si modifica un valore soglia. In altri casi, potrebbe essere necessario che la stessa query elabori numerosi dispositivi o utenti, ognuno con valori soglia differenti per ciascun tipo di dispositivo. 

Questo modello può essere usato per configurare dinamicamente le soglie, scegliere in modo selettivo il tipo di dispositivo a cui la soglia è applicabile filtrando i dati di input e scegliere in modo selettivo i campi da includere nell'output.

## <a name="recommended-design-pattern"></a>Schema progettuale consigliato
Usare un input di dati di riferimento a un processo di Analisi di flusso come ricerca delle soglie di avviso:
- Archiviare i valori soglia nei dati di riferimento, uno per chiave.
- Aggiungere gli eventi di input dei dati di streaming ai dati di riferimento nella colonna chiave.
- Usare il valore associato alla chiave dai dati di riferimento come valore soglia.

## <a name="example-data-and-query"></a>Dati e query di esempio
Nell'esempio vengono generati avvisi quando l'aggregazione dei dati trasmessi in ingresso dai dispositivi in una finestra di un minuto corrisponde ai valori previsti nella regola fornita come dati di riferimento.

Nella query, per ogni deviceId e ogni metricName sotto deviceId è possibile configurare da 0 a 5 dimensioni per GROUP BY. Vengono raggruppati solo gli eventi con i valori di filtro corrispondenti. Una volta raggruppate, le aggregazioni finestra di Min, Max e Avg vengono calcolate per una finestra a cascata di 60 secondi. I filtri sui valori aggregati vengono quindi calcolati in base alla soglia configurata nel riferimento, in modo da generare l'evento di output dell'avviso.

Si supponga ad esempio di disporre di un processo di Analisi di flusso con un input di dati di riferimento denominato **rules** e un input di dati di streaming denominato **metrics**. 

## <a name="reference-data"></a>Dati di riferimento
I dati di riferimento in questo esempio mostrano in che modo può essere rappresentata una regola basata su soglie. Un file JSON contiene i dati di riferimento e viene salvato nell'archivio BLOB di Azure; il contenitore dell'archivio BLOB viene usato come input dei dati di riferimento denominato **rules**. È possibile sovrascrivere questo file JSON e sostituire la configurazione della regola in un momento successivo, senza arrestare o avviare il processo di streaming.

- La regola di esempio viene usata per rappresentare un avviso modificabile quando la CPU supera (la media è maggiore o uguale a) il valore `90` per cento. Il campo `value` è configurabile in base alle esigenze.
- Si noti che la regola ha un campo **operator**, che viene interpretato successivamente in modo dinamico nella sintassi della query in base a `AVGGREATEROREQUAL`. 
- La regola filtra i dati in base a una determinata chiave della dimensione `2` con valore `C1`. Gli altri campi sono stringhe vuote, che indicano di non filtrare il flusso di input in base a tali campi evento. È possibile configurare altre regole per la CPU in modo da filtrare altri campi corrispondenti in base alle esigenze.
- Non tutte le colonne devono essere incluse nell'evento di avviso dell'output. In questo caso, il numero della chiave `2` di `includedDim` è attivato su `TRUE` in modo da rappresentare che tale numero del campo 2 dei dati dell'evento nel flusso verrà incluso negli eventi dell'output idonei. Gli altri campi non sono inclusi nell'output dell'avviso, ma l'elenco dei campi può essere modificato.


```json
{
    "ruleId": 1234, 
    "deviceId" : "978648", 
    "metricName": "CPU", 
    "alertName": "hot node AVG CPU over 90",
    "operator" : "AVGGREATEROREQUAL",
    "value": 90, 
    "includeDim": {
        "0": "FALSE", 
        "1": "FALSE", 
        "2": "TRUE", 
        "3": "FALSE", 
        "4": "FALSE"
    },
    "filter": {
        "0": "", 
        "1": "",
        "2": "C1", 
        "3": "", 
        "4": ""
    }    
}
```

## <a name="example-streaming-query"></a>Query di streaming di esempio
Questa query di esempio di Analisi di flusso crea un join tra i dati di riferimento **rules** dell'esempio precedente e un flusso di input di dati denominato **metrics**.

```sql
WITH transformedInput AS
(
    SELECT
        dim0 = CASE rules.includeDim.[0] WHEN 'TRUE' THEN metrics.custom.dimensions.[0].value ELSE NULL END,
        dim1 = CASE rules.includeDim.[1] WHEN 'TRUE' THEN metrics.custom.dimensions.[1].value ELSE NULL END,
        dim2 = CASE rules.includeDim.[2] WHEN 'TRUE' THEN metrics.custom.dimensions.[2].value ELSE NULL END,
        dim3 = CASE rules.includeDim.[3] WHEN 'TRUE' THEN metrics.custom.dimensions.[3].value ELSE NULL END,
        dim4 = CASE rules.includeDim.[4] WHEN 'TRUE' THEN metrics.custom.dimensions.[4].value ELSE NULL END,
        metric = metrics.metric.value,
        metricName = metrics.metric.name,
        deviceId = rules.deviceId, 
        ruleId = rules.ruleId, 
        alertName = rules.alertName,
        ruleOperator = rules.operator, 
        ruleValue = rules.value
    FROM 
        metrics
        timestamp by eventTime
    JOIN 
        rules
        ON metrics.deviceId = rules.deviceId AND metrics.metric.name = rules.metricName
    WHERE
        (rules.filter.[0] = '' OR metrics.custom.filters.[0].value = rules.filter.[0]) AND 
        (rules.filter.[1] = '' OR metrics.custom.filters.[1].value = rules.filter.[1]) AND
        (rules.filter.[2] = '' OR metrics.custom.filters.[2].value = rules.filter.[2]) AND
        (rules.filter.[3] = '' OR metrics.custom.filters.[3].value = rules.filter.[3]) AND
        (rules.filter.[4] = '' OR metrics.custom.filters.[4].value = rules.filter.[4])
)

SELECT
    System.Timestamp as time, 
    transformedInput.deviceId as deviceId,
    transformedInput.ruleId as ruleId,
    transformedInput.metricName as metric,
    transformedInput.alertName as alert,
    AVG(metric) as avg,
    MIN(metric) as min, 
    MAX(metric) as max, 
    dim0, dim1, dim2, dim3, dim4
FROM
    transformedInput
GROUP BY
    transformedInput.deviceId,
    transformedInput.ruleId,
    transformedInput.metricName,
    transformedInput.alertName,
    dim0, dim1, dim2, dim3, dim4,
    ruleOperator, 
    ruleValue, 
    TumblingWindow(second, 60)
HAVING
    (
        (ruleOperator = 'AVGGREATEROREQUAL' AND avg(metric) >= ruleValue) OR
        (ruleOperator = 'AVGEQUALORLESS' AND avg(metric) <= ruleValue) 
    )
```

## <a name="example-streaming-input-event-data"></a>Dati dell'evento di input di streaming di esempio
Questi dati JSON di esempio rappresentano i dati di input **metrics** che vengono usati nella query di streaming precedente. 

- Sono elencati tre eventi di esempio nell'intervallo di tempo di 1 minuto, con valore `T14:50`. 
- Tutti e tre hanno lo stesso valore `deviceId` `978648`.
- I valori delle metriche CPU variano in ciascun evento, rispettivamente `98`, `95` e `80`. Solo i primi due eventi di esempio superano la regola di avviso della CPU stabilita nella regola.
- Il campo includeDim nella regola di avviso aveva il numero di chiave 2. Il campo della chiave 2 corrispondente negli eventi di esempio è denominato `NodeName`. I tre eventi di esempio hanno rispettivamente valori `N024`, `N024` e `N014`. Nell'output viene visualizzato solo il nodo `N024`, dal momento che sono gli unici dati corrispondenti ai criteri di avviso per uso elevato della CPU. `N014` non soddisfa la soglia di CPU elevata.
- La regola di avviso è configurata con un valore `filter` solo sul numero di chiave 2, che corrisponde al campo `cluster` negli eventi di esempio. I tre eventi di esempio hanno tutti valore `C1` e soddisfano i criteri di filtro.

```json
{
    "eventTime": "2018-04-30T14:50:23.1324132Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N1"
            },
            "1": {
                "name": "Cluster",
                "value": "C1"
            },
            "2": {
                "name": "NodeName",
                "value": "N024"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N1"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 98,
        "count": 1.0,
        "min": 98,
        "max": 98,
        "stdDev": 0.0
    }
}
{
    "eventTime": "2018-04-30T14:50:24.1324138Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N2"
            },
            "1": {
                "name": "Cluster",
                "value": "C1"
            },
            "2": {
                "name": "NodeName",
                "value": "N024"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N2"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 95,
        "count": 1,
        "min": 95,
        "max": 95,
        "stdDev": 0
    }
}
{
    "eventTime": "2018-04-30T14:50:37.1324130Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N3"
            },
            "1": {
                "name": "Cluster",
                "value": "C1 "
            },
            "2": {
                "name": "NodeName",
                "value": "N014"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N3"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 80,
        "count": 1,
        "min": 80,
        "max": 80,
        "stdDev": 0
    }
}
```

## <a name="example-output"></a>Output di esempio
I dati JSON dell'output di esempio mostrano che è stato generato un singolo evento di avviso in base alla regola della soglia della CPU definita nei dati di riferimento. L'evento di output contiene il nome dell'avviso nonché i valori aggregati (average, min, max) dei campi considerati. I dati dell'evento di output includono il numero di chiave 2 del campo `NodeName` con valore `N024` in base alla configurazione della regola (il codice JSON è stato modificato in modo da mostrare le interruzioni di riga per una migliore leggibilità).

```JSON
{"time":"2018-05-01T02:03:00.0000000Z","deviceid":"978648","ruleid":1234,"metric":"CPU",
"alert":"hot node AVG CPU over 90","avg":96.5,"min":95.0,"max":98.0,
"dim0":null,"dim1":null,"dim2":"N024","dim3":null,"dim4":null}
```