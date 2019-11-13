---
title: Tipo di entità Machine-learned-LUIS
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 2289e8ac7744a7b4cbee300e77efda89d29ee2f5
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74017219"
---
# <a name="machine-learned-entity"></a>Entità Machine-Learned 



**L'entità è una scelta ottimale quando i dati di testo:**


![entità elenco](./media/luis-concept-entities/list-entity.png)

## <a name="example-json"></a>JSON di esempio

Si supponga che l'app disponga di un elenco, di nome `Cities`, consentendo variazioni di nomi di città, tra cui città dell'aeroporto (Sea-tac), codice dell'aeroporto (SEA), codice postale (98101) e prefisso telefonico (206).

|Elemento elenco|Sinonimi elenco|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

Nell'espressione precedente la parola `paris` viene mappata all'elemento parigi come parte dell'entità elenco `Cities`. L'entità elenco corrisponde al nome normalizzato dell'elemento e ai sinonimi dell'elemento.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Risposta dell'endpoint di previsione V2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Risposta dell'endpoint di previsione V3](#tab/V3)


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

|Oggetto dati|Nome dell'entità|Valore|
|--|--|--|
|Elenca entità|`Cities`|`paris`|


## <a name="next-steps"></a>Passaggi successivi

Informazioni sull'entità [List](reference-entity-list.md) e sull'entità di [espressioni regolari](reference-entity-regular-expression.md) .