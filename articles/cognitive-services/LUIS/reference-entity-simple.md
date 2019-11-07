---
title: Tipo di entità semplice-LUIS
titleSuffix: Azure Cognitive Services
description: Un'entità semplice descrive un singolo concetto dal contesto acquisito dal computer. Aggiungere un elenco di frasi quando si usa un'entità semplice per migliorare i risultati.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 27eb4d827c1488b0d051505e62286fd66d6a286b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73671509"
---
# <a name="simple-entity"></a>Entità semplice 

Un'entità semplice è un'entità generica che descrive un singolo concetto e che viene appresa dal contesto basato su Machine Learning. Poiché le entità semplici sono in genere nomi, come nomi di società, nomi di prodotto o altre categorie di nomi, è consigliabile aggiungere un [elenco di frasi](luis-concept-feature.md) quando si usa un'entità semplice per migliorare il segnale dei nomi usati. 

**L'entità è una scelta ottimale nei casi seguenti:**

* I dati non sono formattati in modo coerente ma indicano la stessa cosa. 

![entità semplice](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>JSON di esempio

`Bob Jones wants 3 meatball pho`

Nell'espressione precedente `Bob Jones` è etichettata come entità `Customer` semplice.

I dati restituiti dall'endpoint includono il nome dell'entità, il testo individuato nell'espressione, la posizione del testo individuato e il punteggio:

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 risposta endpoint di stima](#tab/V2)

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Risposta dell'endpoint di stima V3](#tab/V3)

Si tratta del codice JSON se `verbose=false` è impostato nella stringa di query:

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ]
}```

This is the JSON if `verbose=true` is set in the query string:

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ],
    "$instance": {
        "Customer": [
            {
                "type": "Customer",
                "text": "Bob Jones",
                "startIndex": 0,
                "length": 9,
                "score": 0.9339134,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
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
|Entità semplice|`Customer`|`bob jones`|

## <a name="next-steps"></a>Passaggi successivi

In questa [esercitazione](luis-quickstart-primary-and-secondary-data.md), estrarre i dati appresi dal computer del nome del processo di lavoro da un enunciato usando l' **entità semplice**. Per aumentare l'accuratezza dell'estrazione, aggiungere un [elenco](luis-concept-feature.md) di parole specifiche per l'entità semplice.