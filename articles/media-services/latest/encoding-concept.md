---
title: Codifica nel cloud con Servizi multimediali | Microsoft Docs
description: Questo argomento descrive il processo di codifica con Servizi multimediali di Microsoft Azure
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 11/05/2018
ms.author: juliako
ms.openlocfilehash: 0df13e3364cebe7cb5804b840889bca971b36be2
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51235055"
---
# <a name="encoding-with-media-services"></a>Codifica con Servizi multimediali

Servizi multimediali consente di codificare file multimediali di qualità elevata in formati che possono essere riprodotti su una vasta gamma di browser e dispositivi. Ad esempio, potrebbe essere necessario trasmettere il contenuto nei formati HLS o MPEG DASH di Apple. Questo argomento contiene indicazioni su come codificare il contenuto con Servizi multimediali v3.

Per eseguire la codifica con Servizi multimediali v3, è necessario creare una trasformazione e un processo. Una trasformazione definisce la serie di istruzioni per le impostazioni di codifica e gli output e il processo è un'istanza della serie di istruzioni. Per altre informazioni, vedere [Trasformazioni e processi](transform-concept.md)

Durante la codifica con Servizi multimediali si usano set di impostazioni per indicare al codificatore come elaborare i file multimediali di input. Ad esempio, è possibile specificare la risoluzione video e/o il numero di canali audio desiderati nel contenuto codificato. 

Si può iniziare rapidamente con uno dei set di impostazioni predefiniti basati sulle procedure consigliate del settore oppure si può compilare un set di impostazioni personalizzato per i requisiti di uno specifico scenario o dispositivo. Per altre informazioni, vedere l'articolo sulla [codifica con una trasformazione personalizzata](customize-encoder-presets-how-to.md). 

## <a name="built-in-presets"></a>Set di impostazioni predefiniti

Attualmente Servizi multimediali supporta i set di impostazioni di codifica predefiniti seguenti:  

|**Nome set di impostazioni**|**Scenario**|**Dettagli**|
|---|---|---|
|**BuiltInStandardEncoderPreset**|Streaming|Usato per configurare un set di impostazioni predefinito per la codifica del video di input con il codificatore Standard. <br/>Attualmente sono supportati i set di impostazioni seguenti:<br/>**EncoderNamedPreset.AdaptiveStreaming** (scelta consigliata). Per altre informazioni, vedere [Codificare con una tabella di coppie velocità in bit-risoluzione generata automaticamente](autogen-bitrate-ladder.md).<br/>**EncoderNamedPreset.AACGoodQualityAudio**: genera un singolo file MP4 contenente solo audio stereo con codifica a 192 kbps.<br/>**EncoderNamedPreset.H264MultipleBitrate1080p**: genera un set di 8 file MP4 con audio AAC stereo e allineamento GOP, con velocità compresa fra 6000 e 400 kbps. La risoluzione parte da 1080p e può scendere fino a 360p.<br/>**EncoderNamedPreset.H264MultipleBitrate720p**: genera un set di 6 file MP4 con audio AAC stereo e allineamento GOP, con velocità compresa fra 3400 e 400 kbps. La risoluzione parte da 720p e può scendere fino a 360p.<br/>**EncoderNamedPreset.H264MultipleBitrateSD**: genera un set di 5 file MP4 con audio AAC stereo e allineamento GOP, con velocità compresa fra 1600 e 400 kbps. La risoluzione parte da 480p e può scendere fino a 360p.<br/><br/>Per altre informazioni, vedere [Eseguire il caricamento, la codifica e lo streaming di video tramite API](stream-files-tutorial-with-api.md).|
|**StandardEncoderPreset**|Streaming|Descrive le impostazioni da usare per la codifica del video di input con il codificatore Standard. <br/>Usare questo set di impostazioni quando si personalizzano i set di impostazioni di trasformazione. Per altre informazioni, vedere la procedura per [Personalizzare i set di impostazioni di trasformazione](customize-encoder-presets-how-to.md).|

## <a name="custom-presets"></a>Set di impostazioni personalizzati

Servizi multimediali supporta in modo completo la personalizzazione di tutti i valori nei set di impostazioni per soddisfare le esigenze e i requisiti di codifica specifici. Per personalizzare i set di impostazioni di trasformazione si usa il set di impostazioni **StandardEncoderPreset**. Per una descrizione dettagliata e un esempio, vedere [How to customize encoder presets](customize-encoder-presets-how-to.md) (Come personalizzare i set di impostazioni del codificatore).

## <a name="scaling-encoding-in-v3"></a>Ridimensionamento della codifica nella versione v3

Attualmente, i clienti devono usare il portale di Azure o le API di AMS v2 per impostare le unità riservate, come descritto in [Panoramica del ridimensionamento dell'elaborazione multimediale](../previous/media-services-scale-media-processing-overview.md). 

## <a name="next-steps"></a>Passaggi successivi

### <a name="tutorials"></a>Esercitazioni

L'esercitazione seguente illustra come codificare il contenuto con Servizi multimediali:

* [Eseguire il caricamento, la codifica e lo streaming con Servizi multimediali](stream-files-tutorial-with-api.md)

### <a name="code-samples"></a>Esempi di codice

Gli esempi seguenti contengono codice che illustra come eseguire la codifica con Servizi multimediali:

* [.NET Core](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore)
* [Interfaccia della riga di comando di Azure](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/media-services)

### <a name="sdks"></a>SDK

È possibile usare uno qualsiasi dei seguenti SDK di Servizi multimediali di Azure v3 supportati per codificare il contenuto.

* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
* [REST](https://docs.microsoft.com/rest/api/media/transforms)
* [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
* [Java](https://docs.microsoft.com/java/api/overview/azure/mediaservices)
* [Python](https://docs.microsoft.com/python/api/overview/azure/media-services?view=azure-python)

