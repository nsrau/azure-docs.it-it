---
title: Migrazione a nuove versioni dell'API Azure Time Series Insights Gen2 | Microsoft Docs
description: Come aggiornare Azure Time Series Insights ambienti Gen2 per usare le nuove versioni disponibili a livello generale.
ms.service: time-series-insights
services: time-series-insights
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/12/2020
ms.custom: shresha
ms.openlocfilehash: d68323bb72779ec5978c721b5258d4c8a8d4940d
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89650799"
---
# <a name="migrating-to-new-azure-time-series-insights-gen2-api-versions"></a>Migrazione a nuove versioni dell'API Gen2 Azure Time Series Insights

## <a name="overview"></a>Panoramica

Se è stato creato un ambiente di Azure Time Series Insights Gen2 quando era disponibile in anteprima pubblica (prima del 16 luglio 2020), aggiornare l'ambiente TSI per usare le nuove versioni di API a livello generale seguendo i passaggi descritti in questo articolo.

La nuova versione dell'API è `2020-07-31` e usa una [sintassi di espressione Time Series](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax)aggiornata.

Gli utenti devono eseguire la migrazione delle [variabili del modello Time Series](./concepts-variables.md)dell'ambiente, delle query salvate, delle query di Power bi e di eventuali strumenti personalizzati che effettuano chiamate agli endpoint dell'API. In caso di domande o dubbi sul processo di migrazione, inviare un ticket di supporto tramite il portale di Azure e menzionare questo documento.

> [!IMPORTANT]
> La versione dell'API `2018-11-01-preview` di anteprima continuerà a essere supportata fino al 31 ottobre 2020. Completare tutti i passaggi applicabili della migrazione prima di, per evitare eventuali rotture nel servizio.

## <a name="migrate-time-series-model-and-saved-queries"></a>Eseguire la migrazione del modello Time Series e delle query salvate

Per consentire agli utenti di eseguire la migrazione delle [variabili del modello Time Series](./concepts-variables.md) e delle query salvate, è disponibile uno strumento incorporato tramite [Esplora Azure Time Series Insights](https://insights.timeseries.azure.com). Passare all'ambiente di cui si desidera eseguire la migrazione e attenersi alla procedura riportata di seguito. **È possibile completare la migrazione parzialmente e tornare a completarla in un secondo momento, tuttavia, non è possibile ripristinare nessuno degli aggiornamenti.**

> [!NOTE]
> È necessario essere un collaboratore all'ambiente per eseguire aggiornamenti al modello Time Series e alle query salvate. Se non si è un collaboratore, sarà possibile migrare solo le query salvate personali. Prima di procedere, esaminare i [criteri di accesso all'ambiente](./concepts-access-policies.md) e il livello di accesso.

1. Verrà richiesto di aggiornare la sintassi utilizzata dalle variabili del modello Time Series e dalle query salvate.

    [![Prompt](media/api-migration/ux-prompt.png)](media/v2-update-overview/overview-one.png#lightbox)

    Se la notifica viene chiusa accidentalmente, è possibile trovarla nel pannello di notifica.

1. Fare clic su **Mostra aggiornamenti** per aprire lo strumento di migrazione.

1. Fare clic su **Scarica tipi**. Poiché la migrazione sovrascriverà i tipi correnti per modificare la sintassi delle variabili, sarà necessario salvare una copia dei tipi correnti. Lo strumento invierà una notifica quando i tipi sono stati scaricati.

    [![Tipi di download](media/api-migration/ux-migration-tool.png)](media/v2-update-overview/overview-one.png#lightbox)

1. Fare clic su **Aggiorna variabili**. Lo strumento invierà una notifica quando le variabili sono state aggiornate.

    > [!IMPORTANT]
    > Se si aggiornano i tipi, le applicazioni personalizzate che usano una versione precedente dell'API ( `2018-11-01-preview` ) dovranno usare la nuova versione API ( `2020-07-31` ) per continuare a funzionare. Se non si è certi di quale versione dell'API si sta usando, rivolgersi all'amministratore prima di eseguire l'aggiornamento. È possibile chiudere lo strumento di migrazione e tornare a questi passaggi in un secondo momento. Altre informazioni su [come eseguire la migrazione di applicazioni personalizzate](#migrate-custom-applications).

    [![Aggiorna variabili](media/api-migration/ux-migration-tool-downloaded-types.png)](media/v2-update-overview/overview-one.png#lightbox)

1. Fare clic su **Aggiorna query salvate**. Lo strumento invierà una notifica quando le query salvate sono state aggiornate.

    [![Aggiornare le query salvate](media/api-migration/ux-migration-tool-updated-variables.png)](media/v2-update-overview/overview-one.png#lightbox)

1. Fare clic su **Done**.

    [![Migrazione completata](media/api-migration/ux-migration-tool-updated-saved-queries.png)](media/v2-update-overview/overview-one.png#lightbox)

Esaminare l'ambiente aggiornato creando un grafico di alcune delle variabili appena create e delle query salvate. Se si verifica un comportamento imprevisto durante la creazione di grafici, inviare commenti e suggerimenti usando lo strumento per il feedback nella finestra di esplorazione.

## <a name="migrate-power-bi-queries"></a>Eseguire la migrazione di query Power BI

Se sono state generate query usando il connettore Power BI, vengono effettuate chiamate a Azure Time Series Insights usando la versione dell'API di anteprima e la sintassi delle espressioni della serie temporale precedente. Queste query continueranno a recuperare correttamente i dati fino a quando l'API di anteprima non sarà deprecata.

Per aggiornare le query in modo da usare la nuova versione dell'API e la nuova sintassi delle espressioni della serie temporale, è necessario rigenerare le query dalla finestra di esplorazione. Leggere altre informazioni su come [creare query usando il connettore Power bi](./how-to-connect-power-bi.md).

> [!NOTE]
> È necessario utilizzare la versione del 2020 luglio del Power BI Desktop. In caso contrario, potrebbe essere visualizzato un [errore di versione del payload della query non valido](./how-to-diagnose-troubleshoot.md#problem-power-bi-connector-shows-unable-to-connect).

## <a name="migrate-custom-applications"></a>Eseguire la migrazione di applicazioni personalizzate

Se l'applicazione personalizzata effettua chiamate agli endpoint REST seguenti, è sufficiente aggiornare la versione dell'API a `2020-07-31` nell'URI:

- API modello della serie temporale
  - API impostazioni modello
    - [Ottieni](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/modelsettings/get)
    - [Aggiornamento](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/modelsettings/update)
  - API dell'istanza
    - [Tutte le operazioni batch](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/executebatch)
    - [Elenco](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/list)
    - [Ricerca](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/search)
    - [Suggerisci](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/suggest)
  - API della gerarchia
    - [Tutte le operazioni batch](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/executebatch)
    - [Elenco](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/list)
  - API di tipi
    - [Elimina, Ottieni operazioni](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch)
    - [Elenco](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/list)

Per gli endpoint REST seguenti, è necessario aggiornare la versione dell'API a `2020-07-31` nell'URI e verificare che tutte le occorrenze della `tsx` Proprietà usino la [sintassi delle espressioni Time Series](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax)aggiornata.

- API di tipi
  - [Operazione Put](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput)
- API per le query
  - [GetEvents](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents)
  - [Getserie](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries)
  - [GetAggregateSeries](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)

### <a name="examples"></a>Esempi

#### <a name="typesbatchput"></a>TypesBatchPut

Vecchio corpo della richiesta (usato da `2018-11-01-preview` ):

```JSON
{
  "put": [
    {
      "id": "c1cb7a33-ed9b-4cf1-9958-f3162fed8ee8",
      "name": "OutdoorTemperatureSensor",
      "description": "This is an outdoor temperature sensor.",
      "variables": {
        "AverageTemperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event.[Temperature_Celsius].Double"
          },
          "filter": {
            "tsx": "$event.[Mode].String = 'outdoor'"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

Il corpo della richiesta è stato aggiornato (usato da `2020-07-31` ):

```JSON
{
  "put": [
    {
      "id": "c1cb7a33-ed9b-4cf1-9958-f3162fed8ee8",
      "name": "OutdoorTemperatureSensor",
      "description": "This is an outdoor temperature sensor.",
      "variables": {
        "AverageTemperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event['Temperature_Celsius'].Double"
          },
          "filter": {
            "tsx": "$event['Mode'].String = 'outdoor'"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

In alternativa, `filter` può essere anche `$event.Mode.String = 'outdoor'` . `value`Deve usare le parentesi quadre per eseguire l'escape del carattere speciale ( `_` ).

#### <a name="getevents"></a>GetEvents

Vecchio corpo della richiesta (usato da `2018-11-01-preview` ):

```JSON
{
  "getEvents": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952",
      "T1"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "filter": {
      "tsx": "($event.[Value].Double != null) OR ($event.[Status].String = 'Good')"
    },
    "projectedProperties": [
      {
        "name": "Temperature",
        "type": "Double"
      }
    ]
  }
}
```

Il corpo della richiesta è stato aggiornato (usato da `2020-07-31` ):

```JSON
{
  "getEvents": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952",
      "T1"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "filter": {
      "tsx": "($event.Value.Double != null) OR ($event.Status.String = 'Good')"
    },
    "projectedProperties": [
      {
        "name": "Temperature",
        "type": "Double"
      }
    ]
  }
}
```

In alternativa, `filter` può essere anche `($event['Value'].Double != null) OR ($event['Status'].String = 'Good')` .

#### <a name="getseries"></a>Getserie

Vecchio corpo della richiesta (usato da `2018-11-01-preview` ):

```JSON
{
  "getSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "inlineVariables": {
      "pressure": {
        "kind": "numeric",
        "value": {
          "tsx": "$event.[Bar-Pressure-Offset]"
        },
        "aggregation": {
          "tsx": "avg($value)"
        }
      }
    },
    "projectedVariables": [
      "pressure"
    ]
  }
}
```

Il corpo della richiesta è stato aggiornato (usato da `2020-07-31` ):

```JSON
{
  "getSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "inlineVariables": {
      "pressure": {
        "kind": "numeric",
        "value": {
          "tsx": "$event['Bar-Pressure-Offset']"
        },
        "aggregation": {
          "tsx": "avg($value)"
        }
      }
    },
    "projectedVariables": [
      "pressure"
    ]
  }
}
```

In alternativa, `value` può essere anche `$event['Bar-Pressure-Offset'].Double` . Se non viene specificato alcun tipo di dati, il tipo di dati viene sempre considerato Double. La notazione tra parentesi quadre deve essere utilizzata per eseguire l'escape del carattere speciale ( `-` ).

#### <a name="aggregateseries"></a>AggregateSeries

Vecchio corpo della richiesta (usato da `2018-11-01-preview` ):

```JSON
{
  "aggregateSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "interval": "PT1M",
    "inlineVariables": {
      "MinTemperature": {
        "kind": "numeric",
        "value": {
          "tsx": "coalesce($event.[Temp].Double, toDouble($event.[Temp].Long))"
        },
        "aggregation": {
          "tsx": "min($value)"
        }
      },
    },
    "projectedVariables": [
      "MinTemperature"
    ]
  }
}
```

Il corpo della richiesta è stato aggiornato (usato da `2020-07-31` ):

```JSON
  "aggregateSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "interval": "PT1M",
    "inlineVariables": {
      "MinTemperature": {
        "kind": "numeric",
        "value": {
          "tsx": "coalesce($event.Temp.Double, toDouble($event.Temp.Long))"
        },
        "aggregation": {
          "tsx": "min($value)"
        }
      },
    },
    "projectedVariables": [
      "MinTemperature"
    ]
  }
}
```

In alternativa, `value` può essere anche `coalesce($event['Temp'].Double, toDouble($event['Temp'].Long))` .

### <a name="potential-errors"></a>Potenziali errori

#### <a name="invalidinput"></a>InvalidInput

Se viene visualizzato l'errore seguente, si sta usando la nuova versione dell'API ( `2020-07-31` ), ma la sintassi del TSX non è stata aggiornata. Esaminare la [sintassi delle espressioni della serie temporale](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax) e gli esempi di migrazione precedenti. Verificare che tutte le `tsx` proprietà siano aggiornate correttamente prima di inviare nuovamente la richiesta dell'API.

```JSON
{
    "error": {
        "code": "InvalidInput",
        "message": "Unable to parse 'value' time series expression (TSX) in variable 'Temperature'.",
        "target": "projectedVariables.Temperature.value",
        "innerError": {
            "parseErrorDetails": [
                {
                    "pos": [
                        0,
                        5
                    ],
                    "line": 1,
                    "msg": "Unsupported Time Series Expression version TSX01 used instead of TSX00.",
                    "code": "UnsupportedTSXVersionTSX01",
                    "target": "$event"
                }
            ],
            "code": "TsxParseError"
        }
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

- Testare l'ambiente tramite [esplora Azure Time Series Insights](./concepts-ux-panels.md) o tramite l'applicazione personalizzata.
