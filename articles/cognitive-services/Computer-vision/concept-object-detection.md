---
title: Rilevamento di oggetti - Visione artificiale
titleSuffix: Azure Cognitive Services
description: Concetti relativi al rilevamento di oggetti tramite l'API Visione artificiale.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: pafarley
ms.openlocfilehash: 7f0f86e783edb55bc3193df073c92c9523a90176
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2018
ms.locfileid: "52976011"
---
# <a name="object-detection"></a>Rilevamento di oggetti

Il rilevamento di oggetti è simile all'[assegnazione di tag](concept-tagging-images.md), ma l'API restituisce le coordinate del rettangolo di selezione (in pixel) per ogni oggetto trovato. Se, ad esempio, un'immagine contiene un cane, gatto e una persona, l'operazione di rilevamento elencherà tali oggetti insieme alle relative coordinate nell'immagine. È possibile usare questa funzionalità per elaborare le relazioni tra gli oggetti in un'immagine. Questa funzionalità consente anche di determinare se sono presenti più istanze dello stesso tag in un'immagine.

L'API di rilevamento applica tag basati sugli oggetti o sugli esseri viventi identificati nell'immagine. A questo punto, non c'è alcuna relazione formale tra la tassonomia usata per l'assegnazione di tag e la tassonomia usata per il rilevamento di oggetti. A livello concettuale, l'API di rilevamento trova solo gli oggetti e gli esseri viventi, mentre l'API di assegnazione di tag può includere anche termini contestuali, ad esempio "interni", che non possono essere localizzati con rettangoli di selezione.

## <a name="object-detection-example"></a>Esempio di rilevamento di oggetti

La risposta JSON seguente illustra le informazioni restituite da Visione artificiale quando viene eseguito il rilevamento degli oggetti nell'immagine di esempio.

![Donna con un dispositivo Microsoft Surface in una cucina](./Images/windows-kitchen.jpg)

```json
{
   "objects":[
      {
         "rectangle":{
            "x":730,
            "y":66,
            "w":135,
            "h":85
         },
         "object":"kitchen appliance",
         "confidence":0.501
      },
      {
         "rectangle":{
            "x":523,
            "y":377,
            "w":185,
            "h":46
         },
         "object":"computer keyboard",
         "confidence":0.51
      },
      {
         "rectangle":{
            "x":471,
            "y":218,
            "w":289,
            "h":226
         },
         "object":"Laptop",
         "confidence":0.85,
         "parent":{
            "object":"computer",
            "confidence":0.851
         }
      },
      {
         "rectangle":{
            "x":654,
            "y":0,
            "w":584,
            "h":473
         },
         "object":"person",
         "confidence":0.855
      }
   ],
   "requestId":"a7fde8fd-cc18-4f5f-99d3-897dcd07b308",
   "metadata":{
      "width":1260,
      "height":473,
      "format":"Jpeg"
   }
}
```

## <a name="next-steps"></a>Passaggi successivi

Concetti relativi alla [classificazione delle immagini](concept-categorizing-images.md) e alla [descrizione delle immagini](concept-describing-images.md).