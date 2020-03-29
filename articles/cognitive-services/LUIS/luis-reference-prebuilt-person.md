---
title: Entità predefinita PersonName - LUIS
titleSuffix: Azure Cognitive Services
description: Questo articolo contiene informazioni sull'entità predefinita personName in Language Understanding, ovvero LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 768c719211e8a8f2133d3798343d076e795a3da0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273426"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>Entità predefinita personName per un'app LUIS
L'entità predefinita personName rileva i nomi di persona. Poiché è già stato eseguito il training per questa entità, non è necessario aggiungere espressioni di esempio contenenti personName per le finalità dell'applicazione. L'entità personName è supportata nelle [impostazioni cultura](luis-reference-prebuilt-entities.md) inglese e cinese.

## <a name="resolution-for-personname-entity"></a>Risoluzione per l'entità personName

Per la query vengono restituiti i seguenti oggetti entità:

`Is Jill Jones in Cairo?`


#### <a name="v3-response"></a>[Risposta V3](#tab/V3)


Il codice JSON `verbose` seguente è `false`impostato con il parametro :

```json
"entities": {
    "personName": [
        "Jill Jones"
    ]
}
```
#### <a name="v3-verbose-response"></a>[Risposta dettagliata V3](#tab/V3-verbose)
Il codice JSON `verbose` seguente è `true`impostato con il parametro :

```json
"entities": {
    "personName": [
        "Jill Jones"
    ],
    "$instance": {
        "personName": [
            {
                "type": "builtin.personName",
                "text": "Jill Jones",
                "startIndex": 3,
                "length": 10,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
    }
}
```
#### <a name="v2-response"></a>[Risposta V2](#tab/V2)

L'esempio seguente illustra la risoluzione dell'entità **builtin.personName**.

```json
"entities": [
{
    "entity": "Jill Jones",
    "type": "builtin.personName",
    "startIndex": 3,
    "endIndex": 12
}
]
```
* * *

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'[endpoint di previsione V3](luis-migration-api-v3.md).

Informazioni sulle entità [posta elettronica](luis-reference-prebuilt-email.md), [numero](luis-reference-prebuilt-number.md) e [ordinale](luis-reference-prebuilt-ordinal.md).
