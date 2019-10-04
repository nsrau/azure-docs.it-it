---
title: Rilevamento della combinazione di colori-Visione artificiale
titleSuffix: Azure Cognitive Services
description: Concetti relativi al rilevamento della combinazione di colori nelle immagini tramite l'API Visione artificiale.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: e0fa85b8a90ea57d9b81bd2eeaa6d080b7582acd
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945287"
---
# <a name="detect-color-schemes-in-images"></a>Rilevare le combinazioni di colori nelle immagini

Visione artificiale analizza i colori in un'immagine per fornire tre diversi attributi: il colore di primo piano dominante, il colore di sfondo dominante e il set di colori dominanti per l'immagine nel suo complesso. I colori restituiti appartengono al set costituito da nero, blu, marrone, grigio, verde, arancione, rosa, viola, rosso, verde acqua, bianco e giallo. 

Visione artificiale estrae anche un colore principale, che rappresenta il colore che risalta di più nell'immagine, in base a una combinazione di colori dominanti e saturazione. Il colore principale viene restituito come codice di colore HTML esadecimale. 

Visione artificiale restituisce anche un valore booleano che indica se un'immagine è in bianco e nero.

## <a name="color-scheme-detection-examples"></a>Esempi di rilevamento di combinazioni di colori

L'esempio seguente illustra la risposta JSON restituita da Visione artificiale quando rileva la combinazione di colori dell'immagine di esempio. In questo caso, l'immagine di esempio non è in bianco e nero, ma i colori di primo piano e di sfondo dominanti sono il nero e i colori dominanti per l'immagine nel suo complesso sono il nero e il bianco.

![Mountain outdoor al tramonto, con la silhouette di una persona](./Images/mountain_vista.png)

```json
{
    "color": {
        "dominantColorForeground": "Black",
        "dominantColorBackground": "Black",
        "dominantColors": ["Black", "White"],
        "accentColor": "BB6D10",
        "isBwImg": false
    },
    "requestId": "0dc394bf-db50-4871-bdcc-13707d9405ea",
    "metadata": {
        "height": 202,
        "width": 300,
        "format": "Jpeg"
    }
}
```

### <a name="dominant-color-examples"></a>Esempi di colori dominanti

La tabella seguente mostra i colori di primo piano, di sfondo e dell'immagine restituiti per ogni immagine di esempio.

| Image | Colori dominanti |
|-------|-----------------|
|![Fiore bianco su sfondo verde](./Images/flower.png)| Primo piano: Nero<br/>Sfondo: Bianco<br/>Colori: nero, bianco, verde|
![Treno che passa per una stazione](./Images/train_station.png) | Primo piano: Nero<br/>Sfondo: Nero<br/>Colori: Nero |

### <a name="accent-color-examples"></a>Esempi di colori principali

 La tabella seguente mostra il colore principale restituito come valore di colore HTML esadecimale per ogni immagine di esempio.

| Image | Colore principale |
|-------|--------------|
|![Persona in piedi su una roccia al tramonto](./Images/mountain_vista.png) | #BB6D10 |
|![Fiore bianco su sfondo verde](./Images/flower.png) | #C6A205 |
|![Treno che passa per una stazione](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Esempi di rilevamento di immagini in bianco e nero

La tabella seguente mostra la valutazione in bianco e nero di Visione artificiale nelle immagini di esempio.

| Image | Bianco e nero? |
|-------|----------------|
|![Immagine in bianco e nero di edifici di Manhattan](./Images/bw_buildings.png) | true |
|![Casa azzurra con giardino](./Images/house_yard.png) | false |

## <a name="next-steps"></a>Passaggi successivi

Concetti relativi al [rilevamento dei tipi di immagine](concept-detecting-image-types.md).
