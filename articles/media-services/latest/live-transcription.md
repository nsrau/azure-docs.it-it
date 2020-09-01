---
title: Trascrizione in tempo reale
titleSuffix: Azure Media Services
description: Informazioni sulla trascrizione Live di servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 698aa849a82e7c9f9a630ca7040f9d449b3765b3
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89264964"
---
# <a name="live-transcription-preview"></a>Trascrizione in tempo reale (anteprima)

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Servizi multimediali di Azure offre video, audio e testo in protocolli diversi. Quando si pubblica il flusso live con MPEG-DASH o HLS/CMAF, insieme a video e audio, il servizio recapita il testo trascritto in TTML compatibile con IMSC 1.1. Il recapito è incluso nei frammenti MPEG-4 Part 30 (ISO/IEC 14496-30). Se si usa il recapito tramite HLS/TS, il testo viene recapitato come VTT in blocchi.

Quando si attiva la trascrizione in tempo reale, vengono applicati addebiti aggiuntivi. Per informazioni sui prezzi, vedere la sezione video live della [pagina dei prezzi di servizi multimediali](https://azure.microsoft.com/pricing/details/media-services/).

Questo articolo descrive come abilitare la trascrizione in tempo reale quando si trasmette un evento live con servizi multimediali di Azure. Prima di continuare, assicurarsi di avere familiarità con l'uso delle API REST di servizi multimediali V3 (vedere [questa esercitazione](stream-files-tutorial-with-rest.md) per informazioni dettagliate). È anche necessario avere familiarità con il concetto di [streaming live](live-streaming-overview.md) . È consigliabile completare l'esercitazione [streaming live con servizi multimediali](stream-live-tutorial-with-api.md) .

## <a name="live-transcription-preview-regions-and-languages"></a>Lingue e lingue dell'anteprima della trascrizione in tempo reale

La trascrizione in tempo reale è disponibile nelle aree geografiche seguenti:

- Asia sud-orientale
- Europa occidentale
- Europa settentrionale
- Stati Uniti orientali
- Stati Uniti centrali
- Stati Uniti centro-meridionali
- Stati Uniti occidentali 2
- Brasile meridionale

Questo è l'elenco delle lingue disponibili che possono essere trascritte, usare il codice lingua nell'API.

| Linguaggio | Codice lingua |
| -------- | ------------- |
| Catalano  | ca-ES |
| Danese (Danimarca) | da-DK |
| Tedesco (Germania) | de-DE |
| Inglese (Australia) | en-AU |
| Inglese (Canada) | en-CA |
| Inglese (Regno Unito) | en-GB |
| Inglese (India) | en-IN |
| Inglese (Nuova Zelanda) | en-NZ |
| Inglese (Stati Uniti) | it-IT |
| Spagnolo (Spagna) | es-ES |
| Spagnolo (Messico) | es-MX |
| Finlandese (Finlandia) | fi-FI |
| Francese (Canada) | fr-CA |
| Francese (Francia) | fr-FR |
| Italiano (Italia) | it-IT |
| Olandese (Paesi Bassi) | nl-NL |
| Portoghese (Brasile) | pt-BR |
| Portoghese (Portogallo) | pt-PT |
| Svedese (Svezia) | sv-SE |

## <a name="create-the-live-event-with-live-transcription"></a>Crea l'evento live con la trascrizione in tempo reale

Per creare un evento live con la trascrizione attivata, inviare l'operazione PUT con la versione API 2019-05-01-Preview, ad esempio:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

L'operazione ha il corpo seguente, in cui viene creato un evento Live pass-through con RTMP come protocollo di inserimento. Si noti l'aggiunta di una proprietà trascrizioni.

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

## <a name="start-or-stop-transcription-after-the-live-event-has-started"></a>Avvia o arresta la trascrizione dopo l'avvio dell'evento Live

È possibile avviare e arrestare la trascrizione in tempo reale mentre l'evento Live si trova nello stato in esecuzione. Per altre informazioni sull'avvio e l'arresto di eventi live, vedere la sezione operazioni a esecuzione prolungata in [sviluppare con le API di servizi multimediali V3](media-services-apis-overview.md#long-running-operations).

Per attivare le trascrizioni Live o aggiornare il linguaggio di trascrizione, applicare patch all'evento Live per includere una proprietà "trascrizioni". Per disattivare le trascrizioni Live, rimuovere la proprietà "trascrizioni" dall'oggetto evento Live.  

> [!NOTE]
> L'attivazione o la disattivazione della trascrizione **più di una volta** durante l'evento Live non è un scenario supportato.

Si tratta della chiamata di esempio per attivare le trascrizioni in tempo reale.

PATCH ```https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview```

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

## <a name="transcription-delivery-and-playback"></a>Recapito e riproduzione della trascrizione

Vedere l'articolo Panoramica della creazione [dinamica dei pacchetti](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery) di come il servizio usa la creazione dinamica dei pacchetti per distribuire video, audio e testo in protocolli diversi. Quando si pubblica il flusso live con MPEG-DASH o HLS/CMAF, insieme a video e audio, il servizio recapita il testo trascritto in TTML compatibile con IMSC 1.1. Il recapito è incluso nei frammenti MPEG-4 Part 30 (ISO/IEC 14496-30). Se si usa il recapito tramite HLS/TS, il testo viene recapitato come VTT in blocchi. È possibile usare un lettore Web come il [Azure Media Player](use-azure-media-player.md) per riprodurre il flusso.  

> [!NOTE]
> Se si usa Azure Media Player, usare la versione 2.3.3 o successiva.

## <a name="known-issues"></a>Problemi noti

Per l'anteprima, di seguito sono riportati i problemi noti della trascrizione Live:

- Le app devono usare le API di anteprima, descritte nella [specifica openapi di servizi multimediali V3](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json).
- La protezione DRM (Digital Rights Management) non si applica alla traccia del testo, ma è possibile solo la crittografia della busta AES.

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica di Servizi multimediali](media-services-overview.md)
