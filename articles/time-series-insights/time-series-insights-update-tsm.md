---
title: Modello Time Series-Azure Time Series Insights | Microsoft Docs
description: Informazioni sul modello Time Series in Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/25/2019
ms.custom: seodec18
ms.openlocfilehash: 5c045a4b5ccda47b786d86f1c004e9da4c8d85f3
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112303"
---
# <a name="time-series-model-in-azure-time-series-insights-preview"></a>Modello Time Series in Azure Time Series Insights Preview

Questo articolo descrive il modello Time Series, le funzionalità e come iniziare a creare e aggiornare i propri modelli nell'ambiente di Azure Time Series Insights Preview.

> [!TIP]
>  * Passare all'ambiente [demo di Contoso Wind Farm](https://insights.timeseries.azure.com/preview/samples) per un esempio di modello Live Time Series.
> * Per informazioni su come spostarsi nell'interfaccia utente del modello Time Series, vedere informazioni su [Azure Time Series Insights Preview Explorer](time-series-insights-update-explorer.md) .

## <a name="summary"></a>summary

I dati raccolti dai dispositivi IoT non includono in genere informazioni contestuali, cosa che rende difficile trovare e analizzare rapidamente i sensori. La motivazione principale per il modello Time Series è semplificare la ricerca e l'analisi dei dati delle serie temporali o degli Internet. Questo obiettivo viene raggiunto consentendo la cura, la manutenzione e l'arricchimento dei dati relativi alle serie temporali per preparare i set di dati pronti per il consumo per l'analisi.

## <a name="scenario-contosos-new-smart-oven"></a>Scenario: nuovo forno intelligente di contoso

**Si consideri lo scenario fittizio di uno smart forno contoso.** In questo scenario, si supponga che ogni Smart forno Contoso disponga di cinque sensori di temperatura, uno per ognuno dei quattro masterizzatori principali e uno per il forno stesso. Fino a poco tempo fa, ogni sensore di temperatura di Contoso ha inviato, archiviato e visualizzato i dati singolarmente. Per il monitoraggio dell'appliance di cucina, Contoso si basa sui grafici di base, uno per ogni singolo sensore.

Sebbene Contoso sia stato soddisfatto con la soluzione iniziale di dati e visualizzazione, sono emerse diverse limitazioni:

* I clienti hanno voluto capire il livello di surriscaldamento del forno quando la maggior parte dei principali masterizzatori si trovava in questo momento. Contoso ha avuto più difficoltà nell'analizzare e presentare una risposta unificata sulle condizioni del forno generale.
* I tecnici di Contoso volevano verificare che i primi masterizzatori eseguiti simultaneamente non comportino un risparmio energetico inefficiente. Si è verificata una difficoltà nel riferimento incrociato che i sensori di temperatura e tensione erano associati tra loro e come individuarli nell'archivio.
* Il team di Contoso Quality Assurance voleva controllare e confrontare la cronologia tra due versioni del sensore. Si è verificato un problema durante la determinazione dei dati appartenenti alla versione del sensore.

Senza la possibilità di strutturare, organizzare e definire il modello Time Series per lo Smart forno, ogni sensore di temperatura mantiene i punti dati dislocati, isolati e meno informativi. Trasformare questi punti dati in informazioni dettagliate di utilità pratica è stato più difficile perché ogni set di dati viveva in modo indipendente dagli altri.

Queste limitazioni hanno rivelato l'importanza degli strumenti di aggregazione e visualizzazione dei dati intelligenti per accompagnare il nuovo forno di Contoso:

* La visualizzazione dei dati risulta utile quando si è in grado di associare e combinare i dati in una visualizzazione pratica. Un esempio mostra i sensori di tensione insieme ai sensori di temperatura.
* La gestione dei dati multidimensionali per diverse entità, insieme alle funzionalità di confronto, zoom e intervallo di tempo, può essere difficile da realizzare.

Il **modello Time Series rappresenta una soluzione pratica** per molti degli scenari rilevati in questo esempio fittizio:

[![grafici del modello Time Series](media/v2-update-tsm/tsi-charting.png)](media/v2-update-tsm/tsi-charting.png#lightbox)

* Il modello Time Series gioca un ruolo fondamentale nelle query e nello spostamento perché contestualizzare i dati consentendo il disegno di confronti tra intervalli di tempo e tra i tipi di sensore e dispositivo.
* I dati sono ulteriormente contestuale perché i dati salvati in modo permanente nel modello Time Series conservano i calcoli delle query in serie temporali come variabili e li usano in fase di query.
* Il modello Time Series organizza e aggrega i dati per migliorare la visualizzazione e le funzionalità di gestione.

### <a name="key-capabilities"></a>Funzionalità principali

Allo scopo di semplificare e agevolare la gestione della contestualizzazione delle serie temporali, il modello di serie temporale abilita le funzionalità seguenti in Anteprima di Time Series Insights. Consente di:

* Consente di creare e gestire calcoli o formule sfruttando funzioni scalari, operazioni di aggregazione e così via.
* Definire le relazioni padre-figlio per consentire lo spostamento, la ricerca e il riferimento.
* Definire le proprietà associate alle istanze, definite come campi dell' *istanza*, e usarle per creare gerarchie.

### <a name="components"></a>Componenti

Il modello Time Series è costituito da tre componenti principali:

* [Istanze del modello Time Series](#time-series-model-instances)
* [Gerarchie di modelli Time Series](#time-series-model-hierarchies)
* [Tipi di modello Time Series](#time-series-model-types)

Questi componenti vengono combinati per specificare un modello Time Series e per organizzare i dati Azure Time Series Insights.

[Panoramica del modello Time Series ![](media/v2-update-tsm/tsm.png)](media/v2-update-tsm/tsm.png#lightbox)

È possibile creare e gestire un modello Time Series tramite l'interfaccia [Time Series Insights Preview](time-series-insights-update-how-to-tsm.md) . Le impostazioni del modello Time Series possono essere gestite tramite l' [API delle impostazioni del modello](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api).

## <a name="time-series-model-instances"></a>Istanze di modello serie temporale

Le *istanze* del modello Time Series sono rappresentazioni virtuali delle serie temporali.

Nella maggior parte dei casi, le istanze vengono identificate in modo univoco da **DeviceID** o **AssetID**, che vengono salvate come ID di serie temporali.

Alle istanze sono associate informazioni descrittive denominate *proprietà dell'istanza*, ad esempio un ID della serie temporale, un tipo, un nome, una descrizione, gerarchie e campi di istanza. Come minimo, le proprietà delle istanze includono informazioni sulla gerarchia.

I *campi di istanza* sono una raccolta di informazioni descrittive che possono includere valori per i livelli della gerarchia, nonché produttore, operatore e così via.

Dopo la configurazione di un'origine evento per l'ambiente Time Series Insights, le istanze vengono individuate e create automaticamente in un modello Time Series. Le istanze possono essere create o aggiornate tramite Esplora Time Series Insights usando le query del modello Time Series.

La [demo di Contoso Wind Farm](https://insights.timeseries.azure.com/preview/samples) offre diversi esempi di istanze attive.

[![istanze del modello Time Series](media/v2-update-tsm/instance.png)](media/v2-update-tsm/instance.png#lightbox)

### <a name="instance-properties"></a>Proprietà istanza

Le istanze sono definite da **timeSeriesId**, **typeid**, **Name**, **Description**, **hierarchyids**e **instanceFields**. Ogni istanza esegue il mapping a un solo *tipo*e una o più *gerarchie*.

| Proprietà | DESCRIZIONE |
| --- | ---|
| timeSeriesId | UUID della serie temporale a cui è associata l'istanza. |
| typeId | UUID del tipo di modello Time Series a cui è associata l'istanza. Per impostazione predefinita, tutte le nuove istanze individuate vengono associate a un tipo predefinito.
| Nome | La proprietà **name** è facoltativa e fa distinzione tra maiuscole e minuscole. Se il **nome** non è disponibile, il valore predefinito è **timeSeriesId**. Se viene specificato un nome, **timeSeriesId** è ancora disponibile nel [pozzetto](time-series-insights-update-explorer.md#4-time-series-well). |
| Descrizione | Descrizione di testo dell'istanza. |
| hierarchyId | Definisce le gerarchie a cui appartiene l'istanza. |
| instanceFields | Proprietà di un'istanza di e di qualsiasi dato statico che definisce un'istanza di. Definiscono i valori delle proprietà non di gerarchia o di gerarchia, supportando anche l'indicizzazione per eseguire operazioni di ricerca. |

> [!NOTE]
> Le gerarchie vengono compilate utilizzando campi di istanza. Per ulteriori definizioni delle proprietà di istanza, è possibile aggiungere **instanceFields** aggiuntive.

Le istanze hanno la rappresentazione JSON seguente:

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
> Per Time Series Insights API dell'istanza e il supporto di creazione, lettura, aggiornamento ed eliminazione (CRUD), vedere l'articolo sull' [esecuzione di query sui dati](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) e la [documentazione Rest dell'API dell'istanza](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api).

## <a name="time-series-model-hierarchies"></a>Gerarchie di modelli serie temporale

Le *gerarchie* dei modelli Time Series organizzano le istanze specificando i nomi delle proprietà e le relative relazioni.

È possibile configurare più gerarchie in un determinato ambiente di Time Series Insights. È possibile eseguire il mapping di un'istanza del modello Time Series a una singola gerarchia o a più gerarchie (relazione molti-a-molti).

L'interfaccia client [demo di Contoso Wind Farm](https://insights.timeseries.azure.com/preview/samples) Visualizza un'istanza standard e una gerarchia dei tipi.

[![gerarchie del modello Time Series](media/v2-update-tsm/hierarchy.png)](media/v2-update-tsm/hierarchy.png#lightbox)

### <a name="hierarchy-definition"></a>Definizione della gerarchia

Le gerarchie sono definite in base all' **ID**, al **nome**e all' **origine**della gerarchia.

| Proprietà | DESCRIZIONE |
| ---| ---|
| id | Identificatore univoco per la gerarchia, che viene utilizzato, ad esempio, quando si definisce un'istanza di. |
| Nome | Stringa utilizzata per fornire un nome per la gerarchia. |
| source | Specifica la gerarchia organizzativa o il percorso, ovvero un ordine padre-figlio dall'alto verso il basso della gerarchia che gli utenti desiderano creare. I campi dell'istanza della mappa delle proprietà padre-figlio. |

Le gerarchie sono rappresentate in JSON come:

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

Nell'esempio JSON precedente:

* `Location` definisce una gerarchia con `states` padre e `cities`figlio. Ogni `location` può avere più `states`, che a loro volta possono avere più `cities`.
* `ManufactureDate` definisce una gerarchia con `year` padre e `month`figlio. Ogni `ManufactureDate` può avere più `years`, che a loro volta possono avere più `months`.

> [!TIP]
> Per Time Series Insights API dell'istanza e il supporto CRUD, vedere l'articolo sull' [esecuzione di query sui dati](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) e la [documentazione Rest dell'API Hierarchy](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api).

### <a name="hierarchy-example"></a>Esempio di gerarchia

Si consideri un esempio in cui la gerarchia **H1** ha `building`, `floor`e `room` come parte della definizione **instanceFieldNames** :

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

Considerati i campi di istanza utilizzati nella definizione precedente e in diverse serie temporali, gli attributi e i valori della gerarchia vengono visualizzati come illustrato nella tabella seguente:

| ID serie temporale | Campi di istanza |
| --- | --- |
| ID1 | "building" = "1000", "floor" = "10", "room" = "55"  |
| ID2 | "building" = "1000", "room" = "55" |
| ID3 | "floor" = "10" |
| ID4 | "building" = "1000", "floor" = "10"  |
| ID5 | Non è impostato alcun valore di "Building", "Floor" o "Room". |

Le serie temporali **ID1** e **ID4** vengono visualizzate come parte della gerarchia **H1** in [Esplora Azure Time Series Insights](time-series-insights-update-explorer.md) perché hanno una *compilazione*, una *superficie*e una *stanza* completamente definite e ordinate correttamente. parametri.

Le altre sono classificate in *istanze senza padre* perché non sono conformi alla gerarchia dei dati specificata.

## <a name="time-series-model-types"></a>Tipi di modelli serie temporale

I *tipi* di modello serie temporale consentono di definire variabili o formule per eseguire calcoli. I tipi sono associati a un'istanza di Time Series Insights specifica.

Un tipo può avere una o più variabili. Un'istanza del modello Time Series, ad esempio, può essere di tipo *sensore di temperatura*, costituito dalle variabili *Media temperature*, *min temperature*e *max temperature*.

La [demo di Contoso Wind Farm](https://insights.timeseries.azure.com/preview/samples) Visualizza diversi tipi di modelli Time Series associati alle rispettive istanze.

[![tipi di modello Time Series](media/v2-update-tsm/types.png)](media/v2-update-tsm/types.png#lightbox)

> [!TIP]
> Per Time Series Insights API dell'istanza e il supporto CRUD, vedere l'articolo sull' [esecuzione di query sui dati](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) e la [documentazione Rest](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api)per l'API di tipo.

### <a name="type-properties"></a>Proprietà del tipo

I tipi di modello Time Series sono definiti in base all' **ID**, al **nome**, alla **Descrizione**e alle **variabili**.

| Proprietà | DESCRIZIONE |
| ---| ---|
| id | UUID per il tipo. |
| Nome | Stringa utilizzata per fornire un nome per il tipo. |
| Descrizione | Descrizione della stringa per il tipo. |
| variables | Specificare le variabili associate al tipo. |

I tipi sono conformi all'esempio JSON seguente:

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

I tipi di Time Series Insights potrebbero avere molte variabili che specificano le regole relative a formule e calcolo per gli eventi.

Ogni variabile può essere di uno dei tre *tipi*seguenti: *numeric*, *CATEGORICAL*e *Aggregate*.

* I tipi **numerici** funzionano con valori continui. 
* I tipi **categorici** funzionano con un set definito di valori discreti.
* I valori di **aggregazione** combinano più variabili di un singolo tipo (tutti numerici o categorici).

Nella tabella seguente vengono illustrate le proprietà rilevanti per ogni tipo di variabile.

[![tipi di modello Time Series](media/v2-update-tsm/variable-table.png)](media/v2-update-tsm/variable-table.png#lightbox)

#### <a name="numeric-variables"></a>Variabili numeriche

| Proprietà Variable | DESCRIZIONE |
| --- | ---|
| Filtro di variabile | I filtri sono clausole condizionali facoltative per limitare il numero di righe da considerare per il calcolo. |
| Valore di variabile | Valori di telemetria usati per il calcolo proveniente dal dispositivo o dai sensori oppure trasformati usando le espressioni della serie temporale. Le variabili di tipo numerico devono essere di tipo *Double*.|
| Interpolazione di variabili | L'interpolazione specifica come ricostruire un segnale usando i dati esistenti. Per le variabili numeriche sono disponibili opzioni di interpolazione *lineare* e *passaggio* . |
| Aggregazione di variabile | Supporta il calcolo tramite gli operatori *AVG*, *min*, *Max*, *Sum*, *count*, *First*, *Last* e Time-weighted (*AVG*, *min*, *Max*, *Sum*, *Left*). |

Le variabili sono conformi all'esempio JSON seguente:

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

| Proprietà Variable | DESCRIZIONE |
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
  "value": "toLong($event.[Status].Double)",
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

| Proprietà Variable | DESCRIZIONE |
| --- | ---|
| Filtro di variabile | I filtri sono clausole condizionali facoltative per limitare il numero di righe da considerare per il calcolo. |
| Aggregazione di variabile | Supporto del calcolo tramite *AVG*, *min*, *Max*, *Sum*, *count*, *First*, *Last*. |

Le variabili sono conformi all'esempio JSON seguente:

```JSON
"Aggregate Speed": {
  "kind": "aggregate",
  "filter": null,
  "aggregation": {
    "tsx": "avg($event.Speed.Double)"
  }
}
```

Le variabili vengono archiviate nella definizione del tipo di un modello Time Series e possono essere fornite inline tramite le [API di query](time-series-insights-update-tsq.md) per eseguire l'override della definizione archiviata.

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Archiviazione e ingresso dei dati nella versione di anteprima di Azure Time Series Insights](./time-series-insights-update-storage-ingress.md).
- Informazioni sulle operazioni comuni del modello Time Series nella [modellazione dei dati in Azure Time Series Insights Preview](./time-series-insights-update-how-to-tsm.md)
- Leggere la documentazione di riferimento del nuovo [modello Time Series](https://docs.microsoft.com/rest/api/time-series-insights/preview-model) .
