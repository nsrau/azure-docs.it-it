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
ms.openlocfilehash: a74f2e53127b506f42ff49018c3df2985396646d
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/29/2018
ms.locfileid: "52619818"
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
                // To use low latency optimally, you should tune your encoder settings down to 1 second GOP size instead of 2 seconds.
                StreamOptionsFlag.LowLatency
            }
        );
```                

Vedere l'esempio completo: [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

## <a name="pass-through-liveevents-latency"></a>Latenza LiveEvent pass-through

La tabella seguente mostra i risultati tipici della latenza (quando è attivato il flag LowLatency) in Servizi multimediali di Azure, misurati dal momento in cui il feed di contributo raggiunge il servizio al momento in cui un lettore può richiedere la riproduzione.

||Bassa latenza (GOP 2 secondi) abilitata|Bassa latenza (GOP 1 secondo) abilitata|
|---|---|---|
|DASH in AMP|10 secondi|8 secondi|
|HLS nel lettore iOS nativo|14 secondi|10 secondi|

> [!NOTE]
> La latenza end-to-end può variare in base alle condizioni della rete locale o con l'introduzione di un livello di memorizzazione nella cache della rete CDN. È consigliabile testare l'esatta configurazione in uso.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dello streaming live](live-streaming-overview.md)
- [Esercitazione sullo streaming live](stream-live-tutorial-with-api.md)

