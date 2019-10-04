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
ms.date: 08/08/2019
ms.author: pafarley
ms.openlocfilehash: c3a80cbdd166f77681665ee3675c1a71ce3a9bd6
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70967081"
---
# <a name="detect-popular-brands-in-images"></a>Rileva i marchi più diffusi nelle immagini

Il rilevamento del marchio è una modalità specializzata del [rilevamento di oggetti](concept-object-detection.md) che sfrutta un database di migliaia di logo diffusi a livello mondiale per identificare marchi commerciali in immagini o video. È ad esempio possibile usare questa funzionalità per individuare i marchi più popolari sui social media o quelli più prevalenti nel posizionamento dei prodotti multimediali.

Il servizio Visione artificiale rileva se sono presenti logo di marchi commerciali in una determinata immagine. In caso affermativo, restituisce il nome del marchio, un punteggio di attendibilità e le coordinate di un rettangolo attorno al logo.

Il database dei logo incorporato include i marchi più popolari, nel campo dell'elettronica di consumo, dell'abbigliamento e così via. Se il marchio che si sta cercando non viene rilevato dal servizio Visione artificiale, può essere opportuno crearlo ed eseguire il training del proprio rilevatore di logo usando il servizio [Visione personalizzata](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/).

## <a name="brand-detection-example"></a>Esempio di rilevamento del marchio

Le risposte JSON seguenti illustrano le informazioni restituite da Visione artificiale quando viene eseguito il rilevamento di marchi nelle immagini di esempio.

![Una maglietta rossa a cui sono applicati un'etichetta e un logo Microsoft](./Images/red-shirt-logo.jpg)

```json
"brands":[  
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":20,
         "y":97,
         "w":62,
         "h":52
      }
   }
]
```

In alcuni casi, il rilevatore di marchi identificherà l'immagine del logo e il nome del marchio stilizzato come due logo separati.

![Una felpa grigia a cui sono applicati un'etichetta e un logo Microsoft](./Images/gray-shirt-logo.jpg)

```json
"brands":[  
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":58,
         "y":106,
         "w":55,
         "h":46
      }
   },
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":58,
         "y":86,
         "w":202,
         "h":63
      }
   }
]
```

## <a name="use-the-api"></a>Usare l'API

La funzionalità per il rilevamento del marchio fa parte dell'API di [analisi delle immagini](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa). È possibile chiamare questa API tramite un SDK nativo o con chiamate REST. Includere `Brands` nel parametro di query **visualFeatures** . Quindi, quando si ottiene la risposta JSON completa, è sufficiente analizzare la stringa per il contenuto della `"brands"` sezione.

* [Avvio rapido: Visione artificiale .NET SDK](./quickstarts-sdk/csharp-sdk.md)
* [Avvio rapido: Analizzare un'immagine (API REST)](./quickstarts/csharp-analyze.md)
