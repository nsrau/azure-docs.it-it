---
title: File di inclusione
description: File di inclusione
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: a1b0afce31d7202c38b049addf546350ff347719
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47044169"
---
Il file **utterances.json** contente le espressioni di esempio segue un formato specifico. 

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