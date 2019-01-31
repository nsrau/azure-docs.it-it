---
title: Ridimensionare e ritagliare le immagini di anteprima - API Ricerca immagini Bing
titleSuffix: Azure Cognitive Services
description: Ridimensionare e ritagliare le immagini di anteprima incluse nelle risposte dell'API Ricerca immagini Bing.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.assetid: F4FFAE91-A003-4F7C-8E60-83A142485E28
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 7d6c59cc4a7f0a77db42ca5919f3af331c42762a
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55197127"
---
# <a name="resize-and-crop-thumbnail-images"></a>Ridimensionare e ritagliare le immagini di anteprima

Durante l'elaborazione di una query di ricerca, Bing genererà le informazioni dell'anteprima per tutte le immagini nella [risposta](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images#bing-image-search-response-format). Queste informazioni possono essere usate per visualizzare tutte le anteprime, oppure un subset delle anteprime restituite. Se si visualizza un subset, offrire la possibilità di vedere le immagini rimanenti.


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
