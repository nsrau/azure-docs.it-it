---
title: Trascrizione Live di servizi multimediali di Azure | Microsoft Docs
description: Questo articolo illustra la trascrizione Live di servizi multimediali di Azure.
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
ms.date: 08/27/2019
ms.author: juliako
ms.openlocfilehash: 98084b9bb6f19d22c7995d3044bb32ceaa947dc5
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74040424"
---
# <a name="live-transcription-preview"></a>Trascrizione in tempo reale (anteprima)

Servizi multimediali di Azure offre testo video, audio e ora in protocolli diversi. Quando si pubblica il flusso live con MPEG-DASH o HLS/CMAF, quindi insieme a video e audio, il servizio consegnerà il testo trascritto in TTML compatibile con IMSC 1.1, incluso nei frammenti MPEG-4 Part 30 (ISO/IEC 14496-30). Se si usa il recapito tramite HLS/TS, il testo viene recapitato come VTT in blocchi. 

Questo articolo descrive come abilitare la trascrizione in tempo reale quando si trasmette un evento live con servizi multimediali di Azure V3. Prima di procedere, assicurarsi di avere familiarità con l'uso delle API REST di servizi multimediali V3 (vedere [questa esercitazione](stream-files-tutorial-with-rest.md) per informazioni dettagliate). È anche necessario avere familiarità con il concetto di [streaming live](live-streaming-overview.md) . È consigliabile completare l'esercitazione [streaming live con servizi multimediali](stream-live-tutorial-with-api.md) . 

> [!NOTE]
> Attualmente, la trascrizione in tempo reale è disponibile solo come funzionalità di anteprima nell'area Stati Uniti occidentali 2. Supporta la trascrizione di parole pronunciate in inglese in testo. Il riferimento all'API per questa funzionalità è presente in questo documento. poiché è in anteprima, i dettagli non sono disponibili con i documenti REST. 

## <a name="creating-the-live-event"></a>Creazione dell'evento Live 

Per creare l'evento Live, è necessario inviare l'operazione PUT alla versione 2019-05-01, ad esempio: 

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

L'operazione ha il corpo seguente, in cui viene creato un evento Live pass-through con RTMP come protocollo di inserimento. Si noti l'aggiunta di una proprietà trascrizioni. L'unico valore consentito per la lingua è en-US. 

```
{ 
  "properties": { 
    "description": "Demonstrate how to enable live transcriptions", 
    "input": { 
      "streamingProtocol": "RTMP", 
      "accessControl": { 
        "ip": { 
          "allow": [ 
            { 
              "name": "Allow All", 
              "address": "0.0.0.0", 
              "subnetPrefixLength": 0 
            } 
          ] 
        } 
      } 
    }, 
    "preview": { 
      "accessControl": { 
        "ip": { 
          "allow": [ 
            { 
              "name": "Allow All", 
              "address": "0.0.0.0", 
              "subnetPrefixLength": 0 
            } 
          ] 
        } 
      } 
    }, 
    "encoding": { 
      "encodingType": "None" 
    }, 
    "transcriptions": [ 
      { 
        "language": "en-US" 
      } 
    ], 
    "vanityUrl": false, 
    "streamOptions": [ 
      "Default" 
    ] 
  }, 
  "location": "West US 2" 
} 
```

È necessario eseguire il polling dello stato dell'evento live fino a quando non passa allo stato "Running", che indica che è ora possibile inviare un feed RTMP di contributo. È ora possibile seguire la stessa procedura descritta in questa esercitazione, ad esempio il controllo del feed di anteprima e la creazione di output Live. 

## <a name="delivery-and-playback"></a>Recapito e riproduzione 

Vedere l'articolo Panoramica della creazione [dinamica dei pacchetti](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery) del modo in cui il servizio usa la creazione dinamica dei pacchetti per distribuire testo video, audio e ora in protocolli diversi. Quando si pubblica il flusso live con MPEG-DASH o HLS/CMAF, quindi insieme a video e audio, il servizio consegnerà il testo trascritto in TTML compatibile con IMSC 1.1, incluso nei frammenti MPEG-4 Part 30 (ISO/IEC 14496-30). Se si usa il recapito tramite HLS/TS, il testo viene recapitato come VTT in blocchi. È possibile usare un lettore Web come il [Azure Media Player](use-azure-media-player.md) per riprodurre il flusso.  

> [!NOTE]
>  Se si usa Azure Media Player, usare la versione 2.3.3 o successiva.

## <a name="known-issues"></a>Problemi noti 

In anteprima, di seguito sono riportati i problemi noti con la trascrizione in tempo reale 

* La funzionalità è disponibile solo negli Stati Uniti occidentali 2.
* Le applicazioni devono usare le API di anteprima, descritte nella specifica della [specifica openapi di servizi multimediali V3](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json) .
* L'unica lingua supportata è l'inglese.
* Per quanto riguarda la protezione del contenuto, è supportata solo la crittografia della busta AES.

## <a name="next-steps"></a>Passaggi successivi

[Panoramica di Servizi multimediali](media-services-overview.md)