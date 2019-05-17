---
title: Codifica nel cloud con Servizi multimediali - Azure | Microsoft Docs
description: Questo argomento descrive il processo di codifica con Servizi multimediali di Microsoft Azure
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 25b3209bed98ea217db9e414caa6f08cee6d8c89
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65761900"
---
# <a name="encoding-with-media-services"></a>Codifica con Servizi multimediali

La codifica di termini in servizi multimediali si applica al processo di conversione di file contenenti video digitali e/o audio da un formato standard a un altro, con lo scopo di (a) di riduzione delle dimensioni dei file e/o (b) che produce un formato compatibile con un ampia gamma di dispositivi e applicazioni. Questo processo viene anche definito come la compressione video o la transcodifica. Vedere le [compressione dei dati](https://en.wikipedia.org/wiki/Data_compression) e il [What ' s codifica e transcodifica?](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx) per ulteriori informazioni sui concetti.

I video sono tipicamente fruibili per i dispositivi e per le applicazioni vengono [download progressivo](https://en.wikipedia.org/wiki/Progressive_download) o tramite [streaming a bitrate adattivo](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming). 

* Per il recapito tramite download progressivo, è possibile usare servizi multimediali di Azure per convertire un il file multimediale digitale (in formato intermedio) in un' [MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14) file che contiene i video in cui è stato codificato con la [H.264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) codec, e audio che è stato codificato con la [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) codec. Questo file MP4 viene scritto in un Asset nell'account di archiviazione. È possibile usare gli SDK o l'API di archiviazione di Azure (ad esempio, [API REST di archiviazione](../../storage/common/storage-rest-api-auth.md), [JAVA SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md), o [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) per scaricare direttamente il file. Se è stato creato l'output Asset con un nome di contenitore specifico nell'archiviazione, usare quel percorso. In caso contrario, è possibile usare servizi multimediali [elencare gli URL di contenitore di asset](https://docs.microsoft.com/rest/api/media/assets/listcontainersas). 
* Per preparare il contenuto per il recapito mediante streaming a bitrate adattivo, i file in formato intermedio deve essere con codifica a bitrate multipli (alto al basso). Per assicurarsi che la transizione normale di qualità, come si riducono la velocità in bit, pertanto è la risoluzione del video. Ciò comporta un cosiddetto ladder codifica: una tabella di risoluzioni e velocità in bit (vedere [ladder velocità in bit adattiva autogenerato](autogen-bitrate-ladder.md)). È possibile usare servizi multimediali per codificare i file in formato intermedio in bitrate multipli: in questo modo, si otterrà un set di file MP4 e streaming configuration file associati, scritti in un Asset nell'account di archiviazione. È quindi possibile usare la [creazione dinamica dei pacchetti](dynamic-packaging-overview.md) funzionalità in servizi multimediali per distribuire il video tramite protocolli, ad esempio di streaming [MPEG-DASH](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) e [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming). Ciò richiede la creazione di un [localizzatore di Streaming](streaming-locators-concept.md) e creare URL corrispondente al protocollo supportato, che può essere passato a dispositivi o applicazioni in base alle loro capacità di streaming.

Il diagramma seguente illustra il flusso di lavoro per la codifica su richiesta con creazione dinamica dei pacchetti.

![Creazione dinamica dei pacchetti](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

Questo argomento contiene indicazioni su come codificare il contenuto con Servizi multimediali v3.

## <a name="transforms-and-jobs"></a>Trasformazioni e processi

Per eseguire la codifica con Servizi multimediali v3, è necessario creare una [trasformazione](https://docs.microsoft.com/rest/api/media/transforms) e un [processo](https://docs.microsoft.com/rest/api/media/jobs). La trasformazione definisce una ricetta per le impostazioni di codifica e l'output; il processo è un'istanza del file recipe. Per altre informazioni, vedere [Trasformazioni e processi](transforms-jobs-concept.md)

Durante la codifica con Servizi multimediali si usano set di impostazioni per indicare al codificatore come elaborare i file multimediali di input. Ad esempio, è possibile specificare la risoluzione video e/o il numero di canali audio desiderati nel contenuto codificato. 

Si può iniziare rapidamente con uno dei set di impostazioni predefiniti basati sulle procedure consigliate del settore oppure si può compilare un set di impostazioni personalizzato per i requisiti di uno specifico scenario o dispositivo. Per altre informazioni, vedere l'articolo sulla [codifica con una trasformazione personalizzata](customize-encoder-presets-how-to.md). 

A partire da gennaio 2019, durante la codifica con Media Encoder Standard per produrre file MP4, un nuovo file con estensione mpi viene generato e aggiunto all'asset di output. Questo file MPI è progettato per migliorare le prestazioni per scenari di [creazione dinamica dei pacchetti](dynamic-packaging-overview.md) e streaming.

> [!NOTE]
> Non è consigliabile modificare o rimuovere il file MPI né creare dipendenze nel proprio servizio sull'esistenza o meno di tale file.

## <a name="built-in-presets"></a>Set di impostazioni predefiniti

Attualmente Servizi multimediali supporta i set di impostazioni di codifica predefiniti seguenti:  

### <a name="builtinstandardencoderpreset-preset"></a>Set di impostazioni BuiltInStandardEncoderPreset

[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) viene usato per configurare un set di impostazioni predefinito per la codifica del video di input con il codificatore Standard. 

Attualmente sono supportati i set di impostazioni seguenti:

- **EncoderNamedPreset.AACGoodQualityAudio**: genera un singolo file MP4 contenente solo audio stereo con codifica a 192 kbps.
- **EncoderNamedPreset.AdaptiveStreaming** (scelta consigliata). Per altre informazioni, vedere [Codificare con una tabella di coppie velocità in bit-risoluzione generata automaticamente](autogen-bitrate-ladder.md).
- **EncoderNamedPreset.ContentAwareEncodingExperimental** -espone un set di impostazioni sperimentale per la codifica compatibile con il contenuto. Dato qualsiasi contenuto di input, il servizio tenta di determinare automaticamente il numero ottimale di livelli, con velocità in bit appropriati e le impostazioni di risoluzione per il recapito per lo streaming adattivo. Gli algoritmi sottostanti continuerà a evolversi nel tempo. L'output conterrà file MP4 con video e audio con interfogliati. Per altre informazioni, vedere [sperimentale set di impostazioni per la codifica compatibile con contenuto](cae-experimental.md).
- **EncoderNamedPreset.H264MultipleBitrate1080p**: genera un set di 8 file MP4 con audio AAC stereo e allineamento GOP, con velocità compresa fra 6000 e 400 kbps. La risoluzione parte da 1080p e può scendere fino a 360p.
- **EncoderNamedPreset.H264MultipleBitrate720p**: genera un set di 6 file MP4 con audio AAC stereo e allineamento GOP, con velocità compresa fra 3400 e 400 kbps. La risoluzione parte da 720p e può scendere fino a 360p.
- **EncoderNamedPreset.H264MultipleBitrateSD**: genera un set di 5 file MP4 con audio AAC stereo e allineamento GOP, con velocità compresa fra 1600 e 400 kbps. La risoluzione parte da 480p e può scendere fino a 360p.
- **EncoderNamedPreset.H264SingleBitrate1080p** -genera un file MP4 in cui il video è codificato con codec H.264 a 6750 kbps e un'altezza immagine di 1080 pixel e l'audio stereo è codificato con codec AAC-LC a 64 kbps.
- **EncoderNamedPreset.H264SingleBitrate720p** -genera un file MP4 in cui il video è codificato con codec H.264 a 4500 kbps e un'altezza immagine pari a 720 pixel e l'audio stereo è codificato con codec AAC-LC a 64 kbps.
- **EncoderNamedPreset.H264SingleBitrateSD** -genera un file MP4 in cui il video è codificato con codec H.264 a 2200 kbps e un'altezza immagine di 480 pixel e l'audio stereo è codificato con codec AAC-LC a 64 kbps.

Per visualizzare l'elenco di set di impostazioni più aggiornata, vedere [predefiniti incorporati da utilizzare per la codifica video](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset).

Per vedere come vengono usati i set di impostazioni, consultare [caricamento, codifica e streaming di file](stream-files-tutorial-with-api.md).

### <a name="standardencoderpreset-preset"></a>Set di impostazioni StandardEncoderPreset

[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) descrive le impostazioni da usare per la codifica del video di input con il codificatore Standard. Usare questo set di impostazioni quando si personalizzano i set di impostazioni di trasformazione. 

#### <a name="considerations"></a>Considerazioni

Durante la creazione di set di impostazioni personalizzati, si applicano le considerazioni seguenti:

- Tutti i valori per l'altezza e larghezza su contenuto AVC devono essere un multiplo di 4.
- In servizi multimediali di Azure v3, tutte le velocità in bit di codifica sono in bit al secondo. Ciò è diverso dal set di impostazioni con le nostre API v2, che usato kilobit al secondo come unità. Ad esempio, se la velocità in bit nella versione 2 è stato specificato come 128 (kilobit/sec), in v3 valore potrebbe essere impostato su 128000 (bit/sec).

#### <a name="examples"></a>Esempi

Servizi multimediali supporta in modo completo la personalizzazione di tutti i valori nei set di impostazioni per soddisfare le esigenze e i requisiti di codifica specifici. Per esempi che illustrano come personalizzare i set di impostazioni del codificatore, vedere:

- [Personalizzare i set di impostazioni con .NET](customize-encoder-presets-how-to.md)
- [Personalizzare i set di impostazioni con CLI](custom-preset-cli-howto.md)
- [Personalizzare i set di impostazioni con REST](custom-preset-rest-howto.md)

## <a name="preset-schema"></a>Schema predefinito

In servizi multimediali v3, predefiniti sono entità fortemente tipizzate nell'API di se stesso. È possibile trovare la definizione di "schema" per questi oggetti in [specifica Openapi (o Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). È anche possibile visualizzare le definizioni di set di impostazioni (ad esempio **StandardEncoderPreset**) nel [API REST](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset), [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) (o altra documentazione di riferimento SDK di servizi multimediali v3).

## <a name="scaling-encoding-in-v3"></a>Ridimensionamento della codifica nella versione v3

Per ridimensionare l'elaborazione multimediale, vedere [scalabilità con CLI](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire la codifica da un URL HTTPS usando i set di impostazioni predefinite](job-input-from-http-how-to.md)
* [Codificare un file locale usando i set di impostazioni predefinite](job-input-from-local-file-how-to.md)
* [Creare un set di impostazioni per i requisiti specifici di uno scenario o un dispositivo di destinazione personalizzato](customize-encoder-presets-how-to.md)
* [Eseguire il caricamento, la codifica e lo streaming con Servizi multimediali](stream-files-tutorial-with-api.md)
