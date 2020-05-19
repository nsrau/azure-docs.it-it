---
title: Tipo di entità di espressione regolare-LUIS
description: Un'espressione regolare è ideale per il testo di un'espressione non elaborata. Maiuscole e minuscole vengono ignorate così come la variante relativa alla lingua.  La corrispondenza tramite le espressioni regolari viene applicata dopo le modifiche del controllo ortografico a livello di carattere e non a livello di token.
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 90260fca10fc087225f6b1286e9fa2dd6d17c836
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83585607"
---
# <a name="regular-expression-entity"></a>Entità di espressione regolare

Un'entità di espressione regolare estrae un'entità in base a un modello di espressione regolare fornito dall'utente.

Un'espressione regolare è ideale per il testo di un'espressione non elaborata. Maiuscole e minuscole vengono ignorate così come la variante relativa alla lingua.  La corrispondenza tramite le espressioni regolari viene applicata dopo le modifiche del controllo ortografico a livello di carattere e non a livello di token. Se l'espressione regolare è troppo complessa, ad esempio con molte parentesi, non è possibile aggiungere l'espressione al modello. Usa una parte ma non tutte la libreria [Regex di .NET](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions) .

**Questa entità è idonea quando:**

* I dati sono formattati in modo coerente con qualsiasi variazione altrettanto coerente.
* L'espressione regolare non richiede più di due livelli di annidamento.

![Entità di espressione regolare](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>Considerazioni sull'utilizzo

Le espressioni regolari possono corrispondere a più di quanto previsto. Un esempio è la corrispondenza numerica di parole, ad esempio `one` e `two` . Un esempio è l'espressione regolare seguente, che corrisponde al numero `one` insieme ad altri numeri:

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
```

Questa espressione Regex corrisponde anche a tutte le parole che terminano con questi numeri, ad esempio `phone` . Per risolvere problemi di questo tipo, assicurarsi che le corrispondenze Regex prendano in considerazione i limiti di parola. L'espressione regolare per utilizzare i limiti di parola per questo esempio viene utilizzata nell'espressione regolare seguente:

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>JSON di esempio

Quando `kb[0-9]{6}` si usa, come definizione di entità di espressioni regolari, la risposta JSON seguente è un enunciato di esempio con le entità di espressioni regolari restituite per la query:

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

Altre informazioni sulle entità:

* [Concetti](luis-concept-entity-types.md)
* [Modalità di creazione](luis-how-to-add-entities.md)