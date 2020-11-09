---
title: Come ottenere l'endpoint di previsione V3
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 05/05/2020
ms.openlocfilehash: 85fcd0308083350a02afd4eadac2b361337f3b33
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93128162"
---
1. Nella pagina **Azure Resources** (Risorse di Azure) (menu a sinistra) della sezione **Manage** (Gestisci) del portale LUIS (menu in alto a destra) copiare la **query di esempio** , visualizzata nella parte inferiore della pagina, nella scheda **Prediction Resources** (Risorse di previsione). L'URL contiene l'ID, la chiave e il nome dello slot dell'app. L'URL dell'endpoint di previsione V3 ha il formato `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

    :::image type="content" source="../media/prediction-resources-example-query.png" alt-text="Query di esempio nella sezione delle risorse di previsione" lightbox="../media/prediction-resources-example-query.png":::
    
    Incollare l'URL in un Web browser. Se l'URL non è visibile, significa che non è presente una risorsa di previsione e sarà necessario crearne una. 

    

    

