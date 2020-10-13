---
title: Componenti legacy di servizi multimediali di Azure | Microsoft Docs
description: Questo argomento illustra i componenti legacy di servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: myoungerman
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2020
ms.author: v-myoung
ms.openlocfilehash: d5a10ccc887124b2bc5595d66baad833a32275cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90600892"
---
# <a name="azure-media-services-legacy-components"></a>Componenti legacy di servizi multimediali di Azure

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Nel tempo, i componenti di servizi multimediali vengono migliorati e i componenti legacy vengono ritirati. Questo articolo consente di eseguire la migrazione dell'applicazione da un componente legacy a un componente corrente.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Piani di ritiro dei componenti legacy e informazioni aggiuntive sulla migrazione

I processori di contenuti multimediali *Windows Azure Media Encoder* (biaigi) e *Azure Media Encoder* (AME) sono deprecati.

* [Eseguire la migrazione da Azure Media Encoder Windows a Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Eseguire la migrazione da Azure Media Encoder a Media Encoder Standard](migrate-azure-media-encoder.md)

Gli Analisi Servizi multimediali processori di contenuti multimediali seguenti sono deprecati o saranno presto deprecati:  
 
|Nome processore di contenuti multimediali|Data di ritiro|Note aggiuntive|
|---|---|
|Azure Media Indexer 2|1 ° gennaio 2020|Questo processore di contenuti multimediali è stato sostituito da [servizi multimediali di Azure video Indexer](../video-indexer/index.yml). Per altre informazioni, vedere [eseguire la migrazione da Azure Media Indexer 2 a servizi multimediali di Azure video Indexer](migrate-indexer-v1-v2.md).|
|[Azure Media Indexer](media-services-index-content.md)|1 marzo 2023|Questo processore di contenuti multimediali verrà sostituito da [servizi multimediali di Azure video Indexer](../video-indexer/index.yml). Per altre informazioni, vedere [eseguire la migrazione da Azure Media Indexer a servizi multimediali di Azure video Indexer](migrate-indexer-v1-v2.md).|
|Rilevamento movimento|1 ° giugno 2020|Al momento non sono disponibili piani di sostituzione.|
|Riepilogo video] (Media-Services-video-summarization.md|1 ° giugno 2020|Al momento non sono disponibili piani di sostituzione.|
|Riconoscimento ottico dei caratteri video|1 ° giugno 2020|Questo processore di contenuti multimediali è stato sostituito da [servizi multimediali di Azure video Indexer](../video-indexer/index.yml). Si consiglia inoltre di usare l' [API di servizi multimediali di Azure V3](../latest/analyzing-video-audio-files-concept.md). <br/>Vedere [confrontare i set di impostazioni e video indexer di servizi multimediali di Azure V3](../video-indexer/compare-video-indexer-with-media-services-presets.md).|
|Face Detector|1 ° giugno 2020|Questo processore di contenuti multimediali è stato sostituito da [servizi multimediali di Azure video Indexer](../video-indexer/index.yml). Si consiglia inoltre di usare l' [API di servizi multimediali di Azure V3](../latest/analyzing-video-audio-files-concept.md). <br/>Vedere [confrontare i set di impostazioni e video indexer di servizi multimediali di Azure V3](../video-indexer/compare-video-indexer-with-media-services-presets.md).|
|Content Moderator|1 ° giugno 2020|Questo processore di contenuti multimediali è stato sostituito da [servizi multimediali di Azure video Indexer](../video-indexer/index.yml). Si consiglia inoltre di usare l' [API di servizi multimediali di Azure V3](../latest/analyzing-video-audio-files-concept.md). <br/>Vedere [confrontare i set di impostazioni e video indexer di servizi multimediali di Azure V3](../video-indexer/compare-video-indexer-with-media-services-presets.md).|

## <a name="next-steps"></a>Passaggi successivi

[Materiale sussidiario sulla migrazione per aggiornare Servizi multimediali da v2 a v3](../latest/migrate-from-v2-to-v3.md)
