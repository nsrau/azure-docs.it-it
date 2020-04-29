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
ms.topic: reference
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: bb3bb27db48255f534e873ed4e93ac62f07016af
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "78273440"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>Entità predefinita Ordinal per un'app LUIS
I numeri ordinali sono una rappresentazione numerica di un oggetto all'interno di un insieme: `first`, `second`, `third`. Poiché è già stato eseguito il training per questa entità, non è necessario aggiungere espressioni di esempio contenenti l'ordinale per le finalità dell'applicazione. L'entità ordinale è supportata in [molte impostazioni cultura](luis-reference-prebuilt-entities.md).

## <a name="types-of-ordinal"></a>Tipi di ordinale
Il numero ordinale viene gestito dal repository GitHub del [testo dei riconoscitori](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45)

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Risoluzione per l'entità ordinale predefinita

Per la query vengono restituiti gli oggetti entità seguenti:

`Order the second option`

#### <a name="v3-response"></a>[Risposta V3](#tab/V3)

Il codice JSON seguente è con `verbose` il parametro impostato `false`su:

```json
"entities": {
    "ordinal": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[Risposta dettagliata V3](#tab/V3-verbose)
Il codice JSON seguente è con `verbose` il parametro impostato `true`su:

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

#### <a name="v2-response"></a>[Risposta V2](#tab/V2)

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

Altre informazioni sull'[endpoint di previsione V3](luis-migration-api-v3.md).

Informazioni su [OrdinalV2](luis-reference-prebuilt-ordinal-v2.md), [numero di telefono](luis-reference-prebuilt-phonenumber.md)e entità di [temperatura](luis-reference-prebuilt-temperature.md) .
