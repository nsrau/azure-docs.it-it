---
title: Tipo di entità di espressione regolare-LUIS
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
ms.openlocfilehash: ae46df875d588186cd083134820f349158d7e307
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695209"
---
# <a name="regular-expression-entity"></a>Entità di espressione regolare 

Un'entità di espressione regolare estrae un'entità in base a un modello di espressione regolare fornito dall'utente.

Un'espressione regolare è ideale per il testo di un'espressione non elaborata. Maiuscole e minuscole vengono ignorate così come la variante relativa alla lingua.  La corrispondenza tramite le espressioni regolari viene applicata dopo le modifiche del controllo ortografico a livello di carattere e non a livello di token. Se l'espressione regolare è troppo complessa, ad esempio con molte parentesi, non è possibile aggiungere l'espressione al modello. Usa una parte ma non tutte la libreria [Regex di .NET](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions) . 

**L'entità è una scelta ottimale nei casi seguenti:**

* I dati sono formattati in modo coerente con qualsiasi variazione altrettanto coerente.
* L'espressione regolare non richiede più di due livelli di annidamento. 

![Entità di espressione regolare](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>Considerazioni sull'utilizzo

Le espressioni regolari possono corrispondere a più di quanto previsto. Un esempio è la corrispondenza di parole numeriche, ad esempio `one` e `two`. Un esempio è l'espressione regolare seguente, che corrisponde al numero `one` insieme ad altri numeri:

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
``` 

Questa espressione Regex corrisponde anche a tutte le parole che terminano con questi numeri, ad esempio `phone`. Per risolvere problemi di questo tipo, assicurarsi che le corrispondenze Regex prendano in considerazione i limiti di parola. L'espressione regolare per utilizzare i limiti di parola per questo esempio viene utilizzata nell'espressione regolare seguente:

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>JSON di esempio

Quando si usa `kb[0-9]{6}`, come definizione di entità di espressioni regolari, la risposta JSON seguente è un enunciato di esempio con le entità di espressione regolare restituite per la query:

`When was kb123456 published?`:

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 risposta endpoint di stima](#tab/V2)

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


#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Risposta dell'endpoint di stima V3](#tab/V3)


Si tratta del codice JSON se `verbose=false` è impostato nella stringa di query:

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

Si tratta del codice JSON se `verbose=true` è impostato nella stringa di query:

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

In questa [esercitazione](luis-quickstart-intents-regex-entity.md)creare un'app per estrarre i dati in formato coerente da un enunciato usando l'entità di **espressioni regolari** .