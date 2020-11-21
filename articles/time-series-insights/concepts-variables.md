---
title: Variabili di modello-Azure Time Series Insights Gen2 | Microsoft Docs
description: Variabili di modello
author: shreyasharmamsft
ms.author: shresha
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 10/01/2020
ms.openlocfilehash: f1c394bb1a568d59e0821b61e7acfcf8f25290f7
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020760"
---
# <a name="time-series-model-variables"></a>Variabili del modello Time Series

Questo articolo descrive le variabili del modello Time Series che specificano le regole relative a formule e calcolo per gli eventi.

Ogni variabile può essere di uno dei tre tipi seguenti: *numeric*, *CATEGORICAL* e *Aggregate*.

* I tipi **numerici** funzionano con valori numerici continui.
* I tipi **categorici** funzionano con un set definito di valori discreti.
* I tipi **aggregati** combinano più variabili di un singolo tipo (tutti numerici o categorici).

Nella tabella seguente vengono illustrate le proprietà rilevanti per ogni tipo di variabile.

[![Tabella delle variabili del modello Time Series](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

## <a name="numeric-variables"></a>Variabili numeriche

| Proprietà Variable | Descrizione |
| --- | ---|
| Filtro di variabile | I filtri sono clausole condizionali facoltative per limitare il numero di righe da considerare per il calcolo. |
| Valore di variabile | Valori di telemetria usati per il calcolo proveniente dal dispositivo o dai sensori oppure trasformati usando le espressioni della serie temporale. Le variabili di tipo numerico devono essere di tipo *Double*.|
| Interpolazione di variabili | L'interpolazione specifica come ricostruire un segnale usando i dati esistenti. Per le variabili numeriche sono disponibili opzioni di interpolazione *lineare* e *passaggio* . |
| Aggregazione di variabile | Eseguire calcoli tramite le [funzioni di aggregazione supportate per i tipi di variabile numerica](/rest/api/time-series-insights/reference-time-series-expression-syntax#numeric-variable-kind). |

Le variabili sono conformi all'esempio JSON seguente:

```JSON
"Interpolated Speed": {
  "kind": "numeric",
  "value": {
    "tsx": "$event['Speed-Sensor'].Double"
  },
  "filter": null,
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span": "P1D"
    }
  },
  "aggregation": {
    "tsx": "right($value)"
  }
}
```

## <a name="categorical-variables"></a>Variabili categoriche

| Proprietà Variable | Descrizione |
| --- | ---|
| Filtro di variabile | I filtri sono clausole condizionali facoltative per limitare il numero di righe da considerare per il calcolo. |
| Valore di variabile | Valori di telemetria usati per il calcolo proveniente dal dispositivo o dai sensori. Le variabili di tipo categorico devono essere *Long* o *String*. |
| Interpolazione di variabili | L'interpolazione specifica come ricostruire un segnale usando i dati esistenti. L'opzione di interpolazione dei *passaggi* è disponibile per le variabili categoriche. |
| Categorie di variabili | Le categorie creano un mapping tra i valori provenienti dal dispositivo o dai sensori a un'etichetta. |
| Categoria predefinita variabile | La categoria predefinita è per tutti i valori di cui non è stato eseguito il mapping nella proprietà "categorie". |

Le variabili sono conformi all'esempio JSON seguente:

```JSON
"Status": {
  "kind": "categorical",
  "value": {
     "tsx": "$event.Status.Long"
},
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span" : "PT1M"
    }
  },
  "categories": [
    {
      "values": [0, 1, 2, 3],
      "label": "Good"
    },
    {
      "values": [4],
      "label": "Bad"
    }
  ],
  "defaultCategory": {
    "label": "Not Applicable"
  }
}
```

## <a name="aggregate-variables"></a>Variabili di aggregazione

| Proprietà Variable | Descrizione |
| --- | ---|
| Filtro di variabile | I filtri sono clausole condizionali facoltative per limitare il numero di righe da considerare per il calcolo. |
| Aggregazione di variabile | Eseguire calcoli tramite le [funzioni di aggregazione supportate per i tipi di variabile di aggregazione](/rest/api/time-series-insights/reference-time-series-expression-syntax#aggregate-variable-kind). |

Le variabili sono conformi all'esempio JSON seguente:

```JSON
"Speed Range": {
  "kind": "aggregate",
  "filter": null,
  "aggregation": {
    "tsx": "max($event.Speed.Double) - min($event.Speed.Double)"
  }
}
```

Le variabili vengono archiviate nella definizione del tipo di un modello Time Series e possono essere fornite inline tramite le API per eseguire l'override o completare la definizione archiviata.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sul [modello Time Series](./concepts-model-overview.md).

* Scopri di più su come definire le variabili inline usando le [API di query](./concepts-query-overview.md).