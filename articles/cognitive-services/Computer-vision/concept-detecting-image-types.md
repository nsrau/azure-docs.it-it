---
title: Rilevamento del tipo di immagine-Visione artificiale
titleSuffix: Azure Cognitive Services
description: Concetti relativi alla funzione di rilevamento del tipo di immagine dell'API Visione artificiale.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4e6c2db5333962d7ae43534998ffc1c48b0dba45
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80244563"
---
# <a name="detecting-image-types-with-computer-vision"></a>Rilevamento dei tipi di immagine con Visione artificiale

Con l'API [analizza immagine](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) , visione artificiale possibile analizzare il tipo di contenuto delle immagini, indicando se un'immagine è ClipArt o un disegno a linee.

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

## <a name="use-the-api"></a>Usare l'API

La funzionalità di rilevamento del tipo di immagine fa parte dell'API [analizza immagine](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) . È possibile chiamare questa API tramite un SDK nativo o con chiamate REST. Includere `ImageType` nel parametro di query **visualFeatures** . Quindi, quando si ottiene la risposta JSON completa, è sufficiente analizzare la stringa per il contenuto della `"imageType"` sezione.

* [Guida introduttiva: Visione artificiale .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Guida introduttiva: analizzare un'immagine (API REST)](./quickstarts/csharp-analyze.md)
