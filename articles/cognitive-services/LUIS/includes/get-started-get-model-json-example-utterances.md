---
title: File di inclusione
description: File di inclusione
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.date: 05/18/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: acf3968510bc45838f26c4b3cf366abdee06f298
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655544"
---
Le espressioni di esempio seguono un formato specifico.

Il campo `text` contiene il testo dell'espressione di esempio. Il campo `intentName` deve corrispondere al nome di una finalità esistente nell'app di LUIS. Il campo `entityLabels` è obbligatorio. Se non si desidera assegnare etichette alle entità, fornire una matrice vuota.

Se la matrice entityLabels non è vuota, è necessario che `startCharIndex` e `endCharIndex` contrassegnino l'entità a cui si fa riferimento nel campo `entityName`. L’indice è in base zero. Se si inizia o termina l'etichetta a uno spazio nel testo, la chiamata API per aggiungere le espressioni non riesce.

```JSON
[
    {
        "text": "order a cheese pizza",
        "intentName": "ModifyOrder",
        "entityLabels":[]
    },
    {
        "text": "order a large pepperoni pizza",
        "intentName": "ModifyOrder",
        "entityLabels": [
            {
                "entityName": "Order",
                "startCharIndex": 8,
                "endCharIndex": 28
            }
        ]
    },
    {
        "text": "order 2 large pepperoni pizzas on thin crust",
        "intentName": "ModifyOrder",
        "entityLabels": [
            {
                "entityName": "Order",
                "startCharIndex": 6,
                "endCharIndex": 61
            },
            {
                "entityName": "FullPizzaWithModifiers",
                "startCharIndex": 6,
                "endCharIndex": 61
            },
            {
                "entity": "Quantity",
                "startPos": 6,
                "endPos": 7
            },
            {
                "entity": "PizzaType",
                "startPos": 14,
                "endPos": 22
            },
            {
                "entity": "Size",
                "startPos": 8,
                "endPos": 12
            },
            {
                "entity": "Crust",
                "startPos": 34,
                "endPos": 37
            }
        ]
    }
]
```
