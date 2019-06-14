---
title: Rilevamento del marchio - Visione artificiale
titleSuffix: Azure Cognitive Services
description: Concetti relativi al rilevamento di marchi o logo tramite l'API Visione artificiale.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: d32beaa51471ccab19804122bfbcb33a6b1a5e3d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60203035"
---
# <a name="detect-popular-brands-in-images"></a>Rilevare i marchi più diffusi nelle immagini

Il rilevamento del marchio è una modalità specializzata del [rilevamento di oggetti](concept-object-detection.md) che sfrutta un database di migliaia di logo diffusi a livello mondiale per identificare marchi commerciali in immagini o video. È ad esempio possibile usare questa funzionalità per individuare i marchi più popolari sui social media o quelli più prevalenti nel posizionamento dei prodotti multimediali.

Il servizio Visione artificiale rileva se sono presenti logo di marchi commerciali in una determinata immagine. In caso affermativo, restituisce il nome del marchio, un punteggio di attendibilità e le coordinate di un rettangolo attorno al logo.

Il database dei logo incorporato include i marchi più popolari, nel campo dell'elettronica di consumo, dell'abbigliamento e così via. Se il marchio che si sta cercando non viene rilevato dal servizio Visione artificiale, può essere opportuno crearlo ed eseguire il training del proprio rilevatore di logo usando il servizio [Visione personalizzata](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/).

## <a name="brand-detection-example"></a>Esempio di rilevamento del marchio

Le risposte JSON seguenti illustrano le informazioni restituite da Visione artificiale quando viene eseguito il rilevamento di marchi nelle immagini di esempio.

![Una felpa grigia a cui sono applicati un'etichetta e un logo Microsoft](./Images/gray-shirt-logo.jpg)

```json
{
   "brands":[
      {
         "name":"Microsoft",
         "confidence":0.706,
         "rectangle":{
            "x":470,
            "y":862,
            "w":338,
            "h":327
         }
      }
   ],
   "requestId":"5fda6b40-3f60-4584-bf23-911a0042aa13",
   "metadata":{
      "width":2286,
      "height":1715,
      "format":"Jpeg"
   }
}
```
In alcuni casi, il rilevatore di marchi identificherà l'immagine del logo e il nome del marchio stilizzato come due logo separati.

![Una maglietta rossa a cui sono applicati un'etichetta e un logo Microsoft](./Images/red-shirt-logo.jpg)

```json
{
   "brands":[
      {
         "name":"Microsoft",
         "confidence":0.657,
         "rectangle":{
            "x":436,
            "y":473,
            "w":568,
            "h":267
         }
      },
      {
         "name":"Microsoft",
         "confidence":0.85,
         "rectangle":{
            "x":101,
            "y":561,
            "w":273,
            "h":263
         }
      }
   ],
   "requestId":"10dcd2d6-0cf6-4a5e-9733-dc2e4b08ac8d",
   "metadata":{
      "width":1286,
      "height":1715,
      "format":"Jpeg"
   }
}
```

## <a name="use-the-api"></a>Usare l'API

La funzionalità per il rilevamento del marchio fa parte dell'API di [analisi delle immagini](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa). È possibile chiamare questa API tramite un SDK nativo o con chiamate REST. Includere `Brands` nella **visualFeatures** parametro di query. Quindi, quando si riceve la risposta JSON completa, è sufficiente analizzare la stringa per il contenuto del `"brands"` sezione.

* [Avvio rapido: Analizzare un'immagine (.NET SDK)](./quickstarts-sdk/csharp-analyze-sdk.md)
* [Avvio rapido: Analizzare un'immagine (API REST)](./quickstarts/csharp-analyze.md)
