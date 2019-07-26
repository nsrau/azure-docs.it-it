---
title: Tipo di entità semplice
titleSuffix: Language Understanding - Azure Cognitive Services
description: Un'entità semplice è un'entità generica che descrive un singolo concetto e che viene appresa dal contesto basato su Machine Learning. Poiché le entità semplici sono in genere nomi quali nomi di società, nomi di prodotti o altre categorie di nomi, aggiungere un elenco di frasi quando si usa un'entità semplice per aumentare il segnale dei nomi usati.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: 3f03b33f685bb5c7c9ba8f2267b8556c5dadade4
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480220"
---
# <a name="simple-entity"></a>Entità semplice 

Un'entità semplice è un'entità generica che descrive un singolo concetto e che viene appresa dal contesto basato su Machine Learning. Poiché le entità semplici sono in genere nomi, come nomi di società, nomi di prodotto o altre categorie di nomi, è consigliabile aggiungere un [elenco di frasi](luis-concept-feature.md) quando si usa un'entità semplice per migliorare il segnale dei nomi usati. 

**L'entità è una scelta ottimale nei casi seguenti:**

* I dati non sono formattati in modo coerente ma indicano la stessa cosa. 

![entità semplice](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>JSON di esempio

`Bob Jones wants 3 meatball pho`

Nell'espressione precedente `Bob Jones` è etichettata come entità `Customer` semplice.

I dati restituiti dall'endpoint includono il nome dell'entità, il testo individuato nell'espressione, la posizione del testo individuato e il punteggio:

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

|Oggetto dati|Nome dell'entità|Value|
|--|--|--|
|Entità semplice|`Customer`|`bob jones`|

## <a name="next-steps"></a>Passaggi successivi

In questa [esercitazione](luis-quickstart-primary-and-secondary-data.md), estrarre i dati appresi dal computer del nome del processo di lavoro da un enunciato usando l' **entità semplice**. Per aumentare l'accuratezza dell'estrazione, aggiungere un [elenco](luis-concept-feature.md) di parole specifiche per l'entità semplice.