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
ms.openlocfilehash: 9e82c1b18bcd2edbe0fb4ed4846823acbb398eca
ms.sourcegitcommit: bf8c447dada2b4c8af017ba7ca8bfd80f943d508
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85378447"
---
Le espressioni di esempio seguono un formato specifico.

Il campo `text` contiene il testo dell'espressione di esempio. Il campo `intentName` deve corrispondere al nome di una finalità esistente nell'app di LUIS. Il campo `entityLabels` è obbligatorio. Se non si desidera assegnare etichette alle entità, fornire una matrice vuota.

Se la matrice entityLabels non è vuota, è necessario che `startCharIndex` e `endCharIndex` contrassegnino l'entità a cui si fa riferimento nel campo `entityName`. L’indice è in base zero. Se si inizia o termina l'etichetta a uno spazio nel testo, la chiamata API per aggiungere le espressioni non riesce.

```JSON
[
  {
    "text": "order a pizza",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 6,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "order a large pepperoni pizza",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 6,
        "endCharIndex": 28
      },
      {
        "entityName": "FullPizzaWithModifiers",
        "startCharIndex": 6,
        "endCharIndex": 28
      },
      {
        "entityName": "PizzaType",
        "startCharIndex": 14,
        "endCharIndex": 28
      },
      {
        "entityName": "Size",
        "startCharIndex": 8,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "I want two large pepperoni pizzas on thin crust",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 7,
        "endCharIndex": 46
      },
      {
        "entityName": "FullPizzaWithModifiers",
        "startCharIndex": 7,
        "endCharIndex": 46
      },
      {
        "entityName": "PizzaType",
        "startCharIndex": 17,
        "endCharIndex": 32
      },
      {
        "entityName": "Size",
        "startCharIndex": 11,
        "endCharIndex": 15
      },
      {
        "entityName": "Quantity",
        "startCharIndex": 7,
        "endCharIndex": 9
      },
      {
        "entityName": "Crust",
        "startCharIndex": 37,
        "endCharIndex": 46
      }
    ]
  }
]
```
