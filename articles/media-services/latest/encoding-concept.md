---
title: Codifica di video e audio con Servizi multimediali
titleSuffix: Azure Media Services
description: Questo articolo illustra la codifica di video e audio con Servizi multimediali di Azure.This article explains about encoding video and audio with Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 6e1c9aa5c2e049d5fc1ebd8bf745417f56d232ec
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366571"
---
# <a name="encoding-video-and-audio-with-media-services"></a>Codifica di video e audio con Servizi multimediali

Il termine codifica in Servizi multimediali si applica al processo di conversione di file contenenti video e/o audio digitali da un formato standard a un altro, allo scopo di (a) ridurre le dimensioni dei file e/o (b) produrre un formato compatibile con un'ampia gamma di dispositivi e app. Questo processo è noto anche come compressione video o transcodifica. Vedere la [compressione dei dati](https://en.wikipedia.org/wiki/Data_compression) e la codifica e la [transcodifica?](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx) per ulteriori informazioni sui concetti.

I video vengono in genere distribuiti a dispositivi e app tramite [download progressivo](https://en.wikipedia.org/wiki/Progressive_download) o tramite [streaming bitrate adattivo.](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)

> [!IMPORTANT]
> Servizi multimediali non fattura i processi annullati o con errori. Ad esempio, un processo che ha raggiunto lo stato di avanzamento del 50% e viene annullato non viene fatturato al 50% dei minuti del processo. Vengono addebitati solo i processi finiti.

* Per eseguire il download progressivo tramite download progressivo, è possibile usare Servizi multimediali di Azure per convertire un file multimediale digitale (mezzanine) in un file [MP4,](https://en.wikipedia.org/wiki/MPEG-4_Part_14) che contiene video codificati con il codec [H.264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) e l'audio codificato con il codec [AAC.](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) Questo file MP4 viene scritto in un asset nell'account di archiviazione. È possibile usare le API di Archiviazione di Azure o gli SDK (ad esempio, [Storage REST API](../../storage/common/storage-rest-api-auth.md) o [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) per scaricare direttamente il file. Se l'asset di output è stato creato con un nome di contenitore specifico nell'archivio, usare tale posizione. In caso contrario, è possibile utilizzare Servizi multimediali per [elencare gli URL dei contenitori](https://docs.microsoft.com/rest/api/media/assets/listcontainersas)di asset. 
* Per preparare il contenuto per la distribuzione mediante streaming a bitrate adattivo, il file mezzanino deve essere codificato a più velocità in bit (dal più al basso). Per garantire una transizione di qualità aggraziata, la risoluzione del video viene abbassata man mano che la velocità in bit viene abbassata. Il risultato è una cosiddetta scala di codifica, ovvero una tabella di risoluzioni e bitrate (vedere la scala a [bit adattiva generata automaticamente](autogen-bitrate-ladder.md)). È possibile utilizzare Servizi multimediali per codificare i file mezzanino a più bitrate. In questo modo, si otterrà un set di file MP4 e file di configurazione di streaming associati scritti in un asset nell'account di archiviazione. È quindi possibile utilizzare la funzionalità [di creazione dinamica](dynamic-packaging-overview.md) di pacchetti in Servizi multimediali per distribuire il video tramite protocolli di streaming come [MPEG-DASH](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) e [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming). Ciò richiede la creazione di un [localizzatore](streaming-locators-concept.md) di streaming e la compilazione di URL di streaming corrispondenti ai protocolli supportati, che possono quindi essere consegnati a dispositivi/app in base alle relative funzionalità.

Nel diagramma seguente viene illustrato il flusso di lavoro per la codifica su richiesta con creazione di pacchetti dinamici.

![Flusso di lavoro per la codifica su richiesta con packaging dinamico](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Questo argomento contiene indicazioni su come codificare il contenuto con Servizi multimediali v3.

## <a name="transforms-and-jobs"></a>Trasformazioni e processi

Per codificare con Servizi multimediali v3, è necessario creare una [trasformazione](https://docs.microsoft.com/rest/api/media/transforms) e un [processo](https://docs.microsoft.com/rest/api/media/jobs). La trasformazione definisce una ricetta per le impostazioni di codifica e gli output; il lavoro è un esempio della ricetta. Per ulteriori informazioni, consultate [Trasformazioni e processi.](transforms-jobs-concept.md)

Durante la codifica con Servizi multimediali si usano set di impostazioni per indicare al codificatore come elaborare i file multimediali di input. In Servizi multimediali v3, si utilizza il codificatore standard per codificare i file. Ad esempio, è possibile specificare la risoluzione video e/o il numero di canali audio desiderati nel contenuto codificato.

Si può iniziare rapidamente con uno dei set di impostazioni predefiniti basati sulle procedure consigliate del settore oppure si può compilare un set di impostazioni personalizzato per i requisiti di uno specifico scenario o dispositivo. Per altre informazioni, vedere l'articolo sulla [codifica con una trasformazione personalizzata](customize-encoder-presets-how-to.md).

A partire da gennaio 2019, durante la codifica con il codificatore standard per produrre file MP4, viene generato un nuovo file mpi che viene aggiunto all'asset di output. Questo file MPI è progettato per migliorare le prestazioni per scenari di [creazione dinamica dei pacchetti](dynamic-packaging-overview.md) e streaming.

> [!NOTE]
> Non è consigliabile modificare o rimuovere il file MPI o prendere alcuna dipendenza dal servizio dall'esistenza (o meno) di tale file.

### <a name="creating-job-input-from-an-https-url"></a>Creazione di input di processo da un URL HTTPSCreating job input from an HTTPS URL

Quando invii Processi per elaborare i tuoi video, devi indicare a Servizi multimediali dove trovare il video di input. Una delle opzioni consiste nello specificare un URL HTTPS come input di processo. Attualmente, Servizi multimediali v3 non supporta la codifica di trasferimento in blocchi tramite URL HTTPS.

#### <a name="examples"></a>Esempi

* [Codifica da un URL HTTPS con .NETEncode from an HTTPS URL with .NET](stream-files-dotnet-quickstart.md)
* [Codifica da un URL HTTPS con RESTEncode from an HTTPS URL with REST](stream-files-tutorial-with-rest.md)
* [Codifica da un URL HTTPS con CLI](stream-files-cli-quickstart.md)
* [Codifica da un URL HTTPS con Node.jsEncode from an HTTPS URL with Node.js](stream-files-nodejs-quickstart.md)

### <a name="creating-job-input-from-a-local-file"></a>Creazione dell'input del processo da un file locale

È possibile archiviare il video di input come asset di Servizi multimediali, nel qual caso si crea un asset di input basato su un file archiviato in locale o nel servizio di Archiviazione BLOB di Azure.

#### <a name="examples"></a>Esempi

[Codificare un file locale utilizzando i predefiniti incorporati](job-input-from-local-file-how-to.md)

### <a name="creating-job-input-with-subclipping"></a>Creazione dell'input del lavoro con il ritaglio secondario

Quando codificate un video, potete specificare di tagliare o ritagliare anche il file sorgente e di produrre un output che abbia solo una parte desiderata del video di input. Questa funzionalità funziona con qualsiasi [trasformazione](https://docs.microsoft.com/rest/api/media/transforms) creata utilizzando i predefiniti [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) o [StandardEncoderPreset.](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)

Potete specificare di creare un [lavoro](https://docs.microsoft.com/rest/api/media/jobs/create) con una singola clip di un video on-demand o un archivio dal vivo (un evento registrato). L'input del processo può essere un Asset o un URL HTTPS.

> [!TIP]
> Se vuoi riprodurre in streaming un sottolipano del video senza ricodificarlo, prendi in considerazione l'uso del [filtro preliminare dei manifesti con Dynamic Packager.](filters-dynamic-manifest-overview.md)

#### <a name="examples"></a>Esempi

Vedi esempi:

* [Ritagliare un video con .NET](subclip-video-dotnet-howto.md)
* [Clip secondario con REST](subclip-video-rest-howto.md)

## <a name="built-in-presets"></a>Set di impostazioni predefiniti

Servizi multimediali supporta i seguenti predefiniti di codifica incorporati:  

### <a name="builtinstandardencoderpreset"></a>BuiltInStandardEncoderPreset

[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) viene usato per configurare un set di impostazioni predefinito per la codifica del video di input con il codificatore Standard.

Attualmente sono supportati i set di impostazioni seguenti:

- **EncoderNamedPreset.AACGoodQualityAudio**: produce un singolo file MP4 contenente solo audio stereo codificato a 192 kbps.
- **EncoderNamedPreset.AdaptiveStreaming** (consigliato): per ulteriori informazioni, vedere [generazione automatica di una scala a bitrate](autogen-bitrate-ladder.md).
- **EncoderNamedPreset.ContentAwareEncodingExperimental**: espone un preimpostato sperimentale per la codifica in grado di riconoscere il contenuto. Dato qualsiasi contenuto di input, il servizio tenta di determinare automaticamente il numero ottimale di layer e le impostazioni di bitrate e risoluzione appropriate per la distribuzione tramite streaming adattivo. Gli algoritmi sottostanti continueranno ad evolversi nel tempo. L'output conterrà file MP4 con interfoliazione video e audio. Per ulteriori informazioni, consultate [Preimpostazione sperimentale per](content-aware-encoding.md)la codifica in base al contenuto.
- **EncoderNamedPreset.H264MultipleBitrate1080p**: produce un set di otto file MP4 allineati a GOP, che vanno da 6000 kbps a 400 kbps e audio AAC stereo. La risoluzione parte da 1080p e può scendere fino a 360p.
- **EncoderNamedPreset.H264MultipleBitrate720p**: produce un set di sei file MP4 allineati a GOP, che vanno da 3400 kbps a 400 kbps e audio AAC stereo. La risoluzione parte da 720p e può scendere fino a 360p.
- **EncoderNamedPreset.H264MultipleBitrateSD**: produce un set di cinque file MP4 allineati a GOP, compresi tra 1600 kbps e 400 kbps e audio AAC stereo. La risoluzione parte da 480p e può scendere fino a 360p.
- **EncoderNamedPreset.H264SingleBitrate1080p**: produce un file MP4 in cui il video è codificato con codec H.264 a 6750 kbps e un'altezza dell'immagine di 1080 pixel e l'audio stereo viene codificato con codec AAC-LC a 64 kbps.
- **EncoderNamedPreset.H264SingleBitrate720p**: produce un file MP4 in cui il video è codificato con codec H.264 a 4500 kbps e un'altezza dell'immagine di 720 pixel e l'audio stereo viene codificato con codec AAC-LC a 64 kbps.
- **EncoderNamedPreset.H264SingleBitrateSD**: produce un file MP4 in cui il video è codificato con codec H.264 a 2200 kbps e un'altezza dell'immagine di 480 pixel e l'audio stereo viene codificato con codec AAC-LC a 64 kbps.

Per visualizzare l'elenco dei predefiniti più aggiornati, consultate Predefiniti incorporati da utilizzare per la [codifica dei video.](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset)

Per vedere come vengono utilizzati i predefiniti, consultate [Caricamento, codifica e streaming](stream-files-tutorial-with-api.md)dei file.

### <a name="standardencoderpreset"></a>StandardEncoderPreset

[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) descrive le impostazioni da usare per la codifica del video di input con il codificatore Standard. Usare questo set di impostazioni quando si personalizzano i set di impostazioni di trasformazione.

#### <a name="considerations"></a>Considerazioni

Quando create predefiniti personalizzati, si applicano le seguenti considerazioni:

- Tutti i valori per altezza e larghezza sul contenuto AVC devono essere un multiplo di quattro.
- In Servizi multimediali di Azure v3, tutte le velocità in bit di codifica sono in bit al secondo. Questo è diverso dai preset con le nostre API v2, che hanno usato kilobit al secondo come unità. Ad esempio, se la velocità in bit 2 è stata specificata come 128 (kilobit/secondo), in v3 verrà impostata su 128000 (bit/secondo).

### <a name="customizing-presets"></a>Personalizzazione dei predefiniti

Servizi multimediali supporta in modo completo la personalizzazione di tutti i valori nei set di impostazioni per soddisfare le esigenze e i requisiti di codifica specifici. Per esempi che illustrano come personalizzare i predefiniti del codificatore, vedere l'elenco seguente:

#### <a name="examples"></a>Esempi

- [Personalizzare i preset con .NET](customize-encoder-presets-how-to.md)
- [Personalizzare i preset con CLI](custom-preset-cli-howto.md)
- [Personalizzare i preset con REST](custom-preset-rest-howto.md)

## <a name="preset-schema"></a>Schema preimpostato

In Servizi multimediali v3, le preimpostazioni sono entità fortemente tipizzate nell'API stessa. È possibile trovare la definizione "schema" per questi oggetti in [Open API Specification (o Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). È inoltre possibile visualizzare le definizioni preimpostate (ad **esempio StandardEncoderPreset**) nell'API [REST](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset), [in .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) (o in altra documentazione di riferimento dell'SDK di Servizi multimediali v3).

## <a name="scaling-encoding-in-v3"></a>Ridimensionamento della codifica nella versione v3

Per ridimensionare l'elaborazione dei supporti, consultate [Scala con CLI.](media-reserved-units-cli-how-to.md)

## <a name="billing"></a>Fatturazione

Servizi multimediali non fattura i processi annullati o con errori. Ad esempio, un processo che ha raggiunto lo stato di avanzamento del 50% e viene annullato non viene fatturato al 50% dei minuti del processo. Vengono addebitati solo i processi finiti.

Per ulteriori informazioni, vedere [prezzi](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="next-steps"></a>Passaggi successivi

* [Caricare, codificare e trasmettere in streaming utilizzando Servizi multimediali.](stream-files-tutorial-with-api.md)
* [Codifica recodifica da un URL HTTPS utilizzando i predefiniti incorporati.](job-input-from-http-how-to.md)
* [Codificare un file locale utilizzando i predefiniti incorporati.](job-input-from-local-file-how-to.md)
* [Creare un predefinito personalizzato per indirizzare lo scenario specifico o i requisiti del dispositivo.](customize-encoder-presets-how-to.md)
