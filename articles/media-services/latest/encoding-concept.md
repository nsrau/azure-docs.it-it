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
ms.date: 02/27/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: de2c60d4449762c4a8fcc3e2f486130f3df37c7c
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243620"
---
# <a name="encoding-with-media-services"></a>Codifica con Servizi multimediali

Servizi multimediali di Azure consente di codificare i file multimediali digitali di alta qualità in file MP4 a bitrate adattivo in modo che i contenuti possono essere riprodotti su un'ampia gamma di browser e dispositivi. Un processo di codifica di servizi multimediali ha esito positivo viene creato un output Asset con un set di velocità in bit adattiva MP4s e streaming i file di configurazione. I file di configurazione includono ISM, con estensione ismc, .mpi e altri file che non deve essere modificato. Al termine il processo di codifica, è possibile sfruttare [creazione dinamica dei pacchetti](dynamic-packaging-overview.md) e avviare lo streaming.

Per rendere i video nell'output di Asset disponibili per i client per la riproduzione, è necessario creare un **localizzatore di Streaming** e creare URL di streaming. Quindi, in base al formato specificato nel manifesto, il client ricevano il flusso nel protocollo prescelto.

Il diagramma seguente illustra lo streaming on demand con flusso di lavoro di creazione dinamica dei pacchetti.

![Creazione dinamica dei pacchetti](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Questo argomento contiene indicazioni su come codificare il contenuto con Servizi multimediali v3.

## <a name="transforms-and-jobs"></a>Trasformazioni e processi

Per eseguire la codifica con Servizi multimediali v3, è necessario creare una [trasformazione](https://docs.microsoft.com/rest/api/media/transforms) e un [processo](https://docs.microsoft.com/rest/api/media/jobs). Una trasformazione definisce la serie di istruzioni per le impostazioni di codifica e gli output e il processo è un'istanza della serie di istruzioni. Per altre informazioni, vedere [Trasformazioni e processi](transforms-jobs-concept.md)

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

- **EncoderNamedPreset.AdaptiveStreaming** (scelta consigliata). Per altre informazioni, vedere [Codificare con una tabella di coppie velocità in bit-risoluzione generata automaticamente](autogen-bitrate-ladder.md).
- **EncoderNamedPreset.AACGoodQualityAudio**: genera un singolo file MP4 contenente solo audio stereo con codifica a 192 kbps.
- **EncoderNamedPreset.H264MultipleBitrate1080p**: genera un set di 8 file MP4 con audio AAC stereo e allineamento GOP, con velocità compresa fra 6000 e 400 kbps. La risoluzione parte da 1080p e può scendere fino a 360p.
- **EncoderNamedPreset.H264MultipleBitrate720p**: genera un set di 6 file MP4 con audio AAC stereo e allineamento GOP, con velocità compresa fra 3400 e 400 kbps. La risoluzione parte da 720p e può scendere fino a 360p.
- **EncoderNamedPreset.H264MultipleBitrateSD**: genera un set di 5 file MP4 con audio AAC stereo e allineamento GOP, con velocità compresa fra 1600 e 400 kbps. La risoluzione parte da 480p e può scendere fino a 360p.<br/><br/>Per altre informazioni, vedere [Eseguire il caricamento, la codifica e lo streaming di video tramite API](stream-files-tutorial-with-api.md).

### <a name="standardencoderpreset-preset"></a>Set di impostazioni StandardEncoderPreset

[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) descrive le impostazioni da usare per la codifica del video di input con il codificatore Standard. Usare questo set di impostazioni quando si personalizzano i set di impostazioni di trasformazione. 

#### <a name="custom-presets"></a>Set di impostazioni personalizzati

Servizi multimediali supporta in modo completo la personalizzazione di tutti i valori nei set di impostazioni per soddisfare le esigenze e i requisiti di codifica specifici. Per personalizzare i set di impostazioni di trasformazione si usa il set di impostazioni **StandardEncoderPreset**. Per una descrizione dettagliata e un esempio, vedere [How to customize encoder presets](customize-encoder-presets-how-to.md) (Come personalizzare i set di impostazioni del codificatore).

## <a name="scaling-encoding-in-v3"></a>Ridimensionamento della codifica nella versione v3

Per ridimensionare l'elaborazione multimediale, vedere [scalabilità con CLI](media-reserved-units-cli-how-to.md).

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire la codifica da un URL HTTPS usando i set di impostazioni predefinite](job-input-from-http-how-to.md)
* [Codificare un file locale usando i set di impostazioni predefinite](job-input-from-local-file-how-to.md)
* [Creare un set di impostazioni per i requisiti specifici di uno scenario o un dispositivo di destinazione personalizzato](customize-encoder-presets-how-to.md)
* [Eseguire il caricamento, la codifica e lo streaming con Servizi multimediali](stream-files-tutorial-with-api.md)
