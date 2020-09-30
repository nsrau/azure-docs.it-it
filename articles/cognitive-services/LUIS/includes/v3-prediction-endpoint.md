---
title: Come ottenere l'endpoint di previsione V3
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 05/05/2020
ms.openlocfilehash: a607ad71915606d6046b4c71291a49b8641bdcc9
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91545430"
---
1. Nella pagina **Azure Resources** (Risorse di Azure) (menu a sinistra) della sezione **Manage** (Gestisci) del portale LUIS (menu in alto a destra) copiare la **query di esempio**, visualizzata nella parte inferiore della pagina, nella scheda **Prediction Resources** (Risorse di previsione).

    Incollare l'URL in un Web browser.

    L'URL contiene l'ID, la chiave e il nome dello slot dell'app. L'URL dell'endpoint di previsione V3 è simile al seguente:

    `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

