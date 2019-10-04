---
title: Modello Time Series in Anteprima di Azure Time Series Insights | Microsoft Docs
description: Informazioni sul modello di serie temporale di Azure Time Series.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/08/2019
ms.custom: seodec18
ms.openlocfilehash: 2a740d8ee0eb50cfa01f36bd8f5590a58e1e6627
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68931921"
---
# <a name="time-series-model"></a>Modello serie temporale

Questo articolo descrive il componente Modello serie temporale di Anteprima di Time Series Insights. Illustra il modello, le funzionalità e come iniziare a compilare e aggiornare un modello personalizzato.

I dati raccolti dai dispositivi IoT non includono in genere informazioni contestuali, cosa che rende difficile trovare e analizzare rapidamente i sensori. La scopo principale del modello di serie temporale è quello di semplificare la ricerca e l'analisi dei dati IoT. Raggiunge questo obiettivo consentendo la cura, la manutenzione e l'arricchimento dei dati della serie temporale per preparare set di dati di livello consumer.

I modelli di serie temporale svolgono un ruolo fondamentale nelle query e nella navigazione perché contestualizzano le entità dispositivo e non dispositivo. I dati resi persistenti nel modello di serie temporale possono essere usati per i calcoli delle query delle serie temporali sfruttando le formule archiviate al loro interno.

[![Panoramica del modello Time Series](media/v2-update-tsm/tsm.png)](media/v2-update-tsm/tsm.png#lightbox)

## <a name="key-capabilities"></a>Funzionalità principali

Allo scopo di semplificare e agevolare la gestione della contestualizzazione delle serie temporali, il modello di serie temporale abilita le funzionalità seguenti in Anteprima di Time Series Insights. Consente di:

* Creare e gestire formule o calcoli, trasformare i dati sfruttando le funzioni scalari, le operazioni di aggregazione e così via.
* Definire relazioni padre-figlio per abilitare l'esplorazione e fare riferimento e fornire contesto ai dati di telemetria delle serie temporali.
* Definire le proprietà associate al componente istanze dei *campi di istanza* e usarle per creare gerarchie.

## <a name="entity-components"></a>Componenti entità

I modelli Time Series hanno tre componenti principali:

* <a href="#time-series-model-types">Tipi di modello Time Series</a>
* <a href="#time-series-model-hierarchies">Gerarchie di modelli Time Series</a>
* <a href="#time-series-model-instances">Istanze del modello Time Series</a>

Questi componenti vengono combinati per specificare un modello Time Series e per organizzare i dati Azure Time Series Insights.

## <a name="time-series-model-types"></a>Tipi di modelli serie temporale

I *tipi* di modello serie temporale consentono di definire variabili o formule per eseguire calcoli. I tipi sono associati a una specifica istanza di Time Series Insights. Un tipo può avere una o più variabili. Un'istanza di Time Series Insights, ad esempio, potrebbe essere di tipo *Temperature Sensor*, che è costituito dalle variabili *avg temperature*, *min temperature* e *max temperature*. Viene creato un tipo predefinito quando inizia il flusso dei dati in Time Series Insights. Il tipo predefinito può essere recuperato e aggiornato dalle impostazioni del modello. I tipi predefiniti hanno una variabile che conta il numero di eventi.

### <a name="time-series-model-type-json-example"></a>Esempio JSON di tipo di modello serie temporale

Esempio:

```JSON
{
    "name": "SampleType",
    "description": "This is sample type",
    "variables": {
        "Avg Temperature": {
            "kind": "numeric",
            "filter": null,
            "value": { "tsx": "$event.temperature.Double" },
            "aggregation": {"tsx": "avg($value)"}
        },
        "Count Temperature": {
            "kind": "aggregate",
            "filter": null,
            "value": null,
            "aggregation": {"tsx": "count()"}
        }
    }
}
```

Per altre informazioni sui tipi di modello serie temporale, vedere la [documentazione di riferimento](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api).

### <a name="variables"></a>Variabili

I tipi di Time Series Insights hanno variabili, denominate calcoli sui valori degli eventi. Le definizioni delle variabili di Time Series Insights contengono regole relative alle formule e ai calcoli. Le definizioni delle variabili includono *tipologia*, *valore*, *filtro*, *riduzione* e *limiti*. Le variabili vengono archiviate nella definizione del tipo nel modello serie temporale e possono essere fornite inline tramite le API di query per eseguire l'override della definizione archiviata.

La matrice seguente funge da legenda per le definizioni delle variabili:

[![Tabella di definizione della variabile di tipo](media/v2-update-tsm/table.png)](media/v2-update-tsm/table.png#lightbox)

| Definizione | Descrizione |
| --- | ---|
| Tipologia di variabile |  Sono supportati i tipi numerici e di *aggregazione* |
| Filtro di variabile | I filtri di variabile specificano una clausola di filtro facoltativa per limitare il numero di righe da tenere in considerazione per il calcolo in base alle condizioni. |
| Valore di variabile | I valori delle variabili vengono e devono essere usati nel calcolo. Campo pertinente a cui fare riferimento per il punto dati in questione. |
| Aggregazione di variabile | La funzione di aggregazione della variabile consente parte del calcolo. Time Series Insights supporta le normali aggregazioni (specificamente *min*, *max*, *avg*, *sum* e *count*). |

## <a name="time-series-model-hierarchies"></a>Gerarchie di modelli serie temporale

Le gerarchie organizzano le istanze specificando i nomi e le relazioni delle proprietà. Potrebbe essere presente un'unica gerarchia o più gerarchie. Non devono essere una parte corrente dei dati, ma ogni istanza deve eseguire il mapping a una gerarchia. Un'istanza del modello serie temporale può eseguire il mapping a una singola gerarchia o a più gerarchie.

Le gerarchie vengono definite da *ID gerarchia*, *nome* e *origine*. Le gerarchie hanno un percorso, ovvero un ordine padre-figlio dall'alto verso il basso della gerarchia che gli utenti vogliono creare. Le proprietà padre-figlio eseguono il mapping dei *campi di istanza*.

### <a name="time-series-model-hierarchy-json-example"></a>Esempio JSON di gerarchia di modelli serie temporale

Esempio:

```JSON
{
    "id": "4c6f1231-f632-4d6f-9b63-e366d04175e3",
    "name": "Location",
    "source": {
        "instanceFieldNames": [
                "state",
                "city"
            ]
    }
}
```

Per altre informazioni sulle gerarchie di modello serie temporale, vedere la [documentazione di riferimento](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api).

### <a name="hierarchy-definition-behavior"></a>Comportamento della definizione della gerarchia

Si consideri l'esempio seguente dove *building*, *floor* e *room* fanno parte della definizione della gerarchia H1:

```plaintext
 H1 = [“building”, “floor”, “room”]
```

A seconda dei *campi di istanza*, vengono visualizzati gli attributi e i valori della gerarchia, come illustrato nella tabella seguente:

| ID serie temporale | Campi di istanza |
| --- | --- |
| ID1 | "building" = "1000", "floor" = "10", "room" = "55"  |
| ID2 | "building" = "1000", "room" = "55" |
| ID3 | "floor" = "10" |
| ID4 | "building" = "1000", "floor" = "10"  |
| ID5 | Non è impostato nessun "building", "floor" o "room" |

Nell'esempio precedente, **ID1** e **ID4** vengono visualizzati come parte della gerarchia H1 in Esplora Azure Time Series Insights e il resto è classificato in istanze senza *padre* perché non sono conformi alla gerarchia dei dati specificata.

## <a name="time-series-model-instances"></a>Istanze di modello serie temporale

Le istanze sono le serie temporali stesse. Nella maggior parte dei casi, *deviceId* o *assetId* è l'identificatore univoco dell'asset nell'ambiente. Alle istanze sono associate informazioni descrittive denominate proprietà delle istanze. Come minimo, le proprietà delle istanze includono informazioni sulla gerarchia. Possono includere anche utili dati descrittivi, ad esempio il produttore, l'operatore o la data dell'ultimo utilizzo.

Le istanze vengono definite da *typeId*, *timeSeriesId*, *name*, *description*, *hierarchyIds* e *instanceFields*. Ogni istanza esegue il mapping a un solo *tipo* e a una o più gerarchie. Le istanze ereditano tutte le proprietà dalle gerarchie ed è possibile aggiungere altri *instanceFields* per definire ulteriormente le proprietà delle istanze.

Gli elementi *instanceFields* sono proprietà di un'istanza e dei dati statici che definiscono un'istanza. Definiscono i valori delle proprietà non di gerarchia o di gerarchia, supportando anche l'indicizzazione per eseguire operazioni di ricerca.

La proprietà *Name* è facoltativa e con distinzione tra maiuscole e minuscole. Se *name* è non disponibile, per impostazione predefinita sarà l'ID serie temporale. Se viene specificata una proprietà *name*, l'ID serie temporale sarà comunque disponibile nel Well (la griglia sotto i grafici nell'explorer).

### <a name="time-series-model-instance-json-example"></a>Esempio JSON di istanza di modello serie temporale

Esempio:

```JSON
{
    "typeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
    "timeSeriesId": ["sampleTimeSeriesId"],
    "name": "sampleName",
    "description": "Sample Instance",
    "hierarchyIds": [
        "1643004c-0a84-48a5-80e5-7688c5ae9295"
    ],
    "instanceFields": {
        "state": "California",
        "city": "Los Angeles"
    }
}
```

Per altre informazioni sulle istanze di modello serie temporale, vedere la [documentazione di riferimento](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api).

### <a name="time-series-model-settings-example"></a>Esempio di impostazioni del modello serie temporale

Esempio:

```JSON
{
    "modelSettings": {
        "name": "DefaultModel",
        "timeSeriesIdProperties": [
            {
                "name": "id",
                "type": "String"
            }
        ],
        "defaultTypeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393"
    }
}
```

Per altre informazioni sulle impostazioni del modello serie temporale, vedere la [documentazione di riferimento](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api).

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Archiviazione e ingresso dei dati nella versione di anteprima di Azure Time Series Insights](./time-series-insights-update-storage-ingress.md).

- Vedere il nuovo [modello serie temporale](https://docs.microsoft.com/rest/api/time-series-insights/preview-model).