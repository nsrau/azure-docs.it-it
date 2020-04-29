---
title: Componenti legacy di servizi multimediali di Azure | Microsoft Docs
description: Questo argomento illustra i componenti legacy di servizi multimediali di Azure.
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
ms.date: 02/27/2020
ms.author: juliako
ms.openlocfilehash: 94a70a1234d902787f248890f0cb538a4ba9c2f9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77921080"
---
# <a name="azure-media-services-legacy-components"></a>Componenti legacy di servizi multimediali di Azure

Nel corso del tempo sono stati apportati miglioramenti e miglioramenti costanti ai componenti di servizi multimediali. Di conseguenza, alcuni componenti legacy sono stati ritirati. È possibile trovare le istruzioni su come eseguire la migrazione dell'applicazione dal componente legacy a un componente corrente negli articoli seguenti.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Piani di ritiro dei componenti legacy e informazioni aggiuntive sulla migrazione

Microsoft sta annunciando la deprecazione dei processori di contenuti multimediali *Windows Azure Media Encoder* (biaigi) e *Azure Media Encoder* (AME). Questi processori verranno ritirati il 31 marzo 2020.

* [Eseguire la migrazione da Azure Media Encoder Windows a Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Eseguire la migrazione da Azure Media Encoder a Media Encoder Standard](migrate-azure-media-encoder.md)

Viene inoltre annunciato il ritiro dei seguenti Analisi Servizi multimediali processori di contenuti multimediali: 
 
|Nome processore di contenuti multimediali|Data di ritiro|Note aggiuntive|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)|1 ° gennaio 2020|Questo processore di contenuti multimediali viene sostituito da [servizi multimediali di Azure video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Per altre informazioni, vedere [eseguire la migrazione da Azure Media Indexer 2 a servizi multimediali di Azure video Indexer](migrate-indexer-v1-v2.md).|
|[Azure Media Indexer](media-services-index-content.md)|1 marzo 2023|Questo processore di contenuti multimediali viene sostituito da [servizi multimediali di Azure video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Per altre informazioni, vedere [eseguire la migrazione da Azure Media Indexer a servizi multimediali di Azure video Indexer](migrate-indexer-v1-v2.md)|
|[Rilevamento movimento](media-services-motion-detection.md)|1 ° giugno 2020|Al momento non sono disponibili piani di sostituzione.|
|[Riepilogo video](media-services-video-summarization.md)|1 ° giugno 2020|Al momento non sono disponibili piani di sostituzione.|
|[Riconoscimento ottico dei caratteri video](media-services-video-optical-character-recognition.md)|1 ° giugno 2020|Questo processore di contenuti multimediali viene sostituito da [servizi multimediali di Azure video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Si consiglia inoltre di usare l' [API di servizi multimediali di Azure V3](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept). <br/>Vedere [confrontare i set di impostazioni di servizi multimediali di Azure V3 e video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|
|[Rilevamento facciale](media-services-face-and-emotion-detection.md)|1 ° giugno 2020|Questo processore di contenuti multimediali viene sostituito da [servizi multimediali di Azure video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Si consiglia inoltre di usare l' [API di servizi multimediali di Azure V3](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept). <br/>Vedere [confrontare i set di impostazioni di servizi multimediali di Azure V3 e video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|
|[Content Moderator](media-services-content-moderation.md)|1 ° giugno 2020|Questo processore di contenuti multimediali viene sostituito da [servizi multimediali di Azure video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Si consiglia inoltre di usare l' [API di servizi multimediali di Azure V3](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept). <br/>Vedere [confrontare i set di impostazioni di servizi multimediali di Azure V3 e video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|

## <a name="next-steps"></a>Passaggi successivi

[Materiale sussidiario sulla migrazione per aggiornare Servizi multimediali da v2 a v3](../latest/migrate-from-v2-to-v3.md)
