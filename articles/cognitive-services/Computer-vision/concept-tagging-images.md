---
title: Assegnazione di tag alle immagini - Visione artificiale
titleSuffix: Azure Cognitive Services
description: Concetti relativi all'assegnazione di tag alle immagini tramite l'API Visione artificiale.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: 7e9181b6e805a768ca5dd8ed558b8feea45f22d3
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2018
ms.locfileid: "52960908"
---
# <a name="tagging-images"></a>Assegnazione di tag alle immagini

Visione artificiale restituisce tag basati su migliaia di oggetti, esseri viventi, panorami e azioni riconoscibili. Quando i tag sono ambigui o di conoscenza non comune, la risposta dell'API indica suggerimenti per chiarire il significato del tag nel contesto di un'impostazione nota. I tag non sono organizzati in una tassonomia e non esiste alcuna gerarchia di ereditarietà. Una raccolta di tag di contenuto costituisce la base per la descrizione di un'immagine visualizzata come linguaggio leggibile dall'utente e formattata in frasi complete. Si noti che a questo punto l'inglese è l'unica lingua supportata per la descrizione dell'immagine.

Dopo il caricamento di un'immagine o la definizione di un URL di immagine, gli algoritmi di Visione artificiale generano tag in base agli oggetti, alle azioni e agli esseri umani identificati nell'immagine. L'assegnazione di tag non è limitata al soggetto principale, ad esempio una persona in primo piano, ma include anche scenari (interni o esterni), arredamenti, strumenti, piante, animali, accessori, gadget e così via.

## <a name="image-tagging-example"></a>Esempio di assegnazione di tag a un'immagine

La risposta JSON seguente illustra le informazioni restituite da Visione artificiale quando esegue l'assegnazione di tag alle caratteristiche visive rilevate nell'immagine di esempio.

![House_Yard](./Images/house_yard.png).

```json
{
    "tags": [
        {
            "name": "grass",
            "confidence": 0.9999995231628418
        },
        {
            "name": "outdoor",
            "confidence": 0.99992108345031738
        },
        {
            "name": "house",
            "confidence": 0.99685388803482056
        },
        {
            "name": "sky",
            "confidence": 0.99532157182693481
        },
        {
            "name": "building",
            "confidence": 0.99436837434768677
        },
        {
            "name": "tree",
            "confidence": 0.98880356550216675
        },
        {
            "name": "lawn",
            "confidence": 0.788884699344635
        },
        {
            "name": "green",
            "confidence": 0.71250593662261963
        },
        {
            "name": "residential",
            "confidence": 0.70859086513519287
        },
        {
            "name": "grassy",
            "confidence": 0.46624681353569031
        }
    ],
    "requestId": "06f39352-e445-42dc-96fb-0a1288ad9cf1",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

Concetti relativi alla [classificazione delle immagini](concept-categorizing-images.md) e alla [descrizione delle immagini](concept-describing-images.md).