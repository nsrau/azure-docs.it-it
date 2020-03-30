---
title: File di inclusione
description: File di inclusione
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.date: 02/14/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: e879afdbd4c34e9d74405644de86421fb2cbab46
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77279514"
---
Le espressioni di esempio seguono un formato specifico.

Il campo `text` contiene il testo dell'espressione di esempio. Il campo `intentName` deve corrispondere al nome di una finalità esistente nell'app di LUIS. Il campo `entityLabels` è obbligatorio. Se non si desidera assegnare etichette alle entità, fornire una matrice vuota.

Se la matrice entityLabels non è vuota, è necessario che `startCharIndex` e `endCharIndex` contrassegnino l'entità a cui si fa riferimento nel campo `entityName`. L'indice è basato su zero, vale a dire che 6 dei primi esempi fanno riferimento alla "S" di Seattle e non allo spazio prima della lettera S maiuscola. Se si inizia o termina l'etichetta in corrispondenza di uno spazio nel testo, la chiamata API per aggiungere le espressioni fallisce.

```JSON
[
  {
    "text": "go to Seattle today",
    "intentName": "BookFlight",
    "entityLabels": [
      {
        "entityName": "Location::LocationTo",
        "startCharIndex": 6,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "purple dogs are difficult to work with",
    "intentName": "BookFlight",
    "entityLabels": []
  }
]
```
