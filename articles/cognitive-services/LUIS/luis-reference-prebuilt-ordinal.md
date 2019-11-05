---
title: Entità predefinita ordinale-LUIS
titleSuffix: Azure Cognitive Services
description: Questo articolo contiene informazioni sull'entità predefinita ordinale in Language Understanding, ovvero LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: b2a2d9e78a0b152da14bb737079cf0dfdef0dc05
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491249"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>Entità predefinita Ordinal per un'app LUIS
I numeri ordinali sono una rappresentazione numerica di un oggetto all'interno di un insieme: `first`, `second`, `third`. Poiché è già stato eseguito il training per questa entità, non è necessario aggiungere espressioni di esempio contenenti l'ordinale per le finalità dell'applicazione. L'entità ordinale è supportata in [molte impostazioni cultura](luis-reference-prebuilt-entities.md). 

## <a name="types-of-ordinal"></a>Tipi di ordinale
L'entità Ordinal viene gestita dal repository GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45)

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Risoluzione per l'entità ordinale predefinita

Per la query vengono restituiti gli oggetti entità seguenti:

`Order the second option`

#### <a name="v3-responsetabv3"></a>[Risposta V3](#tab/V3)

Il codice JSON seguente è con il parametro `verbose` impostato su `false`:

```json
"entities": {
    "ordinal": [
        2
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[Risposta dettagliata V3](#tab/V3-verbose)
Il codice JSON seguente è con il parametro `verbose` impostato su `true`:

```json
"entities": {
    "ordinal": [
        2
    ],
    "$instance": {
        "ordinal": [
            {
                "type": "builtin.ordinal",
                "text": "second",
                "startIndex": 10,
                "length": 6,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-responsetabv2"></a>[Risposta V2](#tab/V2)

L'esempio seguente illustra la risoluzione dell'entità **builtin.ordinal**.

```json
"entities": [
  {
    "entity": "second",
    "type": "builtin.ordinal",
    "startIndex": 10,
    "endIndex": 15,
    "resolution": {
      "value": "2"
    }
  }
]
```
* * * 

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull' [endpoint di stima V3](luis-migration-api-v3.md).

Informazioni su [OrdinalV2](luis-reference-prebuilt-ordinal-v2.md), [numero di telefono](luis-reference-prebuilt-phonenumber.md)e entità di [temperatura](luis-reference-prebuilt-temperature.md) . 
