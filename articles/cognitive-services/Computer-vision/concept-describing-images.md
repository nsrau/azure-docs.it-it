---
title: Descrizione delle immagini - Visione artificiale
titleSuffix: Azure Cognitive Services
description: Concetti relativi alla descrizione delle immagini tramite l'API Visione artificiale.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: 423d1be57bc800108a08a81b72587ca2711bbc3d
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2018
ms.locfileid: "49342416"
---
# <a name="describing-images"></a>Descrizione delle immagini

Gli algoritmi di Visione artificiale analizzano il contenuto in un'immagine. Tale analisi costituisce la base per una descrizione visualizzata come linguaggio leggibile dall'utente in frasi complete. La descrizione riepiloga gli elementi trovati nell'immagine. Gli algoritmi di Visione artificiale generano descrizioni diverse in base alle caratteristiche visive identificate nell'immagine. Tutte le descrizioni vengono valutate e per ognuna viene generato un punteggio di attendibilità. Viene quindi restituito un elenco dei punteggi di attendibilità in ordine decrescente.

## <a name="image-description-example"></a>Esempio di descrizione immagine

La risposta JSON seguente illustra le informazioni restituite da Visione artificiale quando descrive l'immagine di esempio in base alle caratteristiche visive rilevate.

![Edifici in bianco e nero](./Images/bw_buildings.png)

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