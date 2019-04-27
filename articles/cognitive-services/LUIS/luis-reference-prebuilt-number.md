---
title: Entità predefinita numero
titleSuffix: Azure
description: Questo articolo contiene informazioni sull'entità predefinita numero in Language Understanding, ovvero LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 83f7cc7c0da2682244fa9c4e0e2b153aff2e2380
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473213"
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
L'esempio seguente mostra una risposta JSON di LUIS, che include la risoluzione del valore 24, per l'espressione "due dozzine".

```json
{
  "query": "order two dozen eggs",
  "topScoringIntent": {
    "intent": "OrderFood",
    "score": 0.105443209
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.105443209
    },
    {
      "intent": "OrderFood",
      "score": 0.9468431361
    },
    {
      "intent": "Help",
      "score": 0.000399122015
    },
  ],
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
}
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su [valuta](luis-reference-prebuilt-currency.md), [ordinale](luis-reference-prebuilt-ordinal.md) e [percentuale](luis-reference-prebuilt-percentage.md). 
