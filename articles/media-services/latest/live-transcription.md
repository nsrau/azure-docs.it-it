---
title: Trascrizione in tempo reale
titleSuffix: Azure Media Services
description: Informazioni sulla trascrizione live di Servizi multimediali di Azure.Learn about Azure Media Services live transcription.
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
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: b364b6e70e3b5723c483bc3435f0c3a152c03aa9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499867"
---
# <a name="live-transcription-preview"></a>Trascrizione dal vivo (anteprima)

Il servizio multimediale di Azure offre video, audio e testo in protocolli diversi. Quando pubblichi il tuo live streaming utilizzando MPEG-DASH o HLS/CMAF, quindi insieme a video e audio, il nostro servizio fornisce il testo trascritto in TTML compatibile con IMSC1.1. La consegna è confezionata in frammenti MPEG-4 Part 30 (ISO/IEC 14496-30). Se si utilizza la consegna tramite HLS/TS, il testo viene recapitato come VTT in blocchi.

Questo articolo descrive come abilitare la trascrizione live durante lo streaming di un evento live con Servizi multimediali di Azure v3. Prima di continuare, assicurarsi di avere familiarità con l'uso delle API REST di Servizi multimediali v3 (vedere [questa esercitazione](stream-files-tutorial-with-rest.md) per informazioni dettagliate). Si dovrebbe anche avere familiarità con il concetto di [live streaming.](live-streaming-overview.md) Si consiglia di completare l'esercitazione [Streaming in diretta con Servizi multimediali.](stream-live-tutorial-with-api.md)

> [!NOTE]
> Attualmente, la trascrizione dal vivo è disponibile solo come funzione di anteprima nella regione degli Stati Uniti occidentali 2. Supporta la trascrizione delle parole pronunciate in inglese al testo. Il riferimento API per questa funzionalità si trova di seguito: in anteprima, i dettagli non sono disponibili con i nostri documenti REST.

## <a name="creating-the-live-event"></a>Creazione dell'evento live

Per creare l'evento live, si invia l'operazione PUT alla versione 2019-05-01-preview, ad esempio:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

L'operazione ha il corpo seguente (in cui viene creato un evento live pass-through con RTMP come protocollo di inserimento). Si noti l'aggiunta di una proprietà di trascrizioni. L'unico valore consentito per la lingua è en-US.

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

Eseguire il polling dello stato dell'evento live fino a quando non passa allo stato "In esecuzione", che indica che è ora possibile inviare un feed RTMP di contributo. È ora possibile seguire gli stessi passaggi di questa esercitazione, ad esempio controllare il feed di anteprima e creare output dal vivo.

## <a name="transcription-delivery-and-playback"></a>Consegna e riproduzione della trascrizione

Leggi l'articolo Panoramica della [creazione di pacchetti dinamici](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery) su come il nostro servizio utilizza la confezione dinamica per distribuire video, audio e testo in protocolli diversi. Quando pubblichi il tuo live streaming utilizzando MPEG-DASH o HLS/CMAF, quindi insieme a video e audio, il nostro servizio fornisce il testo trascritto in TTML compatibile con IMSC1.1. Questa consegna è confezionata in frammenti MPEG-4 Part 30 (ISO/IEC 14496-30). Se si utilizza la consegna tramite HLS/TS, il testo viene recapitato come VTT in blocchi. È possibile usare un lettore Web, ad esempio [Azure Media Player,](use-azure-media-player.md) per riprodurre il flusso.  

> [!NOTE]
> Se si usa Azure Media Player, usare la versione 2.3.3 o successiva.

## <a name="known-issues"></a>Problemi noti

Per l'anteprima, di seguito sono riportati i problemi noti relativi alla trascrizione dal vivo:

* La funzione è disponibile solo in West US 2.
* Le app devono usare le API di anteprima, descritte in [Media Services v3 OpenAPI Specification](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json).
* L'unica lingua supportata è l'inglese (en-us).
* Con la protezione del contenuto, è supportata solo la crittografia della busta AES.

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica di Servizi multimediali](media-services-overview.md)
