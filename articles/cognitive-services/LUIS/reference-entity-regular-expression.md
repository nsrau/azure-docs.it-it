---
title: Tipo di entità dell'espressione regolare - LUISRegular expression entity type - LUIS
titleSuffix: Azure Cognitive Services
description: Un'espressione regolare è ideale per il testo di un'espressione non elaborata. Maiuscole e minuscole vengono ignorate così come la variante relativa alla lingua.  La corrispondenza tramite le espressioni regolari viene applicata dopo le modifiche del controllo ortografico a livello di carattere e non a livello di token.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: b9da76a80183f353a74d43e667bf6c9219eb6c05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74841218"
---
# <a name="regular-expression-entity"></a>Entità di espressione regolare

Un'entità di espressione regolare estrae un'entità in base a un modello di espressione regolare fornito.

Un'espressione regolare è ideale per il testo di un'espressione non elaborata. Maiuscole e minuscole vengono ignorate così come la variante relativa alla lingua.  La corrispondenza tramite le espressioni regolari viene applicata dopo le modifiche del controllo ortografico a livello di carattere e non a livello di token. Se l'espressione regolare è troppo complessa, ad esempio con molte parentesi, non è possibile aggiungere l'espressione al modello. Utilizza parte ma non tutta la libreria [.NET Regex.](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions)

**Questa entità è idonea quando:**

* I dati sono formattati in modo coerente con qualsiasi variazione altrettanto coerente.
* L'espressione regolare non richiede più di due livelli di annidamento.

![Entità di espressione regolare](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>Considerazioni sull'utilizzo

Le espressioni regolari possono corrispondere a più di quanto previsto corrisponda. Un esempio è la corrispondenza `one` `two`numerica delle parole, ad esempio e . Un esempio è la seguente espressione `one` regolare, che corrisponde al numero insieme ad altri numeri:

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
```

Questa espressione regex trova anche corrispondenza con `phone`tutte le parole che terminano con questi numeri, ad esempio . Per risolvere problemi come questo, assicurati che le corrispondenze regex prendano in considerazione i limiti delle parole. L'espressione regolare per utilizzare i limiti delle parole per questo esempio viene utilizzata nell'espressione regolare seguente:The regex to use word boundaries for this example is used in the following regex:

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>JSON di esempio

Quando `kb[0-9]{6}`si usa , come definizione di entità dell'espressione regolare, la risposta JSON seguente è un'espressione di esempio con le entità di espressione regolare restituite per la query:

`When was kb123456 published?`:

#### <a name="v2-prediction-endpoint-response"></a>[Risposta dell'endpoint di previsione V2](#tab/V2)

```JSON
"entities": [
  {
    "entity": "kb123456",
    "type": "KB number",
    "startIndex": 9,
    "endIndex": 16
  }
]
```


#### <a name="v3-prediction-endpoint-response"></a>[Risposta dell'endpoint di previsione V3](#tab/V3)


Questo è il `verbose=false` codice JSON se è impostato nella stringa di query:This is the JSON if is set in the query string:

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

Questo è il `verbose=true` codice JSON se è impostato nella stringa di query:This is the JSON if is set in the query string:

```json
"entities": {
    "KB number": [
        "kb123456"
    ],
    "$instance": {
        "KB number": [
            {
                "type": "KB number",
                "text": "kb123456",
                "startIndex": 9,
                "length": 8,
                "modelTypeId": 8,
                "modelType": "Regex Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

## <a name="next-steps"></a>Passaggi successivi

In questa [esercitazione](tutorial-regex-entity.md)creare un'app per estrarre dati formattati in modo coerente da un'espressione usando l'entità **Espressione regolare.**