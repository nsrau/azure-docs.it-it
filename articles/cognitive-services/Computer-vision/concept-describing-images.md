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
ms.openlocfilehash: dcf61c642a9f8ad7aa68d72736ce8fdb0e9a6e3c
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945255"
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

## <a name="next-steps"></a>Passaggi successivi

Concetti relativi all'[assegnazione di tag alle immagini](concept-tagging-images.md) e alla [classificazione delle immagini](concept-categorizing-images.md).
