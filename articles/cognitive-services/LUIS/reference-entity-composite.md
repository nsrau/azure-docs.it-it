---
title: Tipo di entità composita
titleSuffix: Language Understanding - Azure Cognitive Services
description: Un'entità composita è costituita da altre entità, ad esempio entità predefinite, semplici, espressioni regolari ed elenchi. Le entità separate formano un'entità intera.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: 55dcc5666e63b8a87ddcb13696991fe02843fbbd
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480154"
---
# <a name="composite-entity"></a>Entità composita 

Un'entità composita è costituita da altre entità, ad esempio entità predefinite, semplici, espressioni regolari ed elenchi. Le entità separate formano un'entità intera. 

**Questa entità è una scelta ottimale quando i dati:**

* Sono correlati tra loro. 
* Sono correlati tra loro nel contesto dell'espressione.
* Usano vari tipi di entità.
* Devono essere raggruppati ed elaborati dall'applicazione client come un'unità di informazioni.
* Hanno una serie di espressioni utente che richiedono l'apprendimento automatico.

![entità composita](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>JSON di esempio

Si consideri un'entità composita `Location::ToLocation` di predefinita `number` e con l'espressione seguente:

`book 2 tickets to paris`

Si noti che tra `2`, il numero, `paris` e ToLocation sono presenti delle parole che non fanno parte di nessuna delle entità. La sottolineatura verde, usata in un'espressione etichettata nel sito Web [LUIS](luis-reference-regions.md), indica un'entità composita.

![Entità composita](./media/luis-concept-data-extraction/composite-entity.png)

Le entità composite vengono restituite in una matrice `compositeEntities` e tutte le entità nell'entità composita vengono restituite nella matrice `entities`:

```JSON

"entities": [
    {
    "entity": "2 tickets to cairo",
    "type": "ticketInfo",
    "startIndex": 0,
    "endIndex": 17,
    "score": 0.67200166
    },
    {
    "entity": "2",
    "type": "builtin.number",
    "startIndex": 0,
    "endIndex": 0,
    "resolution": {
        "subtype": "integer",
        "value": "2"
    }
    },
    {
    "entity": "cairo",
    "type": "builtin.geographyV2",
    "startIndex": 13,
    "endIndex": 17
    }
],
"compositeEntities": [
    {
    "parentType": "ticketInfo",
    "value": "2 tickets to cairo",
    "children": [
        {
        "type": "builtin.geographyV2",
        "value": "cairo"
        },
        {
        "type": "builtin.number",
        "value": "2"
        }
    ]
    }
]
```    

|Oggetto dati|Nome dell'entità|Value|
|--|--|--|
|Entità predefinita - numero|"builtin.number"|"2"|
|Entità predefinita-GeographyV2|"Location::ToLocation"|"paris"|

## <a name="next-steps"></a>Passaggi successivi

In questa [esercitazione](luis-tutorial-composite-entity.md), aggiungere un' **entità composita** per raggruppare i dati estratti di vari tipi in un'unica entità contenitore. Aggregando i dati, l'applicazione client può estrarre facilmente i dati correlati in diversi tipi di dati.
