---
title: Rilevamento dei visi - Visione artificiale
titleSuffix: Azure Cognitive Services
description: Concetti relativi al rilevamento dei visi tramite l'API Visione artificiale.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: bf358d1e8f60f989ced8db966bbf0a5179fab25b
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2018
ms.locfileid: "49342314"
---
# <a name="detecting-faces"></a>Rilevamento dei visi

Visione artificiale rileva i visi umani in un'immagine e genera le informazioni relative all'età, al sesso e al rettangolo per ogni viso rilevato. Visione artificiale fornisce un sottoinsieme delle funzionalità disponibili in [Viso](/azure/cognitive-services/face/) ed è possibile usare il servizio Viso per ottenere analisi più dettagliate, ad esempio l'identificazione dei visi e il rilevamento della posa.  

## <a name="face-detection-examples"></a>Esempi di rilevamento di visi

Il primo esempio illustra la risposta JSON restituita da Visione artificiale per un'immagine che contiene un solo viso umano.

![Viso di donna su tetto - Analisi visione](./Images/woman_roof_face.png)

```json
{
    "faces": [
        {
            "age": 23,
            "gender": "Female",
            "faceRectangle": {
                "top": 45,
                "left": 194,
                "width": 44,
                "height": 44
            }
        }
    ],
    "requestId": "8439ba87-de65-441b-a0f1-c85913157ecd",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Png"
    }
}
```

Il secondo esempio illustra la risposta JSON restituita per un'immagine che contiene più visi umani.

![Viso foto di famiglia - Analisi visione](./Images/family_photo_face.png)

```json
{
    "faces": [
        {
            "age": 11,
            "gender": "Male",
            "faceRectangle": {
                "top": 62,
                "left": 22,
                "width": 45,
                "height": 45
            }
        },
        {
            "age": 11,
            "gender": "Female",
            "faceRectangle": {
                "top": 127,
                "left": 240,
                "width": 42,
                "height": 42
            }
        },
        {
            "age": 37,
            "gender": "Female",
            "faceRectangle": {
                "top": 55,
                "left": 200,
                "width": 41,
                "height": 41
            }
        },
        {
            "age": 41,
            "gender": "Male",
            "faceRectangle": {
                "top": 45,
                "left": 103,
                "width": 39,
                "height": 39
            }
        }
    ],
    "requestId": "3a383cbe-1a05-4104-9ce7-1b5cf352b239",
    "metadata": {
        "height": 230,
        "width": 300,
        "format": "Png"
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

Concetti relativi al [rilevamento di contenuti specifici di dominio](concept-detecting-domain-content.md).