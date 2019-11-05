---
title: Personaname-entità precompilata-LUIS
titleSuffix: Azure Cognitive Services
description: Questo articolo contiene informazioni sull'entità predefinita personName in Language Understanding, ovvero LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 9777c62d97c70d4f6a0d0a4d912dea3fa8decd23
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499546"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>Entità predefinita personName per un'app LUIS
L'entità predefinita personName rileva i nomi di persona. Poiché è già stato eseguito il training per questa entità, non è necessario aggiungere espressioni di esempio contenenti personName per le finalità dell'applicazione. L'entità personName è supportata nelle [impostazioni cultura](luis-reference-prebuilt-entities.md) inglese e cinese.

## <a name="resolution-for-personname-entity"></a>Risoluzione per l'entità personName

Per la query vengono restituiti gli oggetti entità seguenti:

`Is Jill Jones in Cairo?`


#### <a name="v3-responsetabv3"></a>[Risposta V3](#tab/V3)


Il codice JSON seguente è con il parametro `verbose` impostato su `false`:

```json
"entities": {
    "personName": [
        "Jill Jones"
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[Risposta dettagliata V3](#tab/V3-verbose)
Il codice JSON seguente è con il parametro `verbose` impostato su `true`:

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
#### <a name="v2-responsetabv2"></a>[Risposta V2](#tab/V2)

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

Altre informazioni sull' [endpoint di stima V3](luis-migration-api-v3.md).

Informazioni sulle entità [posta elettronica](luis-reference-prebuilt-email.md), [numero](luis-reference-prebuilt-number.md) e [ordinale](luis-reference-prebuilt-ordinal.md). 
