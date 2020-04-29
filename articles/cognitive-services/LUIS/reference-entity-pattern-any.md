---
title: Modello. qualsiasi tipo di entità-LUIS
titleSuffix: Azure Cognitive Services
description: Pattern.any è un segnaposto di lunghezza variabile usato solo nell'espressione del modello del criterio per contrassegnare l'inizio e la fine dell'entità.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 5164bf55ef8233cf34a470524da3bc852678d79a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75979169"
---
# <a name="patternany-entity"></a>Entità pattern.any

Pattern.any è un segnaposto di lunghezza variabile usato solo nell'espressione del modello del criterio per contrassegnare l'inizio e la fine dell'entità.  

Le entità Pattern.any devono essere contrassegnate negli esempi di modello [Pattern](luis-how-to-model-intent-pattern.md), non negli esempi di finalità utente.

**Questa entità è idonea quando:**

* La fine dell'entità può essere confusa con il testo rimanente dell'espressione.

## <a name="usage"></a>Utilizzo

Data un'applicazione client che esegue la ricerca di libri in base al titolo, l'entità pattern.any estrae il titolo completo. Un'espressione di criterio con pattern.any per questa ricerca è `Was {BookTitle} written by an American this year[?]`.

Nella tabella seguente, ogni riga presenta due versioni dell'espressione. Il primo enunciato è il modo in cui LUIS considera inizialmente il enunciato. Non è chiaro dove inizia e termina il titolo del libro. Il enunciato inferiore usa un'entità pattern. Any per contrassegnare l'inizio e la fine dell'entità.

|Espressione con entità in grassetto|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>Was **The Man Who Mistook His Wife for a Hat and Other Clinical Tales** written by an American this year?|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>Era **mezza addormentata nei pigiami rana** scritti da un americano quest'anno?|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>Era **la tristezza di Lemon cake: un romanzo** scritto da un americano quest'anno?|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>**C'è un Wocket in tasca!** written by an American this year?|
||



## <a name="example-json"></a>JSON di esempio

Si consideri la query seguente:

`where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

Con il nome del form incorporato da estrarre come modello. Any:

`Understand your responsibilities as a member of the community`

#### <a name="v2-prediction-endpoint-response"></a>[Risposta dell'endpoint di previsione V2](#tab/V2)

```JSON
"entities": [
  {
    "entity": "understand your responsibilities as a member of the community",
    "type": "FormName",
    "startIndex": 18,
    "endIndex": 78,
    "role": ""
  }
```


#### <a name="v3-prediction-endpoint-response"></a>[Risposta dell'endpoint di previsione V3](#tab/V3)

Si tratta del codice JSON `verbose=false` se è impostato nella stringa di query:

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ]
}
```

Si tratta del codice JSON `verbose=true` se è impostato nella stringa di query:

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ],
    "$instance": {
        "FormName": [
            {
                "type": "FormName",
                "text": "Understand your responsibilities as a member of the community",
                "startIndex": 18,
                "length": 61,
                "modelTypeId": 7,
                "modelType": "Pattern.Any Entity Extractor",
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

In questa [esercitazione](luis-tutorial-pattern.md)usare l'entità **pattern. any** per estrarre i dati da espressioni in cui le espressioni sono ben formattate e dove la fine dei dati può essere confusa facilmente con le parole rimanenti dell'espressione.
