---
title: Modello di serie temporale - Approfondimenti sulle serie temporali di Azure Documenti Microsoft
description: Informazioni sul modello Time Series in Azure Time Series Insights Preview.Learn about Time Series Model in Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/16/2020
ms.custom: seodec18
ms.openlocfilehash: 648578563a0e53d3ed5bda6ab47f85c3c6a2a24e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476655"
---
# <a name="time-series-model-in-azure-time-series-insights-preview"></a>Modello di serie temporale in Azure Time Series Insights Preview

Questo articolo descrive Time Series Model, le funzionalità e come iniziare a compilare e aggiornare i propri modelli nell'ambiente Azure Time Series Insights Preview.This article describes Time Series Model, the capabilities, and how to start building and updating your own models in the Azure Time Series Insights Preview environment.

> [!TIP]
>  * Passare all'ambiente [demo Contoso Wind Farm](https://insights.timeseries.azure.com/preview/samples) per un modello Time Series in tempo reale.
> * Per informazioni su [Azure Time Series Insights Preview explorer,](time-series-insights-update-explorer.md) vedere come esplorare l'interfaccia utente del modello Time Series.Read about the Azure Time Series Insights Preview explorer to learn to navigate the Time Series Model UI.
> * [Informazioni su come usare il modello Time Series](time-series-insights-update-how-to-tsm.md) con il Web Explorer Time Series Insights.

## <a name="summary"></a>Riepilogo

I dati raccolti dai dispositivi IoT non includono in genere informazioni contestuali, cosa che rende difficile trovare e analizzare rapidamente i sensori. La motivazione principale per il modello Time Series è semplificare la ricerca e l'analisi dei dati IoT o Time Series. Raggiunge questo obiettivo abilitando la cura, la manutenzione e l'arricchimento dei dati delle serie temporali per preparare set di dati pronti per il consumatore per l'analisi.

## <a name="scenario-contosos-new-smart-oven"></a>Scenario: nuovo forno intelligente di Contoso

**Si consideri lo scenario fittizio di un forno intelligente Contoso.** In questo scenario, si supponga che ogni forno intelligente Contoso dispone di cinque sensori di temperatura, uno per ognuno dei quattro masterizzatori principali e uno per il forno stesso. Fino a poco tempo fa, ogni sensore di temperatura Contoso inviava, memorizzava e visualizzava i propri dati singolarmente. Per il monitoraggio degli apparecchi da cucina, Contoso si è affidata a grafici di base, uno per ogni singolo sensore.

Mentre Contoso era soddisfatta della sua soluzione iniziale di visualizzazione e dati e dati, sono emerse diverse limitazioni:

* I clienti volevano sapere quanto caldo sarebbe stato il forno complessivo quando la maggior parte dei bruciatori migliori erano accesi. Contoso ha avuto maggiori difficoltà ad analizzare e presentare una risposta unificata sulle condizioni del forno complessivo.
* I tecnici di Contoso volevano verificare che i bruciatori principali eseguiti contemporaneamente non avrebbero comportato un'interruzione dell'alimentazione inefficiente. C'era difficoltà a incrociare quali sensori di temperatura e tensione erano associati tra loro e come localizzarli nel negozio.
* Il team di controllo qualità di Contoso desiderava controllare e confrontare la cronologia tra due versioni di sensori. Si è verificato un difficile determinare quali dati appartenevano a quale versione del sensore.

Senza la possibilità di strutturare, organizzare e definire il modello di time series del forno intelligente generale, ogni sensore di temperatura ha mantenuto i punti dati dislocati, isolati e meno informativi. Trasformare questi punti dati in informazioni utili era più difficile poiché ogni set di dati viveva indipendentemente dagli altri.

Queste limitazioni hanno rivelato l'importanza di strumenti intelligenti di aggregazione e visualizzazione dei dati per accompagnare il nuovo forno di Contoso:

* La visualizzazione dei dati si rivela utile quando si è in grado di associare e combinare i dati in una visualizzazione comoda. Un esempio è quello di mostrare i sensori di tensione insieme ai sensori di temperatura.
* La gestione dei dati multidimensionali per diverse entità, insieme alle funzionalità di confronto, zoom e intervallo di tempo, può essere difficile da eseguire.

**Time Series Model fornisce una soluzione comoda** per molti degli scenari incontrati in questo esempio fittizio:

[![Esempio di grafico a forno intelligente modello di serie temporale](media/v2-update-tsm/time-series-model-smart-oven.png)](media/v2-update-tsm/time-series-model-smart-oven.png#lightbox)

* Il modello Time Series svolge un ruolo fondamentale nelle query e nella navigazione perché contestualizza i dati consentendo di eseguire confronti tra intervalli di tempo e tra i tipi di sensore e dispositivo. (**A**) 
* I dati vengono ulteriormente contestualizzati perché i dati persistenti nel modello Time Series conservano i calcoli delle query di serie temporali come variabili e li riutilizzano in fase di query.
* Time Series Model organizza e aggrega i dati per migliorare le funzionalità di visualizzazione e gestione. (**B**) 

### <a name="key-capabilities"></a>Funzionalità principali

Allo scopo di semplificare e agevolare la gestione della contestualizzazione delle serie temporali, il modello di serie temporale abilita le funzionalità seguenti in Anteprima di Time Series Insights. Consente di:

* Creare e gestire calcoli o formule sfruttando funzioni scalari, operazioni di aggregazione e così via.
* Definire relazioni padre-figlio per abilitare la navigazione, la ricerca e il riferimento.
* Definire le proprietà associate alle istanze, definite come *campi di istanza,* e utilizzarle per creare gerarchie.

### <a name="components"></a>Componenti

Time Series Model ha tre componenti principali:

* [Istanze del modello Time Series](#time-series-model-instances)
* [Gerarchie di modelli Time Series](#time-series-model-hierarchies)
* [Tipi di modelli serie temporale](#time-series-model-types)

Questi componenti vengono combinati per specificare un modello di serie temporale e per organizzare i dati di Azure Time Series Insights.These components are combined to specify a time series model and to organize your Azure Time Series Insights data.

[![Grafico generale del modello Time Series](media/v2-update-tsm/time-series-model-overview.png)](media/v2-update-tsm/time-series-model-overview.png#lightbox)

Un modello time series può essere creato e gestito tramite l'interfaccia [Time Series Insights Preview.](time-series-insights-update-how-to-tsm.md) Le impostazioni del modello Time Series possono essere gestite tramite [l'API Impostazioni modello.](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api)

## <a name="time-series-model-instances"></a>Istanze di modello serie temporale

Le *istanze* del modello Time Series sono rappresentazioni virtuali della serie temporale stessa.

Nella maggior parte dei casi, le istanze vengono identificate in modo univoco da **deviceId** o **assetId**, che vengono salvati come ID di serie temporali.

Alle istanze sono associate informazioni descrittive denominate *proprietà dell'istanza,* ad esempio un ID, un tipo, un nome, una descrizione, delle gerarchie e dei campi di istanza. Come minimo, le proprietà delle istanze includono informazioni sulla gerarchia.

*I campi di istanza* sono una raccolta di informazioni descrittive che possono includere valori per i livelli gerarchici, nonché per produttore, operatore e così via.

Dopo aver configurato un'origine eventi per l'ambiente Time Series Insights, le istanze vengono individuate e create automaticamente in un modello Time Series. Le istanze possono essere create o aggiornate tramite Esplora Time Series Insights tramite query Time Series Model.

La demo del [Farm eolico di Contoso](https://insights.timeseries.azure.com/preview/samples) fornisce diversi esempi di istanze dinamiche.

[![Esempio di istanza del modello Time Series](media/v2-update-tsm/time-series-model-instance.png)](media/v2-update-tsm/time-series-model-instance.png#lightbox)

### <a name="instance-properties"></a>Proprietà istanza

Le istanze sono definite da **timeSeriesId**, **typeId**, **name**, **description**, **hierarchyIds**e **instanceFields**. Ogni istanza esegue il mapping a un solo *tipo*e a una o più *gerarchie*.

| Proprietà | Descrizione |
| --- | ---|
| timeSeriesId | UUID della serie temporale a cui è associata l'istanza. |
| typeId | UUID del tipo di modello Time Series a cui è associata l'istanza. Per impostazione predefinita, tutte le nuove istanze individuate vengono associate a un tipo predefinito.
| name | La proprietà **name** è facoltativa e fa distinzione tra maiuscole e minuscole. Se **name** non è disponibile, il valore predefinito è **timeSeriesId**. Se viene fornito un nome, **timeSeriesId** è ancora disponibile nel [file](time-series-insights-update-explorer.md#4-time-series-well). |
| description | Descrizione testuale dell'istanza. |
| hierarchyIds | Definisce a quali gerarchie appartiene l'istanza. |
| campi di istanza | Proprietà di un'istanza ed eventuali dati statici che definiscono un'istanza. Definiscono i valori delle proprietà non di gerarchia o di gerarchia, supportando anche l'indicizzazione per eseguire operazioni di ricerca. |

> [!NOTE]
> Le gerarchie vengono compilate utilizzando i campi di istanza. È possibile aggiungere ulteriori **instanceField** per altre definizioni di proprietà di istanza.

Le istanze hanno la rappresentazione JSON seguente:Instances have the following JSON representation:

```JSON
{
  "timeSeriesId": ["PU2"],
  "typeId": "545314a5-7166-4b90-abb9-fd93966fa39b",
  "hierarchyIds": ["95f0a8d1-a3ef-4549-b4b3-f138856b3a12"],
  "description": "Pump #2",
  "instanceFields": {
    "Location": "Redmond",
    "Fleet": "Fleet 5",
    "Unit": "Pump Unit 3",
    "Manufacturer": "Contoso",
    "ScalePres": "0.54",
    "scaleTemp": "0.54"
  }
}
```

> [!TIP]
> Per l'API dell'istanza di Time Series Insights e il supporto per la creazione, la lettura, l'aggiornamento e l'eliminazione (CRUD), leggere l'articolo [Query sui dati](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) e la [documentazione REST dell'API dell'istanza.](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api)

## <a name="time-series-model-hierarchies"></a>Gerarchie di modelli serie temporale

Le *gerarchie* del modello Time Series organizzano le istanze specificando i nomi delle proprietà e le relative relazioni.

È possibile configurare più gerarchie in un determinato ambiente Time Series Insights.You can configure multiple hierarchies in a given Time Series Insights environment. Un'istanza del modello Time Series può eseguire il mapping a una singola gerarchia o a più gerarchie (relazione molti-a-molti).

Nell'interfaccia del client [demo Contoso Wind Farm](https://insights.timeseries.azure.com/preview/samples) vengono visualizzate un'istanza standard e una gerarchia dei tipi.

[![Esempio di gerarchia di modelli Time Series](media/v2-update-tsm/time-series-model-hierarchies.png)](media/v2-update-tsm/time-series-model-hierarchies.png#lightbox)

### <a name="hierarchy-definition"></a>Definizione gerarchia

Le gerarchie sono definite in base **all'ID**gerarchia, **al nome**e **all'origine**.

| Proprietà | Descrizione |
| ---| ---|
| id | Identificatore univoco della gerarchia, utilizzato, ad esempio, quando si definisce un'istanza. |
| name | Stringa utilizzata per fornire un nome per la gerarchia. |
| source | Specifica la gerarchia organizzativa o il percorso, ovvero un ordine padre-figlio dall'alto verso il basso della gerarchia che gli utenti desiderano creare. Le proprietà padre-figlio eseguono il mapping dei campi di istanza. |

Le gerarchie sono rappresentate in JSON come:Hierarchies are represented in JSON as:

```JSON
{
  "hierarchies": [
    {
      "id": "6e292e54-9a26-4be1-9034-607d71492707",
      "name": "Location",
      "source": {
        "instanceFieldNames": [
          "state",
          "city"
        ]
      }
    },
    {
      "id": "a28fd14c-6b98-4ab5-9301-3840f142d30e",
      "name": "ManufactureDate",
      "source": {
        "instanceFieldNames": [
          "year",
          "month"
        ]
      }
    }
  ]
}
```

Nell'esempio JSON precedente:In the previous JSON example:

* `Location`definisce una gerarchia con padre `states` e figlio `cities`. Ognuna `location` può `states`avere più , `cities`che a loro volta possono avere più file .
* `ManufactureDate`definisce una gerarchia con padre `year` e figlio `month`. Ognuna `ManufactureDate` può `years`avere più , `months`che a loro volta possono avere più file .

> [!TIP]
> Per il supporto dell'API dell'istanza di Time Series Insights e del formato CRUD, leggere l'articolo [Query sui dati](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) e la documentazione REST dell'API della [gerarchia](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api).

### <a name="hierarchy-example"></a>Esempio di gerarchia

Si consideri un esempio `floor`in `room` cui la gerarchia **H1** `building`ha , , e come parte della relativa definizione **instanceFieldNames:**

```JSON
{
  "id": "aaaaaa-bbbbb-ccccc-ddddd-111111",
  "name": "H1",
  "source": {
    "instanceFieldNames": [
      "building",
      "floor",
      "room"
    ]
  }
}
```

Dati i campi di istanza utilizzati nella definizione precedente e in diverse serie temporali, gli attributi e i valori della gerarchia vengono visualizzati come illustrato nella tabella seguente:

| ID serie temporale | Campi di istanza |
| --- | --- |
| ID1 | "edificio" - "1000", "piano" - "10", "stanza" - "55"  |
| ID2 | "edificio" - "1000", "stanza" - "55" |
| ID3 | "floor" - "10" |
| ID4 | "edificio" - "1000", "piano" - "10"  |
| ID5 | Nessuno di "edificio", "piano" o "stanza" è impostato. |

Gli **ID1** e **l'ID4** delle serie temporali vengono visualizzati come parte della gerarchia *floor* **H1** [nell'esploratore Azure Time Series Insights](time-series-insights-update-explorer.md) perché sono stati definiti in modo completo e ordinato correttamente i parametri *building*, floor e *room.*

Gli altri sono classificati in *Istanze senza padre* perché non sono conformi alla gerarchia di dati specificata.

## <a name="time-series-model-types"></a>Tipi di modelli serie temporale

I *tipi* di modello serie temporale consentono di definire variabili o formule per eseguire calcoli. I tipi sono associati a un'istanza specifica di Time Series Insights.

Un tipo può avere una o più variabili. Ad esempio, un'istanza del modello Time Series potrebbe essere di tipo *Sensore di temperatura*, costituita dalle variabili *avg temperature*, temperature *min*e *temperatura massima*.

La demo di [Contoso Wind Farm](https://insights.timeseries.azure.com/preview/samples) visualizza diversi tipi di modello Time Series associati alle rispettive istanze.

[![Time Series Esempio di tipo di modello](media/v2-update-tsm/time-series-model-types.png)](media/v2-update-tsm/time-series-model-types.png#lightbox)

> [!TIP]
> Per il supporto dell'API dell'istanza di Time Series Insights e del formato CRUD, leggere l'articolo [Query sui dati](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) e la [documentazione REST dell'API Type](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api).

### <a name="type-properties"></a>Proprietà del tipo

I tipi di modello Time Series sono definiti in base a **id**, **nome**, **descrizione**e **variabili**.

| Proprietà | Descrizione |
| ---| ---|
| id | UUID per il tipo. |
| name | Stringa utilizzata per fornire un nome per il tipo. |
| description | Descrizione della stringa per il tipo. |
| variables | Specificare le variabili associate al tipo. |

I tipi sono conformi all'esempio JSON seguente:Types conform to the following JSON example:

```JSON
{
  "types": [
    {
      "id": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
      "name": "DefaultType",
      "description": "Default type",
      "variables": {
        "EventCount": {
          "kind": "aggregate",
          "value": null,
          "filter": null,
          "aggregation": {
            "tsx": "count()"
          }
        },
        "Interpolated Speed": {
          "kind": "numeric",
          "value": {
              "tsx": "$event.[speed].Double"
          },
          "filter": null,
          "interpolation": {
              "kind": "step",
              "boundary": {
                  "span": "P1D"
              }
          },
          "aggregation": {
              "tsx": "left($value)"
          }
        }
      }
    }
  ]
}
```

### <a name="variables"></a>Variabili

I tipi di Time Series Insights possono avere molte variabili che specificano le regole di formula e calcolo sugli eventi.

Ogni variabile può essere di tre *tipi:* *numerico*, *categorico*e *aggregato*.

* I tipi **numerici** funzionano con valori continui. 
* I tipi **categorici** funzionano con un insieme definito di valori discreti.
* **I** valori aggregati combinano più variabili di un singolo tipo (tutte numeriche o tutte categoriche).

Nella tabella seguente vengono visualizzate le proprietà rilevanti per ogni tipo di variabile.

[![Tabella delle variabili del modello Time Series](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

#### <a name="numeric-variables"></a>Variabili numeriche

| Variable (proprietà) | Descrizione |
| --- | ---|
| Filtro di variabile | I filtri sono clausole condizionali facoltative per limitare il numero di righe considerate per il calcolo. |
| Valore di variabile | Valori di telemetria utilizzati per il calcolo provenienti dal dispositivo o dai sensori o trasformati tramite espressioni Time Series.Telemetry values used for computation coming from the device or sensors or transformed by using Time Series Expressions. Le variabili di tipo numerico devono essere di tipo *Double*.|
| Interpolazione variabile | L'interpolazione specifica come ricostruire un segnale utilizzando i dati esistenti. *Le* opzioni step e *Linear* interpolazione sono disponibili per le variabili numeriche. |
| Aggregazione di variabile | Supportare il calcolo tramite gli operatori *Mediag*, *Min*, *Max*, *Sum*, *Count*, *First*, *Last* e time-weighted (*Avg*, *Min*, *Max*, *Sum*, *Left*). |

Le variabili sono conformi all'esempio JSON seguente:Variables conform to the following JSON example:

```JSON
"Interpolated Speed": {
  "kind": "numeric",
  "value": {
    "tsx": "$event.[speed].Double"
  },
  "filter": null,
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span": "P1D"
    }
  },
  "aggregation": {
    "tsx": "left($value)"
  }
}
```

#### <a name="categorical-variables"></a>Variabili categoriche

| Variable (proprietà) | Descrizione |
| --- | ---|
| Filtro di variabile | I filtri sono clausole condizionali facoltative per limitare il numero di righe considerate per il calcolo. |
| Valore di variabile | Valori di telemetria utilizzati per il calcolo proveniente dal dispositivo o dai sensori. Le variabili di tipo categorico devono essere *Long* o *String*. |
| Interpolazione variabile | L'interpolazione specifica come ricostruire un segnale utilizzando i dati esistenti. L'opzione Interpolazione *passo* è disponibile per le variabili di categoria. |
| Categorie di variabili | Le categorie creano un mapping tra i valori provenienti dal dispositivo o i sensori a un'etichetta. |
| Categoria predefinita variabile | La categoria predefinita è per tutti i valori di cui non viene eseguito il mapping nella proprietà "categories". |

Le variabili sono conformi all'esempio JSON seguente:Variables conform to the following JSON example:

```JSON
"Status": {
  "kind": "categorical",
  "value": {
     "tsx": "toLong($event.[Status].Double)"
},
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span" : "PT1M"
    }
  },
  "categories": [
    {
      "values": [0, 1, 2],
      "label": "Good"
    },
    {
      "values": [3],
      "label": "Bad"
    }
  ],
  "defaultCategory": {
    "label": "Not Applicable"
  }
}
```

#### <a name="aggregate-variables"></a>Variabili di aggregazione

| Variable (proprietà) | Descrizione |
| --- | ---|
| Filtro di variabile | I filtri sono clausole condizionali facoltative per limitare il numero di righe considerate per il calcolo. |
| Aggregazione di variabile | Supportare il calcolo tramite *Avg*, *Min*, *Max*, *Sum*, *Count*, *First*, *Last*. |

Le variabili sono conformi all'esempio JSON seguente:Variables conform to the following JSON example:

```JSON
"Aggregate Speed": {
  "kind": "aggregate",
  "filter": null,
  "aggregation": {
    "tsx": "avg($event.Speed.Double)"
  }
}
```

Le variabili vengono archiviate nella definizione del tipo di un modello time series e possono essere fornite inline tramite [le API di query](time-series-insights-update-tsq.md) per eseguire l'override della definizione archiviata.

## <a name="next-steps"></a>Passaggi successivi

- Leggere [Archiviazione e ingresso nell'anteprima di Azure Time Series Insights](./time-series-insights-update-storage-ingress.md).

- Informazioni sulle operazioni comuni del modello Time Series nella [modellazione dei dati in Azure Time Series Insights Preview](./time-series-insights-update-how-to-tsm.md)

- Leggere la nuova documentazione di riferimento [del modello Time Series.Read](https://docs.microsoft.com/rest/api/time-series-insights/preview-model) the new Time Series Model reference documentation.
