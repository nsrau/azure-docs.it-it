---
title: Tipo di entità Machine-learned-LUIS
titleSuffix: Azure Cognitive Services
description: L'entità Machine-Learned è l'entità preferita per la compilazione di applicazioni LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 12/30/2019
ms.author: diberry
ms.openlocfilehash: aac4ba3ec63d425cac782f5db65bba923d24ed71
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75551999"
---
# <a name="machine-learned-entity"></a>Entità basata su Machine Learning

L'entità Machine-Learned è l'entità preferita per la compilazione di applicazioni LUIS.


## <a name="example-json"></a>JSON di esempio

Si supponga che l'app accetti gli ordini di pizza, ad esempio l'esercitazione relativa all' [entità decomponibile](tutorial-machine-learned-entity.md). Ogni ordine può includere più pizze diverse, incluse le dimensioni diverse.

Le espressioni di esempio includono:

|Espressioni di esempio per l'app pizza|
|--|
|`Can I get a pepperoni pizza and a can of coke please`|
|`can I get a small pizza with onions peppers and olives`|
|`pickup an extra large meat lovers pizza`|



#### <a name="v3-prediction-endpoint-response"></a>[Risposta dell'endpoint di previsione V3](#tab/V3)

Poiché un'entità appresa dal computer può avere molti sottocomponenti con vincoli e descrittori, questo è solo un esempio. Deve essere considerata una guida per ciò che verrà restituito dall'entità.

Si consideri la query:

`deliver 1 large cheese pizza on thin crust and 2 medium pepperoni pizzas on deep dish crust`

Si tratta del codice JSON `verbose=false` se è impostato nella stringa di query:

```json
"entities": {
    "Order": [
        {
            "FullPizzaWithModifiers": [
                {
                    "PizzaType": [
                        "cheese pizza"
                    ],
                    "Size": [
                        [
                            "Large"
                        ]
                    ],
                    "Quantity": [
                        1
                    ]
                },
                {
                    "PizzaType": [
                        "pepperoni pizzas"
                    ],
                    "Size": [
                        [
                            "Medium"
                        ]
                    ],
                    "Quantity": [
                        2
                    ],
                    "Crust": [
                        [
                            "Deep Dish"
                        ]
                    ]
                }
            ]
        }
    ],
    "ToppingList": [
        [
            "Cheese"
        ],
        [
            "Pepperoni"
        ]
    ],
    "CrustList": [
        [
            "Thin"
        ]
    ]
}

```

Si tratta del codice JSON `verbose=true` se è impostato nella stringa di query:

```json
"entities": {
    "Order": [
        {
            "FullPizzaWithModifiers": [
                {
                    "PizzaType": [
                        "cheese pizza"
                    ],
                    "Size": [
                        [
                            "Large"
                        ]
                    ],
                    "Quantity": [
                        1
                    ],
                    "$instance": {
                        "PizzaType": [
                            {
                                "type": "PizzaType",
                                "text": "cheese pizza",
                                "startIndex": 16,
                                "length": 12,
                                "score": 0.999998868,
                                "modelTypeId": 1,
                                "modelType": "Entity Extractor",
                                "recognitionSources": [
                                    "model"
                                ]
                            }
                        ],
                        "Size": [
                            {
                                "type": "SizeList",
                                "text": "large",
                                "startIndex": 10,
                                "length": 5,
                                "score": 0.998720646,
                                "modelTypeId": 1,
                                "modelType": "Entity Extractor",
                                "recognitionSources": [
                                    "model"
                                ]
                            }
                        ],
                        "Quantity": [
                            {
                                "type": "builtin.number",
                                "text": "1",
                                "startIndex": 8,
                                "length": 1,
                                "score": 0.999878645,
                                "modelTypeId": 1,
                                "modelType": "Entity Extractor",
                                "recognitionSources": [
                                    "model"
                                ]
                            }
                        ]
                    }
                },
                {
                    "PizzaType": [
                        "pepperoni pizzas"
                    ],
                    "Size": [
                        [
                            "Medium"
                        ]
                    ],
                    "Quantity": [
                        2
                    ],
                    "Crust": [
                        [
                            "Deep Dish"
                        ]
                    ],
                    "$instance": {
                        "PizzaType": [
                            {
                                "type": "PizzaType",
                                "text": "pepperoni pizzas",
                                "startIndex": 56,
                                "length": 16,
                                "score": 0.999987066,
                                "modelTypeId": 1,
                                "modelType": "Entity Extractor",
                                "recognitionSources": [
                                    "model"
                                ]
                            }
                        ],
                        "Size": [
                            {
                                "type": "SizeList",
                                "text": "medium",
                                "startIndex": 49,
                                "length": 6,
                                "score": 0.999841452,
                                "modelTypeId": 1,
                                "modelType": "Entity Extractor",
                                "recognitionSources": [
                                    "model"
                                ]
                            }
                        ],
                        "Quantity": [
                            {
                                "type": "builtin.number",
                                "text": "2",
                                "startIndex": 47,
                                "length": 1,
                                "score": 0.9996054,
                                "modelTypeId": 1,
                                "modelType": "Entity Extractor",
                                "recognitionSources": [
                                    "model"
                                ]
                            }
                        ],
                        "Crust": [
                            {
                                "type": "CrustList",
                                "text": "deep dish crust",
                                "startIndex": 76,
                                "length": 15,
                                "score": 0.761551,
                                "modelTypeId": 1,
                                "modelType": "Entity Extractor",
                                "recognitionSources": [
                                    "model"
                                ]
                            }
                        ]
                    }
                }
            ],
            "$instance": {
                "FullPizzaWithModifiers": [
                    {
                        "type": "FullPizzaWithModifiers",
                        "text": "1 large cheese pizza on thin crust",
                        "startIndex": 8,
                        "length": 34,
                        "score": 0.616001546,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    },
                    {
                        "type": "FullPizzaWithModifiers",
                        "text": "2 medium pepperoni pizzas on deep dish crust",
                        "startIndex": 47,
                        "length": 44,
                        "score": 0.7395033,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    ],
    "ToppingList": [
        [
            "Cheese"
        ],
        [
            "Pepperoni"
        ]
    ],
    "CrustList": [
        [
            "Thin"
        ]
    ],
    "$instance": {
        "Order": [
            {
                "type": "Order",
                "text": "1 large cheese pizza on thin crust and 2 medium pepperoni pizzas on deep dish crust",
                "startIndex": 8,
                "length": 83,
                "score": 0.6881274,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "ToppingList": [
            {
                "type": "ToppingList",
                "text": "cheese",
                "startIndex": 16,
                "length": 6,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "ToppingList",
                "text": "pepperoni",
                "startIndex": 56,
                "length": 9,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "CrustList": [
            {
                "type": "CrustList",
                "text": "thin crust",
                "startIndex": 32,
                "length": 10,
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
#### <a name="v2-prediction-endpoint-response"></a>[Risposta dell'endpoint di previsione V2](#tab/V2)

Questa entità non è disponibile nel runtime di stima V2.
* * *

## <a name="next-steps"></a>Passaggi successivi

Scopri di più sull'entità appresa dal computer, tra cui un' [esercitazione](tutorial-machine-learned-entity.md), [concetti](luis-concept-entity-types.md#design-entities-for-decomposition)e [procedure guidate](luis-how-to-add-entities.md#create-a-machine-learned-entity).

Informazioni sull'entità [List](reference-entity-list.md) e sull'entità di [espressioni regolari](reference-entity-regular-expression.md) .