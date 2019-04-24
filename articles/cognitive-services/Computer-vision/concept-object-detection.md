---
title: Rilevamento di oggetti - Visione artificiale
titleSuffix: Azure Cognitive Services
description: Informazioni sui concetti relativi alla funzionalità di rilevamento di oggetti dell'API visione artificiale - utilizzo e limiti.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 012ab849c926de332da55361c79c76c5a1311169
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60368043"
---
# <a name="detect-common-objects-in-images"></a>Rilevare gli oggetti comuni nelle immagini

Il rilevamento di oggetti è simile all'[assegnazione di tag](concept-tagging-images.md), ma l'API restituisce le coordinate del rettangolo di selezione (in pixel) per ogni oggetto trovato. Se, ad esempio, un'immagine contiene un cane, gatto e una persona, l'operazione di rilevamento elencherà tali oggetti insieme alle relative coordinate nell'immagine. È possibile usare questa funzionalità per elaborare le relazioni tra gli oggetti in un'immagine. Consente inoltre di determinare se sono presenti più istanze dello stesso tag in un'immagine.

L'API di rilevamento applica tag basati sugli oggetti o sugli esseri viventi identificati nell'immagine. Non è attualmente alcuna relazione formali tra la tassonomia di tag e la tassonomia di rilevamento di oggetti. A livello concettuale, l'API di rilevamento trova solo gli oggetti e operazioni living, mentre l'API di Tag possono includere anche contestuali termini quali "interni", non possono essere localizzati con rettangoli.

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

## <a name="limitations"></a>Limitazioni

È importante considerare le limitazioni del rilevamento di oggetti in modo che è possibile evitare o mitigare gli effetti di falsi negativi (oggetti mancanti) e i dettagli limitato.

* Gli oggetti vengono in genere non rilevati se sono piccole dimensioni (inferiore al 5% dell'immagine).
* Gli oggetti vengono in genere non rilevati se sono disposti in stretta collaborazione (una pila di piatti, ad esempio).
* Gli oggetti non vengono differenziati in base alla marca o ai nomi di prodotto (ad esempio, diversi tipi di bibite su uno scaffale di un supermercato). È tuttavia possibile ottenere informazioni sul marchio presente in un'immagine usando la funzionalità per il [rilevamento del marchio](concept-brand-detection.md).

## <a name="use-the-api"></a>Usare l'API

La funzionalità per il rilevamento di oggetti fa parte dell'API di [analisi delle immagini](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa). È possibile chiamare questa API tramite un SDK nativo o con chiamate REST. Includere `Objects` nella **visualFeatures** parametro di query. Quindi, quando si riceve la risposta JSON completa, è sufficiente analizzare la stringa per il contenuto del `"objects"` sezione.

* [Guida introduttiva: Analizzare un'immagine (.NET SDK)](./quickstarts-sdk/csharp-analyze-sdk.md)
* [Guida introduttiva: Analizzare un'immagine (API REST)](./quickstarts/csharp-analyze.md)