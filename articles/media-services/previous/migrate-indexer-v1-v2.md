---
title: Eseguire la migrazione dall'indicizzatore V1 e V2 in servizi multimediali di Azure Video Indexer | Microsoft Docs
description: Questo argomento illustra come eseguire la migrazione da Azure Media Indexer V1 e V2 ai servizi multimediali di Azure Video Indexer.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2020
ms.author: inhenkel
ms.openlocfilehash: 330bffebb870635fd473e88a8eadb300eed40b9b
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518297"
---
# <a name="migrate-from-media-indexer-and-media-indexer-2-to-video-indexer"></a>Eseguire la migrazione da Media Indexer e Media Indexer 2 a Video Indexer

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!IMPORTANT]
> Si consiglia ai clienti di eseguire la migrazione dall'indicizzatore V1 e dall'indicizzatore V2 a usando la [modalità Basic di servizi multimediali V3 AudioAnalyzerPreset](../latest/analyzing-video-audio-files-concept.md). Il processore di contenuti multimediali [Azure Media Indexer](media-services-index-content.md) e i processori di [Azure Media Indexer 2 Preview](./legacy-components.md) sono in fase di ritiro. Per le date di ritiro, vedere questo argomento relativo ai [componenti legacy](legacy-components.md).

Servizi multimediali di Azure Video Indexer si basa su Analisi Servizi multimediali di Azure, ricerca cognitiva di Azure, servizi cognitivi, ad esempio il API Viso, Microsoft Translator, il API Visione artificiale e il servizio Riconoscimento vocale personalizzato. Consente di estrarre informazioni dettagliate dai video tramite i modelli audio e video di Video Indexer. Per informazioni sugli scenari in cui è possibile usare Video Indexer, sulle funzionalità offerte e su come iniziare, vedere [video Indexer modelli video e audio](../video-indexer/video-indexer-overview.md). 

È possibile estrarre informazioni dettagliate dai file video e audio usando i [set di impostazioni di Azure Media Services V3 Analyzer](../latest/analyzing-video-audio-files-concept.md) o direttamente usando le [API video Indexer](https://api-portal.videoindexer.ai/). Attualmente esiste una sovrapposizione tra le funzionalità offerte dalle API Video Indexer e le API di servizi multimediali V3.

> [!NOTE]
> Per comprendere le differenze tra i set di impostazioni Video Indexer vs. Media Services Analyzer, vedere il [documento di confronto](../video-indexer/compare-video-indexer-with-media-services-presets.md).

Questo articolo illustra i passaggi per eseguire la migrazione dal Azure Media Indexer e Azure Media Indexer 2 a servizi multimediali di Azure Video Indexer.  

## <a name="migration-options"></a>Opzioni di migrazione

|Se necessario  |quindi |
|---|---|
|soluzione che fornisce una trascrizione di sintesi vocale per qualsiasi formato di file multimediale nei formati di file sottotitoli codificati: VTT, SRT o TTML<br/>oltre ad altre informazioni dettagliate sull'audio, ad esempio parole chiave, inferenza di argomenti, eventi acustici, previdenza di relatori, estrazione e traduzione di entità| aggiornare le applicazioni in modo da usare le funzionalità di Video Indexer di Azure tramite l'API REST Video Indexer v2 o il set di impostazioni dell'analizzatore audio di servizi multimediali di Azure V3.|
|funzionalità per sintesi vocale| usare direttamente i servizi cognitivi Speech API.|  

## <a name="getting-started-with-video-indexer"></a>Introduzione a Video Indexer

La sezione seguente illustra i collegamenti pertinenti: [come è possibile iniziare a usare video Indexer?](../video-indexer/video-indexer-overview.md#how-can-i-get-started-with-video-indexer) 

## <a name="getting-started-with-media-services-v3-apis"></a>Introduzione alle API di servizi multimediali V3

L'API di servizi multimediali di Azure v3 consente di estrarre informazioni dettagliate dai file audio e video tramite i [set di impostazioni di Azure Media Services V3 Analyzer](../latest/analyzing-video-audio-files-concept.md).

**AudioAnalyzerPreset** consente di estrarre informazioni dettagliate sui contenuti audio da un file audio o video. L'output include un file VTT o TTML per la trascrizione audio e un file JSON (con tutte le informazioni aggiuntive sull'audio). Le informazioni dettagliate includono parole chiave, indicizzazione del parlante e analisi dei sentimenti vocali. AudioAnalyzerPreset supporta anche il rilevamento della lingua per lingue specifiche. Per informazioni dettagliate, vedere [trasformazioni](/rest/api/media/transforms/createorupdate#audioanalyzerpreset).

### <a name="get-started"></a>Introduzione

Per un'introduzione, vedere:

* [Esercitazione](../latest/analyze-videos-tutorial-with-api.md)
* Esempi di AudioAnalyzerPreset: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer) o [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
* Esempi di VideoAnalyzerPreset: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer) o [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/VideoAnalytics/VideoAnalyzer)

## <a name="getting-started-with-cognitive-services-speech-services"></a>Introduzione ai servizi di riconoscimento vocale di servizi cognitivi

[Servizi cognitivi di Azure](../../cognitive-services/index.yml) fornisce un servizio di sintesi vocale che consente di trascrivere flussi audio in testo in tempo reale che le applicazioni, gli strumenti o i dispositivi possono utilizzare o visualizzare. È possibile usare la sintesi vocale per [personalizzare il modello acustico, il modello di linguaggio o il modello di pronuncia](../../cognitive-services/speech-service/how-to-custom-speech-train-model.md). Per altre informazioni, vedere [riconoscimento vocale di servizi cognitivi](../../cognitive-services/speech-service/speech-to-text.md). 

> [!NOTE] 
> Il servizio di riconoscimento vocale non accetta formati di file video e accetta solo [determinati formati audio](../../cognitive-services/speech-service/rest-speech-to-text.md#audio-formats). 

Per altre informazioni sul servizio di sintesi vocale e su come iniziare, vedere [che cos'è la sintesi vocale?](../../cognitive-services/speech-service/speech-to-text.md)

## <a name="known-differences-from-deprecated-services"></a>Differenze note da servizi deprecati

Si noterà che Video Indexer, servizi multimediali di Azure V3 AudioAnalyzerPreset e servizi cognitivi servizi vocali sono più affidabili e produce un output di qualità migliore rispetto ai processori Azure Media Indexer 1 e Azure Media Indexer 2 ritirati.  

Di seguito sono riportate alcune differenze note:

* Servizi cognitivi (Speech Services) non supporta l'estrazione di parole chiave. Tuttavia, Video Indexer e Media Services V3 AudioAnalyzerPreset offrono un set più solido di parole chiave in formato di file JSON.

## <a name="support"></a>Supporto

È possibile aprire un ticket di supporto passando a [Nuova richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>Passaggi successivi

* [Componenti legacy](legacy-components.md)
* [Pagina dei prezzi](https://azure.microsoft.com/pricing/details/media-services/#encoding)