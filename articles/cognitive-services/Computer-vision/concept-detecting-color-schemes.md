---
title: Rilevamento delle combinazioni di colori - Visione artificiale
titleSuffix: Azure Cognitive Services
description: Concetti relativi al rilevamento della combinazione di colori nelle immagini tramite l'API Visione artificiale.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: d882ad89e68936d07ae4d76218c6e3ac450185a8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55151329"
---
# <a name="detect-color-schemes-in-images"></a>Rilevare le combinazioni di colori nelle immagini

Visione artificiale estrae colori da un'immagine. I colori vengono quindi analizzati in tre contesti diversi: il colore dominante in primo piano, il colore dominante sullo sfondo e i colori dominanti per l'intera immagine. I colori vengono raggruppati in 12 colori principali dominanti, ovvero nero, blu, marrone, grigio, verde, arancione, rosa, viola, rosso, verde acqua, bianco e giallo. Visione artificiale analizza i colori estratti da un'immagine per restituire il colore principale che rappresenta il colore dell'immagine visibilmente più acceso, tramite una combinazione di colori dominanti e saturazione. A seconda dei colori in un'immagine, il semplice bianco e nero oppure i colori principali possono essere restituiti in codici esadecimali. Visione artificiale restituisce anche un valore booleano che indica se un'immagine è in bianco e nero.

## <a name="color-scheme-detection-examples"></a>Esempi di rilevamento di combinazioni di colori

L'esempio seguente illustra la risposta JSON restituita da Visione artificiale quando rileva la combinazione di colori dell'immagine di esempio. In questo caso, l'immagine di esempio non è in bianco e nero, ma il colore dominante in primo piano e sullo sfondo è il nero e i colori dominanti per l'intera immagine sono il bianco e il nero.

![Panorama montano](./Images/mountain_vista.png)

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

La tabella seguente descrive, per ogni immagine di esempio, i colori dominanti in primo piano, sullo sfondo e nell'intera immagine, come restituiti da Visione artificiale.

| Image | Colori dominanti |
|-------|-----------------|
|![Fiore bianco su sfondo verde](./Images/flower.png)| Primo piano: Nero<br/>Sfondo: Bianco<br/>Colori: nero, bianco, verde|
![Treno che passa per una stazione](./Images/train_station.png) | Primo piano: Nero<br/>Sfondo: Nero<br/>Colori: Nero |

### <a name="accent-color-examples"></a>Esempi di colori principali

 La tabella seguente descrive, per ogni immagine di esempio, il colore principale espresso in un valore di colore HTML esadecimale, come restituito da Visione artificiale.

| Image | Colore principale |
|-------|--------------|
|![Persona in piedi su una roccia al tramonto](./Images/mountain_vista.png) | #BB6D10 |
|![Fiore bianco su sfondo verde](./Images/flower.png) | #C6A205 |
|![Treno che passa per una stazione](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Esempi di rilevamento di immagini in bianco e nero

La tabella seguente indica se ogni immagine di esempio è in bianco e nero, in base alle informazioni restituite da Visione artificiale.

| Image | Bianco e nero? |
|-------|----------------|
|![Immagine in bianco e nero di edifici di Manhattan](./Images/bw_buildings.png) | true |
|![Casa azzurra con giardino](./Images/house_yard.png) | false |

## <a name="next-steps"></a>Passaggi successivi

Concetti relativi al [rilevamento dei tipi di immagine](concept-detecting-image-types.md).
