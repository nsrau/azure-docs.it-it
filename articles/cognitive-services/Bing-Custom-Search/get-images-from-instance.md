---
title: Ottenere immagini dalla vista personalizzata - Ricerca personalizzata Bing
titleSuffix: Azure Cognitive Services
description: Panoramica generale sull'uso di Ricerca personalizzata Bing per ottenere immagini dalla visualizzazione personalizzata del Web.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 04a2306853b658ecd719106680affc81a95034eb
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48813655"
---
# <a name="get-images-from-your-custom-view"></a>Ottenere immagini dalla visualizzazione personalizzata

Ricerca immagini personalizzata Bing consente di arricchire la propria esperienza di ricerca personalizzata con le immagini. Come per i risultati Web, la ricerca personalizzata supporta la ricerca di immagini nell'elenco di siti Web dell'istanza. È possibile ottenere le immagini usando l'API Ricerca immagini personalizzata di Bing oppure la funzionalità di interfaccia utente ospitata. L'uso della funzionalità di interfaccia utente ospitata è semplice e consigliabile per rendere operativa l'esperienza di ricerca in breve tempo.  Per informazioni sulla configurazione dell'interfaccia utente ospitata per includere le immagini, vedere [Configurare l'esperienza dell'interfaccia utente ospitata](hosted-ui.md).

Per ottenere un maggiore controllo sulla visualizzazione dei risultati di ricerca, è possibile usare l'API Ricerca immagini personalizzata di Bing. Dato che la chiamata all'API è simile alla chiamata all'API Ricerca immagini Bing, vedere [Informazioni su Ricerca immagini Bing](../Bing-Image-Search/overview.md) per esempi di chiamata all'API. Prima di procedere, è tuttavia consigliabile acquisire familiarità con le [informazioni di riferimento sull'API Ricerca immagini personalizzata](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-images-api-v7-reference). Le differenze principali sono i parametri di query supportati (è necessario includere il parametro di query customConfig) e l'endpoint al quale si inviano richieste.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->