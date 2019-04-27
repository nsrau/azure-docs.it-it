---
title: Ridimensionare e ritagliare le immagini di anteprima - API Ricerca immagini Bing
titleSuffix: Azure Cognitive Services
description: Ridimensionare e ritagliare le immagini di anteprima incluse nelle risposte dell'API Ricerca immagini Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: F4FFAE91-A003-4F7C-8E60-83A142485E28
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: c53b84f31100ab72458ab6fb79b2009450eda15c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60914586"
---
# <a name="resize-and-crop-thumbnail-images"></a>Ridimensionare e ritagliare le immagini di anteprima

Durante l'elaborazione di una query di ricerca, Bing genererà le informazioni dell'anteprima per tutte le immagini nella [risposta](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images#bing-image-search-response-format). Queste informazioni possono essere usate per visualizzare tutte le anteprime, oppure un subset delle anteprime restituite. Se si visualizza un subset, offrono un'opzione per visualizzare le immagini rimanenti.


<!-- Removing image until we can replace it with a sanatized version.
![Expanded view of thumbnail image](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Se l'utente fa clic sull'anteprima, è possibile usare [contentUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-contenturl) per consentire all'utente di visualizzare l'immagine con dimensioni normali. Assicurarsi di assegnare un attributo all'immagine.

Se `shoppingSourcesCount` o `recipeSourcesCount` sono maggiori di zero, aggiungere all'anteprima un badge, ad esempio un carrello, per indicare che per l'elemento nell'immagine esiste la possibilità di acquisto o una ricetta.

<!-- this is a sanitized version but we're removing all images for now until everything is sanitized.
![Shopping sources badge](./media/cognitive-services-bing-images-api/bing-images-shopping-source.PNG)
-->

Per ottenere informazioni dettagliate sull'immagine, ad esempio le pagine Web che includono l'immagine o le persone riconosciute nell'immagine, usare [imageInsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-imageinsightstoken). Per informazioni dettagliate, vedere [Informazioni dettagliate sulle immagini](image-insights.md).

## <a name="resizing-and-cropping-thumbnails"></a>Ridimensionamento e ritaglio delle anteprime

È anche possibile ridimensionare ed espandere le anteprime, ad esempio quando un cursore del puntatore viene posizionato sopra l’anteprima.
> [!NOTE]
> Se si espande l'immagine, assicurarsi di assegnarle un attributo, ad esempio estraendo l'host da [hostPageDisplayUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-hostpagedisplayurl) e visualizzandolo sotto l'immagine.

[!INCLUDE [cognitive-services-bing-resize-crop-thumbnails](../../../includes/cognitive-services-bing-resize-crop-thumbnails.md)]
