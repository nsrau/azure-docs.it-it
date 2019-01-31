---
title: Ottenere video dalla vista personalizzata - Ricerca personalizzata Bing
titlesuffix: Azure Cognitive Services
description: Panoramica generale sull'uso di Ricerca personalizzata Bing per ottenere video dalla visualizzazione personalizzata del Web.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 5b73acb633512960d7aa6d6eea9b1d6e10dd3c22
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55174296"
---
# <a name="get-videos-from-your-custom-view"></a>Ottenere video dalla visualizzazione personalizzata

Ricerca video personalizzata Bing consente di arricchire la propria esperienza di ricerca personalizzata con i video. Come per i risultati Web, la ricerca personalizzata supporta la ricerca di video nell'elenco di siti Web dell'istanza. È possibile ottenere i video usando l'API Ricerca video personalizzata di Bing oppure la funzionalità di interfaccia utente ospitata. L'uso della funzionalità di interfaccia utente ospitata è semplice e consigliabile per rendere operativa l'esperienza di ricerca in breve tempo. Per informazioni sulla configurazione dell'interfaccia utente ospitata per includere i video, vedere [Configurare l'esperienza dell'interfaccia utente ospitata](hosted-ui.md).

Per ottenere un maggiore controllo sulla visualizzazione dei risultati di ricerca, è possibile usare l'API Ricerca video personalizzata di Bing. Dato che la chiamata all'API è simile alla chiamata all'API Ricerca video Bing, vedere [Informazioni su Ricerca video Bing](../Bing-Video-Search/search-the-web.md) per esempi di chiamata all'API. Prima di procedere, è tuttavia consigliabile acquisire familiarità con le [informazioni di riferimento sull'API Ricerca video personalizzata](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-videos-api-v7-reference). Le differenze principali sono i parametri di query supportati (è necessario includere il parametro di query customConfig) e l'endpoint al quale si inviano richieste.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->
