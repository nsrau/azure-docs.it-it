---
title: Eseguire la migrazione dall'indicizzatore V1 e V2 in servizi multimediali di Azure Video Indexer | Microsoft Docs
description: Questo argomento illustra come eseguire la migrazione da Azure Media Indexer V1 e V2 ai servizi multimediali di Azure Video Indexer.
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
ms.date: 09/20/2019
ms.author: juliako
ms.openlocfilehash: 3f6f61523b8509f23ffc98b236daaf0e646a013a
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310425"
---
# <a name="migrate-from-media-indexer-and-media-indexer-2-to-video-indexer"></a>Eseguire la migrazione da Media Indexer e Media Indexer 2 a Video Indexer

Il processore di contenuti multimediali [Azure Media Indexer](media-services-index-content.md) verrà ritirato il 1 ° ottobre del 2020. I processori di contenuti multimediali [Azure Media Indexer Indexer 2 (anteprima)](media-services-process-content-with-indexer2.md) verranno ritirati il 1 ° gennaio 2020.  [Servizi multimediali di Azure video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) sostituisce questi processori di contenuti multimediali legacy.

Servizi multimediali di Azure Video Indexer si basa su Analisi Servizi multimediali di Azure, ricerca di Azure, servizi cognitivi, ad esempio API Viso, Microsoft Translator, il API Visione artificiale e il servizio Riconoscimento vocale personalizzato. Consente di estrarre informazioni dettagliate dai video tramite i modelli audio e video di Video Indexer. Per informazioni sugli scenari in cui è possibile usare Video Indexer, sulle funzionalità offerte e su come iniziare, vedere [video Indexer modelli video e audio](../video-indexer/video-indexer-overview.md). 

È possibile estrarre informazioni dettagliate dai file video e audio usando i [set di impostazioni di Azure Media Services V3 Analyzer](../latest/analyzing-video-audio-files-concept.md) o direttamente usando le [API video Indexer](https://api-portal.videoindexer.ai/). Attualmente esiste una sovrapposizione tra le funzionalità offerte dalle API Video Indexer e le API di servizi multimediali V3.

> [!NOTE]
> Per comprendere quando è opportuno usare Video Indexer invece dei set di impostazioni di analisi di Servizi multimediali, vedere il [documento di confronto](../video-indexer/compare-video-indexer-with-media-services-presets.md). 

Questo articolo illustra i passaggi per eseguire la migrazione dal Azure Media Indexer e Azure Media Indexer 2 a servizi multimediali di Azure Video Indexer.  

## <a name="migration-options"></a>Opzioni di migrazione 

|Se necessario  |quindi |
|---|---|
|soluzione che fornisce una trascrizione vocale per qualsiasi formato di file multimediale nei formati di file sottotitoli codificati: VTT, SRT o TTML<br/>oltre ad altre informazioni dettagliate sull'audio, ad esempio parole chiave, inferenza di argomenti, eventi acustici, previdenza di relatori, estrazione e traduzione di entità| aggiornare le applicazioni in modo da usare le funzionalità di Video Indexer di Azure tramite l'API REST Video Indexer v2 o il set di impostazioni dell'analizzatore audio di servizi multimediali di Azure V3.|
|funzionalità per sintesi vocale| usare direttamente i servizi cognitivi Speech API.|  

## <a name="getting-started-with-video-indexer"></a>Introduzione a Video Indexer

La sezione seguente indica i collegamenti pertinenti: [Come è possibile iniziare a usare Video Indexer?](https://docs.microsoft.com/azure/media-services/video-indexer/video-indexer-overview#how-can-i-get-started-with-video-indexer) 

## <a name="getting-started-with-media-services-v3-apis"></a>Introduzione alle API di servizi multimediali V3

L'API di servizi multimediali di Azure v3 consente di estrarre informazioni dettagliate dai file audio e video tramite i [set di impostazioni di Azure Media Services V3 Analyzer](../latest/analyzing-video-audio-files-concept.md). 

**AudioAnalyzerPreset** consente di estrarre informazioni dettagliate sui contenuti audio da un file audio o video. L'output include un file VTT o TTML per la trascrizione audio e un file JSON (con tutte le informazioni aggiuntive sull'audio). Le informazioni dettagliate includono parole chiave, indicizzazione del parlante e analisi dei sentimenti vocali. AudioAnalyzerPreset supporta anche il rilevamento della lingua per lingue specifiche. Per informazioni dettagliate, vedere [trasformazioni](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset).

### <a name="get-started"></a>Attività iniziali

Per iniziare, vedere:

* [Esercitazione](../latest/analyze-videos-tutorial-with-api.md)
* Esempi di AudioAnalyzerPreset: SDK [Java](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer) o [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
* Esempi di VideoAnalyzerPreset: SDK [Java](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer) o [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/VideoAnalytics/VideoAnalyzer)

## <a name="getting-started-with-cognitive-services-speech-services"></a>Introduzione ai servizi di riconoscimento vocale di servizi cognitivi

[Servizi cognitivi di Azure](https://docs.microsoft.com/azure/cognitive-services/) fornisce un servizio di sintesi vocale che consente di trascrivere flussi audio in testo in tempo reale che le applicazioni, gli strumenti o i dispositivi possono utilizzare o visualizzare. È possibile usare la sintesi vocale per [personalizzare il modello acustico, il modello di linguaggio o il modello di pronuncia](../../cognitive-services/speech-service/how-to-custom-speech-train-model.md). Per altre informazioni, vedere [riconoscimento vocale di servizi cognitivi](../../cognitive-services/speech-service/speech-to-text.md). 

> [!NOTE] 
> Il servizio di riconoscimento vocale non accetta formati di file video e accetta solo [determinati formati audio](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text#audio-formats). 

Per altre informazioni sul servizio di sintesi vocale e su come iniziare, vedere [che cos'è la sintesi vocale?](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-to-text)

## <a name="known-differences-from-deprecated-services"></a>Differenze note da servizi deprecati 

Si noterà che Video Indexer, servizi multimediali di Azure V3 AudioAnalyzerPreset e servizi cognitivi servizi vocali sono più affidabili e produce un output di qualità migliore rispetto ai processori Azure Media Indexer 1 e Azure Media Indexer 2 ritirati.  

Di seguito sono riportate alcune differenze note: 

* I file di didascalia chiusi in formato SAMI non saranno più supportati. Si tratta di un formato di didascalia obsoleto che non viene più usato. Viene sostituita da TTML, WebVTT e SRT.  
* I file BLOB di indicizzazione audio (AIB) non saranno più supportati. Questa funzionalità era specifica per la tecnologia dell'indicizzatore 1 e non è più disponibile.  
* Servizi cognitivi (Speech Services) non supporta l'estrazione di parole chiave. Tuttavia, Video Indexer e Media Services V3 AudioAnalyzerPreset offrono un set più solido di parole chiave in formato di file JSON. 

## <a name="contact-us"></a>Contattaci 

In caso di domande sulla deprecazione di Azure Media Indexer 1 e Azure Media Indexer 2 o sulla migrazione ad altri servizi, contattare Microsoft tramiteamshelp@microsoft.com 

## <a name="next-steps"></a>Passaggi successivi

* [Componenti legacy](legacy-components.md)
* [Pagina dei prezzi](https://azure.microsoft.com/pricing/details/media-services/#encoding)


