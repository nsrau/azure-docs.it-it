---
title: Numero di entità predefinite-LUIS
titleSuffix: Azure Cognitive Services
description: Questo articolo contiene informazioni sull'entità predefinita numero in Language Understanding, ovvero LUIS.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: 13594886b83d4474ee2531185db5868a5198ca64
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541960"
---
# <a name="number-prebuilt-entity-for-a-luis-app"></a>Entità predefinita numero per un'app LUIS
I modi in cui i valori numerici vengono usati per quantificare, esprimere e descrivere i tipi di informazioni sono diversi. Questo articolo illustra solo alcuni degli esempi possibili. LUIS interpreta le variazioni nelle espressioni utente e restituisce valori numerici coerenti. Poiché è già stato eseguito il training per questa entità, non è necessario aggiungere espressioni di esempio contenenti il numero per le finalità dell'applicazione.

## <a name="types-of-number"></a>Tipi di numero
Il numero è gestito dal repository GitHub del [testo dei riconoscitori](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml)

## <a name="examples-of-number-resolution"></a>Esempi di risoluzione dei numeri

| Espressione        | Entità   | Soluzione |
| ------------- |:----------------:| --------------:|
| ```one thousand times```  | ```"one thousand"``` |   ```"1000"```      |
| ```1,000 people```        | ```"1,000"```    |   ```"1000"```      |
| ```1/2 cup```         | ```"1 / 2"```    |    ```"0.5"```      |
|  ```one half the amount```     | ```"one half"```     |    ```"0.5"```      |
| ```one hundred fifty orders``` | ```"one hundred fifty"``` | ```"150"``` |
| ```one hundred and fifty books``` | ```"one hundred and fifty"``` | ```"150"```|
| ```a grade of one point five```| ```"one point five"``` |  ```"1.5"``` |
| ```buy two dozen eggs```    | ```"two dozen"``` | ```"24"``` |


LUIS include il valore riconosciuto di un' **`builtin.number`** entità nel `resolution` campo della risposta JSON restituita.

## <a name="resolution-for-prebuilt-number"></a>Risoluzione per il numero predefinito

Per la query vengono restituiti gli oggetti entità seguenti:

`order two dozen eggs`

#### <a name="v3-response"></a>[Risposta V3](#tab/V3)

Il codice JSON seguente è con il `verbose` parametro impostato su `false` :

```json
"entities": {
    "number": [
        24
    ]
}
```
#### <a name="v3-verbose-response"></a>[Risposta dettagliata V3](#tab/V3-verbose)

Il codice JSON seguente è con il `verbose` parametro impostato su `true` :

```json
"entities": {
    "number": [
        24
    ],
    "$instance": {
        "number": [
            {
                "type": "builtin.number",
                "text": "two dozen",
                "startIndex": 6,
                "length": 9,
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
#### <a name="v2-response"></a>[Risposta V2](#tab/V2)

L'esempio seguente mostra una risposta JSON di LUIS, che include la risoluzione del valore 24, per l'espressione "due dozzine".

```json
"entities": [
  {
    "entity": "two dozen",
    "type": "builtin.number",
    "startIndex": 6,
    "endIndex": 14,
    "resolution": {
      "subtype": "integer",
      "value": "24"
    }
  }
]
```
* * *

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'[endpoint di previsione V3](luis-migration-api-v3.md).

Informazioni su [valuta](luis-reference-prebuilt-currency.md), [ordinale](luis-reference-prebuilt-ordinal.md) e [percentuale](luis-reference-prebuilt-percentage.md).
