---
title: Elencare il tipo di entità-LUIS
titleSuffix: Azure Cognitive Services
description: Le entità elenco rappresentano un set chiuso e fisso di parole correlate insieme ai relativi sinonimi. LUIS non individua valori aggiuntivi per le entità elenco. Usare la funzionalità consigli per visualizzare i suggerimenti per le nuove parole in base all'elenco corrente.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 1757faf8ab2be0b62956b6939ee068929f9275a4
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695246"
---
# <a name="list-entity"></a>Entità elenco 

Le entità elenco rappresentano un set chiuso e fisso di parole correlate insieme ai relativi sinonimi. LUIS non individua valori aggiuntivi per le entità elenco. Usare la funzione **consigliata** per visualizzare i suggerimenti per le nuove parole in base all'elenco corrente. Se sono presenti più entità elenco con lo stesso valore, ogni entità viene restituita nella query endpoint. 

Un'entità elenco non è stata acquisita dal computer. Si tratta di una corrispondenza di testo esatta. LUIS contrassegna eventuali corrispondenze a un elemento in qualsiasi elenco come un'entità nella risposta. 

**L'entità è una scelta ottimale quando i dati di testo:**

* Sono un set noto.
* Non cambia spesso. Se è necessario modificare spesso l'elenco o si desidera che l'elenco si espanda in modo automatico, è preferibile usare un'entità semplice con boosting con un elenco di frasi. 
* Il set non supera i [limiti](luis-boundaries.md) massimi di LUIS per questo tipo di entità.
* Il testo nell'espressione è una corrispondenza esatta con un sinonimo o il nome canonico. LUIS non usa l'elenco di là delle corrispondenze esatte del testo. Le corrispondenze fuzzy, senza distinzione tra maiuscole e minuscole, stemming, plurali e altre varianti non vengono risolte con un'entità List. Per gestire le variazioni, è consigliabile usare un [criterio](luis-concept-patterns.md#syntax-to-mark-optional-text-in-a-template-utterance) con la sintassi del testo facoltativo.

![entità elenco](./media/luis-concept-entities/list-entity.png)

## <a name="example-json"></a>JSON di esempio

Si supponga che l'app disponga di un elenco, di nome `Cities`, consentendo variazioni di nomi di città, tra cui città dell'aeroporto (Sea-tac), codice dell'aeroporto (SEA), codice postale (98101) e prefisso telefonico (206).

|Elemento elenco|Sinonimi elenco|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

Nell'espressione precedente la parola `paris` viene mappata all'elemento parigi come parte dell'entità elenco `Cities`. L'entità elenco corrisponde al nome normalizzato dell'elemento e ai sinonimi dell'elemento.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 risposta endpoint di stima](#tab/V2)

```JSON
  "entities": [
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 18,
      "endIndex": 22,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    }
  ]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Risposta dell'endpoint di stima V3](#tab/V3)


Si tratta del codice JSON se `verbose=false` è impostato nella stringa di query:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ]
}
```

Si tratta del codice JSON se `verbose=true` è impostato nella stringa di query:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ],
    "$instance": {
        "Cities": [
            {
                "type": "Cities",
                "text": "paris",
                "startIndex": 18,
                "length": 5,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * * 

|Oggetto dati|Nome dell'entità|Value|
|--|--|--|
|Elenca entità|`Cities`|`paris`|


## <a name="next-steps"></a>Passaggi successivi

In questa [esercitazione](luis-quickstart-intent-and-list-entity.md)si apprenderà come usare un' **entità list** per estrarre corrispondenze esatte del testo da un elenco di elementi noti. 
