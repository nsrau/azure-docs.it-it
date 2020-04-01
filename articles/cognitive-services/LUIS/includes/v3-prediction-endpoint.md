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
ms.openlocfilehash: 23e7f88b23ac7af7470183877eeb1be89e79779c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80287748"
---
1. Nel portale LUIS, nella sezione **Manage** (Gestisci) del menu in alto a destra, nella pagina **Azure Resources** (Risorse di Azure) accessibile dal menu a sinistra, nella scheda **Prediction Resources** (Risorse di previsione), copiare la **query di esempio** nella parte inferiore della pagina.

    Incollare l'URL in un Web browser.

    L'URL contiene l'ID, la chiave e il nome dello slot dell'app. L'URL dell'endpoint di previsione V3 è simile al seguente:

    `https://REGION.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

