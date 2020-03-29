---
title: Eseguire la migrazione dall'indicizzatore v1 e v2 all'indicizzatore video di Servizi multimediali di Azure. Documenti Microsoft
description: Questo argomento illustra come eseguire la migrazione da Azure Media Indexer v1 e v2 all'indicizzatore video di Servizi multimediali di Azure.This topic discusses how to migrate from Azure Media Indexer v1 and v2 to Azure Media Services Video Indexer.
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
ms.openlocfilehash: 2268c074480f99ca23117ca2ffd2c87c1dbb10a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513236"
---
# <a name="migrate-from-media-indexer-and-media-indexer-2-to-video-indexer"></a>Eseguire la migrazione da Media Indexer e Media Indexer 2 all'indicizzatore videoMigrate from Media Indexer and Media Indexer 2 to Video Indexer

Il processore multimediale [dell'indicizzatore multimediale multimediale di Azure Media](media-services-index-content.md) e i processori multimediali di anteprima di [Azure Media Indexer 2](media-services-process-content-with-indexer2.md) vengono ritirati. Per le date di pensionamento, vedere questo argomento relativo ai [componenti legacy.](legacy-components.md) [L'indicizzatore video di Servizi multimediali di Azure](https://docs.microsoft.com/azure/media-services/video-indexer/) sostituisce questi processori multimediali legacy.

L'indicizzatore video di Servizi multimediali di Azure è basato su Azure Media Analytics, Ricerca cognitiva di Azure, Servizi cognitivi (ad esempio l'API Face, Microsoft Translator, l'API Visione artificiale e il servizio di riconoscimento vocale personalizzato). Consente di estrarre informazioni dettagliate dai video tramite i modelli audio e video di Video Indexer. Per vedere in quali scenari è possibile utilizzare Video Indexer, quali funzionalità offre e come iniziare, consultate [Modelli video e audio dell'indicizzatore video.](../video-indexer/video-indexer-overview.md) 

È possibile estrarre informazioni dettagliate dai file video e audio usando i [predefiniti dell'analizzatore](../latest/analyzing-video-audio-files-concept.md) di Servizi multimediali di Azure o direttamente usando [le API dell'indicizzatore video](https://api-portal.videoindexer.ai/). Attualmente, esiste una sovrapposizione tra le funzionalità offerte dalle API dell'indicizzatore video e le API di Servizi multimediali v3.

> [!NOTE]
> Per capire quando si desidera utilizzare i predefiniti dell'analizzatore Video Indexer e Media Services, consultare il documento di [confronto.](../video-indexer/compare-video-indexer-with-media-services-presets.md) 

Questo articolo illustra i passaggi per la migrazione dall'indicizzatore multimediale di Azure e dall'indicizzatore multimediale di Azure 2 all'indicizzatore video di Servizi multimediali di Azure.This article discusses the steps for migrating from the Azure Media Indexer and Azure Media Indexer 2 to Azure Media Services Video Indexer.  

## <a name="migration-options"></a>Opzioni di migrazione 

|Se si richiede  |quindi |
|---|---|
|una soluzione che fornisce una trascrizione vocale per qualsiasi formato di file multimediale in un formato di file di sottotitoli codificati: VTT, SRT o TTML<br/>così come ulteriori approfondimenti audio come: parole chiave, inferenza degli argomenti, eventi acustici, diarizzazione degli altoparlanti, estrazione e traduzione di entità| aggiornare le applicazioni in modo che utilizzino le funzionalità dell'indicizzatore video di Azure tramite l'API REST dell'indicizzatore video v2 o la configurazione dell'analizzatore audio di Servizi multimediali di Azure.|
|funzionalità di sintesi vocale| utilizzare direttamente l'API di riconoscimento vocale di Servizi cognitivi.|  

## <a name="getting-started-with-video-indexer"></a>Introduzione all'indicizzatore video

La sezione seguente indica i collegamenti pertinenti: [Come è possibile iniziare a utilizzare Video Indexer?](https://docs.microsoft.com/azure/media-services/video-indexer/video-indexer-overview#how-can-i-get-started-with-video-indexer) 

## <a name="getting-started-with-media-services-v3-apis"></a>Introduzione alle API di Servizi multimediali v3

L'API di Servizi multimediali di Azure consente di estrarre informazioni dettagliate dai file video e audio tramite [i predefiniti dell'analizzatore di Servizi multimediali](../latest/analyzing-video-audio-files-concept.md)di Azure. 

**AudioAnalyzerPreset** consente di estrarre informazioni dettagliate sui contenuti audio da un file audio o video. L'output include un file VTT o TTML per la trascrizione audio e un file JSON (con tutte le informazioni audio aggiuntive). Le informazioni audio includono parole chiave, indicizzazione degli altoparlanti e analisi del sentiment del parlato. AudioAnalyzerPreset supporta anche il rilevamento della lingua per lingue specifiche. Per informazioni [dettagliate, consultate Trasformazioni.](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset)

### <a name="get-started"></a>Introduzione

Per un'introduzione, vedere:

* [Esercitazione](../latest/analyze-videos-tutorial-with-api.md)
* Esempi di AudioAnalyzerPreset: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer) o [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
* Esempi di VideoAnalyzerPreset: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer) o [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/VideoAnalytics/VideoAnalyzer)

## <a name="getting-started-with-cognitive-services-speech-services"></a>Introduzione ai servizi di riconoscimento vocale di Servizi cognitivi

[Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/) di Azure offre un servizio di sintesi vocale che trascrive flussi audio in testo in tempo reale che le applicazioni, gli strumenti o i dispositivi possono usare o visualizzare. È possibile utilizzare la sintesi vocale per [personalizzare il proprio modello acustico, un modello linguistico o](../../cognitive-services/speech-service/how-to-custom-speech-train-model.md)un modello di pronuncia. Per ulteriori informazioni, vedere [Servizi cognitivi sintesi vocale](../../cognitive-services/speech-service/speech-to-text.md). 

> [!NOTE] 
> Il servizio di sintesi vocale non accetta formati di file video e accetta solo [determinati formati audio.](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text#audio-formats) 

Per altre informazioni sul servizio di sintesi vocale e su come iniziare, vedere [Che cos'è la sintesi vocale?](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-to-text)

## <a name="known-differences-from-deprecated-services"></a>Differenze note dai servizi deprecati 

Si scoprirà che l'indicizzatore video, i servizi AudioAnalyzerPreset di Servizi multimediali di Azure v3 e i servizi di riconoscimento vocale di Servizi cognitivi sono più affidabili e producono un output di qualità migliore rispetto ai processori di Azure Media Indexer 1 e Azure Media Indexer 2 ritirati.  

Alcune differenze note includono: 

* Servizi cognitivi di riconoscimento vocale non supporta l'estrazione di parole chiave. Tuttavia, Video Indexer e Media Services v3 AudioAnalyzerPreset offrono entrambi un set più affidabile di parole chiave in formato di file JSON. 

## <a name="need-help"></a>Richiesta di assistenza

È possibile aprire un ticket di supporto passando a [Nuova richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>Passaggi successivi

* [Componenti legacy](legacy-components.md)
* [Pagina dei prezzi](https://azure.microsoft.com/pricing/details/media-services/#encoding)


