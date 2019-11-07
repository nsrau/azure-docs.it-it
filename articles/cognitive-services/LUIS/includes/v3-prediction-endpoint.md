---
title: Come ottenere l'endpoint di previsione V3
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: bc6e43faca47e360daa8214e6b9f6e9df4a2f130
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495325"
---
1. Nel portale LUIS, nella sezione Gestisci (menu in alto a destra), nella pagina chiavi ed endpoint (menu a sinistra), selezionare l'URL endpoint nella parte inferiore della pagina.

    Con questa azione viene aperta un scheda del browser con l'URL endpoint nella barra degli indirizzi.

    L'URL contiene l'ID, la chiave e il nome dello slot dell'app. L'URL dell'endpoint di previsione V3 è simile al seguente:

    `https://REGION.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-KEY&<optional-name-value-pairs>&query=UTTERANCE-TEXT.`

