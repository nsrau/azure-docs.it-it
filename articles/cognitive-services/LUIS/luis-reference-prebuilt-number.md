---
title: Numero di entità predefinite-LUIS
titleSuffix: Azure Cognitive Services
description: Questo articolo contiene informazioni sull'entità predefinita numero in Language Understanding, ovvero LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 6a59cf83b3912e31b8aae67319902ce516519af8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491294"
---
# <a name="number-prebuilt-entity-for-a-luis-app"></a>Entità predefinita numero per un'app LUIS
I modi in cui i valori numerici vengono usati per quantificare, esprimere e descrivere i tipi di informazioni sono diversi. Questo articolo illustra solo alcuni degli esempi possibili. LUIS interpreta le variazioni nelle espressioni utente e restituisce valori numerici coerenti. Poiché è già stato eseguito il training per questa entità, non è necessario aggiungere espressioni di esempio contenenti il numero per le finalità dell'applicazione. 

## <a name="types-of-number"></a>Tipi di numero
Il numero viene gestito dal repository GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml)

## <a name="examples-of-number-resolution"></a>Esempi di risoluzione dei numeri

| Espressione        | Entità   | Risoluzione |
| ------------- |:----------------:| --------------:|
| ```one thousand times```  | ```"one thousand"``` |   ```"1000"```      | 
| ```1,000 people```        | ```"1,000"```    |   ```"1000"```      |
| ```1/2 cup```         | ```"1 / 2"```    |    ```"0.5"```      |
|  ```one half the amount```     | ```"one half"```     |    ```"0.5"```      |
| ```one hundred fifty orders``` | ```"one hundred fifty"``` | ```"150"``` |
| ```one hundred and fifty books``` | ```"one hundred and fifty"``` | ```"150"```|
| ```a grade of one point five```| ```"one point five"``` |  ```"1.5"``` |
| ```buy two dozen eggs```    | ```"two dozen"``` | ```"24"``` |


LUIS include il valore riconosciuto di un'entità **`builtin.number`** nel campo `resolution` della risposta JSON restituita.

## <a name="resolution-for-prebuilt-number"></a>Risoluzione per il numero predefinito

Per la query vengono restituiti gli oggetti entità seguenti:

`order two dozen eggs`

#### <a name="v3-responsetabv3"></a>[Risposta V3](#tab/V3)

Il codice JSON seguente è con il parametro `verbose` impostato su `false`:

```json
"entities": {
    "number": [
        24
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[Risposta dettagliata V3](#tab/V3-verbose)

Il codice JSON seguente è con il parametro `verbose` impostato su `true`:

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
#### <a name="v2-responsetabv2"></a>[Risposta V2](#tab/V2)

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

Altre informazioni sull' [endpoint di stima V3](luis-migration-api-v3.md).

Informazioni su [valuta](luis-reference-prebuilt-currency.md), [ordinale](luis-reference-prebuilt-ordinal.md) e [percentuale](luis-reference-prebuilt-percentage.md). 
