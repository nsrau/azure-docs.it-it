---
title: Entità predefinite di DatetimeV2-LUIS
titleSuffix: Azure Cognitive Services
description: Questo articolo contiene informazioni sull'entità predefinita datetimeV2 in Language Understanding, ovvero LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 4c16953d3c708516edbe0b3c13b091dc3181b187
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465070"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>Entità predefinita DatetimeV2 per un'app LUIS

L'entità predefinita **datetimeV2** estrae i valori di data e ora. Questi valori vengono risolti in un formato standardizzato per l'uso da parte dei programmi client. Quando un'espressione ha una data o un'ora non complete, LUIS include _i valori passati e futuri_ nella risposta dell'endpoint. Poiché è già stato eseguito il training per questa entità, non è necessario aggiungere espressioni di esempio contenenti datetimeV2 per le finalità dell'applicazione. 

## <a name="types-of-datetimev2"></a>Tipi di datetimeV2
DatetimeV2 è gestito dal repository GitHub del [testo dei riconoscitori](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml) .

## <a name="example-json"></a>JSON di esempio 

Di seguito è riportato il seguente enunciato e la relativa risposta JSON parziale.

`8am on may 2nd 2019`

#### <a name="v3-responsetab1-1"></a>[Risposta V3](#tab/1-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "datetime",
            "values": [
                {
                    "timex": "2019-05-02T08",
                    "resolution": [
                        {
                            "value": "2019-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-responsetab1-2"></a>[Risposta dettagliata V3](#tab/1-2)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "datetime",
            "values": [
                {
                    "timex": "2019-05-02T08",
                    "resolution": [
                        {
                            "value": "2019-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.datetime",
                "text": "8am on may 2nd 2019",
                "startIndex": 0,
                "length": 19,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-responsetab1-3"></a>[Risposta V2](#tab/1-3)

```json
"entities": [
  {
    "entity": "8am on may 2nd 2019",
    "type": "builtin.datetimeV2.datetime",
    "startIndex": 0,
    "endIndex": 18,
    "resolution": {
      "values": [
        {
          "timex": "2019-05-02T08",
          "type": "datetime",
          "value": "2019-05-02 08:00:00"
        }
      ]
    }
  }
]
 ```

|Nome proprietà |Tipo e descrizione proprietà|
|---|---|
|Persona giuridica|**stringa** - Testo estratto dall'espressione con tipo di data, ora, intervallo di date o intervallo di tempo.|
|type|**string** - Uno dei [sottotipi di datetimeV2](#subtypes-of-datetimev2)
|startIndex|**int** - L'indice nell'espressione dove inizia l'entità.|
|endIndex|**int** - L'indice nell'espressione dove termina l'entità.|
|resolution|Dispone di una matrice `values` con uno, due o quattro [valori di risoluzione](#values-of-resolution).|
|end|Il valore finale di un intervallo di tempo o date, nello stesso formato di `value`. Usato solo se `type` è `daterange`, `timerange`, o `datetimerange`|

* * * 

## <a name="subtypes-of-datetimev2"></a>Sottotipi di datetimeV2

L'entità predefinita **datetimeV2** include i sottotipi seguenti. Nella tabella di seguito vengono forniti esempi di ciascun sottotipo:
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`


## <a name="values-of-resolution"></a>Valori di risoluzione
* La matrice contiene un elemento se la data o l'ora dell'espressione sono specificate completamente e prive di ambiguità.
* La matrice contiene due elementi se il valore datetimeV2 è ambiguo. Per ambiguità si intendono anche un anno, un'ora o un intervallo di tempo specifici mancanti. Vedere [Date ambigue](#ambiguous-dates) per alcuni esempi. Se l'ora non specifica il valore antemeridiano o postmeridiano, vengono inclusi entrambi i valori.
* La matrice contiene quattro elementi, se l'espressione ha due elementi che presentano ambiguità. Per ambiguità si intendono anche elementi che hanno:
  * Una data o un intervallo di date ambigui per quanto riguarda l'anno
  * Un'ora o un intervallo di tempo ambigui per quanto riguarda il valore antemeridiano o postmeridiano. Ad esempio, le 3:00 del 3 aprile, in quanto il sistema orario statunitense arriva a 12 ore.

Ogni elemento della matrice `values` può contenere i campi seguenti: 

|Nome proprietà|Descrizione proprietà|
|--|--|
|timex|Ora, data o intervallo di date espressi nel formato TIMEX che segue lo [standard ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e gli attributi TIMEX3 per l'annotazione con il linguaggio TimeML. Questa annotazione è descritta nelle [linee guida TIMEX](http://www.timeml.org/tempeval2/tempeval2-trial/guidelines/timex3guidelines-072009.pdf).|
|type|Sottotipo, che può essere uno degli elementi seguenti: `datetime`, `date`, `time`, `daterange`, `timerange`, `datetimerange`, `duration``set`.|
|value|**Facoltativo.** Un oggetto datetime nel formato yyyy:MM:dd  (date), HH:mm:ss (time) yyyy:MM:dd HH:mm:ss (datetime). Se `type` è `duration`, il valore è il numero di secondi (durata) <br/> Usato solo se `type` è `datetime` oppure `date`, `time` o `duration.|

## <a name="valid-date-values"></a>Valori di daae validi

**datetimeV2** supporta date comprese tra gli intervalli seguenti:

| Min | Max |
|----------|-------------|
| 1 gennaio 1900   | 31 dicembre 2099 |

## <a name="ambiguous-dates"></a>Date ambigue

Se la data può essere nel passato o nel futuro, LUIS fornisce entrambi i valori. Un esempio è un'espressione che include il mese e la data senza l'anno.  

Ad esempio, data la seguente espressione:

`May 2nd`

* Se la data corrente è il 3 maggio 2017, LUIS indica sia "2017-05-02" sia "2018-05-02" come valori. 
* Se la data corrente è il 1° maggio 2017, LUIS indica sia "2016-05-02" sia "2017-05-02" come valori.

L'esempio seguente illustra la risoluzione dell'entità "may 2nd". Questa risoluzione presuppone che la data corrente sia compresa tra il 2 maggio 2017 e il 1° maggio 2018.
I campi con `X` nel campo `timex` sono parti della data non specificati in modo esplicito nell'espressione.

## <a name="date-resolution-example"></a>Esempio di risoluzione della data


Di seguito è riportato il seguente enunciato e la relativa risposta JSON parziale.

`May 2nd`

#### <a name="v3-responsetab2-1"></a>[Risposta V3](#tab/2-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-05-02",
                    "resolution": [
                        {
                            "value": "2019-05-02"
                        },
                        {
                            "value": "2020-05-02"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-responsetab2-2"></a>[Risposta dettagliata V3](#tab/2-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-05-02",
                    "resolution": [
                        {
                            "value": "2019-05-02"
                        },
                        {
                            "value": "2020-05-02"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "May 2nd",
                "startIndex": 0,
                "length": 7,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-responsetab2-3"></a>[Risposta V2](#tab/2-3)

```json
  "entities": [
    {
      "entity": "may 2nd",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2019-05-02"
          },
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2020-05-02"
          }
        ]
      }
    }
  ]
```
* * * 

## <a name="date-range-resolution-examples-for-numeric-date"></a>Esempi di risoluzione di un intervallo di date per data numerica

L'entità `datetimeV2` estrae gli intervalli di data e tempo. I campi `start` e `end` specificano l'inizio e la fine dell'intervallo. Per il `May 2nd to May 5th`enunciato, LUIS fornisce valori **DateRange** per l'anno corrente e per l'anno successivo. Nel campo `timex`, i valori `XXXX` indicano l'ambiguità dell'anno. `P3D` indica che il periodo di tempo è di tre giorni.

Di seguito è riportato il seguente enunciato e la relativa risposta JSON parziale.

`May 2nd to May 5th`

#### <a name="v3-responsetab3-1"></a>[Risposta V3](#tab/3-1)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
                    "resolution": [
                        {
                            "start": "2019-05-02",
                            "end": "2019-05-05"
                        },
                        {
                            "start": "2020-05-02",
                            "end": "2020-05-05"
                        }
                    ]
                }
            ]
        }
    ]
}
```


#### <a name="v3-verbose-responsetab3-2"></a>[Risposta dettagliata V3](#tab/3-2)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
                    "resolution": [
                        {
                            "start": "2019-05-02",
                            "end": "2019-05-05"
                        },
                        {
                            "start": "2020-05-02",
                            "end": "2020-05-05"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.daterange",
                "text": "May 2nd to May 5th",
                "startIndex": 0,
                "length": 18,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-responsetab3-3"></a>[Risposta V2](#tab/3-3)

```json
"entities": [
    {
      "entity": "may 2nd to may 5th",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 17,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2019-05-02",
            "end": "2019-05-05"
          }
        ]
      }
    }
  ]
```
* * * 

## <a name="date-range-resolution-examples-for-day-of-week"></a>Esempi di risoluzione di un intervallo di date per giorno della settimana

Nell'esempio seguente viene illustrato il modo in cui LUIS USA **datetimeV2** per risolvere l'espressione `Tuesday to Thursday`. In questo esempio la data corrente è il 19 giugno. LUIS include valori **daterange** per entrambi gli intervalli di date che precedono e seguono la data corrente.

Di seguito è riportato il seguente enunciato e la relativa risposta JSON parziale.

`Tuesday to Thursday`

#### <a name="v3-responsetab4-1"></a>[Risposta V3](#tab/4-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
                    "resolution": [
                        {
                            "start": "2019-10-08",
                            "end": "2019-10-10"
                        },
                        {
                            "start": "2019-10-15",
                            "end": "2019-10-17"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-responsetab4-2"></a>[Risposta dettagliata V3](#tab/4-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
                    "resolution": [
                        {
                            "start": "2019-10-08",
                            "end": "2019-10-10"
                        },
                        {
                            "start": "2019-10-15",
                            "end": "2019-10-17"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.daterange",
                "text": "Tuesday to Thursday",
                "startIndex": 0,
                "length": 19,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-responsetab4-3"></a>[Risposta V2](#tab/4-3)

```json
  "entities": [
    {
      "entity": "tuesday to thursday",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 19,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
            "type": "daterange",
            "start": "2019-04-30",
            "end": "2019-05-02"
          }
        ]
      }
    }
  ]
```
* * * 

## <a name="ambiguous-time"></a>Ora ambigua
La matrice di valori ha due elementi ora se l'ora o l'intervallo di tempo sono ambigui. In caso sia presente un'ora ambigua, vengono indicati sia il valore antemeridiano sia quello postmeridiano.

## <a name="time-range-resolution-example"></a>Esempio di risoluzione di un intervallo di tempo

La risposta JSON DatetimeV2 è cambiata nell'API V3. L'esempio seguente illustra come LUIS usa **datetimeV2** per risolvere l'espressione con un intervallo di tempo.

Modifiche dall'API v2:
* `datetimeV2.timex.type` proprietà non viene più restituita perché viene restituita a livello padre, `datetimev2.type`. 
* La proprietà `datetimeV2.value` è stata rinominata `datetimeV2.timex`.

Di seguito è riportato il seguente enunciato e la relativa risposta JSON parziale.

`from 6pm to 7pm`

#### <a name="v3-responsetab5-1"></a>[Risposta V3](#tab/5-1)

Il codice JSON seguente è con il parametro `verbose` impostato su `false`:

```JSON

"entities": {
    "datetimeV2": [
        {
            "type": "timerange",
            "values": [
                {
                    "timex": "(T18,T19,PT1H)",
                    "resolution": [
                        {
                            "start": "18:00:00",
                            "end": "19:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```
#### <a name="v3-verbose-responsetab5-2"></a>[Risposta dettagliata V3](#tab/5-2)

Il codice JSON seguente è con il parametro `verbose` impostato su `true`:

```json

"entities": {
    "datetimeV2": [
        {
            "type": "timerange",
            "values": [
                {
                    "timex": "(T18,T19,PT1H)",
                    "resolution": [
                        {
                            "start": "18:00:00",
                            "end": "19:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.timerange",
                "text": "from 6pm to 7pm",
                "startIndex": 0,
                "length": 15,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-responsetab5-3"></a>[Risposta V2](#tab/5-3)

```json
  "entities": [
    {
      "entity": "6pm to 7pm",
      "type": "builtin.datetimeV2.timerange",
      "startIndex": 0,
      "endIndex": 9,
      "resolution": {
        "values": [
          {
            "timex": "(T18,T19,PT1H)",
            "type": "timerange",
            "start": "18:00:00",
            "end": "19:00:00"
          }
        ]
      }
    }
  ]
```

* * * 

## <a name="time-resolution-example"></a>Esempio di risoluzione temporale

Di seguito è riportato il seguente enunciato e la relativa risposta JSON parziale.

`8am`

#### <a name="v3-responsetab6-1"></a>[Risposta V3](#tab/6-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "time",
            "values": [
                {
                    "timex": "T08",
                    "resolution": [
                        {
                            "value": "08:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```
#### <a name="v3-verbose-responsetab6-2"></a>[Risposta dettagliata V3](#tab/6-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "time",
            "values": [
                {
                    "timex": "T08",
                    "resolution": [
                        {
                            "value": "08:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.time",
                "text": "8am",
                "startIndex": 0,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-responsetab6-3"></a>[Risposta V2](#tab/6-3)

```json
"entities": [
  {
    "entity": "8am",
    "type": "builtin.datetimeV2.time",
    "startIndex": 0,
    "endIndex": 2,
    "resolution": {
      "values": [
        {
          "timex": "T08",
          "type": "time",
          "value": "08:00:00"
        }
      ]
    }
  }
]
```

* * * 

## <a name="deprecated-prebuilt-datetime"></a>Entità datetime predefinita deprecata

L'entità `datetime` predefinita è deprecata e sostituita da **datetimeV2**. 

Per sostituire `datetime` con `datetimeV2` nell'app LUIS, completare i passaggi seguenti:

1. Aprire il riquadro **Entità** dell'interfaccia web LUIS. 
2. Eliminare l'entità **datetime** predefinita.
3. Fare clic su **Aggiungi un'entità predefinita**
4. Selezionare **datetimeV2** e fare clic su **Salva**.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'[endpoint di previsione V3](luis-migration-api-v3.md).

Informazioni sulle entità [dimensioni](luis-reference-prebuilt-dimension.md), [posta elettronica](luis-reference-prebuilt-email.md) e [numero](luis-reference-prebuilt-number.md). 

