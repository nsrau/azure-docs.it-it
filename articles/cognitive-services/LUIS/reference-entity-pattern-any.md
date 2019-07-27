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
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: cda6c724a36a73dc34c2bf8e7158e3e3ec92d46b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563218"
---
# <a name="patternany-entity"></a>Entità pattern.any 

Pattern.any è un segnaposto di lunghezza variabile usato solo nell'espressione del modello del criterio per contrassegnare l'inizio e la fine dell'entità.  

Le entità Pattern.any devono essere contrassegnate negli esempi di modello [Pattern](luis-how-to-model-intent-pattern.md), non negli esempi di finalità utente.

**L'entità è una scelta ottimale nei casi seguenti:**

* La fine dell'entità può essere confusa con il testo rimanente dell'espressione. 

## <a name="usage"></a>Utilizzo

Data un'applicazione client che esegue la ricerca di libri in base al titolo, l'entità pattern.any estrae il titolo completo. Un'espressione di criterio con pattern.any per questa ricerca è `Was {BookTitle} written by an American this year[?]`. 

Nella tabella seguente, ogni riga presenta due versioni dell'espressione. Il primo enunciato è il modo in cui LUIS considera inizialmente il enunciato. Non è chiaro dove inizia e termina il titolo del libro. Il enunciato inferiore usa un'entità pattern. Any per contrassegnare l'inizio e la fine dell'entità. 

|Espressione con entità in grassetto|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>Was **The Man Who Mistook His Wife for a Hat and Other Clinical Tales** written by an American this year?|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>Was **Half Asleep in Frog Pajamas** written by an American this year?|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>Was **The Particular Sadness of Lemon Cake: A Novel** written by an American this year?|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>Was **There's A Wocket In My Pocket!** written by an American this year?|
||

## <a name="example-json"></a>JSON di esempio

```JSON
{
  "query": "where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.999999464
  },
  "intents": [
    {
      "intent": "FindForm",
      "score": 0.999999464
    },
    {
      "intent": "GetEmployeeBenefits",
      "score": 4.883697E-06
    },
    {
      "intent": "None",
      "score": 1.02040713E-06
    },
    {
      "intent": "GetEmployeeOrgChart",
      "score": 9.278342E-07
    },
    {
      "intent": "MoveAssetsOrPeople",
      "score": 9.278342E-07
    }
  ],
  "entities": [
    {
      "entity": "understand your responsibilities as a member of the community",
      "type": "FormName",
      "startIndex": 18,
      "endIndex": 78,
      "role": ""
    }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi

In questa [esercitazione](luis-tutorial-pattern-any.md)usare l'entità **pattern. any** per estrarre i dati da espressioni in cui le espressioni sono ben formattate e dove la fine dei dati può essere confusa facilmente con le parole rimanenti dell'espressione.