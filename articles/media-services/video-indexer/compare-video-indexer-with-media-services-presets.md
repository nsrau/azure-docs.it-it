---
title: Confronto tra i set di impostazioni di Video Indexer e Servizi multimediali di Azure v3 | Microsoft Docs
description: Questo argomento mette a confronto i set di impostazioni di Video Indexer e di Servizi multimediali di Azure v3.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: juliako
ms.openlocfilehash: e9b66721135456320134f41c614b194d1e22b10f
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49380786"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Confrontare i set di impostazioni di Servizi multimediali di Azure v3 e di Video Indexer 

Questo articolo mette a confronto le funzionalità delle **API di Video Indexer** e delle **API di Servizi multimediali v3**. 

Attualmente è presente una sovrapposizione tra le funzionalità offerte dalle [API di Video Indexer v2](https://api-portal.videoindexer.ai/) e dalle [API di Servizi multimediali v3](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json). La tabella seguente illustra le linee guida correnti per comprendere meglio le differenze e le analogie. 

## <a name="compare"></a>Confronto

|Funzionalità|API di Video Indexer |Set di impostazioni di analisi video e analisi audio<br/>nelle API di Servizi multimediali v3|
|---|---|---|
|Informazioni dettagliate sui contenuti multimediali|[Avanzate](../../cognitive-services/video-indexer/video-indexer-output-json-v2.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json) |[Concetti fondamentali](../latest/intelligence-concept.md)|
|Esperienze|Vedere l'elenco completo delle funzionalità supportate: <br/> [Overview](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json)|Vengono restituite solo informazioni dettagliate sui video|
|Fatturazione|[Prezzi di Servizi multimediali](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Prezzi di Servizi multimediali](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Conformità|Da definire|Servizi multimediali è conforme con molte certificazioni. Aprire il file [Azure Compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) e cercare "Servizi multimediali" per vedere se è conforme con un certificato specifico.|
|Versione di valutazione gratuita|Stati Uniti orientali|Non disponibile|
|Disponibilità |Stati Uniti occidentali, Asia orientale, Europa settentrionale|Vedere [Stato di Azure](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>Passaggi successivi

[Panoramica di Video Indexer](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json)

[Panoramica di Servizi multimediali v3](../../media-services/latest/media-services-overview.md)
