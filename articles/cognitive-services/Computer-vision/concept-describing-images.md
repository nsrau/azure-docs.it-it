---
title: Descrizioni immagini-Visione artificiale
titleSuffix: Azure Cognitive Services
description: Concetti relativi alla funzione di descrizione dell'immagine dell'API Visione artificiale.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 7bf95a2b49608ef1f031a3b443db92b42cdae624
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80244716"
---
# <a name="describe-images-with-human-readable-language"></a>Descrivere le immagini con un linguaggio leggibile dall'utente

Visione artificiale può analizzare un'immagine e generare una frase leggibile che ne descrive il contenuto. L'algoritmo restituisce in realtà diverse descrizioni basate su diverse funzionalità visive e a ogni descrizione viene assegnato un punteggio di confidenza. L'output finale è un elenco di descrizioni ordinate dall'attendibilità più alta a quella più bassa.

## <a name="image-description-example"></a>Esempio di descrizione immagine

La risposta JSON seguente illustra le informazioni restituite da Visione artificiale quando descrive l'immagine di esempio in base alle caratteristiche visive rilevate.

![Immagine in bianco e nero di edifici di Manhattan](./Images/bw_buildings.png)

```json
{
    "description": {
        "tags": ["outdoor", "building", "photo", "city", "white", "black", "large", "sitting", "old", "water", "skyscraper", "many", "boat", "river", "group", "street", "people", "field", "tall", "bird", "standing"],
        "captions": [
            {
                "text": "a black and white photo of a city",
                "confidence": 0.95301952483304808
            },
            {
                "text": "a black and white photo of a large city",
                "confidence": 0.94085190563213816
            },
            {
                "text": "a large white building in a city",
                "confidence": 0.93108362931954824
            }
        ]
    },
    "requestId": "b20bfc83-fb25-4b8d-a3f8-b2a1f084b159",
    "metadata": {
        "height": 300,
        "width": 239,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>Usare l'API

La funzionalità Descrizione immagine fa parte dell'API [analizza immagine](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) . È possibile chiamare questa API tramite un SDK nativo o con chiamate REST. Includere `Description` nel parametro di query **visualFeatures** . Quindi, quando si ottiene la risposta JSON completa, è sufficiente analizzare la stringa per il contenuto della `"description"` sezione.

* [Guida introduttiva: Visione artificiale .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Guida introduttiva: analizzare un'immagine (API REST)](./quickstarts/csharp-analyze.md)

## <a name="next-steps"></a>Passaggi successivi

Informazioni sui concetti correlati relativi all' [assegnazione di tag alle immagini](concept-tagging-images.md) e alla [categorizzazione delle immagini](concept-categorizing-images.md).
