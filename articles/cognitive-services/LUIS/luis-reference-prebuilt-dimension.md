---
title: Entità predefinite della dimensione-LUIS
titleSuffix: Azure Cognitive Services
description: Questo articolo contiene informazioni sull'entità predefinita dimensioni in Language Understanding, ovvero LUIS.
services: cognitive-services
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.openlocfilehash: 0b5f081dc8c0bc203ccafde4a513371d11e36879
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91538348"
---
# <a name="dimension-prebuilt-entity-for-a-luis-app"></a>Entità predefinita dimension per un'app LUIS
L'entità predefinita dimensioni rileva i diversi tipi di dimensioni, indipendentemente dalle impostazioni cultura dell'app LUIS. Poiché è già stato eseguito il training per questa entità, non è necessario aggiungere espressioni di esempio contenenti le dimensioni per le finalità dell'applicazione. L'entità dimensioni è supportata in [molte impostazioni cultura](luis-reference-prebuilt-entities.md).

## <a name="types-of-dimension"></a>Tipi di dimensioni

La dimensione viene gestita dal repository GitHub del [testo dei riconoscitori](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml) .

## <a name="resolution-for-dimension-entity"></a>Risoluzione per l'entità dimensioni

Per la query vengono restituiti gli oggetti entità seguenti:

`10 1/2 miles of cable`

#### <a name="v3-response"></a>[Risposta V3](#tab/V3)

Il codice JSON seguente è con il `verbose` parametro impostato su `false` :

```json
"entities": {
    "dimension": [
        {
            "number": 10.5,
            "units": "Mile"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[Risposta dettagliata V3](#tab/V3-verbose)
Il codice JSON seguente è con il `verbose` parametro impostato su `true` :

```json
"entities": {
    "dimension": [
        {
            "number": 10.5,
            "units": "Mile"
        }
    ],
    "$instance": {
        "dimension": [
            {
                "type": "builtin.dimension",
                "text": "10 1/2 miles",
                "startIndex": 0,
                "length": 12,
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

L'esempio seguente illustra la risoluzione dell'entità **builtin.dimension**.

```json
{
    "entity": "10 1/2 miles",
    "type": "builtin.dimension",
    "startIndex": 0,
    "endIndex": 11,
    "resolution": {
    "unit": "Mile",
    "value": "10.5"
    }
}
```
* * *

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'[endpoint di previsione V3](luis-migration-api-v3.md).

Informazioni sulle entità [posta elettronica](luis-reference-prebuilt-email.md), [numero](luis-reference-prebuilt-number.md) e [ordinale](luis-reference-prebuilt-ordinal.md).
