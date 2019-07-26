---
title: Tipo di entità di espressione regolare
titleSuffix: Language Understanding - Azure Cognitive Services
description: Un'espressione regolare è ideale per il testo di un'espressione non elaborata. Maiuscole e minuscole vengono ignorate così come la variante relativa alla lingua.  La corrispondenza tramite le espressioni regolari viene applicata dopo le modifiche del controllo ortografico a livello di carattere e non a livello di token.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: dfffea26ccd8dfcecea2c6a2e07234f3ab27f72b
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480231"
---
# <a name="regular-expression-entity"></a>Entità di espressione regolare 

Un'entità di espressione regolare estrae un'entità in base a un modello di espressione regolare fornito dall'utente.

Un'espressione regolare è ideale per il testo di un'espressione non elaborata. Maiuscole e minuscole vengono ignorate così come la variante relativa alla lingua.  La corrispondenza tramite le espressioni regolari viene applicata dopo le modifiche del controllo ortografico a livello di carattere e non a livello di token. Se l'espressione regolare è troppo complessa, ad esempio con molte parentesi, non è possibile aggiungere l'espressione al modello. Usa una parte ma non tutte la libreria [Regex di .NET](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions) . 

**L'entità è una scelta ottimale nei casi seguenti:**

* I dati sono formattati in modo coerente con qualsiasi variazione altrettanto coerente.
* L'espressione regolare non richiede più di due livelli di annidamento. 

![Entità di espressione regolare](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>Considerazioni sull'utilizzo

Le espressioni regolari possono corrispondere a più di quanto previsto. Un esempio è la corrispondenza numerica di parole, `one` ad `two`esempio e. Un esempio è l'espressione regolare seguente, che corrisponde al `one` numero insieme ad altri numeri:

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
``` 

Questa espressione Regex corrisponde anche a tutte le parole che terminano con questi numeri `phone`, ad esempio. Per risolvere problemi di questo tipo, assicurarsi che le corrispondenze Regex prendano in considerazione i limiti di parola. L'espressione regolare per utilizzare i limiti di parola per questo esempio viene utilizzata nell'espressione regolare seguente:

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>JSON di esempio

Quando si `kb[0-9]{6}`USA, come definizione di entità di espressioni regolari, la risposta JSON seguente è un enunciato di esempio con le entità di espressioni `When was kb123456 published?`regolari restituite per la query:

```JSON
{
  "query": "when was kb123456 published?",
  "topScoringIntent": {
    "intent": "FindKBArticle",
    "score": 0.933641255
  },
  "intents": [
    {
      "intent": "FindKBArticle",
      "score": 0.933641255
    },
    {
      "intent": "None",
      "score": 0.04397359
    }
  ],
  "entities": [
    {
      "entity": "kb123456",
      "type": "KB number",
      "startIndex": 9,
      "endIndex": 16
    }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi

In questa [esercitazione](luis-quickstart-intents-regex-entity.md)creare un'app per estrarre i dati in formato coerente da un enunciato usando l'entità di **espressioni regolari** .