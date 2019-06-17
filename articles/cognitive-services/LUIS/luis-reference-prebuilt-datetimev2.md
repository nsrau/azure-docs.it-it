---
title: Entità predefinite DatetimeV2
titleSuffix: Azure
description: Questo articolo contiene informazioni sull'entità predefinita datetimeV2 in Language Understanding, ovvero LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: e7577dcf4859b1192121fe0406d0efb63a9f5990
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65148645"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>Entità predefinita DatetimeV2 per un'app LUIS

L'entità predefinita **datetimeV2** estrae i valori di data e ora. Questi valori vengono risolti in un formato standardizzato per l'uso da parte dei programmi client. Quando un'espressione ha una data o un'ora non complete, LUIS include _i valori passati e futuri_ nella risposta dell'endpoint. Poiché è già stato eseguito il training per questa entità, non è necessario aggiungere espressioni di esempio contenenti datetimeV2 per le finalità dell'applicazione. 

## <a name="types-of-datetimev2"></a>Tipi di datetimeV2
L'entità DatetimeV2 viene gestita dal repository GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml)

## <a name="example-json"></a>JSON di esempio 
La risposta JSON di esempio seguente presenta un'entità `datetimeV2` con un sottotipo di `datetime`. Per esempi di altri tipi di entità datetimeV2, vedere [Sottotipi di datetimeV2](#subtypes-of-datetimev2)</a>.

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

## <a name="json-property-descriptions"></a>Descrizioni delle proprietà JSON

|Nome proprietà |Tipo e descrizione proprietà|
|---|---|
|Entità|**stringa** - Testo estratto dall'espressione con tipo di data, ora, intervallo di date o intervallo di tempo.|
|type|**string** - Uno dei [sottotipi di datetimeV2](#subtypes-of-datetimev2)
|startIndex|**int** - L'indice nell'espressione dove inizia l'entità.|
|endIndex|**int** - L'indice nell'espressione dove termina l'entità.|
|resolution|Dispone di una matrice `values` con uno, due o quattro [valori di risoluzione](#values-of-resolution).|
|end|Il valore finale di un intervallo di tempo o date, nello stesso formato di `value`. Usato solo se `type` è `daterange`, `timerange`, o `datetimerange`|

## <a name="subtypes-of-datetimev2"></a>Sottotipi di datetimeV2

L'entità predefinita **datetimeV2** include i sottotipi seguenti. Nella tabella di seguito vengono forniti esempi di ciascun sottotipo:
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`
* `duration`
* `set`

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
|type|Il sottotipo, che può essere uno dei seguenti elementi: `datetime`, `date`, `time`, `daterange`, `timerange`, `datetimerange`, `duration`, `set`.|
|value|**Facoltativo.** Un oggetto datetime nel formato yyyy:MM:dd  (date), HH:mm:ss (time) yyyy:MM:dd HH:mm:ss (datetime). Se `type` è `duration`, il valore è il numero di secondi (durata) <br/> Usato solo se `type` è `datetime` oppure `date`, `time` o `duration.|

## <a name="valid-date-values"></a>Valori di daae validi

**datetimeV2** supporta date comprese tra gli intervalli seguenti:

| Min | Max |
|----------|-------------|
| 1 gennaio 1900   | 31 dicembre 2099 |

## <a name="ambiguous-dates"></a>Date ambigue

Se la data può essere nel passato o nel futuro, LUIS fornisce entrambi i valori. Un esempio è un'espressione che include il mese e la data senza l'anno.  

Ad esempio, data l'espressione "2 maggio":
* Se la data corrente è il 3 maggio 2017, LUIS indica sia "2017-05-02" sia "2018-05-02" come valori. 
* Se la data corrente è il 1° maggio 2017, LUIS indica sia "2016-05-02" sia "2017-05-02" come valori.

L'esempio seguente illustra la risoluzione dell'entità "may 2nd". Questa risoluzione presuppone che la data corrente sia compresa tra il 2 maggio 2017 e il 1° maggio 2018.
I campi con `X` nel campo `timex` sono parti della data non specificati in modo esplicito nell'espressione.

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

## <a name="date-range-resolution-examples-for-numeric-date"></a>Esempi di risoluzione di un intervallo di date per data numerica

L'entità `datetimeV2` estrae gli intervalli di data e tempo. I campi `start` e `end` specificano l'inizio e la fine dell'intervallo. Per l'espressione "Dal 2 maggio a l 5 maggio", LUIS fornisce valori **daterange** per l'anno corrente e l'anno successivo. Nel campo `timex`, i valori `XXXX` indicano l'ambiguità dell'anno. `P3D` indica che il periodo di tempo è di tre giorni.

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

## <a name="date-range-resolution-examples-for-day-of-week"></a>Esempi di risoluzione di un intervallo di date per giorno della settimana

L'esempio seguente illustra come LUIS usa **datetimeV2** per risolvere l'espressione "Da martedì a giovedì". In questo esempio la data corrente è il 19 giugno. LUIS include valori **daterange** per entrambi gli intervalli di date che precedono e seguono la data corrente.

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
## <a name="ambiguous-time"></a>Ora ambigua
La matrice di valori ha due elementi ora se l'ora o l'intervallo di tempo sono ambigui. In caso sia presente un'ora ambigua, vengono indicati sia il valore antemeridiano sia quello postmeridiano.

## <a name="time-range-resolution-example"></a>Esempio di risoluzione di un intervallo di tempo

L'esempio seguente illustra come LUIS usa **datetimeV2** per risolvere l'espressione con un intervallo di tempo.

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

## <a name="preview-api-version-3x"></a>Versione di anteprima API 3.x

Risposta JSON DatetimeV2 ha subito l'API di V3. 

Modifiche dalla versione 2 di API:
* `datetimeV2.timex.type` proprietà non viene più restituita perché ciò avviene a livello di elemento padre, `datetimev2.type`. 
* Il `datetimeV2.timex` proprietà è stata rinominata in `datetimeV2.value`.

Per il utterance `8am on may 2nd 2017`, la versione V3 di DatetimeV2 è:

```JSON
{
    "query": "8am on may 2nd 2017",
    "prediction": {
        "normalizedQuery": "8am on may 2nd 2017",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.6826963
            }
        },
        "entities": {
            "datetimeV2": [
                {
                    "type": "datetime",
                    "values": [
                        {
                            "timex": "2017-05-02T08",
                            "value": "2017-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    }
}
```

Il codice JSON seguente è con il `verbose` parametro è impostato su `false`:

```json
{
    "query": "8am on may 2nd 2017",
    "prediction": {
        "normalizedQuery": "8am on may 2nd 2017",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.6826963
            }
        },
        "entities": {
            "datetimeV2": [
                {
                    "type": "datetime",
                    "values": [
                        {
                            "timex": "2017-05-02T08",
                            "value": "2017-05-02 08:00:00"
                        }
                    ]
                }
            ],
            "$instance": {
                "datetimeV2": [
                    {
                        "type": "builtin.datetimeV2.datetime",
                        "text": "8am on may 2nd 2017",
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
    }
}
```

## <a name="deprecated-prebuilt-datetime"></a>Entità datetime predefinita deprecata

L'entità `datetime` predefinita è deprecata e sostituita da **datetimeV2**. 

Per sostituire `datetime` con `datetimeV2` nell'app LUIS, completare i passaggi seguenti:

1. Aprire il riquadro **Entità** dell'interfaccia web LUIS. 
2. Eliminare l'entità **datetime** predefinita.
3. Fare clic su **Aggiungi un'entità predefinita**
4. Selezionare **datetimeV2** e fare clic su **Salva**.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle entità [dimensioni](luis-reference-prebuilt-dimension.md), [posta elettronica](luis-reference-prebuilt-email.md) e [numero](luis-reference-prebuilt-number.md). 

