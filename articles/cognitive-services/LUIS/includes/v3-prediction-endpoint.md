---
title: Come ottenere l'endpoint di previsione V3
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/05/2020
ms.author: diberry
ms.openlocfilehash: 18f5422202cf972d49349cc04b845c623cabffa3
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589141"
---
1. Nella pagina **Azure Resources** (Risorse di Azure) (menu a sinistra) della sezione **Manage** (Gestisci) del portale LUIS (menu in alto a destra) copiare la **query di esempio**, visualizzata nella parte inferiore della pagina, nella scheda **Prediction Resources** (Risorse di previsione).

    Incollare l'URL in un Web browser.

    L'URL contiene l'ID, la chiave e il nome dello slot dell'app. L'URL dell'endpoint di previsione V3 è simile al seguente:

    `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

