---
title: Latenza LiveEvent in Servizi multimediali di Azure | Microsoft Docs
description: Panoramica della latenza LiveEvent e procedura per l'impostazione di una bassa latenza.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/16/2018
ms.author: juliako
ms.openlocfilehash: b167d3424d520cf8be515eec9d495164dd9785ab
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2018
ms.locfileid: "52682096"
---
# <a name="liveevent-latency-in-media-services"></a>Latenza LiveEvent in Servizi multimediali di Azure

Questo articolo illustra come impostare una bassa latenza in un **LiveEvent**. Esamina anche alcuni risultati tipici visualizzati quando si usano le impostazioni di bassa latenza in diversi lettori. I risultati variano a seconda della rete CDN e della latenza di rete. 

Per usare la nuova funzionalità **LowLatency** impostare il flag **StreamOptionsFlag** su **LowLatency** nel **LiveEvent**. Quando il flusso è attivo in esecuzione, nella pagina di demo di [Azure Media Player](http://ampdemo.azureedge.net/) (AMP) è possibile impostare le opzioni di riproduzione per il profilo di euristica a bassa latenza.

L'esempio di .NET seguente mostra come impostare **LowLatency** nel **LiveEvent**:

```csharp
LiveEvent liveEvent = new LiveEvent(
            location: mediaService.Location, 
            description: "Sample LiveEvent for testing",
            vanityUrl: false,
            encoding: new LiveEventEncoding(
                        // Set this to Basic to enable a transcoding LiveEvent, and None to enable a pass-through LiveEvent
                        encodingType:LiveEventEncodingType.None, 
                        presetName:null
                    ),
            input: new LiveEventInput(LiveEventInputProtocol.RTMP,liveEventInputAccess), 
            preview: liveEventPreview,
            streamOptions: new List<StreamOptionsFlag?>()
            {
                // Set this to Default or Low Latency
                // To use low latency optimally, you should tune your encoder settings down to 1 second "Group Of Pictures" (GOP) length instead of 2 seconds.
                StreamOptionsFlag.LowLatency
            }
        );
```                

Vedere l'esempio completo: [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

## <a name="liveevents-latency"></a>Latenza LiveEvents

La tabella seguente mostra i risultati tipici della latenza (quando è attivato il flag LowLatency) in Servizi multimediali, misurati dal momento in cui il feed di contributo raggiunge il servizio al momento in cui un utente vede la riproduzione sul lettore. Per un uso ottimale della bassa latenza, è consigliabile ridurre le impostazioni del codificatore fino alla durata di un 1 secondo GOP (Group Of Pictures). Quando si usa una durata GOP maggiore, ridurre al minimo il consumo di larghezza di banda e ridurre la velocità in bit nella stessa frequenza dei fotogrammi. È particolarmente utile nei video con meno movimento.

### <a name="pass-through"></a>Pass-through 

||Bassa latenza (GOP 2 secondi) abilitata|Bassa latenza (GOP 1 secondo) abilitata|
|---|---|---|
|DASH in AMP|10 secondi|8 secondi|
|HLS nel lettore iOS nativo|14 secondi|10 secondi|

### <a name="live-encoding"></a>Codifica live

||Bassa latenza (GOP 2 secondi) abilitata|Bassa latenza (GOP 1 secondo) abilitata|
|---|---|---|
|DASH in AMP|14 secondi|10 secondi|
|HLS nel lettore iOS nativo|18s|13s|

> [!NOTE]
> La latenza end-to-end può variare in base alle condizioni della rete locale o con l'introduzione di un livello di memorizzazione nella cache della rete CDN. È consigliabile testare l'esatta configurazione in uso.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dello streaming live](live-streaming-overview.md)
- [Esercitazione sullo streaming live](stream-live-tutorial-with-api.md)

