---
title: Rilevamento viso-Visione artificiale
titleSuffix: Azure Cognitive Services
description: Informazioni sui concetti relativi alla funzionalità di rilevamento viso del API Visione artificiale.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 089c59c041110209e55cc3fb10b1c5adf54bd13e
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94538275"
---
# <a name="face-detection-with-computer-vision"></a>Rilevamento volto con Visione artificiale

Visione artificiale può rilevare i visi umani all'interno di un'immagine e generare l'età, il sesso e il rettangolo per ogni viso rilevato. 

> [!NOTE]
> Questa funzionalità è disponibile anche nel servizio [Viso](/azure/cognitive-services/face/). Vedere questa alternativa per analisi più dettagliate dei visi, tra cui l'identificazione dei visi e il rilevamento delle pose. 

## <a name="face-detection-examples"></a>Esempi di rilevamento di visi

L'esempio seguente mostra la risposta JSON restituita da Visione artificiale per un'immagine che contiene un solo viso umano.

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

L'esempio successivo mostra la risposta JSON restituita per un'immagine che contiene più visi umani.

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

## <a name="use-the-api"></a>Usare l'API

La funzionalità di rilevamento viso fa parte dell'API [analizza immagine](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) . È possibile chiamare questa API tramite un SDK nativo o con chiamate REST. Includere `Faces` nel parametro di query **visualFeatures** . Quindi, quando si ottiene la risposta JSON completa, è sufficiente analizzare la stringa per il contenuto della `"faces"` sezione.

* [Guida introduttiva: Visione artificiale .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Guida introduttiva: analizzare un'immagine (API REST)](./quickstarts/csharp-analyze.md)
