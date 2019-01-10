---
title: Rilevamento dei tipi di immagine - Visione artificiale
titleSuffix: Azure Cognitive Services
description: Concetti relativi alla funzione di rilevamento del tipo di immagine dell'API Visione artificiale.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 04062d5625126712c5f14c41d610d55caf4c28b5
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2018
ms.locfileid: "53583103"
---
# <a name="detecting-image-types-with-computer-vision"></a>Rilevamento dei tipi di immagine con Visione artificiale

Visione artificiale può analizzare il tipo di contenuto delle immagini e indicare se un'immagine è un elemento grafico ClipArt, valutando l'attendibilità di tale identificazione su una scala, oppure se è un disegno di linee.

## <a name="detecting-clip-art"></a>Rilevamento di ClipArt

Visione artificiale analizza un'immagine e valuta l'attendibilità dell'identificazione come immagine ClipArt su una scala da 0 a 3, come descritto nella tabella seguente.

| Valore | Significato |
|-------|---------|
| 0 | Non-clip-art |
| 1 | Ambiguous |
| 2 | Normal-clip-art |
| 3 | Good-clip-art |

### <a name="clip-art-detection-examples"></a>Esempi di rilevamento di ClipArt

Le risposte JSON seguenti illustrano le informazioni restituite da Visione artificiale quando valuta l'attendibilità dell'identificazione delle immagini di esempio come ClipArt.

![Immagine clip art di una fetta di formaggio](./Images/cheese_clipart.png)

```json
{
    "imageType": {
        "clipArtType": 3,
        "lineDrawingType": 0
    },
    "requestId": "88c48d8c-80f3-449f-878f-6947f3b35a27",
    "metadata": {
        "height": 225,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![Casa azzurra con giardino](./Images/house_yard.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "a9c8490a-2740-4e04-923b-e8f4830d0e47",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="detecting-line-drawings"></a>Rilevamento di disegni di linee

Visione artificiale analizza un'immagine e restituisce un valore booleano che indica se l'immagine è un disegno di linee.

### <a name="line-drawing-detection-examples"></a>Esempi di rilevamento di disegni di linee

Le risposte JSON seguenti illustrano le informazioni restituite da Visione artificiale quando indica se le immagini di esempio sono disegni di linee.

![Disegno di un leone](./Images/lion_drawing.png)

```json
{
    "imageType": {
        "clipArtType": 2,
        "lineDrawingType": 1
    },
    "requestId": "6442dc22-476a-41c4-aa3d-9ceb15172f01",
    "metadata": {
        "height": 268,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![Fiore bianco su sfondo verde](./Images/flower.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "98437d65-1b05-4ab7-b439-7098b5dfdcbf",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

Concetti relativi all'[assegnazione di tag alle immagini](concept-tagging-images.md) e alla [classificazione delle immagini](concept-categorizing-images.md).