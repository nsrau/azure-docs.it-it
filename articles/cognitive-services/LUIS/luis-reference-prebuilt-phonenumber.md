---
title: Numero di telefono Entità precostruite - LUIS
titleSuffix: Azure Cognitive Services
description: Questo articolo contiene informazioni sull'entità predefinita numero di telefono in Language Understanding, ovvero LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 4e2c8e27c6d4195252c6a5b423fa98b2a4247182
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270462"
---
# <a name="phone-number-prebuilt-entity-for-a-luis-app"></a>Entità predefinita con numero di telefono per un'app LUIS
L'entità`phonenumber` consente di estrarre una serie di numeri di telefono che includono il codice nazionale. Poiché è già stato eseguito il training per questa entità, non è necessario aggiungere espressioni di esempio all'applicazione. L'entità `phonenumber` è supportata solo nelle impostazioni cultura `en-us`.

## <a name="types-of-a-phone-number"></a>Tipi di un numero di telefono
`Phonenumber`è gestito dal repository GitHub di [tipo Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml)

## <a name="resolution-for-this-prebuilt-entity"></a>Risoluzione per questa entità predefinita

Per la query vengono restituiti i seguenti oggetti entità:

`my mobile is 1 (800) 642-7676`

#### <a name="v3-response"></a>[Risposta V3](#tab/V3)

Il codice JSON `verbose` seguente è `false`impostato con il parametro :

```json
"entities": {
    "phonenumber": [
        "1 (800) 642-7676"
    ]
}
```
#### <a name="v3-verbose-response"></a>[Risposta dettagliata V3](#tab/V3-verbose)
Il codice JSON `verbose` seguente è `true`impostato con il parametro :

```json
"entities": {
    "phonenumber": [
        "1 (800) 642-7676"
    ],
    "$instance": {

        "phonenumber": [
            {
                "type": "builtin.phonenumber",
                "text": "1 (800) 642-7676",
                "startIndex": 13,
                "length": 16,
                "score": 1.0,
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

L'esempio seguente illustra la risoluzione dell'entità **builtin.phonenumber**.

```json
"entities": [
    {
        "entity": "1 (800) 642-7676",
        "type": "builtin.phonenumber",
        "startIndex": 13,
        "endIndex": 28,
        "resolution": {
            "score": "1",
            "value": "1 (800) 642-7676"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'[endpoint di previsione V3](luis-migration-api-v3.md).

Informazioni sulle entità [percentuale](luis-reference-prebuilt-percentage.md), [numero](luis-reference-prebuilt-number.md) e [temperatura](luis-reference-prebuilt-temperature.md).
